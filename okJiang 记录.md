# okJiang 记录

## 1. 两数之和

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> um;
        for (int i = 0; i < nums.size(); ++ i)
            um[nums[i]] = i;
        for (int i = 0; i < nums.size(); ++ i)
            if (um.find(target - nums[i]) != um.end()&&um[target - nums[i]] != i) 
                return vector<int>({i, um[target - nums[i]]});
        return vector<int>();
    }
};
```

## 2. 两数相加

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* t1 = l1;
        int c = 0;
        while (l1 && l2) {
            l1->val = l1->val + l2->val + c;
            c = l1->val/10;
            l1->val = l1->val%10;
            if (l1->next == nullptr || l2->next == nullptr) break;
            l1 = l1->next;
            l2 = l2->next;
        }
        if (l2->next) {
            l1->next = l2->next;
        }
        while (l1->next&&c) {
            l1 = l1->next;
            l1->val = l1->val + c;
            c = l1->val/10;
            l1->val = l1->val%10;
            if (l1->next == nullptr) break;
        }
        if (c) {
            l1->next = new ListNode(c);
        }
        return t1;
    }
};
```

## [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (!l1) return l2;
        if (!l2) return l1;
        if (l1->val > l2->val) swap(l1, l2);
        ListNode* ans = l1;
        while (l1 && l2) {
            if (l1->val <= l2->val && l1->next && l1->next->val >= l2->val) {
                ListNode* temp = l2->next;
                l2->next = l1->next;
                l1->next = l2;
                l2 = temp;
            } else {
                if (!l1->next) {
                    l1->next = l2;
                    break;
                }
                l1 = l1->next;
            }
        }
        return ans;
    }
};
```



## [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```cpp
class Solution {
public:
    vector<int> find(vector<int>& nums, int target, int left, int right) {
        if (left > right) return vector<int>({-1, -1});
        int mid = (left + right) >> 1;
        if (nums[mid] == target) {
            int l, r;
            if (nums.back() == target) 
                r = nums.size()-1;
            else 
                r = findRight(nums, target, mid+1, right);
            if (nums.front() == target)
                l = 0;
            else 
                l = findLeft(nums, target, left, mid-1);
            
            return vector<int>({l, r});
        } else if (nums[mid] > target) {
            return find(nums, target, left, mid-1);
        } else {
            return find(nums, target, mid+1, right);
        }
    }
    int findLeft(vector<int>& nums, int target, int left, int right) {
        if (left > right) return left;
        if (left < 0) return 0;
        int mid = (left + right) >> 1;
        if (nums[mid] != target&&nums[mid+1] == target) {
            return mid+1;
        }
        if (nums[mid-1] != target&&nums[mid] == target) {
            return mid;
        }
        if (nums[mid] == target) {
            return findLeft(nums, target, left, mid-1);
        } else {
            return findLeft(nums, target, mid+1, right);
        }
    }
    int findRight(vector<int>& nums, int target, int left, int right) {
        if (left > right) return right;
        int mid = (left + right) >> 1;
        if (nums[mid-1] == target&&nums[mid] != target) {
            return mid-1;
        }
        if (nums[mid] == target&&nums[mid+1] != target) {
            return mid;
        }
        if (nums[mid] == target) {
            return findRight(nums, target, mid+1, right);
        } else {
            return findRight(nums, target, left, mid-1);
        }
    }
    vector<int> searchRange(vector<int>& nums, int target) {
        return find(nums, target, 0, nums.size()-1);
    }
};
```



## [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if (head == nullptr || head->next == nullptr)
            return false;
        ListNode *fast, *slow;
        slow = head; fast = head->next;
        while (fast && slow) {
            if (fast == slow) return true;
            slow = slow->next;
            if (!fast->next || !fast->next->next) return false;
            fast = fast->next->next;
        }
        return false;
    }
};
```



## [230. 二叉搜索树中第K小的元素](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

- 递归

  ```cpp
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
   *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
   *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
   * };
   */
  class Solution {
  public:
      int n, ans;
      int kthSmallest(TreeNode* root, int k) {
          n = k;
          dfs(root);
          return ans;
      }
      void dfs(TreeNode* root) {
          if (!root) return;
          if (n == 0) return;
          dfs(root->left);
          if (n == 0) return;
          n --;
          ans = root->val;
          dfs(root->right);
      }
  };
  ```

- 迭代

  ```cpp
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
   *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
   *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
   * };
   */
  class Solution {
  public:
      int kthSmallest(TreeNode* root, int k) {
          stack<TreeNode*> st;
          
          while (k) {
              while (root->left) {
                  st.push(root);
                  root = root->left;
              }
              k --;
              if (k == 0) break;
              if (root->right) {
                  root = root->right;
              } else {
                  root = st.top();
                  st.pop();
                  root->left = nullptr;
              }
          }
          return root->val;
      }
  };
  ```

  

## [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

- 用 `map` 维护窗口：`key` 是 `nums[i]`

  ```cpp
  class Solution {
  public:
      vector<int> maxSlidingWindow(vector<int>& nums, int k) {
          map<int, int> m;
          int left = 0, right = 0, len = nums.size();
          while (right < k) {
              m[nums[right++]] ++;
          }
          vector<int> ans;
          ans.push_back((m.rbegin())->first);
          while (right < len) {
              if (--m[nums[left]] == 0) {
                  m.erase(nums[left]);
              }
              m[nums[right]] ++;
              left ++;
              right ++;
              
              ans.push_back((m.rbegin())->first);
          }
          return ans;
      }
  };
  ```

- 用最大堆维护窗口：内部元素是 `nums[i]` 和 `i` 的 `pair`

  ```cpp
  class Solution {
  public:
      vector<int> maxSlidingWindow(vector<int>& nums, int k) {
          priority_queue<pair<int, int>> stack;
          for (int i = 0; i < k; i ++) {
              stack.push({nums[i], i});
          }
          vector<int> ans = {stack.top().first};
          for (int i = k; i < nums.size(); ++ i) {
              stack.push({nums[i], i});
              while (stack.top().second <= i-k) {
                  stack.pop();
              }
              ans.push_back(stack.top().first);
          }
          return ans;
      }
  };
  ```

- 用单调队列维护窗口：内部元素是下标

  ```cpp
  class Solution {
  public:
      vector<int> maxSlidingWindow(vector<int>& nums, int k) {
          deque<int> dq;
          for (int i = 0; i < k; ++ i) {
              while (!dq.empty() && nums[i] > nums[dq.back()]) {
                  dq.pop_back();
              }
              dq.push_back(i);
          }
          vector<int> ans = {nums[dq.front()]};
          for (int i = k; i < nums.size(); ++ i) {
              while (!dq.empty() && nums[i] > nums[dq.back()]) {
                  dq.pop_back();
              }
              dq.push_back(i);
              while (dq.front() <= i-k) {
                  dq.pop_front();
              }
              ans.push_back(nums[dq.front()]);
          }
          return ans;
      }
  };
  ```



## [79. 单词搜索](https://leetcode-cn.com/problems/word-search/)

```cpp
class Solution {
public:
    string s;
    int n, m;
    vector<vector<int>> next = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
    bool dfs(vector<vector<char>>& board, vector<vector<bool>>& flag, int x, int y, int cnt) {
        if (cnt == s.length()) return true;
        for (int i = 0; i < next.size(); ++ i) {
            int dx = x + next[i][0];
            int dy = y + next[i][1];
            if (dx >= 0&&dx < n&&dy >= 0&&dy < m&&s[cnt] == board[dx][dy]&&!flag[dx][dy]) {
                flag[dx][dy] = true;
                if (dfs(board, flag, dx, dy, cnt+1))
                    return true;
                flag[dx][dy] = false;
            }
        }
        return false;
    }
    bool exist(vector<vector<char>>& board, string word) {
        n = board.size();
        m = board[0].size();
        vector<vector<bool>> flag(n, vector<bool>(m, false));
        s = word;
        for (int i = 0; i < n; ++ i) {
            for (int j = 0; j < m; ++ j) {
                if (board[i][j] == word[0]) {
                    flag[i][j] = true;
                    if (dfs(board, flag, i, j, 1))
                        return true;
                    flag[i][j] = false;
                }
            }
        }
        return false;
    }
};
```

------

## 2021/5/27

### [300. 最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

- dp

  ```cpp
  class Solution {
  public:
      int lengthOfLIS(vector<int>& nums) {
          int n = nums.size();
          vector<int> dp(n, 1);
          int ans = 1;
          for (int i = 1; i < nums.size(); ++ i) {
              for (int j = 0; j < i; ++ j) {
                  if (nums[i] > nums[j]) 
                      dp[i] = max(dp[i], dp[j]+1);
              }
              ans = max(ans, dp[i]);
          }
          return ans;
      }
  };
  ```

- 贪心+二分：主要是没想到怎么存之前到下标，一开始想到是用单调栈，但是行不通

  ```cpp
  class Solution {
  public:
      void binaryfind(vector<int>& minLast, int left, int right, int target) {
          if (left == right) {
              minLast[left] = target;
              return;
          }
          int mid = (left + right) >> 1;
          // cout << target << ": " << left << ' ' << mid << ' ' << right << endl;
          if (minLast[mid] < target && minLast[mid+1] >= target) {
              minLast[mid+1] = target;
          } else if (minLast[mid-1] < target && minLast[mid] >= target) {
              minLast[mid] = target;
          } else if (minLast[mid] > target) {
              binaryfind(minLast, left, mid-1, target);
          } else if (minLast[mid] < target) {
              binaryfind(minLast, mid+1, right, target);
          }
      }
      int lengthOfLIS(vector<int>& nums) {
          vector<int> minLast = {nums[0]};
          for (int i = 1; i < nums.size(); ++ i) {
              if (nums[i] == minLast.back()) 
                  continue;
              if (nums[i] <= minLast.front()) {
                  minLast[0] = nums[i];
              } else if (nums[i] > minLast.back()) {
                  minLast.push_back(nums[i]);
              } else {
                  binaryfind(minLast, 0, minLast.size()-1, nums[i]);
              }
          }
          return minLast.size();
      }
  };
  ```

### [66. 加一](https://leetcode-cn.com/problems/plus-one/)

```cpp
class Solution {
public:
    bool check9(vector<int>& digits) {
        for (int i = 0; i < digits.size(); ++ i) {
            if (digits[i] != 9) return false;
        }
        return true;
    }
    vector<int> plusOne(vector<int>& digits) {
        if (check9(digits)) {
            vector<int> ans(digits.size()+1, 0);
            ans[0] = 1;
            return ans;
        }
        int c = 1, cnt = digits.size()-1;
        while (c) {
            digits[cnt] ++;
            c = digits[cnt]/10;
            digits[cnt] = digits[cnt]%10;
            -- cnt;
        }
        return digits;
    }
};
```

### [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

```cpp
class Solution {
public:
    int binaryFind(vector<int>& nums, int target, int left, int right) {
        if (left > right) return -1;
        int mid = (left + right) >> 1;
        if (target < nums.back()) {
            if (nums[mid] > nums.back()) return binaryFind(nums, target, mid+1, right);
            if (nums[mid] < target) return binaryFind(nums, target, mid+1, right);
            if (nums[mid] > target) return binaryFind(nums, target, left, mid-1);
            return mid;
        } else {
            if (nums[mid] < nums.front()) return binaryFind(nums, target, left, mid-1);
            if (nums[mid] < target) return binaryFind(nums, target, mid+1, right);
            if (nums[mid] > target) return binaryFind(nums, target, left, mid-1);
            return mid;
        }
    }
    int search(vector<int>& nums, int target) {
        if (target == nums.back()) return nums.size()-1;
        if (target == nums.front()) return 0;
        
        return binaryFind(nums, target, 0, nums.size()-1);
    }
};
```

### [81. 搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)

```cpp
class Solution {
public:
    bool binaryFind(vector<int>& nums, int target, int left, int right) {
        if (left > right) return false;
        int mid = (left + right) >> 1;
        if (target < nums.back()) {
            if (nums[mid] > nums.back()) return binaryFind(nums, target, mid+1, right);
            if (nums[mid] == nums.back()) 
                return binaryFind(nums, target, mid+1, right) 
                    || binaryFind(nums, target, left, mid-1);
            if (nums[mid] < target) return binaryFind(nums, target, mid+1, right);
            if (nums[mid] > target) return binaryFind(nums, target, left, mid-1);
            return true;
        } else {
            if (nums[mid] < nums.front()) return binaryFind(nums, target, left, mid-1);
            if (nums[mid] == nums.front()) 
                return binaryFind(nums, target, mid+1, right) 
                    || binaryFind(nums, target, left, mid-1);
            if (nums[mid] < target) return binaryFind(nums, target, mid+1, right);
            if (nums[mid] > target) return binaryFind(nums, target, left, mid-1);
            return true;
        }
    }
    bool search(vector<int>& nums, int target) {
        if (target == nums.back()) return true;
        if (target == nums.front()) return true;
        
        return binaryFind(nums, target, 0, nums.size()-1);
    }
};
```

### [127. 单词接龙](https://leetcode-cn.com/problems/word-ladder/)

```cpp
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        // 思考：
        // 1. 把所有的 word 看成节点，word 之间的转换看成是节点之间的连通，所以可以建图
        // 2. 建图之后就好做了，计算两个节点的最短路径
        // 3. 通常的建图需要 O(n^2) 的时间复杂度，两两比较
        // 4. 由于 word.length <= 10，一个字母有 26 种可能，那么一个单词可连通的节点最多只有 260 种
        // 5. 所以可以遍历所有可能去建图，时间复杂度是 O(260n) = O(n)
        
        // 步骤：
        // 1. 创建一个集合，存下所有的 wordlist
        // 2. 遍历每个 word 的所有可能连通节点，查看是否存在集合中，建图
        // 3. 建图用链表，减少空间复杂度
        // 4. 之后使用 bfs 来计算最短路程
        
        // unordered_set<string> uss(wordList.begin(), wordList.end());
        
        unordered_map<string, int> umsi;
        for (int i = 0; i < wordList.size(); ++ i) {
            umsi[wordList[i]] = i;
        }
        if (umsi.find(beginWord) == umsi.end()) {
            wordList.push_back(beginWord);
            umsi[beginWord] = wordList.size()-1;
        }
        
        vector<list<int>> vls(wordList.size(), list<int>());
        
        for (int j = 0; j < wordList.size(); ++ j) {
            for (int i = 0; i < wordList[j].length(); ++ i) {
                for (char ch = 'a'; ch <= 'z'; ++ ch) {
                    if (wordList[j][i] == ch) continue;
                    string temp = wordList[j];
                    temp[i] = ch;
                    if (umsi.find(temp) != umsi.end()) {
                        vls[j].push_back(umsi[temp]);
                    }
                }
            }    
        }
        // for (int i = 0; i < vls.size(); ++ i) {
        //     cout << wordList[i] << ": ";
        //     for (auto s: vls[i])
        //         cout << wordList[s] << ' ';
        //     cout << endl;
        // }
        // bfs
        if (umsi.find(endWord) == umsi.end())
            return 0;
        int begin = umsi[beginWord];
        int end = umsi[endWord];
        vector<bool> visited(wordList.size(), false);
        queue<pair<int, int>> q;
        q.push({begin, 1});
        visited[begin] = true;
        while (!q.empty()) {
            auto p = q.front();
            q.pop();
            for (auto i: vls[p.first]) {
                if (i == end) return p.second+1;
                if (visited[i]) continue;
                q.push({i, p.second+1});
                visited[i] = true;
            }
        }
        return 0;
    }
};
```



## 2021/5/28

### [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

- 快慢指针

```cpp
class Solution {
public:
    // 一直在纠结 i == nums[i] 的情况，这种情况它就自循环了
    // 其实不用纠结，首先它必须是从其他节点过来的，也就是有nums[j] == i
    // 所以出现这种情况的时候，i就一定是重复节点了
    int findDuplicate(vector<int>& nums) {
        int slow = 0, fast = 0;
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);
        
        int l = 0;
        while (l != slow) {
            l = nums[l];
            slow = nums[slow];
        }
        return slow;
    }
};
```

### [69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

```cpp
class Solution {
public:
    int mySqrt(int x) {
        long long i = 1;
        while (i*i <= x)
            i ++;
        return i-1;    
    }
};
```

### [88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)

```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        for (int i = 0; i < n; i ++) {
            nums1[i+m] = nums2[i];
        }
        sort(nums1.begin(), nums1.end());
    }
};
```

### [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

- 递归

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void dfs(TreeNode* root, vector<int>& v) {
        if (!root) return;
        dfs(root->left, v);
        v.push_back(root->val);
        dfs(root->right, v);
    }
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> ans;
        dfs(root, ans);
        return ans;
    }
};
```

- 迭代

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> ans;
        stack<TreeNode*> s;
        s.push(root);
        while (!s.empty()&&root) {
            root = s.top();
            while (root->left) {
                s.push(root->left);
                root->left = nullptr;
                root = s.top();
            }
            
            ans.push_back(root->val);
            s.pop();
            if (root->right) {
                s.push(root->right);
                root->right = nullptr;
            }
            // cout << root->val << ' ' << s.empty() << endl;
        }
        return ans;
    }
};
```

## 2021/5/29

### [108. 将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* dfs(vector<int>& nums, int left, int right) {
        if (left > right) return nullptr;
        int mid = (left + right) >> 1;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = dfs(nums, left, mid-1);
        root->right = dfs(nums, mid+1, right);
        return root;
    }
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return dfs(nums, 0, nums.size()-1);
    }
};
```

### [238. 除自身以外数组的乘积](https://leetcode-cn.com/problems/product-of-array-except-self/)

```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        vector<int> ans(nums.size(), 1);
        for (int i = 1; i < nums.size(); ++ i) {
            ans[i] = ans[i-1]*nums[i-1];
            // cout << ans[i] << ' ';
        }
        
        int R = 1;
        for (int i = nums.size()-1; i >= 0; -- i) {
            if (i == 0) {
                ans[i] = R;
                break;
            }
            ans[i] = ans[i]*R;
            R = R*nums[i];
        }
        return ans;
    }
};
```

## 2021/5/30

### [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int m = prices[0];
        int ans = 0;
        
        for (int i = 0; i < prices.size(); ++ i) {
            ans = max(ans, prices[i]-m);
            m = min(m, prices[i]);
        }
        return ans;
    }
};
```

### [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int ans = 0;
        int buy = prices[0];
        for (int i = 1; i < prices.size(); ++ i) {
            if (prices[i] > prices[i-1]) {
                ans += prices[i] - buy;
            }
            buy = prices[i];
        }
        return ans;
    }
};
```

## 2021/6/1

### [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

- dp

  ```cpp
  class Solution {
  public:
      int coinChange(vector<int>& coins, int amount) {
          vector<int> dp(amount+1);
          dp[0] = 0;
          for (int i = 1; i < dp.size(); ++ i) {
              int tmp = INT_MAX;
              for (int j = 0; j < coins.size(); ++ j) {
                  if (i >= coins[j]&&dp[i-coins[j]] != -1)
                      tmp = min(tmp, dp[i-coins[j]]+1);
              }
              if (tmp != INT_MAX)
                  dp[i] = tmp;
              else 
                  dp[i] = -1;
          }
          return dp.back();
      }
  };
  ```

### [50. Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

```cpp
class Solution {
public:
    double myPow(double x, int n) {
        long long m = n;
        if (m < 0) {
            x = 1/x;
            m = -m;
        }
        double t = x;
        x = 1;
        while (m) {
            long long i = 2;
            double t2 = t;
            while (i < m) {
                i *= 2;
                t2 *= t2;
            }
            m -= i/2;
            x *= t2;
        }
        return x;
    }
};
```

### [326. 3的幂](https://leetcode-cn.com/problems/power-of-three/)

```cpp
class Solution {
public:
    bool isPowerOfThree(int n) {
        if (n <= 0) return false;
        while (n % 3 == 0) {
            n /= 3;
        }
        if (n == 1) return true;
        return false;
    }
};
```

### [204. 计数质数](https://leetcode-cn.com/problems/count-primes/)

```cpp
class Solution {
public:
    int countPrimes(int n) {
        vector<int> isPrime(n, 1);
        
        int ans = 0;
        for (int i = 2; i < n; i ++) {
            if (isPrime[i]) {
                ans ++;
                isPrime[i] = 0;
                if ((long long)i*i < n) {
                    for (long long j = i*i; j < n; j += i) {
                        isPrime[j] = 0;
                    }
                }
            }
        }
        return ans;
    }
};
```

## 2021/6/2

### [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if (nums.empty()) return 0;
        unordered_set<int> s;
        for (int i = 0; i < nums.size(); ++ i) {
            s.insert(nums[i]);
        }
        int ans = 1;
        int cnt;
        for (auto num: s) {
            if (!s.count(num-1)) {
                int t = 1;
                while (s.count(num+t)) {
                    t ++;
                }
                ans = max(ans, t);
            }
        }
        return ans;
    }
};
```

### [29. 两数相除](https://leetcode-cn.com/problems/divide-two-integers/)

```cpp
class Solution {
public:
    int divide(int dividend, int divisor) {
        if (dividend == INT_MIN && divisor == -1) 
            return INT_MAX;
        if (divisor == INT_MIN) {
            if (dividend == INT_MIN) return 1;
            return 0;
        }
            
        int isNegative = -1;
        long long a, b; a = dividend; b = divisor;
        if (a < 0) {
            a = -a;
            isNegative = -isNegative;
        }
        if (b < 0) {
            b = -b;
            isNegative = -isNegative;
        }
        long long nums = 1;
        long long sum = b;
        vector<pair<long long, long long>> vp;
        vp.push_back({1, sum});
        while (sum < a) {
            sum = sum + sum;
            nums = nums + nums;
            vp.push_back({nums, sum});
        }##
        long long ans = 0;
        for (int i = vp.size()-1; i >= 0; -- i) {
            // cout << vp[i].second << ' ';
            if (a < vp[i].second) continue;
            a -= vp[i].second;
            ans += vp[i].first;
        }
        if (isNegative == 1) ans = -ans;
        return ans;
    }
};
```

### [268. 丢失的数字](https://leetcode-cn.com/problems/missing-number/)

```cpp
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        
        int n = nums.size();
        while (1) {
            int i;
            for (i = 0; i < n; ++ i) {
                if (nums[i] == i || nums[i] == n) continue;
                break;
            }
            if (i == n) break;
            while (nums[i] != i && nums[i] != n) {
                swap(nums[i], nums[nums[i]]);
            }
        }
        for (int i = 0; i < n; ++ i) {
            if (nums[i] == n) return i;
        }
        return n;
    }
};
```

### [236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root) return root;
        if (root == p||root == q) return root;
        
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        if (left != p && left != q && left != nullptr) return left;
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        if (right != p && right != q && right != nullptr) return right;
        
        bool leftIsPQ = false;
        bool rightIsPQ = false;
        if (left == p||left == q) leftIsPQ = true;
        if (right == p||right == q) rightIsPQ = true;
        
        if (leftIsPQ && rightIsPQ) return root;
        if (leftIsPQ) return left;
        if (rightIsPQ) return right;
        return nullptr;

    }
};
```

## 2021/6/3

### [217. 存在重复元素](https://leetcode-cn.com/problems/contains-duplicate/)

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_set<int> s;
        for (int i = 0; i < nums.size(); ++ i) {
            if (s.count(nums[i]))
                return true;
            s.insert(nums[i]);
        }
        return false;
    }
};
```

### [240. 搜索二维矩阵 II](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int col = matrix.size();
        int row = matrix[0].size();
        int x = 0; int y = row-1;
        while (x < col && y >= 0) {
            if (matrix[x][y] == target) {
                return true;
            }
            if (matrix[x][y] > target) {
                y --;
            } else {
                x ++;
            }
        }
        return false;
    }
};
```

## 2021/6/14

### [162. 寻找峰值](https://leetcode-cn.com/problems/find-peak-element/)

```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        if (nums.size() == 1) return 0;
        int l = 0;
        int r = nums.size()-1;
        
        while (l < r) {
            if (nums[l] > nums[l+1]) return l;
            if (nums[r] > nums[r-1]) return r;
            int mid = (l+r) >> 1;
            if (nums[mid] > nums[mid-1] && nums[mid] > nums[mid+1]) {
                return mid;
            } else if (nums[mid] < nums[mid-1]) {
                r = mid;
            } else {
                l = mid;
            }
        }
        return l;
    }
};
```

### [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

```cpp
class Solution {
public:
    int climbStairs(int n) {
        int a = 0, b = 1;
        for (int i = 0; i < n; ++ i) {
            int c = a + b;
            a = b;
            b = c;
        }
        return b;
    }
};
```

### [171. Excel表列序号](https://leetcode-cn.com/problems/excel-sheet-column-number/)

```cpp
class Solution {
public:
    int titleToNumber(string columnTitle) {
        long long ans = 0;
        for (int i = 0; i < columnTitle.length(); ++ i) {
            ans = ans*26 + columnTitle[i]-'A'+1;
        }
        return ans;
    }
};
```

### [150. 逆波兰表达式求值](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

```cpp
class Solution {
public:
    bool check(string s) {
        if (s.length() != 1) return false;
        if (s[0] != '/'&&s[0] != '*'&&s[0] != '+'&&s[0] != '-')
            return false;
        return true;
    }
    int evalRPN(vector<string>& tokens) {
        stack<long long> sll;
        for (auto s: tokens) {
            if (!check(s)) {
                sll.push(stoi(s));
                continue;
            }
            if (sll.empty()) {
                cout << "符号数目与数字数目不匹配";
                return 0;
            }
            int b = sll.top();
            sll.pop();
            if (sll.empty()) {
                cout << "符号数目与数字数目不匹配";
                return 0;
            }
            int a = sll.top();
            sll.pop();
            int c;
            if (s[0] == '/') {
                c = a/b;
            } else if (s[0] == '*') {
                c = a*b;
            } else if (s[0] == '+') {
                c = a+b;
            } else if (s[0] == '-') {
                c = a-b;
            }
            // cout << c << ' ';
            sll.push(c);
        }
        return sll.top();
    }
};
```

### [169. 多数元素](https://leetcode-cn.com/problems/majority-element/)

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int sum = 0;
        int candidate = nums[0];
        for (int i = 0; i < nums.size(); ++ i) {
            if (sum == 0) candidate = nums[i];
            if (nums[i] == candidate) {
                sum ++;
            } else {
                sum --;
            }
        }
        return candidate;
    }
};
```

### [190. 颠倒二进制位](https://leetcode-cn.com/problems/reverse-bits/)

- 逐位移动

  ```cpp
  class Solution {
  public:
      uint32_t reverseBits(uint32_t n) {
          uint32_t ans = 0;
          for (int i = 0; i < 32; ++ i) {
              ans = ans << 1;
              ans |= n&1;
              n = n >> 1;
          }
          return ans;
      }
  };
  ```

- 分治

  ```cpp
  class Solution {
  public:
      const uint32_t M1 = 0x55555555;
      const uint32_t M2 = 0x33333333;
      const uint32_t M4 = 0x0f0f0f0f;
      const uint32_t M8 = 0x00ff00ff;
      
      uint32_t reverseBits(uint32_t n) {
          n = n >> 1 & M1 | (n & M1) << 1;
          n = n >> 2 & M2 | (n & M2) << 2;
          n = n >> 4 & M4 | (n & M4) << 4;
          n = n >> 8 & M8 | (n & M8) << 8;
          
          return n >> 16 | n << 16;
      }
  };
  ```

  



