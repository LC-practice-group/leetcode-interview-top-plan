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

