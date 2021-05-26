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

