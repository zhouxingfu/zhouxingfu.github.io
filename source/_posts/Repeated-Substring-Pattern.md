---
title: Repeated Substring Pattern
date: 2020-09-23 19:36:00
tags:
categories: leetcode
---
__<font color=red>题目</font>__

____Given a non-empty string check if it can be constructed by taking a substring of it and appending multiple copies of the substring together. You may assume the given string consists of lowercase English letters only and its length will not exceed 10000.____

__Example 1:__  

    Input: "abab"
    Output: True
    Explanation: It's the substring "ab" twice.  

__Example 2:__  

    Input: "aba"
    Output: False 


__Example 3:__  

    Input: "abcabcabcabc"
    Output: True
    Explanation: It's the substring "abc" four times. (And the substring "abcabc" twice.)  


__思路__  

start : 代表要比较的子串的开始索引  

length : 代表当前的字串长度, length最多为str.length()/2长度，而且还得是偶数字节

循环不定式  
如果duplicate的length [1, str.length()/2]

那么我们可以实时得到默认的子串个数就是 str.length() / length  = compare_times

这样就是说我们要比较compare_times次，其中每一次比较要比较length长度


```
class Solution {
public:
    bool repeatedSubstringPattern(string s) {
        int len = 1, start=0, cmp_times=0;
        for(int len = 1; len <= (floor)(s.length() / 2); ++len)
        {
            if(s.length() % len != 0)
                continue;
            cmp_times = s.length() / len;
            bool bCurDup = true;
            for(int i=1; i<cmp_times; ++i)
            {
                if(bCurDup == true)
                    for(int j=0; j<len; ++j)
                    {
                        if(s[j] != s[i*len+j])
                        {
                            bCurDup = false;
                            break;
                        }
                    }
                else
                {
                    break;
                }
            }
            if(bCurDup == true)
            {
                return true;
            }
        }
        
        return false;
    }
};
```

