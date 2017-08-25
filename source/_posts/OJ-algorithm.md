---
title: OJ & 算导
date: 2017-08-18 17:58:32
tags: 
- OJ
- algorithm
---

眨眼间已是八月中旬，不得不让人感慨时不我待！

暑假的这半个月安排的比较乱，给自己安排的主要任务就是看完王福强巨佬的《spring揭秘》，目前已经看过大半，然而关于spring我依然有许多不会。除了看书之外我接触了一下Python，练习过几个小的java项目，比如爬虫，还有就是开始刷oj了，目前是在牛客网上写了一些剑指offer的一些题，题目不难，但不能流畅的用代码实现。目前打算先在牛客上练练手，保持一天AC一道题，等到开学后在LeetCode上试试。还是谈谈目前刷题状态吧！
<!-- more -->
## 题目描述
输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

## 思路与实现
看到这道题，我的第一反应是建立一个等长辅助数组，遍历所给数组，先将奇数放入辅助数组，后放入偶数，于是辅助数组分成两个部分，前半部分奇数，后半部分偶数，且相对顺序保持不变，最后拷贝回所给数组。
有大致想法后我直接开始写代码，这带来了一个问题: 写代码时并没有考虑周全，有不少细节没处理好，写到一半又回去改前面的代码，于是花了不少时间才最终写出来粗略版本。
{% codeblock %}
public class Solution {
    public void reOrderArray(int [] array) {
        int[] temp = new int[array.length];
        int j = 0;
        for(int i = 0; i < array.length; i++){
            if(array[i] % 2 != 0){
                temp[j] = array[i];
                j++;
            }
        }
        int oddIndex = 0;
        int evenIndex = j;
        for(int i = oddIndex; i < array.length; i++){
            if(array[i] % 2 == 0){
                temp[j] = array[i];
                j++;
            }
            array[i] = temp[oddIndex];
            oddIndex++;
        }
        for(int i = evenIndex; i < temp.length; i++){
            array[i] = temp[i];
        }
    }
}
{% endcodeblock %}
运行通过，但我在本地断点调试之后发现还有改进空间，且有一部代码完全可以删掉，修改后代码如下：
{% codeblock %}
public class Solution {
    public void reOrderArray(int [] array) {
        int[] temp = new int[array.length];
        int j = 0;
        for(int i = 0; i < array.length; i++){
            if(array[i] % 2 != 0){
                temp[j] = array[i];
                j++;
            }
        }
        int tempIndex = 0;
        for(int i = tempIndex; i < array.length; i++){
            if(array[i] % 2 == 0){
                temp[j] = array[i];
                j++;
            }
            array[i] = temp[tempIndex];
            tempIndex++;
        }
    }
}
{% endcodeblock %}
第二次遍历个人认为还算比较巧妙。总结来看，还是不要着急直接就写代码，先在纸上吧大致的伪代码写出来再动手可能会比较好。
不过看过讨论区之后，好吧。。其实这道题考察的是冒泡排序。。。先偶后奇则交换。不过冒泡的时间复杂度为O(n2), 采用辅助数组的话则是用空间换取时间，时间复杂度为O(n)。

## 姗姗来迟的算导
{% asset_img algorithm.jpg  姗姗来迟的算导 %}
这次买书发货比较慢以至于今天我才拿到手。这算是我第三次学习数据结构和算法了，第一次是学完C后看的《大话数据结构》，第二次是学过Java之后看的《数据结构与算法分析--Java语言描述》，但目前算法能力并不高甚至还很菜鸡，毕竟光看书是不够的，尽管我还写过一些小的应用demo。希望这次能够提高一下我的算法能力吧！






