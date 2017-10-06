---
title: 互异元素全排列--递归实现
date: 2017-09-01 21:07:08
tags: 
- Algorithm
- Future
- Java
---

## 代码实现
```
import java.util.*;

public class Solution {
	public void permutation1(String str, String result, int len){
		if(result.length() == len){
			System.out.println(result);
		}
		else {
			for(int i = 0; i < str.length(); i++){
				if(result.indexOf(str.charAt(i)) < 0){
					permutation1(str, result + str.charAt(i), len);
				}
			}
		}
	}
	public static void main(String[] args){
		String s = "123";
		resutl = "";
		(new Solution()).permutation1(s, result, s.length());
	}
}
```

## 思路
暂无
