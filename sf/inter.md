[TOC]



# 57 · 三数之和
```
class Solution {
public:
    vector<vector<int>> threeSum(vector<int> &n) {
        int m = n.size();
        sort(n.begin(), n.end());
        set<vector<int>> res;
        for (int i = 0; i < m; i++)
        {
            int l = i + 1;
            int r = m - 1;
            while (l < r)
            {
                int sum = n[l] + n[r] + n[i];
                //cout << n[i] << " " << n[l] << " " << n[r] << " " << sum << endl;
                if (sum == 0)
                {
                    res.insert({n[i], n[l], n[r]});
                    l++;
                }
                else if (sum > 0)
                {
                    r--;
                }
                else
                {
                    l++;
                }
            }
        }
        vector<vector<int>> rr(res.begin(), res.end());
        return rr;
    }
};
```
# 667 · 最长的回文序列
区间dp
```
class Solution {
public:
    /**
     * @param s: the maximum length of s is 1000
     * @return: the longest palindromic subsequence's length
     */
    int longestPalindromeSubseq(string &s) {
        if (s.empty())
            return 0;
        int m = s.size();
        vector<vector<int>> f(m, vector<int>(m, 0));
        for(int len = 1; len <= m; len++)
        {
            for (int i = 0; i + len - 1 < m; i++)
            {
                int j = i + len - 1;
                if (len == 1)
                    f[i][j] = 1;
                else if (s[i] == s[j])
                    f[i][j] = f[i + 1][j - 1] + 2;
                else
                    f[i][j] = max(f[i + 1][j], f[i][j - 1]);
            }
        }
        return f[0][m - 1];
    }
};
```
# 594 · 字符串查找 II
```
class Solution {
public:
    typedef unsigned long long ULL;

    ULL getHash(vector<ULL>& h, vector<ULL>& p, int L, int R)
    {
        return h[R] - h[L - 1] * p[R - (L - 1)];
    }
    int strStr2(string &source, string &target) {
        int m = source.size();
        int n = target.size();

        vector<ULL> shash(m + 1, 0);
        vector<ULL> thash(n + 1, 0);
        vector<ULL> p(m + 1, 0);
        int P = 31;

        p[0] = 1;
        for (int i = 1; i <= m; i++)
        {
            p[i] = p[i - 1] * P;
            shash[i] = shash[i - 1] * P + source[i - 1];
        }

        for (int i = 1; i <= n; i++)
        {
            thash[i] = thash[i - 1] * P + target[i - 1];
        }

        for (int i = 1; i + n - 1 <= m; i++)
        {
            ULL hh = getHash(shash, p, i, i + n - 1);
            if (hh == thash[n])
                return i - 1;
        }
        return -1;
    }
};
```
# 200 · 最长回文子串
```
class Solution {
public:
    /**
     * @param s: input string
     * @return: a string as the longest palindromic substring
     */
    string longestPalindrome(string &s) {
        int m = s.size();
        vector<vector<bool>> f(m, vector<bool>(m));
        for (int i = 0; i < m; i++)
        {
            f[i][i] = true;
        }

        int maxlen = 0;
        string res = "";
        for (int len = 1; len <= m; len++)
        {
            for (int i = 0; i + len - 1 < m; i++)
            {
                int j = i + len - 1;
                if (s[i] == s[j])
                {
                    // i + 1 > j - 1 确定 单个字符 或者 两个字符的情况
                    if (i + 1 > j - 1 || f[i + 1][j - 1])
                    {
                        f[i][j] = true;
                        maxlen = len;
                        res = s.substr(i, len);
                    }
                }
            }
        }
        return res;
    }
};
```

# 539 · Move Zeroes

同向双指针
```
class Solution {
public:
    void  moveZeroes(vector<int> &nums) {
        int m = nums.size();
        int l = 0;
        int r = 0;
        while (r < m)
        {
            if (nums[r] != 0)
            {
                swap(nums[l], nums[r]);
                l++;
            }
            r++;
        }
    }
};
```
# 443 · Two Sum - Greater than target
相向双指针
```
class Solution {
public:
    int twoSum2(vector<int> &nums, int target) {
        int m = nums.size();
        sort (nums.begin(), nums.end());
        int l = 0;
        int r = m - 1;
        int res = 0;

        while (l < r)
        {
            int sum = nums[l] + nums[r];
            if (sum > target)
            {
                res += r - l;
                r--;
            }
            else
            {
                l++;
            }
        }
        return res;
    }
};
```


# 382 · Triangle Count

从大到小找，另外两个小边和如果大于这个大边，其他也都大于
```
class Solution {
public:
    /**
     * @param s: A list of integers
     * @return: An integer
     */
    int triangleCount(vector<int> &s) {
        sort(s.begin(), s.end());
        int m = s.size();
        int res = 0;

        // 从大到小找，另外两个小边和如果大于这个大边，其他也都大于
        for (int i = m - 1; i >= 0; i--)
        {
            int l = 0;
            int r = i - 1;

            while (l < r && r >= 0)
            {
                int sum = s[l] + s[r];
                if (sum > s[i])
                {
                    res += (r - l);
                    r--;
                }
                else
                {
                    l++;
                }
            }
        }
        return res;
    }
};
```