---
title: Largest Time for Given Digits
date: 2020-09-22 14:30:15
tags:
categories: leetcode
---

__<font color=green>题目描述</font>__


Given an array arr of 4 digits, find the latest 24-hour time that can be made using each digit exactly once.

24-hour times are formatted as "HH:MM", where HH is between 00 and 23, and MM is between 00 and 59. The earliest 24-hour time is 00:00, and the latest is 23:59.

Return the latest 24-hour time in "HH:MM" format.  If no valid time can be made, return an empty string.


Example 1:
```
Input: A = [1,2,3,4]
Output: "23:41"
Explanation: The valid 24-hour times are "12:34", "12:43", "13:24", "13:42", "14:23", "14:32", "21:34", "21:43", "23:14", and "23:41". Of these times, "23:41" is the latest.
```
Example 2:
```
Input: A = [5,5,5,5]
Output: ""
Explanation: There are no valid 24-hour times as "55:55" is not valid.
```
Example 3:
```
Input: A = [0,0,0,0]
Output: "00:00"
```
Example 4:
```
Input: A = [0,0,1,0]
Output: "10:00"
```

<!--more--> 

最开始我的想法是要arr[0]<=2 arr[1]<=3 arr[2]<=5，由此写了下面的实现

```C++
class Solution {
public:
    string largestTimeFromDigits(vector<int>& arr) {
        string result = "";
        vector<int> temp = arr;
        std::sort(temp.begin(), temp.end());
        unsigned int hour_high = -1, hour_low = -1, minute_high = -1, minute_low = -1;
        vector<int>::iterator it;
        if( (temp[0]<3) and (temp[1]<4) and (temp[2]<6))
        {
            //find the last indexA < 3 and last indexB < 4
            
            pair<bool, int> indexA(false,-1), indexB(false, -1);
            for(int i=temp.size()-1; i>=0; --i)
            {
                if(temp[i] < 3 && indexA.first == false)
                {
                    indexA.second = i;
                    indexA.first = true;
                }
                if(temp[i] < 4 && indexB.first == false)
                {
                    indexB.second = i;
                    indexB.first = true;
                }                
            }
            //
            if(indexA.second == -1 || indexB.second == -1)
                return "";
                
            //hour_high should be certain, then
            hour_high = temp[indexA.second];
            //then loop back from indexB.second, choose hour_low
            for(int j=indexB.second; j>=0; --j)
            {
                if(j!=indexA.second)
                {
                    hour_low = temp[j];
                    indexB.second = j;
                    break;
                }
            }
            bool bMinuteHighUsed = false, bMinuteLowUsed = false;
            for(int k = temp.size()-1; k>=0; --k)
            {
                if(k==indexA.second or k== indexB.second)
                    continue;
                if(temp[k]>=6)
                {
                    minute_low = temp[k];
                    bMinuteLowUsed = true;
                }
                else
                {
                    if(bMinuteHighUsed == false)
                    {
                        minute_high = temp[k];
                        bMinuteHighUsed = true;
                    }
                    else
                    {
                        if(bMinuteLowUsed == false)
                        {
                            minute_low = temp[k];
                            bMinuteLowUsed = true;                       
                        }
                    }
                }
            }
            
            //compose result string
            result = std::to_string(hour_high) + std::to_string(hour_low) + ":" + std::to_string(minute_high) + std::to_string(minute_low);
        }
        else
        {
            return "";
        }
        return result;
    }
};
```

后面commit之后，发现case没过，是因为明显忘记了还有19:28这样的结果啊，所以arr[1]是无法被限定死的。  

现在的思路是任意选择两个，组成hour，有两种顺序，即使满足hour 属于 [0,23]区间，也不证明要一定保留，还必须满足minute属于[0,59]，因此需要列举出所有的case。  

下面是我的实现，但从算法实现上看很繁琐，一点也不简洁。

```C++
class Solution {
public:
    string largestTimeFromDigits(vector<int>& arr) {
        string result = "";
        vector<int> temp = arr;
        std::sort(temp.begin(), temp.end());
        if(temp[0] >2 or temp[0]<0 or temp[1]<0 or temp[2] < 0 or temp[3] < 0)
        {
            return "";
        }
        
        //use pair to store
        pair<int, int> hour_index(-1, -1), hour(-1, -1), minute_index(-1, -1), minute(-1, -1);
        int hour_max = -1, minute_max = -1;
        for(int i=0; i<temp.size(); ++i)
            for(int j=0; j<temp.size(); ++j)
            {
                if(i == j)
                {
                    continue;
                }
                else
                {
                    int tmp_hour = temp[i]*10 + temp[j];
                    if(tmp_hour > hour_max and tmp_hour <= 23)
                    {                    
                        int tmp_minute_max = -1;
                        pair<int, int> tmp_minute_index(-1, -1), tmp_minute(-1, -1);
                        for(int m=0; m<temp.size(); ++m)
                        {
                            if(m == j or m == i)
                                continue;
                            if(tmp_minute_index.first == -1)
                            {
                                tmp_minute_index.first = m;
                                tmp_minute.first = temp[m];
                            }
                            else
                            {
                                tmp_minute_index.second = m;
                                tmp_minute.second = temp[m];
                            }
                        }
                        
                        int tmp1 = tmp_minute.first * 10 + tmp_minute.second;
                        int tmp2 = tmp_minute.first + tmp_minute.second *10;
                        if(tmp1 > 59 and tmp2 > 59)
                        {
                            tmp_minute = std::make_pair<int,int>(-1, -1);
                            tmp_minute_index = std::make_pair<int,int>(-1, -1);
                        }
                        else if(tmp1 <= 59 and tmp2 > 59)
                        {                         
                            tmp_minute_max = tmp1;
                        }
                        else if(tmp1 >59 and tmp2 <= 59)
                        {
                            tmp_minute_max = tmp2;
                            swap(tmp_minute.first, tmp_minute.second);
                            swap(tmp_minute_index.first, tmp_minute_index.second);
                        }
                        else
                        {
                            if(tmp1 < tmp2)
                            {
                                tmp_minute_max = tmp2;
                                swap(tmp_minute.first, tmp_minute.second);
                                swap(tmp_minute_index.first, tmp_minute_index.second);
                            }
                            else
                            {
                                tmp_minute_max = tmp1;
                            }
                        }
                        
                        if(tmp_minute.first == -1 or tmp_minute.second == -1)
                            continue;
                        
                        //
                        if(tmp_hour > hour_max)
                        {
                            hour.first = temp[i];
                            hour.second = temp[j];
                            minute.first = tmp_minute.first;
                            minute.second = tmp_minute.second;
                            hour_max = hour.first*10 + hour.second;
                        }
                        else if(tmp_hour == hour_max  and minute_max < tmp_minute_max)
                        {
                            hour.first = temp[i];
                            hour.second = temp[j];
                            minute.first = tmp_minute.first;
                            minute.second = tmp_minute.second;
                            hour_max = hour.first*10 + hour.second;
                        }
                        
                    }
                    else
                    {
                        continue;
                    }
                }
            }
        
        if(hour.first == -1 or hour.second == -1 or minute.first == -1 or minute.second == -1)
            return "";
        
        result = to_string(hour.first) + to_string(hour.second) +  ":" + to_string(minute.first) + to_string(minute.second);
        
        return result;
    }
};
```

