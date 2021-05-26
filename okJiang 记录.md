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



