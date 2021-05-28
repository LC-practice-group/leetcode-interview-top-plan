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

## [4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

```

```



## [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

```c++
class Solution {
public:
    string longestPalindrome(string s) { 
        //dp dp[i][j]表示从 下标i到j的最长回文串 包含ij
        //如果 s[i]==s[j] dp[i][j] = dp[i+1][j-1]+1
        //如果 != dp[i][j] = max(dp[i+1][j],dp[i][j-1])
        //因为递推关系是 从左下来 所以i要从后往前 j要从前往后
        int n = s.size();
        // cout<<n<<endl;
        if(!n) return 0;
        vector<vector<bool>> dp(n,vector<bool>(n,false));

        for(int i =0;i<n;++i){
            dp[i][i]=true;
        }
        
        int min_l = 0;
        int max_r = 0;

        for(int i=n-1;i>=0;--i){
            for(int j=i+1;j<n;++j){
                if(s[i]==s[j]){
                    if(dp[i+1][j-1]||j-i==1){
                        dp[i][j]=true;
                        if(j-i>max_r-min_l){
                            max_r = j;
                            min_l = i;
                        }
                    }
                    else dp[i][j] =false;
                }
                else dp[i][j] = false;
            }
        }

        string ans = "";
        for(int i =min_l;i<=max_r;++i){
            ans+=s[i];
        }

        return ans;
    }
};
```



## [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

```c++
class Solution {
public:
    int reverse(int x) {
        long long tmp = 0;
        int sign =1;
        if(x<0) sign = -1;
        x = abs(x);
        while(x>0){
            int pos = x%10;
            x = x/10;
            tmp = tmp*10+pos;
        }

        if(tmp>INT_MAX) return 0;
        else return tmp*sign;
    }
};
```



## [8. 字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

```c++
class Solution {
public:
    int myAtoi(string s) {
        int n = s.size();
        int cur = 0;
        //读入丢弃所有前置空格
        while(cur<n&&s[cur]==' ') ++cur;
        
        //处理符号
        int sign = 1;
        if(s[cur]=='-'){
            sign=-1;
            ++cur;
        }
        else if(s[cur]=='+'){
            ++cur;
        }
        
        
        //处理数字
        long long ans = 0;
        for(int i=cur;i<n;++i){
            // cout<<s[i]<<endl;
            if('0'<=s[i]&&s[i]<='9'){
                ans = ans*10+s[i]-'0';
                if(ans>=INT_MAX||ans<=INT_MIN) break; //炸longlong判断
            }
            else break;
        }
        
        ans = ans * sign;
        if(ans<INT_MIN) ans = INT_MIN;
        if(ans>INT_MAX) ans = INT_MAX;
        return ans;
    }
};
```

## [10. 正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)

```

```



## [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int n = height.size();
        if(!n) return 0;
        
        int max_c = 0;
        //双指针 利用起来 移动谁？ 左右较小的才需要移动 移动小的值才可能变大
        int l = 0;
        int r = n-1;
        
        while(r>l){
            int c = min(height[r],height[l])*(r-l);
            max_c = max(max_c,c);
            if(height[r]>height[l]) ++l;
            else --r;  
        }
        
        return max_c;

    }
};
```

## [13. 罗马数字转整数](https://leetcode-cn.com/problems/roman-to-integer/)

```c++
class Solution {
public:
    int romanToInt(string s) {
        // char ch[7] = {'M','D','C','L','X','V','I'};
        // int num[7] = {1000,500,100,50,10,5,1};

        unordered_map<char,int> mp = {{'M',1000},{'D',500},{'C',100},{'L',50},{'X',10},{'V',5},{'I',1}};

        int n = s.size();
        int ans =0;
        
        for(int i=0;i<n;++i){
            char now = s[i];
            char next = ' ';
            if(i+1<n) next = s[i+1];
            if(next!=' '&&mp[next]>mp[now]) {
                ans+= mp[next]-mp[now];
                ++i;//把后面的i消费掉
            }
            else ans+= mp[now];
        }
        return ans;
    }
};
```



## [14. 最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/) 

```c++
class Solution {
public:
    //trie裸题
    
    struct trieNode{
        int num =0;//经过此节点的数量
        trieNode* next[26];
        trieNode(){
            memset(next,0,sizeof(next));
        }
    };
    
    class trie{
        public:
        trieNode* root;
        trie(int n){
            root = new trieNode();
            root->num = n;//虚头的数量得和全部得数量相等
        }
        void insert(string s){
            int n = s.size();
            trieNode* cur = root;
            for(int i=0;i<n;++i){
                if(!cur->next[s[i]-'a']){
                    cur->next[s[i]-'a'] = new trieNode();
                }
                cur = cur->next[s[i]-'a'];
                ++(cur->num);
            }
        }
        
        string search(){
            string ans = "";
            trieNode* cur = root;
            while(cur){
                bool flag = false;
                for(int i=0;i<26;++i){
                    if(cur->next[i]&&cur->next[i]->num==root->num){
                        ans+='a'+i;
                        cur=cur->next[i];
                        flag = true;
                    }
                }
                if(!flag) cur=nullptr;//一个也没找到就要退出循环
            }
            
            return ans;
        }
        
    };
    
    string longestCommonPrefix(vector<string>& strs) {
        int n = strs.size();
        if(!n) return "";
        string ans = "";
            
        trie tr = trie(n);//初始化字典树
        for(int i=0;i<n;++i){
            tr.insert(strs[i]);
        }
        
        ans = tr.search();
        
        return ans;
    }
};
```

## [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

```c++
class Solution {
public:
    void find(vector<int> num,int cur,int target,vector<vector<int>>& ans){
        int n = num.size();
        int l = cur+1;
        int r = n-1;
        while(r>l){
            if(num[l]+num[r]==target) {
                //注意去重
                ans.push_back({-target,num[l],num[r]});
                while(l+1<n-1&&num[l+1]==num[l]) ++l;
                ++l;
                while(r-1>l&&num[r-1]==num[r]) --r;
                --r;
            }
            else if(num[l]+num[r]<target){
                while(l+1<n-1&&num[l+1]==num[l]) ++l;
                ++l;
            } 
            else {
                while(r-1>l&&num[r-1]==num[r]) --r;
                --r;
            }
        }
    }
    
    
    
    vector<vector<int>> threeSum(vector<int>& nums) {
        //选择出一个数字之后 就会下降为两数之和问题
        int n = nums.size();
        
        //去重 需要sort
        sort(nums.begin(),nums.end());
        
        vector<vector<int>> ans;
        
        for(int i=0;i<n-2;++i){//给后面的数字留下两个位置
            find(nums,i,-nums[i],ans);
            while((i+1<n-2)&&(nums[i+1]==nums[i])) ++i;//去重
        }
        
        return ans;
    }
};
```

## [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

```c++
class Solution {
public:
    vector<string> ans;
    unordered_map<char,string> mp={{'2',"abc"},{'3',"def"},{'4',"ghi"},{'5',"jkl"},{'6',"mno"},{'7',"pqrs"},{'8',"tuv"},{'9',"wxyz"}};
    
    void dfs(int cur,int n,string now,string digits){
        if(cur==n){
            ans.push_back(now);
            return;
        }
        string sg = mp[digits[cur]];
        for(auto s:sg){
            now +=s;
            dfs(cur+1,n,now,digits);
            now.pop_back();
        }
    }
    vector<string> letterCombinations(string digits) {
        //1.字典树快速查出 指数级 
        //2.回溯
        int n = digits.size();
        if(!n) return {};
        dfs(0,n,"",digits);
        return ans;
    }
};
```

## [19. 删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* preNode = head;
        ListNode* vhead = new ListNode(0,head);
        
        for(int i=0;i<n;++i) preNode = preNode->next;
        
        ListNode* dNode = head;
        ListNode* pp = vhead;
        while(preNode){
            preNode = preNode->next;
            pp = dNode;
            dNode = dNode->next;
        }
        
        pp->next  = dNode->next;
        
        delete dNode;
        
        return vhead->next;
    }
};
```

##　[20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

```c++
class Solution {
public:
    bool isValid(string s) {
        //栈应用
        unordered_map<char,char> mp ={
            {')','('},
            {']','['},
            {'}','{'}
        };
        
        int n = s.size();
        stack<char> st;
        for(int i =0;i<n;++i){
            if(!mp.count(s[i])) st.push(s[i]);
            else{
                if(st.empty()||st.top()!=mp[s[i]]) return false;
                else st.pop();
            }
        }
        return st.empty(); //为空匹配成功
    }
};
```

## [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

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
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        // if(l1.size()>l2.size()){
        //     ListNode* tmp = l1;
        //     l1 = l2;
        //     l2 = tmp;
        // }//让L1默认长
        
        ListNode* cur = new ListNode(0);
        ListNode* hair = cur;
        while(l1&&l2){
            if(l1->val<l2->val){
                cur->next = l1;
                l1=l1->next;
            }
            else{
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;  
        }
        
        while(l1){
            cur->next = l1;
            l1=l1->next;
            cur = cur->next;
        }
        
        
        while(l2){
            cur->next = l2;
            l2=l2->next;
            cur = cur->next;
        }
        
        
        return hair->next;
    }
};
```

## [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

```

```

## [23. 合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

```

```



## [26. 删除有序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        //原地采用移动法
        int n=nums.size();
        if(!n) return 0;
        int pos = 0;//现在去重到的位置
        for(int i=0;i<n;){
            if(nums[i]==nums[pos]){
                i++;
            }
            else{
                pos++;
                nums[pos] = nums[i];
            }
        }

        return pos+1;
    }
};
```

## [28. 实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)

```c++
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n1 = haystack.size();
        int n2 = needle.size();
        if(!n2) return 0;
        if(n2>n1) return -1;//剪枝
        
        int cur1 = 0;
        int cur2 = 0;
        while(cur1<n1){
            if(haystack[cur1]==needle[cur2]) {
                ++cur1;
                ++cur2;
                if(cur2==n2) return cur1-cur2;
            }
            else{//回退
                cur1 = cur1-cur2+1;//跳过原来的位置
                cur2 = 0;//回退到0
            }
        }
        return -1;//找不到-1
    }
};
```

## [29. 两数相除](https://leetcode-cn.com/problems/divide-two-integers/)

```c++
class Solution {
public:
    int divide(int dividend, int divisor) {
        //倍增法 位运算不算乘除法
        //二分查找
        //边界条件 判断一下溢出的情况
        if(dividend==INT_MIN&&divisor==-1) return INT_MAX;
        if(divisor==1) return dividend;
        if(divisor==-1) return -dividend;


        long long a = dividend;
        long long b = divisor;
        //确定一下符号 全部变成正数
        int sign = 1;
        if(a<0){
            sign=-sign;
            a=-a;
        }
        if(b<0){
            sign=-sign;
            b=-b;
        }

        // if(b>a) return 0;

        
        //确定初始二分区间
        long long l= 0;
        long long r= a;

        while(r>l){
            int mid = (l+r)>>1;
            if(mul(mid,b)>a){
                r = mid;
            }
            else{
                l = mid+1;
            }
        }
        if(sign>0) return l-1;
        else return 1-l;
    }

    long long mul(long long a,long long b){
        //倍增乘法
        long long x = a;
        long long y = b;
        
        long long ans =0;
        while(x){
            if(x&1) ans+=y;
            x=x>>1;
            y = y+y;
        }
        return ans;
    }
};
```

## [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        //变种二分 讨论清楚每种情况的区间转移
        int n = nums.size();
        int l = 0;
        int r = n-1;
        while(r>=l){
            int mid = (r+l)>>1;
            // cout<<"l: "<<l<<endl;
            // cout<<"r: "<<r<<endl;
            // cout<<"mid: "<<mid<<endl;
            //存在旋转
            if(nums[mid]==target) return mid;
            if(nums[mid]>=nums[0]&&target>=nums[l]&&target<nums[mid]){
                //左边有序
                r = mid-1;//找左边
            }
            else if(nums[mid]>=nums[0]&&(target>nums[mid]||target<nums[l])){
                l = mid+1;//找右边
            }
            //出来右边有序
            else if(target>nums[mid]&&target<=nums[r]){
                l= mid+1;
            }
            else{
                r = mid-1;
            }
        }
            return -1;

    }
};
```

## [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```c++
class Solution {
public:
    int low_bound(vector<int>& nums, int target){
        int n =nums.size();
        int l =0;
        int r =n-1;
        while(r>l){
            int mid = (l+r)>>1;
            if(nums[mid]>=target){
                r = mid;
            }
            else l= mid+1;
        }


        return l;
    }

    int up_bound(vector<int>& nums, int target){
        int n =nums.size();
        int l =0;
        int r =n-1;

         while(r>l){
            int mid = (l+r)>>1;
            if(nums[mid]>target){
                r = mid;
            }
            else l= mid+1;
        }
        return l;
    }



    vector<int> searchRange(vector<int>& nums, int target) {
        //upbound lowbound
        if(!nums.size()) return {-1,-1};

        int low  = low_bound(nums,target);
        int up = up_bound(nums,target);
    
        if(nums[low]!=target) return {-1,-1};
        if(nums[up]==target) return {low,up};
        return {low,up-1};
    }
};
```

## [36. 有效的数独](https://leetcode-cn.com/problems/valid-sudoku/)

```c++
class Solution {
public:
    bool row[9][9];
    bool col[9][9];
    bool block[3][3][9];

    bool isValidSudoku(vector<vector<char>>& board) {
        //分别储存行的分布情况 列的分布情况 块的分布情况
        memset(row,0,sizeof(row));
        memset(col,0,sizeof(col));
        memset(block,0,sizeof(block));

        for(int i=0;i<9;++i){
            for(int j=0;j<9;++j){
                if(board[i][j]=='.') continue;
                else{
                    int num = board[i][j]-'1';
                    if(row[i][num]||col[j][num]||block[i/3][j/3][num]) return false;
                    else{
                        row[i][num]=col[j][num]=block[i/3][j/3][num]=true;
                    }
                }
            }
        }

        return true;
    }
};
```

## [38. 外观数列](https://leetcode-cn.com/problems/count-and-say/)

```c++
class Solution {
public:

    string find(int n,string pre){
        pre += 'a';//哨兵
        char pre_ch = ' ';
        int num = 0;
        int ssize = pre.size();
        string ans = "";

        for(int i=0;i<ssize;++i){
            if(pre[i]!=pre_ch){
                if(num){
                    ans+=num+'0';
                    ans+= pre_ch;
                }
                pre_ch = pre[i];
                num=1; 
            }
            else{
                ++num;
            }
        }

        return ans;

    }

    string countAndSay(int n) {
        if(n==1) return "1";
        string pre = "1";   
        for(int i=1;i<n;++i){
            pre = find(i,pre);
        }

        return pre;
    }
};
```

