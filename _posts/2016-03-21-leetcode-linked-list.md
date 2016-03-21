---
layout: post
title: Singly Linked List On Leetcode.com
excerpt: "A document about real-time data process using Kafka + Cassandra + Spark"
tags: [Algorithm, Leetcode.com, Linked List]
comments: true
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


## Introduction

This part is about operations on singly linked list. Questions are copied from [Leetcode.com](http://leetcode.com) and sorted by the question numbers. Solutions are designed and programed by [Yao Yu](/about).

---

#### 2. Add Two Numbers

**Difficulty: Medium**

You are given two linked lists representing two non-negative numbers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

Sample:

    Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
    Output: 7 -> 0 -> 8

Solution:

{% highlight java linenos %}
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

---

#### 19. Remove Nth Node From End of List

**Difficulty: Easy**

Given a linked list, remove the n-th node from the end of list and return its head.

Sample:

    Given linked list: 1->2->3->4->5, and n = 2.
    After removing the second node from the end, the linked list becomes 1->2->3->5.

Solution:

{% highlight java linenos %}
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode ret = new ListNode(0);
        ret.next = head;
        ListNode p1 = ret;
        ListNode p2 = ret;
        while(n > 0) {
            p2 = p2.next;
            n--;
        }
        while(p2.next != null) {
            p1 = p1.next;
            p2 = p2.next;
        }
        p1.next = p1.next.next;
        return ret.next;
    }
}
{% endhighlight %}

---

#### 21. Merge Two Sorted Lists 

**Difficulty: Easy**

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

Solution:

{% highlight java linenos %}
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode p_ret = new ListNode(0);
        ListNode ret = p_ret;
        ListNode p_l1 = l1;
        ListNode p_l2 = l2;
        while(p_l1 != null || p_l2 != null) {
            if(p_l1 == null) {
                p_ret.next = p_l2;
                break;
            } 
            else if(p_l2 == null) {
                p_ret.next = p_l1;
                break;
            } 
            else {
                if(p_l1.val <= p_l2.val) {
                    ListNode p_temp = new ListNode(p_l1.val);
                    p_ret.next = p_temp;
                    p_ret = p_ret.next;
                    p_l1 = p_l1.next;
                }
                else {
                    ListNode p_temp = new ListNode(p_l2.val);
                    p_ret.next = p_temp;
                    p_ret = p_ret.next;
                    p_l2 = p_l2.next;
                }
            }
            if(ret.next == null) {
                ret.next = p_ret;
            }
        }
        return ret.next;
    }
}
{% endhighlight %}

---

#### 24. Swap Nodes in Pairs

**Difficulty: Medium**

Given a linked list, swap every two adjacent nodes and return its head.

Sample:

	Given 1->2->3->4, you should return the list as 2->1->4->3.

Note:

Your algorithm should use only constant space. You may not modify the values in the list, only nodes itself can be changed.

Solution:

{% highlight java linenos %}
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        ListNode ret = new ListNode(0);
        ret.next = head;
        ListNode p1 = ret;
        ListNode p2 = head;
        ListNode temp;
        while(p2 != null && p2.next != null) {
            temp = p2.next.next;
            p2.next.next = p2;
            p1.next = p2.next;
            p2.next = temp;
            p1 = p2;
            p2 = temp;
        }
        return ret.next;
    }
}
{% endhighlight %}

---

To be continued.
