# 面试热题

## 1. 两数之和

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> hashtable;
        for(int i = 0; i < nums.size(); i++) hashtable[target - nums[i]] = i;
        for(int i = 0; i < nums.size(); i++){
            if(hashtable.count(nums[i]) && i != hashtable[nums[i]])
                return vector<int>({i, hashtable[nums[i]]});
        }
        return vector<int>();
    }
};
```

## 2. 两数相加

```C++
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
//原地算法
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int carry = 0, temp = 0;
        ListNode* root = l1;
        while(l1 && l2){
            temp = l1->val + l2->val + carry;
            l1->val = temp % 10;
            carry = temp / 10;
            //如果l1或l2到达最尾端，直接跳出循环
            if(!l1->next || !l2->next) break;
            l1 = l1->next;
            l2 = l2->next;
        }
        if(l2->next) l1->next = l2->next;
        while(l1->next && carry){
            l1 = l1->next;
            temp = l1->val + carry;
            l1->val = temp % 10;
            carry = temp / 10;
            //如果l1到达尾端，跳出循环
            if(!l1->next){
                break;
            } 
        }
        if(carry) l1->next = new ListNode(carry);
        return root;
    }
};
```

## 3. 无重复字符的最长子串

连续子串常用方法有DP，双指针滑动窗口，前缀和。

这里使用双指针。

```C++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int left = 0, right = 0, maxLen = 0;
        unordered_set<char> hashset;
        while(right < s.size()){
            if(!hashset.count(s[right]))
                hashset.insert(s[right++]);
            else{
                hashset.erase(s[left++]);
                maxLen = max(maxLen, right - left + 1);
            }
        }
        maxLen = max(maxLen, right - left);//此时right = s.size()已经溢出
        return maxLen;
    }
};
```

## 4. 寻找两个正序数组的中位数

```C++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int m = nums1.size(), n = nums2.size();
        if(m > n)
            return findMedianSortedArrays(nums2, nums1);
        int k = (m + n + 1) / 2;
        int left = 0, right = m;
        while(left < right){
            int i = (left + right + 1) / 2;//取中位数
            int j = k - i;
            if(nums1[i - 1] > nums2[j]) right = i - 1;
            else left = i;
        }
        int left2 = k - left;
        int nums1LeftMax = left == 0 ? INT_MIN : nums1[left - 1];
        int nums1RightMin = left == m ? INT_MAX : nums1[left];
        int nums2LeftMax = left2 == 0? INT_MIN : nums2[left2 - 1];
        int nums2RightMin = left2 == n ? INT_MAX : nums2[left2];

        if((m + n) % 2)  return max(nums1LeftMax, nums2LeftMax);
        else return (max(nums1LeftMax, nums2LeftMax) + min(nums1RightMin, nums2RightMin)) / 2.0;
    }
};
```

## 5. 最长回文串

```C++
class Solution {
public:
    pair<int, int> expand(const string& s, int l, int r){
        while(l >= 0 && r < s.size() && s[l] == s[r]){
            l--, r++;
        }
        return make_pair(l + 1, r - 1);
    }
    string longestPalindrome(string s) {
        string temp = "";
        for(int i = 0; i < s.size(); i++){
            pair<int, int> a, b;
            a = expand(s, i, i);
            b = expand(s, i, i + 1);
            if(a.second - a.first + 1 > temp.size()) temp = s.substr(a.first, a.second - a.first + 1);
            if(b.second - b.first + 1 > temp.size()) temp = s.substr(b.first, b.second - b.first + 1);
        }
        return temp;
    }
};
```

## 7. 整数反转

```C++
class Solution {
public:
    int reverse(int x) {
        int res = 0;
        while(x){
            if(res > INT_MAX / 10 || res < INT_MIN / 10) return 0;
            res = res * 10 + x % 10;
            x /= 10;
        }
        return res;
    }
};
```

## 8. 字符串转换整数

```C++
class Solution {
public:
    int myAtoi(string s) {
        int i = 0;
        while(s[i] == ' ') i++;
        int posi = 1;//缺省为正数
        if(s[i] == '+' || s[i] == '-'){
            posi = s[i] == '-' ? -1 : 1;
            i++;
        }
        long long res = 0;
        while(i < s.size() && s[i] >= '0' && s[i] <= '9'){
            res = res * 10 + s[i++] - '0';
            if(posi * res > INT_MAX) return INT_MAX;
            else if(posi * res < INT_MIN) return INT_MIN;
        }
        return posi * res;
    }
};
```

## 15. 三数之和

排序后，先从前往后遍历第一个数nums[i]，然后再区间[i + 1, size)两边向中间查找，时间复杂度O(n^2^)

```C++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        if(nums.size() < 3) return res;
        sort(nums.begin(), nums.end());
        for(int i = 0; i < nums.size() - 2; i++){
            if(nums[i] > 0) break;
            if(i > 0 && nums[i] == nums[i - 1]) continue;//跳过重复数字
            int j = i + 1, k = nums.size() - 1;
            while(j < k){
                int sum = nums[i] + nums[j] + nums[k];
                if(sum == 0) {
                    res.push_back(vector<int>({nums[i], nums[j], nums[k]}));
                    do j++; while(j < k && nums[j] == nums[j - 1]);//跳到下一个不相同的数
                    do k--; while(j < k && nums[k] == nums[k + 1]);
                }
                else if(sum < 0) j++;
                else k--;
            }
        }
        return res;
    }
};
```

## 17.电话号码的字母组合

经典回溯问题

```C++
class Solution {
public:
    unordered_map<char,string> hashtable = {{'2',"abc"},{'3',"def"},{'4',"ghi"},{'5',"jkl"},
                                            {'6',"mno"},{'7',"pqrs"},{'8',"tuv"},{'9',"wxyz"}};
    vector<string> res;
    void makeString(string temp, int i, const string& digits){
        if(i == digits.size())
            res.push_back(temp);
        string str = hashtable[digits[i]];
        for(int j = 0; j < str.size(); j++){
            temp.push_back(str[j]);
            makeString(temp, i + 1, digits);
            temp.pop_back();
        }
    }
    vector<string> letterCombinations(string digits) {
        if(digits.size() == 0) return res;
        makeString("", 0, digits);
        return res;
    }
};
```

## 19. 删除链表的倒数第N个节点

需要考虑到n为链表长度的情况

```C++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *prev = head, *curr = head;
        while(n--){
            if(!prev->next) return head->next;//如果n为链表长度，表示删除头结点
            prev = prev->next;
        }
        while(prev->next){
            prev = prev->next;
            curr = curr->next;
        }
        curr->next = curr->next->next;
        return head;
    }
};
```

## 21. 合并两个有序链表

```C++
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
        ListNode* dummy = new ListNode;
        ListNode* curr = dummy;
        while(l1 && l2){
            if(l1->val < l2->val){
                curr->next = l1;
                l1 = l1->next;
            }else{
                curr->next = l2;
                l2 = l2->next;
            }
            curr = curr->next;
        }
        if(l1) curr->next = l1;
        else curr->next = l2;
        return dummy->next;
    }
}
```

## 22.括号生成

回溯问题

```C++
class Solution {
public:
    vector<string> res;
    void makebrackets(int l, int r, int n, string temp){
        if(l + r == n * 2){
            res.push_back(temp);
            return;
        }
        if(l < n){
            temp.push_back('(');
            makebrackets(l + 1, r, n, temp);
            temp.pop_back();
        } 
        if(r < l){
            temp.push_back(')');
            makebrackets(l, r + 1, n, temp);
            temp.pop_back();
        }
    }
    vector<string> generateParenthesis(int n) {
        makebrackets(0, 0, n, "");
        return res;
    }
};
```

## 23.合并K个升序链表

template<

  class T,
  class Container = [std::vector](http://en.cppreference.com/w/cpp/container/vector)<T>,
  class Compare = [std::less](http://en.cppreference.com/w/cpp/utility/functional/less)<typename Container::value_type>

\> class priority_queue;

```C++
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
    struct cmp{
    bool operator()(ListNode* a, ListNode* b){
        return a->val > b->val;
        }
    };
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode* dummy = new ListNode;
        ListNode* curr = dummy;
        priority_queue<ListNode*, vector<ListNode*>, cmp> pq;
        for(auto& l : lists) if(l) pq.push(l);
        while(!pq.empty()){
            ListNode* temp = pq.top();
            pq.pop();
            curr->next = temp;
            curr = curr->next;
            if(temp->next) pq.push(temp->next);
        }
        return dummy->next;
    }
};
```

## 26.删除有序数组中的重复项

```C++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(nums.size()==0) return 0;
        int i = 0;
        for(int j = 1; j < nums.size(); j++){
            if(nums[i] != nums[j]){
                nums[++i] = nums[j];
            }
        }
        return i + 1;
    }
};
```

## 28. 实现strStr()

KMP算法，对需要查找的字符串找到最长的相同前缀和后缀，在查找过程中出现不相同则返回到前缀的地方继续查找。

```C++
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.size(), m = needle.size();
        if(m == 0) return 0;
        vector<int> next(m);
        for(int i = 1, j = 0; i < m; i++){
            while(j && needle[i] != needle[j]) j = next[j - 1];
            if(needle[i] == needle[j]) j++;
            next[i] = j;
        }
        for(int i = 0, j = 0; i < n; i++){
            while(j && haystack[i] != needle[j]) j = next[j - 1];
            if(haystack[i] == needle[j]) j++;
            if(j == m) return i - m + 1;
        }
        return -1;
    }
};
```

## 33.搜索旋转排序数组

二分法

```C++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        while(l <= r){
            int mid = l + r >> 1;
            if(target == nums[mid]) return mid;
            if(nums[mid] >= nums[0]){//左边有序
                if(target >= nums[0] && target < nums[mid]) r = mid - 1;
                else l = mid + 1;
            }else{//右边有序
                if(target <= nums.back() && target > nums[mid]) l = mid + 1;
                else r = mid - 1;
            }
        }
        return -1;
    }
};
```

## 34. 在排序数组中查找元素的第一个和最后一个位置

```C++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        int mid;
        while(l <= r){
            mid = l + r >> 1;
            if(nums[mid] == target)
                break;
            else if(nums[mid] < target)
                l = mid + 1;
            else
                r = mid - 1;
        }
        if(l > r) return vector<int>({-1, -1});
        l = mid, r = mid;
        while(l > 0 && nums[l] == nums[l - 1]) l--;
        while(r < nums.size() - 1 && nums[r] == nums[r + 1]) r++;
        return vector<int>({l, r});
    }
};
```

## 36.有效的数独

```C++
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        int n = board.size();
        vector<vector<bool>> row(n, vector<bool>(n + 1, false)), col(n, vector<bool>(n + 1, false)), mat(n, vector<bool>(n + 1, false));
        for(int i = 0; i < n; i++){
            for(int j = 0; j < n; j++){
                if(board[i][j] == '.') continue;
                int num = board[i][j] - '0';
                int m = i / 3 + (j / 3) * 3;
                if(row[i][num] || col[j][num] || mat[m][num]) return false;
                row[i][num] = true;
                col[j][num] = true;
                mat[m][num] = true;
            }
        }
        return true;
    }
};
```

## 198. 打家劫舍

经典DP问题

```C++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n == 0) return 0;
        if(n == 1) return nums[0];
        vector<int> f(n, 0);
        f[0] = nums[0], f[1] = max(nums[0], nums[1]);
        for(int i = 2; i < n; i++){
            f[i] = max(f[i - 2] + nums[i], f[i - 1]);
        }
        return f[n - 1];
    }
};
```

## 200.岛屿问题

```C++
class Solution {
public:
    vector<int> x = {1, -1, 0, 0};
    vector<int> y = {0, 0, 1, -1};
    void sinklands(vector<vector<char>>& grid, int i, int j, int& m, int& n){
        if(i < 0 || i >= m || j < 0 || j >= n || grid[i][j] != '1') return;//边界外或者不等于‘1’
        grid[i][j] = '0';
        for(int k = 0; k < 4; k++)
            sinklands(grid, i + x[k], j + y[k], m, n);
    }
    int numIslands(vector<vector<char>>& grid) {
        int m = grid.size(), n = grid[0].size();
        int count = 0;
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(grid[i][j] == '1'){
                    count++;
                    sinklands(grid, i, j, m, n);
                }
            }
        }
        return count;
    }
};
```

## 208. 实现Trie（前缀树）

```C++
class Trie {
private:
    bool isEnd;
    Trie* Next[26];
public:
    /** Initialize your data structure here. */
    Trie() {
        isEnd = false;
        memset(Next, 0, sizeof(Next));
    }
    
    /** Inserts a word into the trie. */
    void insert(string word) {
        Trie* node = this;
        for(char ch : word){
            if(node->Next[ch - 'a'] == nullptr)
                node->Next[ch - 'a'] = new Trie;
            node = node->Next[ch - 'a'];
        }
        node->isEnd = true;
    }
    
    /** Returns if the word is in the trie. */
    bool search(string word) {
        Trie* node = this;
        for(char ch: word){
            node = node->Next[ch - 'a'];
            if(node == nullptr) return false;
        }
        return node->isEnd;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    bool startsWith(string prefix) {
        Trie* node = this;
        for (char c : prefix) {
            node = node->Next[c-'a'];
            if (node == NULL) {
                return false;
            }
        }
        return true;
    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
```

## 210. 课程表II

dfs拓扑排序

先构造一个二维数组，存放每个点的出度，然后遍历所有的点进行dfs，需要记录每个点的遍历状态，未搜索，搜索中与搜索完毕，如果存在搜索中的点再次被搜索，那么说明成环了。使用stack来保存遍历后的点，先被遍历的点必须先学习，使用stack来记录学习顺序。

```C++
class Solution {
public:
    vector<vector<int>> edges;
    stack<int> stk;
    vector<int> isVisited;
    bool valid;
    void dfs(int crs){
        isVisited[crs] = 1;
        for(int t: edges[crs]){
            if(isVisited[t] == 0) {
                dfs(t);
                if(!valid) return;//如果有环，则直接返回 
            }
            else if(isVisited[t] == 1){//存在环
                valid = false;
                return;
            }
        }
        stk.push(crs);
        isVisited[crs] = 2;
    }
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        valid = true;
        isVisited = vector<int>(numCourses, 0);
        edges = vector<vector<int>>(numCourses, vector<int>());
        for(vector<int> temp : prerequisites){
            edges[temp[1]].push_back(temp[0]);
        }
        for(int i = 0; i < numCourses; i++){
            if(isVisited[i] == 0) dfs(i);
        }
        vector<int> res;
        if(!valid) return res;
        while(!stk.empty()){
            res.push_back(stk.top());
            stk.pop();
        }
        return res;
    }
};
```

bfs

记录下所有点的入度，思路是先找到入度为0的点t，通过队列记录，然后对于所有入度有t的点，入度减一，直到入度为0， 这时候表示可以选课，那么直接放入队列，等待放入结果中，不能直接放入结果数组中，需要用队列去判断当前图中还有没有入度为0的点，如果没有，则跳出循环，再判断是否所有点都被放入结果数组。对于有环的图，无法实现入度为0，所有当有点未被记录到结果数据时，说明当前图中有环，则直接返回空数组。

```C++
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        queue<int> q;
        vector<vector<int>> edges(numCourses, vector<int>());
        vector<int> indeg(numCourses, 0);
        for(vector<int> temp : prerequisites){
            edges[temp[1]].push_back(temp[0]);
            indeg[temp[0]] ++;
        }
        for(int i = 0; i < numCourses; i++){
            if(indeg[i] == 0)
                q.push(i);
        }
        vector<int> res;
        while(!q.empty()){
            int u = q.front();
            q.pop();
            res.push_back(u);
            for(int temp : edges[u]){
                indeg[temp]--;
                if(indeg[temp] == 0)
                    q.push(temp);
            }
        }
        if(res.size() != numCourses) return vector<int>({});
        return res;
    }
};
```

## 215. 数组中的第K大最大元素

堆排序

```C++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int, vector<int>, greater<int>> pq;
        for(int num : nums){
            if(pq.size() < k){
                pq.push(num);
            }else if(num > pq.top()){
                pq.pop();
                pq.push(num);
            }
        }
        return pq.top();
    }
};
```

## 217. 存在重复元素

使用map

```go
func containsDuplicate(nums []int) bool {
    m := make(map[int]int)
    for i := range nums {
        _, b := m[nums[i]]
        if b == false {
            m[nums[i]] = 1
        }else{
            return true;
        }
    } 
    return false;
}
```

## 523. 连续的子数组和

尝试使用前缀和，超时。

```C++
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> s(n + 1, 0);
        s[1] = nums[0];
        for(int i = 2; i <= n; i++){
            s[i] = s[i - 1] + nums[i - 1];//前缀和 s[i] = nums[0] + nums[1] + ... + nums[i - 1]
        }
        for(int i = 0; i <= n - 2; i++){
            for(int j = i + 2; j <= n; j++){
                if((s[j] - s[i]) % k == 0)
                    return true;
            }
        }
        return false;
    }
};
```

使用unordered_set优化，利用s[i]与s[j]对k取余相同
$$
s[i]\%k=t
$$

$$
s[j]\%k=t
$$



即
$$
s[i]=m*k+t
$$

$$
s[j]=n*k+t
$$

两式相减，得到
$$
s[i]-s[j]=(m-n)*k
$$
如果$i-j>=2$，则为满足条件的子数组

```C++
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        if(n < 2) return false;
        vector<int> s(n + 1, 0);
        s[1] = nums[0];
        for(int i = 2; i <= n; i++){
            s[i] = s[i - 1] + nums[i - 1];//前缀和 s[i] = nums[0] + nums[1] + ... + nums[i - 1]
        }
        unordered_set<int> hs;
        for(int i = 2; i <= n; i++){
            hs.insert(s[i - 2] % k);
            if(hs.count(s[i] % k)) return true;
        }
        return false;
    }
};
```

