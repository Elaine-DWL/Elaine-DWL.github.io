# 【leetcode】滑动窗口系列总结

这类题目，在做过的题中，感觉和滑动窗口相关的题目主要有两类：

1. 求包含字符串t中所有单词的，s中的字串。【这类题目主要是自己设计滑窗，通过扩张滑窗来找】
2. 题目固定了滑窗大小，滑窗不断移动，求滑窗移动过程中的最值。【优先队列】

## 1. 滑窗的扩张

### 最小窗口子串

[leetcode76 Minimum Windom Substring](https://leetcode.com/problems/minimum-window-substring/)

```
给你两个字符串S和T,从S中找一个最短的字串，该字串包含T中所有字符。
Input: S = "ADOBECODEBANC", T = "ABC"
Output: "BANC"
```

* 思路

先注意，T中很可能有重复字符。

用两个指针`left`和`right`，来表示我们在S上的滑动窗口。
首先初始化两个指针都指向S的第一个元素。
向右扩张`right`指针，并不断记录当前区间中包含**多少个S中的字符**（`cnt`），如果`cnt == T.size()`说明该区间刚好包含T中所有字符。但是该区间不一定是最小的，因为`left`所指向的字符如果不在T中，那么其实当前这个字符串是可以更小的，也就是说可以通过将`left`右移来缩小这个区间，知道`left`所指向的字符也是S中的字符时，这时的区间，是以`right`为末端的子字符串中，满足要求得最小字符串。
接下来，将`left`右移一位，然后通过`right`右移扩大窗口，重复上述过程。

有一个问题，就是如何来对`cnt`计算及更新。这里有一个小技巧，反正我是想不出来，只能记住了。

首先用哈希表mp统计T中各个字符的数量。在我们扩张窗口的时候，对当前字符ch的数量--，如果该字符是T中的，因为mp[ch]在--之前的值时大于0的，所以--之后的值一定是大于等于0的。同样，如果该字符不是T中的，初始时mp[ch]是等于0的，所以--之后的值一定是小于0。   所以可以通过判断--操作后mp[ch]的大小来判断当前ch是否是T中出现了的，从而对cnt进行统计。

mp[ch] 可以理解成，当前区间还需要ch字符的数量

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        int left=0, right=0, cnt=0, min_len=INT_MAX, n=s.size();
        string res;
        unordered_map<char, int> mp;
        for(char ch:t) mp[ch]++;
        for(right=left; right<n; right++){// 向右扩张窗口
            mp[s[right]]--;//将当前区间还需要字符s[right]的数量-1
            if(mp[s[right]]>=0) cnt++;// 如果当前s[right]字符的出现，有效地解决了t中字符的需求 
            while(cnt==t.size()){// 当前区间刚好包含所有t中的字符串
                if(right-left+1<min_len){
                    min_len = right-left+1;
                    res = s.substr(left, min_len);
                }
                if(mp[s[left]]==0){//注意这里 !!!
                    cnt--;
                }
                mp[s[left]]++;
                left++;
            }
        }
        return res;
    }
};
```

