---
title: 错误运用递归的一些反思
date: 2017-08-21 22:28:24
tags: Algorithm
---


## 题目描述
输入一个链表，输出该链表中倒数第k个结点

<!-- more -->

## 错误代码实现
{% codeblock %}
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if(head == null || k <= 0){
            return null;
        }
       	if(FindKthToTail(head, k) != FindKthToTail(head.next, k)){
       		return head;
      	}
      	return FindKthToTail(head.next, k);
     }
}
{% endcodeblock %}

## 思路与反思
粗略一看以为这道题很简单(事实上确实不难)，一次性写完代码就提交了，结果直接报错。思路如下：运用递归，当head的倒数第k个节点不等于head.next的倒数第k个节点时(此时FindKthToTail(head.next, k)为null)，head即为我们所要寻找的倒数第k个节点，否则返回head.next的倒数第k个节点。思路不错，但是代码就错得离谱了！！因为代码里会一直递归直到返回null,因为第15行的if条件判断里居然使用递归函数本身！最后用了完全不同的思路，放弃了递归，代码如下：

## 正确实现

{% codeblock %}
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if(head == null || k <= 0){
            return null;
        }
		ListNode first = head;
        ListNode second = head;
        for(int i = 1; i < k; i++){
            first = first.next;
            if(first == null){
                return null;
            }
        }
        while(first.next != null){
            first = first.next;
            second = second.next;
        }
        return second;
    }
}
{% endcodeblock %}

## 最后的反思
递归的一个重要特性就是不断的调用本身，同时不断降低问题的规模，这就带来了一个问题，怎样使它终止？ 方法就是在递归函数中有一段条件代码可以明确地可以结束递归，称为递归出口。而这段条件代码执行的条件则是问题的规模已经达到最小，此时直接给出问题的直接解答，也就是基准条件，斐波那契就是一个十分经典的递归问题，可以从中感受到递归的思想！那么如何写出高质量的递归程序？
{% blockquote %}
(1) 是每次调用在规模上都有所缩小（通常是减半）；

(2) 是相邻两次重复之间有紧密的联系，前一次要为后一次做准备（通常前一次的输出就作为后一次的输入）；

(3) 是在问题的规模极小时必须用直接给出解答而不再进行递归调用，因而每次递归调用都是有条件的(以规模未达到直接解答的大小为条件)，无条件递归调用将会成为死循环而不能正常结束。
{% endblockquote %}
遵循以上三个准则即可！