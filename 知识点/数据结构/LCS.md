# 求最长公共子序列

给定两个字符串 text1 和 text2，返回这两个字符串的最长 公共子序列 。如果不存在 公共子序列 ，返回 0 。

一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。
两个字符串的 公共子序列 是这两个字符串所共同拥有的子序列。

```python
class Solution:
    def LCS(self , s1 , s2 ):
        # write code here
        m, n = len(s1), len(s2)
        dp = [['' for _ in range(n+1)] for _ in range(m+1)]
        for i in range(1, m+1):
            for j in range(1, n+1):               
                if s1[i-1] == s2[j-1]:
                    dp[i][j] = dp[i-1][j-1] + s2[j-1]
                else:
                    if len(dp[i][j-1]) > len(dp[i-1][j]):
                        dp[i][j] = dp[i][j-1]
                    else:
                        dp[i][j] = dp[i-1][j]
        if not dp[m][n]:
            return "-1"
        return dp[m][n]
```

