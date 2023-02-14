[TOC]



# 57 · 三数之和（双指针，固定一个，动另外两个）

固定一个，动另外两个

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
# 667 · 最长的回文序列（区间DP）
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

# 200 · 最长回文子串（区间 DP，从里往外算）

区间 DP，从里往外算

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
# 594 · 字符串查找 II（字符串hash）
```
class Solution {
public:
    typedef unsigned long long ULL;

    ULL getHash(vector<ULL>& h, vector<ULL>& p, int L, int R)
    {
        return h[R] - h[L - 1] * p[R - (L - 1)];  //左移R - (L - 1)位后h[R] = 123456 h[L-1] = 123000
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

# 539 · Move Zeroes（同向双指针）

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
# 443 · Two Sum - Greater than target（相向双指针）
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


# 382 · Triangle Count（两个小边和如果大于这个大边，其他也都大于）

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

# 460 · Find K Closest Elements（isLeftClosest）

k个最接近目标值的数

```
class Solution {
public:
     bool isLeftClosest(vector<int> &a, int target, int l, int r)
     {
        if (l < 0)
            return false;
        if (r >= a.size())
            return true;
        if (target - a[l] <= a[r] - target)
            return true;
        return false;  
     }
    vector<int> kClosestNumbers(vector<int> &a, int target, int k) {
        vector<int> res;
        int l = 0;
        int r = a.size() - 1;
        while (l < r)
        {
            int mid = (l + r) / 2;
            if (a[mid] >= target)
            {
                r = mid;
            }
            else
            {
                l = mid + 1;
            }
        }
        int left  = l - 1;
        int right = l;
        while (k--)
        {
            if (isLeftClosest(a, target, left, right))
            {
                res.push_back(a[left--]);
            }
            else
            {
                res.push_back(a[right++]);
            }
        }
        return res;
    }
};
```
# 62 · Search in Rotated Sorted Array（比较左端点，想象成一个菱形）
想象成一个菱形，如果是上半部分，就判断是上坡，还是下坡，最后决定是r = mid或l = mid + 1

和左端点比较，确定是上半部还是下半部，判断坡度，确定是上升还是下降

```
class Solution {
public:
    bool check(vector<int> &A, int mid, int l, int r, int target)
    {
        int m = A.size();
        if (A[l] <= A[mid])
        {
            if (A[l] <= target && target <= A[mid])
            {
                return true;
            }
            else
                return false;
        }
        else
        {
            if (A[mid] <= target && target <= A[r])
            {
                return false;
            }
            else
                return true;
        }
    }
    int search(vector<int> &A, int target) {
        if (A.empty())
            return -1;
        int m = A.size();
        int l = 0;
        int r = m - 1;
        while (l < r)
        {
            int mid = (l + r) / 2;
            if(check(A, mid, l, r, target))
            {
                r = mid;
                if (A[mid] == target)
                    return mid;
            }
            else
            {
                l = mid + 1;
                if (A[mid] == target)
                    return mid;
            }
        }
        if (A[l] != target)
            return -1;
        return l;
    }
};
```

# 585 · Maximum Number in Mountain Sequence（比较两个数即可）

```
class Solution {
public:
    bool check(vector<int> &nums, int mid)
    {
        int m = nums.size();
        if ((mid - 1 >= 0 && nums[mid - 1] > nums[mid]) || 
            (mid + 1 < m && nums[mid] > nums[mid + 1]))
        {
            return true;
        }
        return false;
    }
    int mountainSequence(vector<int> &nums) {
        int m = nums.size();
        int l = 0;
        int r = m - 1;
        while (l < r)
        {
            int mid = (l + r) / 2;
            if (check(nums, mid))
            {
                r = mid;
            }
            else
            {
                l = mid + 1;
            }
        }
        return nums[l];
    }
};
```

# 137 · Clone Graph

1. 建立原始节点和新clone节点的map结构
2. 每次只处理当前节点和子节点，只将当前节点的子节点，加入到当前节点的clone节点中（当前节点 -> 子节点, 不处理 子节点->当前节点）

```
/**
 * Definition for undirected graph.
 * struct UndirectedGraphNode {
 *     int label;
 *     vector<UndirectedGraphNode *> neighbors;
 *     UndirectedGraphNode(int x) : label(x) {};
 * };
 */

class Solution {
public:
    UndirectedGraphNode* cloneGraph(UndirectedGraphNode *node) {
        if(!node)
            return nullptr;
        unordered_map<UndirectedGraphNode *, UndirectedGraphNode *> mclone;

        UndirectedGraphNode* croot = new UndirectedGraphNode(node->label);
        mclone[node] = croot;
        queue<UndirectedGraphNode *> q;
        q.push(node);

        while (!q.empty())
        {
            auto cur = q.front(); q.pop();
            for (auto n : cur->neighbors)
            {
                auto it = mclone.find(n);
                if (it != mclone.end())
                {
                    mclone[cur]->neighbors.push_back(it->second);
                }
                else
                {
                    auto newNode = new UndirectedGraphNode(n->label);
                    mclone[cur]->neighbors.push_back(newNode);
                    mclone[n] = newNode;
                    q.push(n);
                }
            }
        }
        return croot;

    }
};
```

# 120 · Word Ladder

方法1：将cur和dict里面的单词比较，只差一个字母的入队，宽搜

方法2： cur单词的每个位置用26字母替换，然后看是否在dict，且没有visit，如果是，入队

```
class Solution {
public:
    int ladderLength(string &start, string &end, unordered_set<string> &dict) {
        dict.insert(end);
        queue<string> q;
        unordered_set<string> visit;

        q.push(start);
        int step = 0;
        while(!q.empty())
        {
            int levelnum = q.size();
            while (levelnum--)
            {
                auto cur = q.front(); q.pop();
                if(cur == end)
                    return step + 1;
                int len = cur.size();
                for (int i = 0; i < len; i++)
                {
                    string ts = cur;
                    for (int j = 0; j < 26; j++)
                    {
                        if(ts[i] == 'a' + j)
                            continue;
                        ts[i] = 'a' + j;
                        if (!dict.count(ts) || visit.count(ts))
                            continue;
                        q.push(ts);
                        visit.insert(ts);
                    }
                }
            }
            step++;
        }
    }
};
```

# 178 · Graph Valid Tree （树 验证）

判断树的三个条件

1. 联通性

2. 边数 = 节点数 - 1

3. 是否有环

```
class Solution {
public:
    map<int, int> p;
    int find(int x)
    {
        if (p[x] != x) p[x] = find(p[x]);
        return p[x];
    }
    bool validTree(int n, vector<vector<int>> &edges) {
        if (edges.empty())
            return n == 1? true:false;
        for (int i = 0; i< n; i++)
        {
            p[i] = i;
        }

        for (auto n : edges)
        {
            if (find(n[0]) == find(n[1]))
            {
                return false;
            }

            p[find(n[0])] = find(n[1]);
        }

        std::set<int> res;
        for (int i = 0; i< n; i++)
        {
            res.insert(find(i));
        }
        if (res.size() != 1)
            return false;

        return true;
    }
};
```

