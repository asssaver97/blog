---
title: 【LeetCode刷题笔记】354.俄罗斯套娃信封问题
date: 2022-02-21 12:41:03
tags: 刷题
categories: LeetCode
---
题目
---
[**354.俄罗斯套娃信封问题**](https://leetcode-cn.com/problems/russian-doll-envelopes/)

给你一个二维整数数组`envelopes`，其中`envelopes[i] = [wi, hi]`，表示第`i`个信封的宽度和高度。

当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。

请计算**最多能有多少个**信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。

注意：不允许旋转信封。

示例 1：
```
输入：envelopes = [[5,4],[6,4],[6,7],[2,3]]
输出：3
解释：最多信封的个数为 3, 组合为: [2,3] => [5,4] => [6,7]。
```
示例 2：
```
输入：envelopes = [[1,1],[1,1],[1,1]]
输出：1
```

提示：
* `1 <= envelopes.length <= 105`
* `envelopes[i].length == 2`
* `1 <= wi, hi <= 105`
<!--more-->

题解
---
本质是个求二维的[最长递增自序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)
核心在于如何将其降维，方法是排序：
先对宽度w进行升序排序，如果w相同，则对高度h进行降序排序。之后对排序好的数组的h纬度进行最长递增子序列算法即可。
前置知识点：
1. sort用法：`sort(vec.begin(),vec.end(),myfunc)`，其中myfunc为自定义的排序方式，当然参数三也可以省略。
2. lambda函数用法：`[捕获，可以为空](行参){函数体}`

代码
---
注意！在用例更新后，单纯的最长递增子序列算法已经不可用，会超时，必须用二分法。(但我觉得二分法不是人能想出来的，脑筋急转弯没意思，所以这里还是贴上正常思路的代码。)
```cpp
class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        sort(envelopes.begin(), envelopes.end(), 
        [](const vector<int>& x, const vector<int>& y){
            return x[0]<y[0] || (x[0]==y[0] && x[1]>y[1]);
        });
        int n = envelopes.size();
        vector<int> dp(n, 1);
        for (int i = 1; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                if (envelopes[i][1] > envelopes[j][1]) {
                    dp[i] = max(dp[i], dp[j]+1);
                }
            }
        }
        return *max_element(dp.begin(), dp.end());
    }
};
```
