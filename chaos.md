# Chaos记录

##   [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        if(!n) return {};
        unordered_map<int,int> mp;
        for(int i=0;i<n;++i){
            if(mp.count(nums[i])) return {i,mp[nums[i]]};
            else mp[target-nums[i]] = i;
        }
        return {};
    }
};
```

## [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

```c++
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
    int add(int a,int b,int& go){
        int ans = (a+b+go)%10;
        go = (a+b+go)/10;
        return ans;
    }


    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* head = new ListNode();//虚头
        int go = 0;//进位符
        ListNode* cur = head;
        while(l1&&l2){//都不为空就循环
            int res = add(l1->val,l2->val,go);
            ListNode* newN = new ListNode(res);
            cur->next = newN;
            cur = cur->next;
            l1 = l1->next;
            l2 = l2->next;
        }

        if(l2) l1=l2;//交换

        while(l1){
            int num = 0;//跳出循环看看进位情况
            num = add(l1->val,0,go);
            ListNode* newN = new ListNode(num);
            cur->next = newN;
            cur = cur->next;
            l1 = l1->next;
        }

        if(go){
            ListNode* newN = new ListNode(go);
            cur->next = newN;
        }

        return head->next;
    }
};
```



## [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        //dp+hash 记录下最后一次出现的位置
        //定义 dp[i] 在i位置时(必须包括i位置的字符)的最长子串长度 
        //1.if s[i] not in map, dp[i] -> dp[i]= dp[i-1]+1
        //2.if s[i] in map,计算距离上次出现的距离d 如果这个长度大于dp[i-1]+1 dp[i]=dp[i-1]+1 否则 dp[i]=d;
        //ans = max(dp[i])

        int n = s.size();
        if(!n) return 0;
        unordered_map<char,int> map;

        vector<int>dp(n);
        int ans = 1;
        dp[0]= 1; //初始态
        map[s[0]]=0;
        for(int i =1;i<n;++i){
            int tmp = dp[i-1]+1;
            if(!map.count(s[i])) dp[i] = tmp;
            else{
                int d = i-map[s[i]]; //这是只算一头 注意和dp[i-1]的关系
                if(d>tmp) dp[i] = tmp;
                else dp[i] = d; 
            }
            // cout<<dp[i]<<endl;
            ans = max(ans,dp[i]);
            map[s[i]] = i;
        }

        return ans;
    }
};
```

