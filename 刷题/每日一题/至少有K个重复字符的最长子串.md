# 至少有K个重复字符的最长子串


## 题目描述
    找到给定字符串（由小写字符组成）中的最长子串 T ， 要求 T 中的每一字符出现次数都不少于 k 。输出 T 的长度。

    示例 1:
    输入:
    s = "aaabb", k = 3
    输出:
    3
    最长子串为 "aaa" ，其中 'a' 重复了 3 次。

    示例 2:
    输入:
    s = "ababbc", k = 2
    输出:
    5
    最长子串为 "ababb" ，其中 'a' 重复了 2 次， 'b' 重复了 3 次。


## 题目难度
    Tags: 中等

## 题目解析
+ 如果某个字符的总数小于k，那么包含这个字符的子串必定不符合规定，所以按照此字符进行分割，分而治之
```java
class Solution {
    public int longestSubstring(String s, int k) {
        int length = s.length();
        if(length < 1) {
            return 0;
        }
        return divideConquer(s.toCharArray(),0,length-1,k);
    }


     private int divideConquer(char[] s,int left ,int right ,int k) {
         //先统计所有字符出现的次数
        int[] dummy = new int[26];
        for(int i = left; i<= right; i++ ) {
            dummy[s[i]-'a'] ++;
        }

        // 找到分割点
        char split = '/';
        for(int i = 0 ; i <26;i++) {
            if(dummy[i] != 0 && dummy[i] < k) {
                split = (char)(i  + 'a');
                break;
            }
        }
        // 全都符合条件
        if(split == '/') {
            return right - left + 1;
        }

        // 分而治之
        int ptr = left;
        int res = 0;
        while( ptr <= right) {
            // 去除前面重复的
            while(ptr <= right && s[ptr] == split) {
                ptr ++;
            }
            if(ptr > right) {
                return res;
            }

            int start = ptr;
            while(ptr <= right && s[ptr] != split) {
                ptr++;
            }
            int length = divideConquer(s,start,ptr-1,k);
            res  = Math.max(res, length);
        }
        return res;
     }
}
```