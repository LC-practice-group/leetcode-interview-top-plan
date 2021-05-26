# Doddddz 

## 001. 两数之和

```go
func twoSum(q []int, k int) []int {
    m := make(map[int]int)

    for idx, i := range q {
        mul := k - i
        if _, ex := m[mul]; ex {
            return []int{ idx, m[mul] }
        }
        m[i] = idx
    }

    return nil
}
```

## 002. 两数相加

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
    dummy := &ListNode {}
    t := dummy

    k := 0
    for l1 != nil || l2 != nil || k != 0 {
        if l1 != nil {
            k += l1.Val
            l1 = l1.Next
        }
        if l2 != nil {
            k += l2.Val
            l2 = l2.Next
        }
        t.Next = &ListNode {
            Val: k % 10, 
        }
        t = t.Next
        k /= 10
    }

    return dummy.Next

}
```

## 003. 无重复字符的最长子串

```go
func lengthOfLongestSubstring(s string) int {
    m := [130]int{}

    max := 0

    for i, j := 0, 0; i < len(s); i++ {
        m[s[i]]++

        for j < i && m[s[i]] > 1 {
            m[s[j]]--
            j++
        }
        if i - j + 1 > max {
            max = i - j + 1
        }
    }

    return max
}
```

## 004. 寻找两个正序数组的中位数

```go

```

## 005. 最长回文子串

```go
func longestPalindrome(s string) string {
    ans := ""

    for i := 0; i < len(s); i++ {
        l, r := i, i + 1
        for l > -1 && r < len(s) && s[l] == s[r] { l--; r++ }
        if r - l - 1 > len(ans) { ans = s[l + 1: r] }
        l, r = i - 1, i + 1
        for l > -1 && r < len(s) && s[l] == s[r] { l--; r++ }
        if r - l - 1 > len(ans) { ans = s[l + 1: r] }
    }

    return ans
}
```



## 006. Z 字形变换

```go

```



## 007. 整数反转

```go
func reverse(x int) int {
	neg := x < 0;
	que := []int{}

	if x < 0 {
		x = -x
	}

	for x != 0 {
		que = append(que, x % 10)
		x /= 10
	}

	for _, i := range que {
		if !neg && x > (math.MaxInt32 - i) / 10 {
			return 0
		} else if neg && -x < (math.MinInt32 + i) / 10 {
			return 0
		} else if -x * 10 - i == math.MinInt32 {
			return math.MinInt32
		}

		x = x * 10 + i
	}
	if neg {
		return -x
	} else {
		return x
	}
}
```

## 008. 字符串转换整数 (atoi)

```go
func myAtoi(s string) int {
    i, ans := 0, 0
    neg := false
    s += "*"

    for s[i] == ' ' { i++ }
    if s[i] == '+' {
        i++
    } else if s[i] == '-' {
        neg = true
        i++
    }
    for s[i] >= '0' && s[i] <= '9' {
        t := int(s[i] - '0')
        if !neg && ans > (math.MaxInt32 - t) / 10 {
            return math.MaxInt32
        } else if neg && -ans < (math.MinInt32 + t) / 10 {
            return math.MinInt32
        } else if -ans * 10 - t == math.MinInt32 {
            return math.MinInt32
        }
        ans = ans * 10 + t
        i++
    }

    if neg {
        return -ans
    } else {
        return ans
    }
}
```

## 009. 回文数

```go
func isPalindrome(x int) bool {
    if x < 0 {
        return false
    }

    q := []rune(strconv.Itoa(x))

    for i := 0; i < len(q); i++ {
        j, k := i, i
        for j > -1 && k < len(q) && q[j] == q[k] {
            j--; k++;
        }
        if j == -1 && k == len(q) {
            return true
        }
        j, k = i - 1, i
        for j > -1 && k < len(q) && q[j] == q[k] {
            j--; k++;
        }
        if j == -1 && k == len(q) {
            return true
        }
    }

    return false
}
```

## 010. 正则表达式匹配

```go

```

## 011. 盛最多水的容器

```go
func maxArea(q []int) int {
    i, j, max := 0, len(q) - 1, 0

    for i < j {
        if q[i] > q[j] {
            if (j - i) * q[j] > max {
                max = (j - i) * q[j]
            }
            j--
        } else {
            if (j - i) * q[i] > max {
                max = (j - i) * q[i]
            }
            i++
        }
    }

    return max
}
```

## 014. 最长公共前缀

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        int min = 0x3f3f3f3f;
        for (var s : strs) min = Math.min(min, s.length());

        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < min; i++) {
            char c = strs[0].charAt(i);
            for (var s : strs) {
                if (s.charAt(i) != c) return sb.toString();
            }
            sb.append(c);
        }

        return sb.toString();
    }
}
```

## 015. 三数之和

```java
class Solution {
    private final List<List<Integer>> list = new ArrayList<>();

    public List<List<Integer>> threeSum(int[] q) {
        Arrays.sort(q);
        
        for (int i = 0; i < q.length; i++) {
            if (i != 0 && q[i] == q[i - 1]) continue;
            for (int j = i + 1, k = q.length - 1; j < k; j++) {
                if (j != i + 1 && q[j] == q[j - 1]) continue;
                while (k > j + 1 && q[i] + q[j] + q[k - 1] >= 0) k--;
                if (q[i] + q[j] + q[k] == 0) {
                    List<Integer> tmp = new ArrayList<>();
                    tmp.add(q[i]); 
                    tmp.add(q[j]); 
                    tmp.add(q[k]);
                    list.add(tmp);
                }
            }
        }

        return list;
    }
}
```

## 017. 电话号码的字母组合

```java
class Solution {
    private final List<String> list = new ArrayList<>();

    public List<String> letterCombinations(String s) {
        if (s.length() == 0) return list;

        char[] c = s.toCharArray();
        dfs(0, c, new char[s.length()]);

        return list;
    }

    private void dfs(int u, char[] c, char[] s) {
        if (u == c.length) {
            list.add(new String(s));
            return;
        }

        int len = 0;
        char start;
        if (c[u] >= '2' && c[u] <= '6') {
            len = 3; start = (char) ('a' + (c[u] - '2') * 3);
        } else if (c[u] == '7') {
            len = 4; start = 'p';
        } else if (c[u] == '8') {
            len = 3; start = 't';
        } else {
            len = 4; start = 'w';
        }

        for (int i = 0; i < len; i++) {
            s[u] = (char) (start + i);
            dfs(u + 1, c, s); 
        }
    }
}
```

## 019. 删除链表的倒数第 N 个结点

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    private int idx = 1;

    public ListNode removeNthFromEnd(ListNode head, int n) {
        idx = n;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        dfs(dummy);

        return dummy.next;
    }

    private ListNode dfs(ListNode node) {
        if (node == null) return node;
        ListNode pos = dfs(node.next);
        idx--;
        if (idx == 0) return pos;
        node.next = pos;
        return node;
    }
}
```

## 020. 有效的括号

```java
class Solution {
    public boolean isValid(String s) {
        char[] c = new char[s.length()];
        int idx = -1;

        for (var i : s.toCharArray()) {
            if (i == '[' || i == '(' || i == '{') c[++idx] = i;
            else if (idx > -1 && c[idx] == get(i)) idx--;
            else return false;
        } 

        return idx == -1;
    }

    public char get(char c) {
        switch(c) {
            case ']' -> { return '['; }
            case '}' -> { return '{'; }
            case ')' -> { return '('; }
        }
        return 's';
    }
}
```

## 021. 合并两个有序链表

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1), t = dummy;
        dummy.next = l1;

        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                t = t.next = l1;
                l1 = l1.next;
            } else {
                t = t.next = l2;
                l2 = l2.next;
            }
        }

        if (l1 != null) t.next = l1;
        if (l2 != null) t.next = l2;

        return dummy.next;
    }
}
```

## 022. 括号生成

```java
class Solution {
    private List<String> list = new ArrayList<>();

    public List<String> generateParenthesis(int n) {
        dfs(0, 0, n, "");
        return list;
    }

    private void dfs(int l, int r, int u, String s) {
        if (l == u && r == u) {
            list.add(s);
        } else {
            if (l < u && l >= r) dfs(l + 1, r, u, s + "(");
            if (r < u) dfs(l, r + 1, u, s + ")");
        }
    }
}
```

## 023. 合并K个升序链表

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    private PriorityQueue<ListNode> que = new PriorityQueue<>(Comparator.comparing((ListNode node) -> node.val));

    public ListNode mergeKLists(ListNode[] lists) {
        ListNode dummy = new ListNode(-1), t = dummy;

        for (var i : lists) {
            if (i != null) que.add(i);
        }
        while (!que.isEmpty()) {
            ListNode tmp = que.poll();
            t = t.next = tmp;
            if (tmp.next != null) que.add(tmp.next);
        }

        return dummy.next;
    }
}
```

## 026. 删除有序数组中的重复项

```java
class Solution {
    public int removeDuplicates(int[] q) {
        int idx = 0;
        
        for (int i = 0; i < q.length; i++) {
            if (i != 0 && q[i] == q[i - 1]) continue;
            q[idx++] = q[i];
        }

        return idx;
    }
}
```

## 033. 搜索旋转排序数组

```java
class Solution {
    public int search(int[] q, int k) {
        int l = 0, r = q.length - 1;
        while (l < r) {
            int mid = l + r + 1>> 1;
            if (q[mid] >= q[0]) l = mid;
            else r = mid - 1;
        }

        if (q[0] > k) {
            l = l + 1; r = q.length - 1;
        } else {
            l = 0;
        }

        while (l < r) {
            int mid = l + r >> 1;
            if (q[mid] >= k) r = mid;
            else l = mid + 1;
        }

        return l >-1 && l < q.length && q[l] == k ? l : -1;
    }
}
```

## 034. 在排序数组中查找元素的第一个和最后一个位置

```java
class Solution {
    public int[] searchRange(int[] q, int k) {
        if (q.length == 0) return new int[]{-1, -1};
        int l = 0, r = q.length - 1;
        while (l < r) {
            int mid = l + r >> 1;
            if (q[mid] >= k) r = mid;
            else l = mid + 1;
        }
        if (q[l] != k) return new int[]{ -1, -1 };
        int t = l;
        l = 0; r = q.length - 1;
        while (l < r) {
            int mid = l + r + 1 >> 1;
            if (q[mid] <= k) l = mid;
            else r = mid - 1;
        }

        return new int[]{ t, l };
    }
}
```

## 036. 有效的数独

```java

```

## 038. 外观数列

```java
class Solution {
    private StringBuilder pre = new StringBuilder("1"), net = new StringBuilder();

    public String countAndSay(int n) {
        for (int i = 0; i < n - 1; i++) {
            char[] c = pre.toString().toCharArray();
            for (int j = 0, k = 0; j < c.length; j = k) {
                while (k < c.length && c[j] == c[k]) k++;
                net.append(k - j).append(c[j]);
            }
            pre = net;
            net = new StringBuilder();
        }

        return pre.toString();
    }
}
```

