[TOC]
# Count submatrix

### L1504. Count Submatrices With All Ones
### L1277. Count Square Submatrices with All Ones
### 1860 · the Number of 0-submatrix

# 接雨水 等高图



# Gas station



# Word Search



# Word Break



# Buy Stock



# Pali



# LIS

### 77 · Longest Common Subsequence(LCS)

f(i, j) 是1-i和i- j两个子序列的LCS

f(i - 1, j) 和 f(i, j - 1)都包含 f(i - 1, j - 1)这种情况，但是取max的时候实际还是取的他们三者的最大值，是否重复不重要。



```
class Solution {
public:
    int longestCommonSubsequence(string &A, string &B) {
        int m = A.size();
        int n = B.size();
        A = " " + A;
        B = " " + B;
        vector<vector<int>> f(m + 1, vector<int>(n + 1, 0));
        
        for (int i = 1; i <= m; i++)
        {
            for (int j = 1; j <= n; j++)
            {
                f[i][j] = max(f[i - 1][j], f[i][j - 1]);
                if (A[i] == B[j])
                {
                    f[i][j] = max(f[i][j], f[i - 1][j - 1] + 1);
                }
            }
        }
        return f[m][n];
    }
};
```

### 79 · Longest Common Substring
连续的情况，只需要考虑f(i - 1, j - 1)
```
class Solution {
public:
    int longestCommonSubstring(string &A, string &B) {
        int m1 = A.size();
        int m2 = B.size();
        vector<vector<int>> f(m1 + 1, vector<int>(m2 + 1, 0));
        int res = 0;
        for (int i = 1; i <= m1; i++)
        {
            for (int j = 1; j <= m2; j++)
            {
                if (A[i - 1] == B[j - 1])
                {
                    f[i][j] = max(f[i][j], f[i - 1][j - 1] + 1);
                    res = max(res, f[i][j]);
                }
            }
        }
        return res;
    }
};
```

# 摘水果



# 二叉树

### 175 · Invert Binary Tree

### 453 · Flatten Binary Tree to Linked List

### 1534 · Convert Binary Search Tree to Sorted Doubly Linked List

### [L116. Populating Next Right Pointers in Each Node](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

### [L654. Maximum Binary Tree](https://leetcode-cn.com/problems/maximum-binary-tree/)

### 73 · Construct Binary Tree from Preorder and Inorder Traversal

### 72 · Construct Binary Tree from Inorder and Postorder Traversal

### [1593 · Construct Binary Tree from Preorder and Postorder Traversal](https://www.lintcode.com/problem/1593/?_from=problem_tag&fromId=undefined)

### 7 · Serialize and Deserialize Binary Tree

### 1108 · Find Duplicate Subtrees 

 用字符串记录树的模式，用记忆化搜索查重

### 树序列化反序列化



# Others

### 1778 · Odd Even Jump



# 相同的形状

### 1433 · Image Overlap

```
class Solution {
public:
    /**
     * @param a: the matrix A
     * @param b: the matrix B
     * @return: maximum possible overlap
     */
    int largestOverlap(vector<vector<int>> &a, vector<vector<int>> &b) {
        int m = a.size();
	   int n = a[0].size();
		map<pair<int, int>, int> c;
		int res = 0;
		vector<pair<int, int>> pa;
		vector<pair<int, int>> pb;
		for (int i = 0; i < m; i++)
		{
			for (int j = 0; j < n; j++)
			{
				if (a[i][j])
					pa.push_back({i, j});
				if (b[i][j])
					pb.push_back({i, j});
			}
		}
		for (auto ai : pa)
		{
			for (auto bi : pb)
			{
				int dx = ai.first  - bi.first;
				int dy = ai.second - bi.second;
				c[{dx, dy}]++;
				res = max(res, c[{dx, dy}]);
			}
		}

		return res;
    }
};
```



### 88. Lowest Common Ancestor of a Binary Tree  （LCA）最小公共祖先

```python
class Solution:
    """
    @param: root: The root of the binary tree.
    @param: A: A TreeNode in a Binary.
    @param: B: A TreeNode in a Binary.
    @return: Return the least common ancestor(LCA) of the two nodes.
    """
    res = None
    def dfs(self, root, A, B):
        if root == None:
            return False

        left = self.dfs(root.left, A, B)
        right = self.dfs(root.right, A, B)
        if self.res != None:
            return False
        if root.val == A.val or root.val == B.val:
            if left or right:
                self.res = root
            return True
        if left and right:
            self.res = root
        return left or right
    def lowestCommonAncestor(self, root, A, B):
        if self.dfs(root, A, B):
            return root
        return self.res
```

