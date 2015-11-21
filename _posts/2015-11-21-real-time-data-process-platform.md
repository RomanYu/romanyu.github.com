---
layout: post
title: Real Time Data Process Platform 
excerpt: "A document about real-time data process using Kafka + Cassandra + Spark"
tags: [Spark, Cassandra, Kafka]
comments: true
---

## Backgroud

The mobile phone will send a message to our server when an App is installed, in general, the message - called 'activate log' - contains some information, such as the timestamp the app installed, the name and version of the app, etc. In order to see publishes of upgrade or new versions are good or bad, we need to know how many users install the App. 

The matter should be noticed is that a device should be caculated only once even if the same App is installed many times. Maybe a user re-install the same App after uninstalled it, we just need the message received at the first installation, and discard others - reduplicative activate log.

In general case, we get more than 200 thousand activates (both new installation and upgrade) just in the United States, at some special time, the number may exceed 1 million.

## Previous Work

We have a non-real-time process platform, using Hadoop for caculation, HDFS for storage of log files, HBase for dropping-reduplication. 

HBase[^1] has a natrual charater suitable to dropping-reduplication bacause each inserted data has a timestamp, each cell can store one or more records of multiple versions with different timestamps. The version with the maximal timestamp means that it is the latest inserted one, and we will get the letest version of each cell when we query Hbasei by default. In order to discard all the later activate log but store the first one, just guarantee the first log has the maximal timestamp, although the reduplicative records are inserted into HBase, we can also get the first log with maximal timestamp. 

[^1]: <http://hbase.apache.org/>

The official release of HBase has no method to insert data using custom timestamp, my collegue modified the source of `HBaseStorage(org.apache.pig.backend.hadoop.hbase.HBaseStorage)` to support custom timestamp in Pig[^2], so we insert the first activate log with a maximal custom timestamp.

[^2]: <http://pig.apache.org/>

We insert daily activate log into HBase to discard reduplication, and dump it on HDFS, then start a Hadoop[^3] Streaming job to do a statistic.

[^3]: <http://hadoop.apache.org/>

## Imporve

There are some factors that block us to realize real-time computing, Hadoop Streaming job has its drawback to real-time job, and HBase's shell has limitations and so on.

#### Spark

Compare to traditional off-line batch job, Spark[^4] Streaming makes it easy to build scalable fault-tolerant streaming applications. 

[^4]: <http://spark.apache.org/>

> Internally, it works as follows. Spark Streaming receives live input data streams and divides the data into batches, which are then processed by the Spark engine to generate the final stream of results in batches.

<figure>
    <a href="/attachment/real-time-process-platform/streaming-flow.png"><img src="/attachment/real-time-process-platform/streaming-flow.png"></a>
</figure>

You could select a specific interval for yourself, so Spark Streaming gets a small batch data and process on it which could improve the real-time ability. 

#### Cassandra

Compare to the limitation of shell in HBase, Cassandra[^5] - another NoSql - provides a more convenient acess, that is Cql. Although Cql is not as strong as Sql of relational database, it has the basic CRUD, a.k.a. `insert`/`select`/`update`/`delete` in SQL, which make it much clearer and easier to handle with data.

[^5]: <http://cassandra.apache.org/>

> bin/cqlsh is an interactive command line interface for Cassandra. cqlsh allows you to execute CQL (Cassandra Query Language) statements against Cassandra. Using CQL, you can define a schema, insert data, execute queries.

In additional, you could filter data in Cassandra server using `select` statement, that is, computing system could just read the specific data, not a complete table, which make process more effective. And many advantages make of Cassandra lead it to be massively scalable. 

> Cassandra supplies linear scalability, meaning that capacity may be easily added simply by adding new nodes online. For example, if 2 nodes can handle 100,000 transactions per second, 4 nodes will support 200,000 transactions/sec and 8 nodes will tackle 400,000 transactions/sec:

<figure>
    <a href="/attachment/real-time-process-platform/intro-cassandra.png"><img src="/attachment/real-time-process-platform/intro-cassandra.png"></a>
</figure>

Meanwhile, although the insert statement in Cassandra doesn't check primary key confilcts like relational database in default, it supports write-time as the timestamp of each cell in HBase, record with a larger write-time will overwrite the smaller one.

~~~
INSERT INTO NerdMovies (movie, director, main_actor, year)
                VALUES ('Serenity', 'Joss Whedon', 'Nathan Fillion', 2005)
                USING TIMESTAMP 86400;
~~~

#### Kakfa

We receive many logs about activate and active and others every day, which are divided by some key words, then we sent the data we need to Kafka[^6] cluster. Also, messages in Kafka are divided by topics, that is, you could use any topic you want.

> Apache Kafka is publish-subscribe messaging rethought as a distributed commit log.

[^6]: <http://kafka.apache.org/>

<figure>
    <a href="/attachment/real-time-process-platform/producer-consumer.png"><img src="/attachment/real-time-process-platform/producer-consumer.png"></a>
</figure>

Something may be confused is that whether the log would be discard after any consumer used it? No, Kafka sustains its messages until the date of this log file is over a threshold which you can set yourself or 7 days in default.

## Results

The picture below shows the statitstics of Spark Streaming, as we can see, the input rate is around 9.11 events/sec that means there are more than 9 messages read from the Kafka cluster averagely. In brief, our processing contains parse, formation and insertion on every log, and the job process on a small batch data every 10 senconds, the processing time of a batch is around 81ms.

<figure>
    <a href="/attachment/real-time-process-platform/streaming-statistics.png"><img src="/attachment/real-time-process-platform/streaming-statistics.png"></a>
</figure>

Then, we start another job every minute to query the data newly inserted into Cassnadra and do a statistic. So we can know how many new users install our App and how many old users upgrade our App in time because our results are freshed every minute.

We can also use this framework to calculate DAU, a.k.a daily active user in real time.

(END)
