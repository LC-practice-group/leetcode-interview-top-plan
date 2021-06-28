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

```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        //dp
        int n = s.size();
        int m = p.size();

        vector<vector<int>> dp(n+1,vector<int>(m+1,0));

        dp[0][0]=true;

		//非常易错 补齐了0 i也要从0开始 因为 * 可以匹配空串 不能忽略
        for(int i=0;i<=n;++i){
            for(int j=1;j<=m;++j){
                if(p[j-1]!='*'){
                    if(i>=1&&(p[j-1]=='.'||s[i-1]==p[j-1])) dp[i][j]=dp[i-1][j-1];
                }
                else{
                    //匹配0个
                    dp[i][j] = dp[i][j-2];
                    //匹配上了 0个或者多个
                    if(i>=1&&(p[j-2]=='.'||s[i-1]==p[j-2])) dp[i][j] = dp[i-1][j]|dp[i][j-2];
                    }
            }
        }

        return dp[n][m];

    }
};
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

## [41. 缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)

```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        for(int i=0;i<nums.size();++i){
            while(nums[i]<nums.size()&&nums[i]>0&&nums[nums[i]-1]!=nums[i]){
                swap(nums[i],nums[nums[i]-1]);
            }
        }

        for(int i=0;i<nums.size();++i){
            if(nums[i]!=i+1) return i+1;
        }
        return nums.size()+1;
    }
};
```

## [42. 接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        vector<int> lmax(n,0);
        vector<int> rmax(n,0);

        int l = 0;
        int r = 0;
        for(int i=0;i<n;++i){
            lmax[i] = l;
            l = max(height[i],l);
        }
        for(int i=n-1;i>=0;--i){
            rmax[i] = r;
            r = max(height[i],r);
        }

        int ans = 0;
        for(int i=0;i<n;++i){
            if(height[i]<min(lmax[i],rmax[i])){
                ans+= min(lmax[i],rmax[i])-height[i];
            } 
        }

        return ans;
    }
};
```



## [46. 全排列](https://leetcode-cn.com/problems/permutations/)

```c++
class Solution {
public:
    vector<vector<int>> ans;
    void dfs(int cur,vector<int>& now,vector<int> mark,vector<int>& nums){
        // cout<<now.size()<<endl;
        if(cur==nums.size()){
            ans.push_back(now);
            return;
        }
        for(int i=0;i<nums.size();++i){
            if(!mark[i]){//没有使用过
                now.push_back(nums[i]);
                mark[i]=1;
                dfs(cur+1,now,mark,nums);
                mark[i]=0;
                now.pop_back();
            }
        }

    }
    vector<vector<int>> permute(vector<int>& nums) {
        vector<int> now;
        vector<int> mark(nums.size(),0);
        dfs(0,now,mark,nums);
        return ans;
    }
};
```



## [48. 旋转图像](https://leetcode-cn.com/problems/rotate-image/)

```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        //先沿对角线对称 再上下反转
        int n =matrix.size();

        //对角线对称
        for(int i=0;i<n;++i){
            for(int j=0;j<n-i-1;++j){
                swap(matrix[i][j],matrix[n-j-1][n-i-1]);
            }
        }
        //上下反转
        for(int i=0;i<n/2;++i){
            for(int j=0;j<n;++j){
                swap(matrix[i][j],matrix[n-i-1][j]);
            }
        }
    }
};
```

## [49. 字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        //排序当作key不能直接用hash因为无序
        unordered_map<string,vector<string>> mp;
        for(auto s:strs){
            string tmp =s;
            sort(tmp.begin(),tmp.end());
            mp[tmp].push_back(s);
        }
        vector<vector<string>> ans;
        for(auto [str,ans_p]:mp){
            ans.push_back(ans_p);
        }
        return ans;
    }
};
```

## [50. Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

```c++
//递归
class Solution {
public:
    double myPow(double x, int n) {
        //快速幂
        if(n==0) return 1;
        if(n==1) return x;
        int flag = 1;
        if(n<0){
            flag = 0;
            n = abs(n);
        }
        double p = myPow(x,n/2);
        double tmp = n%2==1?x*p*p:p*p;
        return flag?tmp:1/tmp;
    }
};
//迭代解
class Solution {
public:
    double quickMul(double x,long long n){
        double ans = 1.0;
        //当前需要计入的x幂次
        double q = x;
        while(n){
            if(n%2==1){
                ans *= q;
            }
            q *=q;
            n /=2;
        }
        return ans;
    }


    double myPow(double x, int n) {
        long long N=n;//防爆
        return N>0?quickMul(x,N):1/quickMul(x,-N);
    }
};
```



## [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        //dp
        int n = nums.size();
        if(!n) return 0;
        vector<int> dp(n+1);
        int sum_max = INT_MIN;
        for(int i =0;i<n;++i){
            dp[i+1] = max(dp[i]+nums[i],nums[i]);
            sum_max = max(sum_max,dp[i+1]);
        }
        return sum_max;
    }
};
```

## 

## [54. 螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

```c++
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        //方向数组
        vector<int> dx = {0,1,0,-1};
        vector<int> dy = {1,0,-1,0};
        

        vector<int> ans;

        int n  = matrix.size();
        int m  = matrix[0].size();
        vector<vector<int>> mm(n,vector<int>(m,0));

        int xx = dx[0];
        int yy = dy[0];

        int x = 0;
        int y = 0;

        int mark = 0;//位移标记
        for(int i=0;i<m*n;++i){
            mm[x][y] =1;
            ans.push_back(matrix[x][y]);
            if((x+xx>=n||x+xx<0)||(y+yy>=m||y+yy<0)||mm[x+xx][y+yy]) ++mark;
            xx = dx[mark%4];
            yy = dy[mark%4];
            x+=xx;
            y+=yy;
        }

        return ans;
    }
};
```



## [55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        //dp
        //dp[i]表示能否到达i
        
        int n = nums.size();

        int farest = 0;
        
        for(int i=0;i<n;++i){
            if(farest>=i){
                farest = max(i+nums[i],farest);
            }
            if(farest>=n-1) return true;
        }

        return false;
    }
};
```



## [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

```c++
class Solution {
public:
    bool judgeMerge(vector<int> a,vector<int> b){
        return a[1]>=b[0];
    }

    vector<int> mm(vector<int> a,vector<int> b){
        vector<int> ans;
        // cout<<"mm"<<endl;
        int l = min(a[0],b[0]);
        int r = max(a[1],b[1]);
        ans.push_back(l);
        ans.push_back(r);
        return ans;
    }


    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        //开头排序逐个合并
        auto f = [&](vector<int> a,vector<int> b){
            return a[0]<b[0];
        };
        sort(intervals.begin(),intervals.end(),f);
        int n = intervals.size();
        vector<vector<int>> ans;
        ans.push_back(intervals[0]);
        for(int i =1;i<n;++i){
            if(judgeMerge(ans[ans.size()-1],intervals[i])){
                vector<int> top = ans[ans.size()-1];
                ans.pop_back();
                ans.push_back(mm(top,intervals[i]));
            }
            else{
                ans.push_back(intervals[i]);
            }
        }

        return ans;
    }
};
```

##　[62. 不同路径](https://leetcode-cn.com/problems/unique-paths/)

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>> dp(m,vector<int>(n,1));
        
        for(int i=1;i<m;++i){
            for(int j=1;j<n;++j){
                dp[i][j] = dp[i-1][j]+dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
};
```

## [66. 加一](https://leetcode-cn.com/problems/plus-one/)

```c++
class Solution {
public:
    void add(int&a,int& flag){
        if(a>=0&&a<=8){
            ++a;
            flag = 0;
            return;
        }
        else {
            a=0;
            flag = 1;
        }
    }


    vector<int> plusOne(vector<int>& digits) {
        reverse(digits.begin(),digits.end());
        int flag = 1;
        int cur = 0;
        int n = digits.size();  
        while(flag&&cur<n){
            add(digits[cur],flag);
            ++cur;
        }
        if(flag==1){
            digits.push_back(1);
        }

        reverse(digits.begin(),digits.end());
        return digits;
    }
};
```

##　[69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

```c++
class Solution {
public:
    int mySqrt(int x) {
        long long l = 0;
        long long r = x;
        while(r>l){
            long long mid = (l+r+1)/2;
            if(mid*mid<=x) l = mid;  
            else r = mid-1;
        }
        return l;
    }
};
```

## [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

```c++
class Solution {
public:
    int climbStairs(int n) {
        //状压dp
        if(n==1) return 1;
        if(n==2) return 2;
        int prepre = 1;
        int pre = 2;
        for(int i=3;i<=n;++i){
            int tmp = pre + prepre;
            prepre = pre;
            pre = tmp;
        }
        return pre;
    }
};
```

## [73. 矩阵置零](https://leetcode-cn.com/problems/set-matrix-zeroes/)

```c++
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        //利用第一列的解法好好品一下
        int row0 = 0;
        int col0 = 0;
        int r = matrix.size();
        int c = matrix[0].size();

        for(int i=0;i<r;++i){
            if(matrix[i][0]==0) col0 = 1;
        }

        for(int i=1;i<r;++i){
            for(int j=1;j<c;++j){
                if(matrix[i][j]==0){
                    matrix[0][j]=0;
                    matrix[i][0]=0;
                }
            }
        }

        for(int i=r-1;i>=0;++i){
            for(int j=1;j<c;++j){
                if(matrix[0][j]==0||matrix[i][0]==0){
                    matrix[i][j] = 0;
                }
            }
        }

        if(col0){
            for(int i=0;i<r;++i) matrix[i][0] = 0;
        }
    }
};
```

## [75. 颜色分类](https://leetcode-cn.com/problems/sort-colors/)

```c++
class Solution {
public:
    void quickSort(vector<int>& nums,int l,int r){
        if(l>=r) return;
        int s = l;
        int e = r;
        int piv = nums[l];
        while(s<e){
            while(s<e&&nums[e]>=piv) e--;
            while(s<e&&nums[s]<=piv) s++;
            if(s<e) swap(nums[s],nums[e]);
        }
        nums[l] = nums[s];
        nums[s] = piv;
        quickSort(nums,l,s-1);
        quickSort(nums,s+1,r);
        
    }
    void sortColors(vector<int>& nums) {
        quickSort(nums,0,nums.size()-1);
    }
};
```

## [76. 最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)

```c++
class Solution {
public:
    string minWindow(string s, string t) {
        int n  = s.size();
        int nt = t.size();
        if(n<nt) return "";
        //滑动窗口+hash
        unordered_map<char,int> mp;
        for(auto ch:t){
            mp[ch]++;
        }

        int cnt = nt;//还需要匹配的个数
        //滑动窗口 
        int l  =0;
        int r = 0;

        int L = 0;
        int R = INT_MAX;//作为无匹配的区分边界

        //滑动
        while(r<n){
            if(r<n&&mp[s[r]]>0) --cnt;
            mp[s[r]]--;
            ++r;//影响了最后的R-L不需要加1
            while(cnt==0&&l<=r){
                if(r-l<R-L){
                    L = l;
                    R = r;
                }
                if(mp.find(s[l])!=mp.end()&&mp[s[l]]==0) ++cnt;
                if(mp.count(s[l])) mp[s[l]]++;
                ++l;
            }
        }
        return R==INT_MAX?"":s.substr(L,R-L);
    }
};
```

## [78. 子集](https://leetcode-cn.com/problems/subsets/)

```c++
class Solution {
public: 
    vector<vector<int>> ans;
    void dfs(vector<int>& nums,int cur,vector<int>& now){
        if(cur>nums.size()) return;
        ans.push_back(now);
        for(int i=cur;i<nums.size();++i){
            now.push_back(nums[i]);
            dfs(nums,i+1,now);
            now.pop_back();
        }
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        //dfs
        vector<int> tmp;
        dfs(nums,0,tmp);
        return ans;
    }
};
```

## [79. 单词搜索](https://leetcode-cn.com/problems/word-search/)

```c++
class Solution {
public:
    bool bfs(vector<vector<char>>& board, string word,int cur,int i,int j,vector<vector<int>>& mark){
        if(cur==word.size()) return true;
        if(i<0||i>=board.size()) return false;
        if(j<0||j>=board[0].size()) return false;
        

        if(board[i][j]!=word[cur]) return false;
        if(mark[i][j]) return false;

        mark[i][j] =1;
        bool res = bfs(board,word,cur+1,i-1,j,mark)||
            bfs(board,word,cur+1,i+1,j,mark)||
            bfs(board,word,cur+1,i,j-1,mark)||
            bfs(board,word,cur+1,i,j+1,mark);
        mark[i][j] = 0;
        return res;
    }
    bool exist(vector<vector<char>>& board, string word) {
        //bfs四个方向搜 不允许重复使用
        int n  = board.size();
        int m  = board[0].size();
        vector<vector<int>> mark(n,vector<int>(m,0));
        for(int i = 0;i<n;++i){
            for(int j =0;j<m;++j){
                if(bfs(board,word,0,i,j,mark)) return true;
            }
        }
        return false;
    }
};
```

## [84. 柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        //单调栈 维护一个单调递增的栈 有元素出栈时说明那些矩形无法向左和右延申了
        int ans = 0;
        stack<pair<int,int>> st;
        heights.push_back(0);//哨兵
        int n = heights.size();
        for(int i=0;i<n;++i){
            if(st.empty()) st.push(make_pair(heights[i],i));
            else{
                while(!st.empty()&&st.top().first>heights[i]){
                    auto [top,idx] = st.top();
                    if(heights[i]>=top) st.push(make_pair(heights[i],i)); 
                    else{
                        //说明有元素要出栈了
                        st.pop();//出栈
                        int pre = -1;//之前的下标
                        if(!st.empty()) pre = st.top().second; 
                        int tmp = top*(i-pre-1);
                        ans = max(tmp,ans);
                    }
                }
                st.push(make_pair(heights[i],i));
            }
        }
        return ans;
    }
};
```



## [88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)

```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        //逆序 从后往前
        int cur1 = m-1;
        int cur2 = n-1;
        while(cur1>=0&&cur2>=0){
            if(nums1[cur1]>nums2[cur2]){
                nums1[cur1+cur2+1] = nums1[cur1];
                --cur1;
            }
            else{
                nums1[cur1+cur2+1] = nums2[cur2];
                --cur2;
            }
        }
        if(cur1<0){
            while(cur2>=0){
                nums1[cur2]=nums2[cur2];
                --cur2;
            } 
        }
    }
};
```

## [91. 解码方法](https://leetcode-cn.com/problems/decode-ways/)

```c++
class Solution {
public:
    int numDecodings(string s) {
        //dp dp[i]依赖dp[i-1]和dp[i-2]
        //dp[i]表示到下标i位置的解码个数
        int n = s.size();
        vector<int> dp(n,0);
        if(s[0]<='0'||s[0]>'9') return 0;
        dp[0] =1;
        if(n==1) return dp[0];
        //n>=2
        if(s[1]>='1'&&s[1]<='9') dp[1]++;
        int two = (s[0]-'0')*10+s[1]-'0';
        if(two>=1&&two<=26) dp[1]++;


        for(int i=2;i<n;++i){
            int pre = dp[i-1];
            int prepre = dp[i-2];
            //判断dp[i-1]的有效性
            if(s[i]=='0') pre=0;
            //判断dp[i-2]的有效性
            int two = (s[i-1]-'0')*10+s[i]-'0';
            if(s[i-1]=='0'||two<1||two>26) prepre=0;
            dp[i] = pre+prepre;
        }
        return dp[n-1];
    } 
};
```

## [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

```c++
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
	//递归
    vector<int> ans;
    void dfs(TreeNode* root){
        if(!root) return;
        dfs(root->left);
        ans.push_back(root->val);
        dfs(root->right);
    }
    vector<int> inorderTraversal(TreeNode* root) {
        dfs(root);
        return ans;
    }
};


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
        //非递归
        if(!root) return {};
        vector<int> ans;
        stack<TreeNode*> st;
        TreeNode* cur  = root;
        while(!st.empty()||cur){
            while(cur){
                st.push(cur);
                cur = cur->left;
            }
            cur = st.top();
            st.pop();
            ans.push_back(cur->val);
            cur = cur->right;
        }
        return ans;
    }
};
```

## [98. 验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

```c++
//1.
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
    bool dfs(TreeNode* root,long long lower,long long upper){
        //找到左树的最大值 和右树的最小值
        if(!root) return true;
        if(root->val<=lower||root->val>=upper) return false;
        return dfs(root->left,lower,root->val)&&dfs(root->right,root->val,upper);
    }
    bool isValidBST(TreeNode* root) {
        //递归
        return dfs(root,LONG_MIN,LONG_MAX);
    }
};
//2.
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
    long long pre = LONG_MIN;
    bool dfs(TreeNode* root){
        //找到左树的最大值 和右树的最小值
        if(!root) return true;
        if(!dfs(root->left)) return false;
        if(root->val<=pre) return false;
        pre = root->val;
        return dfs(root->right);
    }
    bool isValidBST(TreeNode* root) {
        //中序过程中找答案
        return dfs(root);
    }
};

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
    bool isValidBST(TreeNode* root) {
        //中序过程中找答案 非递归
        if(!root) return true;
        stack<TreeNode*> st;
        TreeNode* cur = root;
        long long pre = LONG_MIN;
        while(!st.empty()||cur){
            while(cur){
                st.push(cur);
                cur = cur->left;
            }
            cur = st.top();
            st.pop();
            if(cur->val<=pre) return false;
            pre = cur->val;
            cur = cur->right;
        }
        return true;
    }
};
```

## [101. 对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

```c++
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
    bool judge(TreeNode* left,TreeNode* right){
        if(!left&&!right) return true;
        if(!right&&left) return false;
        if(right&&!left) return false;
        if(right->val!=left->val) return false; 
        return judge(left->right,right->left)&&judge(left->left,right->right);
    }
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        return judge(root->left,root->right);
    }
};
```

## [102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

```c++
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
    vector<vector<int>> levelOrder(TreeNode* root) {
        if(!root) return {};
        queue<TreeNode*> q;
        vector<vector<int>> ans;
        q.push(root);
        while(!q.empty()){
            int n = q.size();
            vector<int> tmp;
            for(int i =0;i<n;++i){
                TreeNode* cur=q.front();
                q.pop();
                tmp.push_back(cur->val);
                if(cur->left) q.push(cur->left);
                if(cur->right) q.push(cur->right);
            }
            ans.push_back(tmp);
        }
        return ans;
    }
};
```

## [103. 二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

```c++
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
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        if(!root) return {};
        vector<vector<int>> ans;
        deque<TreeNode*> q;
        q.emplace_back(root);
        int flag = 0;//标注顺序还是逆序
        while(!q.empty()){
            vector<int> tmp;
            int n = q.size();
            for(int i=0;i<n;++i){
                TreeNode* cur = q.front();q.pop_front();
                tmp.emplace_back(cur->val);
                if(cur->left) q.emplace_back(cur->left);
                if(cur->right) q.emplace_back(cur->right);
            }
            if(flag) reverse(tmp.begin(),tmp.end());
            ans.emplace_back(tmp);
            flag^=1;
        }
        return ans;
    }
};

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
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        if(!root) return {};
        vector<vector<int>> ans;
        queue<TreeNode*> q;
        q.emplace(root);
        int flag = 0;//标注顺序还是逆序
        while(!q.empty()){
            deque<int> tmp;
            int n = q.size();
            for(int i=0;i<n;++i){
                TreeNode* cur = q.front();q.pop();
                if(!flag) tmp.emplace_back(cur->val);
                else tmp.emplace_front(cur->val);
                if(cur->left) q.emplace(cur->left);
                if(cur->right) q.emplace(cur->right);
            }
            ans.emplace_back(vector<int>{tmp.begin(),tmp.end()});
            flag^=1;
        }
        return ans;
    }
};
```

## [104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```c++
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
    int depth(TreeNode* cur,int d){
        if(!cur) return d-1;
        int ld= depth(cur->left,d+1);
        int rd= depth(cur->right,d+1);
        return max(ld,rd);
        }
    int maxDepth(TreeNode* root) {
        return depth(root,1);
    }
};
```

## [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

```c++
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
    unordered_map<int,int> mp;//优化中序查询

    TreeNode* build(vector<int>& preorder,vector<int>& inorder,int ps,int pe,int is,int ie){
        //递归 按序号进行切分
        //出口
        if(ps>pe||is>ie) return nullptr;
        //1.前序的开头是根
        int n = preorder.size();//总长度
        int rval = preorder[ps];
        TreeNode* root = new TreeNode(rval);
        int iidx = mp[rval];
        int lpLen = iidx-is;//左部分的长度

        root->left  = build(preorder,inorder,ps+1,ps+lpLen,is,iidx-1);
        root->right = build(preorder,inorder,ps+lpLen+1,pe,iidx+1,ie);
        return root;
    }
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        for(int i=0;i<n;++i){
            mp[inorder[i]] = i;
        }
        return build(preorder,inorder,0,n-1,0,n-1);
    }
};
```



## [108. 将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

```c++
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
    TreeNode* build(vector<int>& nums,int l,int r){
        if(r<l) return nullptr;
        int mid = (l+r)>>1;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = build(nums,l,mid-1);
        root->right = build(nums,mid+1,r);
        return root;
    }
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        //找到中间数作为根，递归
        int n = nums.size();
        return build(nums,0,n-1);
    }
};
```

## [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

```c++
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    void dfs1(Node* root){
        if(!root) return;
        if(root->left&&root->right){
            root->left->next=root->right;//只处理自己的两个子树
            root->right->next = nullptr;//处理每行最右边的
        }
        dfs1(root->left);
        dfs1(root->right);
    }

    void dfs2(Node* l,Node* r){
        //由于是完美二叉树 要么都存在要么都不存在
        if(!l||!r) return;
        // cout<<l->val<<endl;
        // cout<<r->val<<endl;
        if(l->right&&r->left){
            (l->right)->next = r->left;//跨父的处理
        }
        dfs2(l->left,l->right);
        dfs2(l->right,r->left);//这里极容易错
        dfs2(r->left,r->right);
    }



    Node* connect(Node* root) {
        //想法1 层次遍历完事 空间 O(n)
        //想法2 双递归 一个处理自己的子树 一个处理跨父子树
        if(!root) return root;
        //先处理根
        root->next = nullptr;
        dfs1(root);
        dfs2(root->left,root->right);
        return root;  
    }
};


/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) {
        //想法1 层次遍历完事 空间 O(n)
        //想法2 双递归 一个处理自己的子树 一个处理跨父子树
        //想法3 利用连接好的next指针 在n-1层完成对n层的连接 跳树的体会
        if(!root) return root;
        //先处理根
        root->next = nullptr;
        Node* leftmost = root;
        while(leftmost){
            Node* head  = leftmost;
            while(head){
                if(!head->left||!head->right) return root;
                //同父
                head->left->next = head->right;
                //异父
                if(head->next){   //要有另一个父才能走这个分支不然报错
                head->right->next = head->next->left;
                }
                head = head->next;
            }
            leftmost = leftmost->left;
        }
        return root;  
    }
};
```

## [118. 杨辉三角](https://leetcode-cn.com/problems/pascals-triangle/)

```c++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ans;
        for(int i=1;i<=numRows;++i){
            vector<int> row(i,1);
            if(i==1){
                ans.emplace_back(row);
                continue;
            }
            else{
                for(int j=0;j<i;++j){
                    int pre = 0;
                    int now = 0;
                    if(j-1>=0) pre = ans[i-2][j-1];
                    if(j<ans[i-2].size()) now = ans[i-2][j];
                    row[j]  = pre+now;
                }
                ans.emplace_back(row);
            }
        }
        return ans;
    }
};
```

## [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        //记录i之前的最小值
        int minP = INT_MAX;
        int ans = INT_MIN;
        for(auto p:prices){
            minP = min(minP,p);
            ans = max(ans,p-minP);
        }
        return ans;
    }
};
```

## [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        //找到所有上升区间的端点 由于上升区间是连续的 可以每次只看后面一个
        int ans = 0;
        int n = prices.size();
        for(int i =0;i<n-1;++i){
            if(prices[i+1]>prices[i]) ans += prices[i+1]-prices[i];
        }
        return ans;
    }
};
```

## [124. 二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

```c++
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
    int ans  = INT_MIN;//结果
    int dfs(TreeNode* root){
        if(!root) return 0;
        int l = dfs(root->left);
        int r = dfs(root->right);
        int val = root->val;
        ans = max({ans,l+r+val,l+val,r+val,val});//最大值有可能在两个分支中产生
        return max({l+val,r+val,val});//只能走一个分支
    }
    int maxPathSum(TreeNode* root) {
        //递归找左半和右半的最大路径和加起来
        dfs(root);
        return ans;
    }
};
```

## [125. 验证回文串](https://leetcode-cn.com/problems/valid-palindrome/)

```c++
class Solution {
public:
    bool judge(char c){
        if('0'<=c&&c<='9'||'a'<=c&&c<='z'||'A'<=c&&c<='Z') return true;
        return false;
    }
    char trans(char c){
        if('0'<=c&&c<='9'||'a'<=c&&c<='z') return c;
        if('A'<=c&&c<='Z') return c-'A'+'a';
        return c;
    }
    bool isPalindrome(string s) {
        //双指针 遇到非法跳过合法比较
        if(!s.size()) return true;
        int n = s.size();
        int l = 0;
        int r = n-1;

        while(l<r){
            while(l<r&&!judge(s[l])) ++l;
            while(l<r&&!judge(s[r])) --r;
            if(l<r&&trans(s[l])!=trans(s[r])) return false;
            ++l;
            --r;
        }
        return true;
    }
};
```

## [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        //哈希表
        unordered_set<int> s;
        int ans = 0;//处理空数组

        for(auto n:nums){
            s.insert(n);
        }
        for(auto n:s){
            if(s.count(n-1)) continue;//之前已经被查找过了
            int now = 1;
            int cur = n;
            while(s.count(cur+1)){
                ++now;
                ++cur;
            }
            ans = max(ans,now);
        }
        return ans;

    }
};
```

## [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

```c++
class Solution {
public:
    void bfs(vector<vector<char>>& tmp,vector<vector<int>>& mark,int i,int j,char to){
        int n = tmp.size();
        int m = tmp[0].size();
        if(i<0||i>=n||j<0||j>=m) return;//递归出口
        if(mark[i][j]) return;//遍历过了
        if(tmp[i][j]!='O') return;//不是O也出
        mark[i][j]=1;
        tmp[i][j] = to;
        bfs(tmp,mark,i+1,j,to);
        bfs(tmp,mark,i-1,j,to);
        bfs(tmp,mark,i,j+1,to);
        bfs(tmp,mark,i,j-1,to);
    }
    void solve(vector<vector<char>>& board) {
        //关键是处理边界上的 可以扩边
        int m = board.size();
        int n = board[0].size();
        vector<vector<char>> tmp(m+2,vector<char>(n+2,'O'));
        vector<vector<int>> mark(m+2,vector<int>(n+2,0));
        for(int i=0;i<m;++i){
            for(int j=0;j<n;++j){
                tmp[i+1][j+1] = board[i][j];
            }
        }
        //bfs 处理tmp
        for(int i=0;i<m+2;++i){
            for(int j=0;j<n+2;++j){
                if(i==0&&j==0) bfs(tmp,mark,0,0,'#');
                else bfs(tmp,mark,i,j,'X');
            }
        }

        for(int i=1;i<m+1;++i){
            for(int j=1;j<n+1;++j){
                if(tmp[i][j]=='#') board[i-1][j-1]='O';
                else board[i-1][j-1]=tmp[i][j];
            }
        }
    }
};
```

## [131. 分割回文串](https://leetcode-cn.com/problems/palindrome-partitioning/)

```c++
class Solution {
public:
    vector<vector<string>> ans;
    vector<string> tmp;

    void dfs(string s,int cur,vector<vector<int>> dp){
        int n = s.size();
        if(cur == n){
            ans.push_back(tmp);
            return;
        }
        for(int j=cur;j<n;++j){
            if(dp[cur][j]){
                tmp.push_back(s.substr(cur,j-cur+1));
                dfs(s,j+1,dp);
                tmp.pop_back();
            }
        }
    }

    vector<vector<string>> partition(string s) {
        //找所有情况显然是回溯
        //但是枚举下一个回文串的位置需要双指针 会产生重复计算
        //dp 预处理出所有位置的回文串情况
        int n = s.size();
        vector<vector<int>> dp(n,vector<int>(n,true));

        //预处理
        for(int i=n-1;i>=0;--i){
            for(int j=i+1;j<n;++j){
                dp[i][j]=(s[i]==s[j])&&dp[i+1][j-1];
            }
        }
        dfs(s,0,dp);
        return ans;
    }
};
```

## [134. 加油站](https://leetcode-cn.com/problems/gas-station/)

```c++
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
        //如果从x到不了y+1,说明x-y中的任何一点都到不了y+1
        //那么直接从y+1开始下轮查找
        int n = gas.size();
        int sum = 0;//现在的油量
        int num = 0;//现在跑过的站点数
        int cur = -1;

        for(int i=0;i<n;++i){
            if(sum+gas[i]-cost[i]<0) continue;
            else {
                for(int j=0;j<n;j++){
                if(sum+gas[(i+j)%n]-cost[(i+j)%n]<0){
                    i = i+j;
                    sum=0;
                    break;
                }
                else sum=sum+gas[(i+j)%n]-cost[(i+j)%n];
                if(j==n-1){
                    // cout<<i<<endl;
                    cur = i;
                }
            }
            }
        }
        return cur;
    }
};
```



## [136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int i = 0;
        for(auto n:nums){
            i^=n;
        }
        return i;
    }
};
```

## [138. 复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

```c++
在公司电脑上
```

## [139. 单词拆分](https://leetcode-cn.com/problems/word-break/)

```c++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> sm;
        for(auto w:wordDict){
            sm.insert(w);
        }
        
        int n = s.size();
        vector<int> dp(n+1,0);
        dp[0] = true;
        for(int i=0;i<=n;++i){
            for(int j=0;j<i;++j){
                if(dp[j]&&sm.find(s.substr(j,i-j))!=sm.end()){
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[n];

    }
};
```



## [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

```c++
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
        if(!head) return false;
        //快慢指针
        ListNode* s = head;
        ListNode* f = head->next;
        while(f&&f->next){
            if(s==f) return true;
            s = s->next;
            f = f->next->next;
        }
        return false;
    }
};
```

