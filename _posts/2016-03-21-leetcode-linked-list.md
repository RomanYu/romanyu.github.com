---
layout: post
title: Singly Linked List On Leetcode.com
excerpt: "A document about real-time data process using Kafka + Cassandra + Spark"
tags: [Algorithm, Leetcode.com, Linked List]
comments: true
---

## Linked List

This part is about operations on singly linked list.

#### 2. Add Two Numbers

You are given two linked lists representing two non-negative numbers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)

Output: 7 -> 0 -> 8

Solution:

{% highlight java %}
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode p_l1 = l1;
        ListNode p_l2 = l2;
        ListNode p_ret = new ListNode(0);
        ListNode ret = p_ret;
        int carry = 0;
        while(p_l1 != null || p_l2 != null) {
            int sum = 0;
            if(p_l1 == null) {
                sum = (0 + p_l2.val + carry);
                p_l2 = p_l2.next;
            }
            else if(p_l2 == null) {
                sum = (p_l1.val + 0 + carry);
                p_l1 = p_l1.next;
            }
            else {
                sum = (p_l1.val + p_l2.val + carry);
                p_l1 = p_l1.next;
                p_l2 = p_l2.next;
            }
            int number = sum % 10;
            carry = sum / 10;
            ListNode p_temp = new ListNode(number);
            p_ret.next = p_temp;
            p_ret = p_ret.next;
            if(ret.next == null)
                ret.next = p_ret;
        }
        if(carry != 0)
            p_ret.next = new ListNode(carry);
        return ret.next;
    }
}
{% endhighlight %}
