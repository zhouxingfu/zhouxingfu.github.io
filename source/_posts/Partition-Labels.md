---
title: Partition Labels
date: 2020-09-23 22:24:48
tags:
categories: leetcode
---
__<font color=red>题目</font>__  

__A string S of lowercase English letters is given. We want to partition this string into as many parts as possible so that each letter appears in at most one part, and return a list of integers representing the size of these parts.__  

__Example 1:__  

    Input: S = "ababcbacadefegdehijhklij"
    Output: [9,7,8]
    Explanation:
    The partition is "ababcbaca", "defegde", "hijhklij".
    This is a partition so that each letter appears in at most one part.
    A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits S into less parts.

__Note:__

* S will have length in range [1, 500].
* S will consist of lowercase English letters ('a' to 'z') only.  

<!--more-->

__思路__  

输入是一个string，要求我们拆分成尽可能多的子串，不同字串间没有重复字符。  
要求的输出是每个子串的size。  


我们可以先对string做一次全遍历，用map< char, pair<int, int> > 记录每个字符在string中出现的起始索引，至此，我们得到了一些集合，接下来就是求这些集合的并集，集合求并集的前提是 —— 这两个集合有相交区域，否则无法求并集。  

下面是一个不算成功的实现，耗时太高，内存占用太大。
在写的时候还遇到一个问题，就是<font color=blue>__map默认是按照key的升序来排列的，而且map的Compare是根据key来比较的，就算我们写一个自己的Compare，也没办法按照value来排序；想着用unordered_map，但今天才意识到，原来unordered_map的存储顺序也不是插入顺序，是根据hash的计算。map底层是红黑树，unordered_map底层是hash。__</font>  __

现在相出的解决方案是map<K, T> 重新保存为map<T, K>，但这样也就增加耗时。

```
class Solution {
public:
    vector<int> partitionLabels(string S) {
        map<char, pair<int, int>> map_assemble;
        vector<pair<int, int>> vec_result;
        int length = S.length();
        for(int i=0; i<length; ++i)
        {
            if(map_assemble.find(S[i]) != map_assemble.end())
            {
                //update
                map_assemble[S[i]].second = i;
            }
            else
            {
                //add
                map_assemble[S[i]] = make_pair(i, i);
            }
        }
        
        map<pair<int, int>, char> map_assemble_reverse;
        for(auto it= map_assemble.begin(); it!=map_assemble.end(); ++it)
        {
            map_assemble_reverse.insert(make_pair(it->second, it->first));
        }
        //求相交集合的并集
         map<pair<int, int>, char>::iterator it;
        
        for(it = map_assemble_reverse.begin(); it!=map_assemble_reverse.end(); ++it)
        {
            bool bProcessed = false;
            for(auto it1=vec_result.begin(); it1!=vec_result.end(); ++it1)
            {
                int a1 = it->first.first, b1 = it->first.second;
                int a2 = it1->first, b2 = it1->second;
                if((a1 > b2) or (b1 < a2))
                {
                    continue;
                }
                else
                {
                    it1 = vec_result.erase(it1);
                    vec_result.push_back(make_pair(min(a1, a2), max(b1, b2)));
                    bProcessed = true;
                    break;
                }
            }
            if(bProcessed)
                continue;
            else
                vec_result.push_back(make_pair(it->first.first, it->first.second));
        }
        
        //convert vector<pair<int,int>> to vector<int>
        vector<int> final_result;
        for(auto it= vec_result.begin(); it!=vec_result.end(); ++it)
        {
            final_result.push_back(it->second - it->first + 1);
        }
        return final_result;
    }
};
```

