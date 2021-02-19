# 最大连续1的个数 III

## 题目描述
    示例 1：
    输入：A = [1,1,1,0,0,0,1,1,1,1,0], K = 2
    输出：6
    解释： 
    [1,1,1,0,0,1,1,1,1,1,1]
    粗体数字从 0 翻转到 1，最长的子数组长度为 6。

    示例 2：
    输入：A = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], K = 3
    输出：10
    解释：
    [0,0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,1]
    粗体数字从 0 翻转到 1，最长的子数组长度为 10。


## 题目难度
    Tags：中等
## 题目解析
+ 滑动窗口

```java
class Solution {
    public int longestOnes(int[] A, int K) {
        // 滑动窗口
        int length = A.length;

        int left = 0;
        int right = 0;
        int cnt = K;
        int max = -1;

        while(right < length) {
            if(A[right] == 0) {
                cnt--;
            }
            // 移动窗口左指针
            while(cnt < 0 ) {
                max = Math.max(max,right - left);
                if(A[left] == 0) {
                    cnt++;   
                }
                left ++;
            }
            right++;
        }
        return Math.max(max,right - left);
    }
}
```