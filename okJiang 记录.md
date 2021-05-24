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

