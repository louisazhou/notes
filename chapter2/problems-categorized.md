# Problems Categorized

## 数组和矩阵

**把数组中的 0 移到末尾**

[283. Move Zeroes \(Easy\)](https://leetcode.com/problems/move-zeroes/description/)

```text
For example, given nums = [0, 1, 0, 3, 12], after calling your function, nums should be [1, 3, 12, 0, 0].
```

```text
public void moveZeroes(int[] nums) {
    int idx = 0;
    for (int num : nums) {
        if (num != 0) {
            nums[idx++] = num;
        }
    }
    while (idx < nums.length) {
        nums[idx++] = 0;
    }
}
```

**改变矩阵维度**

[566. Reshape the Matrix \(Easy\)](https://leetcode.com/problems/reshape-the-matrix/description/)

```text
Input:
nums =
[[1,2],
 [3,4]]
r = 1, c = 4

Output:
[[1,2,3,4]]

Explanation:
The row-traversing of nums is [1,2,3,4]. The new reshaped matrix is a 1 * 4 matrix, fill it row by row by using the previous list.
```

```text
public int[][] matrixReshape(int[][] nums, int r, int c) {
    int m = nums.length, n = nums[0].length;
    if (m * n != r * c) {
        return nums;
    }
    int[][] reshapedNums = new int[r][c];
    int index = 0;
    for (int i = 0; i < r; i++) {
        for (int j = 0; j < c; j++) {
            reshapedNums[i][j] = nums[index / n][index % n];
            index++;
        }
    }
    return reshapedNums;
}
```

**找出数组中最长的连续 1**

[485. Max Consecutive Ones \(Easy\)](https://leetcode.com/problems/max-consecutive-ones/description/)

```text
public int findMaxConsecutiveOnes(int[] nums) {
    int max = 0, cur = 0;
    for (int x : nums) {
        cur = x == 0 ? 0 : cur + 1;
        max = Math.max(max, cur);
    }
    return max;
}
```

**一个数组元素在 \[1, n\] 之间，其中一个数被替换为另一个数，找出重复的数和丢失的数**

[645. Set Mismatch \(Easy\)](https://leetcode.com/problems/set-mismatch/description/)

```text
Input: nums = [1,2,2,4]
Output: [2,3]
```

```text
Input: nums = [1,2,2,4]
Output: [2,3]
```

最直接的方法是先对数组进行排序，这种方法时间复杂度为 O\(NlogN\)。本题可以以 O\(N\) 的时间复杂度、O\(1\) 空间复杂度来求解。

主要思想是通过交换数组元素，使得数组上的元素在正确的位置上。遍历数组，如果第 i 位上的元素不是 i + 1，那么一直交换第 i 位和 nums\[i\] - 1 位置上的元素。

```text
public int[] findErrorNums(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        while (nums[i] != i + 1 && nums[nums[i] - 1] != nums[i]) {
            swap(nums, i, nums[i] - 1);
        }
    }
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != i + 1) {
            return new int[]{nums[i], i + 1};
        }
    }
    return null;
}

private void swap(int[] nums, int i, int j) {
    int tmp = nums[i];
    nums[i] = nums[j];
    nums[j] = tmp;
}
```

类似题目：

* [448. Find All Numbers Disappeared in an Array \(Easy\)](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/description/)，寻找所有丢失的元素
* [442. Find All Duplicates in an Array \(Medium\)](https://leetcode.com/problems/find-all-duplicates-in-an-array/description/)，寻找所有重复的元素。

**找出数组中重复的数，数组值在 \[1, n\] 之间**

[287. Find the Duplicate Number \(Medium\)](https://leetcode.com/problems/find-the-duplicate-number/description/)

要求不能修改数组，也不能使用额外的空间。

二分查找解法：

```text
public int findDuplicate(int[] nums) {
     int l = 1, h = nums.length - 1;
     while (l <= h) {
         int mid = l + (h - l) / 2;
         int cnt = 0;
         for (int i = 0; i < nums.length; i++) {
             if (nums[i] <= mid) cnt++;
         }
         if (cnt > mid) h = mid - 1;
         else l = mid + 1;
     }
     return l;
}
```

双指针解法，类似于有环链表中找出环的入口：

```text
public int findDuplicate(int[] nums) {
    int slow = nums[0], fast = nums[nums[0]];
    while (slow != fast) {
        slow = nums[slow];
        fast = nums[nums[fast]];
    }
    fast = 0;
    while (slow != fast) {
        slow = nums[slow];
        fast = nums[fast];
    }
    return slow;
}
```

**有序矩阵查找**

[240. Search a 2D Matrix II \(Medium\)](https://leetcode.com/problems/search-a-2d-matrix-ii/description/)

```text
[
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]
]
```

```text
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return false;
    int m = matrix.length, n = matrix[0].length;
    int row = 0, col = n - 1;
    while (row < m && col >= 0) {
        if (target == matrix[row][col]) return true;
        else if (target < matrix[row][col]) col--;
        else row++;
    }
    return false;
}
```

**有序矩阵的 Kth Element**

[378. Kth Smallest Element in a Sorted Matrix \(\(Medium\)\)](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/description/)

```text
matrix = [
  [ 1,  5,  9],
  [10, 11, 13],
  [12, 13, 15]
],
k = 8,

return 13.
```

解题参考：[Share my thoughts and Clean Java Code](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/discuss/85173)

二分查找解法：

```text
public int kthSmallest(int[][] matrix, int k) {
    int m = matrix.length, n = matrix[0].length;
    int lo = matrix[0][0], hi = matrix[m - 1][n - 1];
    while(lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        int cnt = 0;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n && matrix[i][j] <= mid; j++) {
                cnt++;
            }
        }
        if(cnt < k) lo = mid + 1;
        else hi = mid - 1;
    }
    return lo;
}
```

堆解法：

```text
public int kthSmallest(int[][] matrix, int k) {
    int m = matrix.length, n = matrix[0].length;
    PriorityQueue pq = new PriorityQueue();
    for(int j = 0; j < n; j++) pq.offer(new Tuple(0, j, matrix[0][j]));
    for(int i = 0; i < k - 1; i++) { // 小根堆，去掉 k - 1 个堆顶元素，此时堆顶元素就是第 k 的数
        Tuple t = pq.poll();
        if(t.x == m - 1) continue;
        pq.offer(new Tuple(t.x + 1, t.y, matrix[t.x + 1][t.y]));
    }
    return pq.poll().val;
}

class Tuple implements Comparable {
    int x, y, val;
    public Tuple(int x, int y, int val) {
        this.x = x; this.y = y; this.val = val;
    }

    @Override
    public int compareTo(Tuple that) {
        return this.val - that.val;
    }
}
```

**数组相邻差值的个数**

[667. Beautiful Arrangement II \(Medium\)](https://leetcode.com/problems/beautiful-arrangement-ii/description/)

```text
Input: n = 3, k = 2
Output: [1, 3, 2]
Explanation: The [1, 3, 2] has three different positive integers ranging from 1 to 3, and the [2, 1] has exactly 2 distinct integers: 1 and 2.
```

题目描述：数组元素为 1~n 的整数，要求构建数组，使得相邻元素的差值不相同的个数为 k。

让前 k+1 个元素构建出 k 个不相同的差值，序列为：1 k+1 2 k 3 k-1 ... k/2 k/2+1.

```text
public int[] constructArray(int n, int k) {
    int[] ret = new int[n];
    ret[0] = 1;
    for (int i = 1, interval = k; i <= k; i++, interval--) {
        ret[i] = i % 2 == 1 ? ret[i - 1] + interval : ret[i - 1] - interval;
    }
    for (int i = k + 1; i < n; i++) {
        ret[i] = i + 1;
    }
    return ret;
}
```

**数组的度**

[697. Degree of an Array \(Easy\)](https://leetcode.com/problems/degree-of-an-array/description/)

```text
Input: [1,2,2,3,1,4,2]
Output: 6
```

题目描述：数组的度定义为元素出现的最高频率，例如上面的数组度为 3。要求找到一个最小的子数组，这个子数组的度和原数组一样。

```text
public int findShortestSubArray(int[] nums) {
    Map numsCnt = new HashMap<>();
    Map numsLastIndex = new HashMap<>();
    Map numsFirstIndex = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int num = nums[i];
        numsCnt.put(num, numsCnt.getOrDefault(num, 0) + 1);
        numsLastIndex.put(num, i);
        if (!numsFirstIndex.containsKey(num)) {
            numsFirstIndex.put(num, i);
        }
    }
    int maxCnt = 0;
    for (int num : nums) {
        maxCnt = Math.max(maxCnt, numsCnt.get(num));
    }
    int ret = nums.length;
    for (int i = 0; i < nums.length; i++) {
        int num = nums[i];
        int cnt = numsCnt.get(num);
        if (cnt != maxCnt) continue;
        ret = Math.min(ret, numsLastIndex.get(num) - numsFirstIndex.get(num) + 1);
    }
    return ret;
}
```

**对角元素相等的矩阵**

[766. Toeplitz Matrix \(Easy\)](https://leetcode.com/problems/toeplitz-matrix/description/)

```text
1234
5123
9512

In the above grid, the diagonals are "[9]", "[5, 5]", "[1, 1, 1]", "[2, 2, 2]", "[3, 3]", "[4]", and in each diagonal all elements are the same, so the answer is True.
```

```text
public boolean isToeplitzMatrix(int[][] matrix) {
    for (int i = 0; i < matrix[0].length; i++) {
        if (!check(matrix, matrix[0][i], 0, i)) {
            return false;
        }
    }
    for (int i = 0; i < matrix.length; i++) {
        if (!check(matrix, matrix[i][0], i, 0)) {
            return false;
        }
    }
    return true;
}

private boolean check(int[][] matrix, int expectValue, int row, int col) {
    if (row >= matrix.length || col >= matrix[0].length) {
        return true;
    }
    if (matrix[row][col] != expectValue) {
        return false;
    }
    return check(matrix, expectValue, row + 1, col + 1);
}
```

**嵌套数组**

[565. Array Nesting \(Medium\)](https://leetcode.com/problems/array-nesting/description/)

```text
Input: A = [5,4,0,3,1,6,2]
Output: 4
Explanation:
A[0] = 5, A[1] = 4, A[2] = 0, A[3] = 3, A[4] = 1, A[5] = 6, A[6] = 2.

One of the longest S[K]:
S[0] = {A[0], A[5], A[6], A[2]} = {5, 6, 2, 0}
```

题目描述：S\[i\] 表示一个集合，集合的第一个元素是 A\[i\]，第二个元素是 A\[A\[i\]\]，如此嵌套下去。求最大的 S\[i\]。

```text
public int arrayNesting(int[] nums) {
    int max = 0;
    for (int i = 0; i < nums.length; i++) {
        int cnt = 0;
        for (int j = i; nums[j] != -1; ) {
            cnt++;
            int t = nums[j];
            nums[j] = -1; // 标记该位置已经被访问
            j = t;

        }
        max = Math.max(max, cnt);
    }
    return max;
}
```

**分隔数组**

[769. Max Chunks To Make Sorted \(Medium\)](https://leetcode.com/problems/max-chunks-to-make-sorted/description/)

```text
Input: arr = [1,0,2,3,4]
Output: 4
Explanation:
We can split into two chunks, such as [1, 0], [2, 3, 4].
However, splitting into [1, 0], [2], [3], [4] is the highest number of chunks possible.
```

题目描述：分隔数组，使得对每部分排序后数组就为有序。

```text
public int maxChunksToSorted(int[] arr) {
    if (arr == null) return 0;
    int ret = 0;
    int right = arr[0];
    for (int i = 0; i < arr.length; i++) {
        right = Math.max(right, arr[i]);
        if (right == i) ret++;
    }
    return ret;
}
```

## 字符串

**两个字符串包含的字符是否完全相同**

[242. Valid Anagram \(Easy\)](https://leetcode.com/problems/valid-anagram/description/)

```text
s = "anagram", t = "nagaram", return true.
s = "rat", t = "car", return false.
```

字符串只包含小写字符，总共有 26 个小写字符。可以用 HashMap 来映射字符与出现次数。因为键的范围很小，因此可以使用长度为 26 的整型数组对字符串出现的字符进行统计，然后比较两个字符串出现的字符数量是否相同。

```text
public boolean isAnagram(String s, String t) {
    int[] cnts = new int[26];
    for (char c : s.toCharArray()) {
        cnts[c - 'a']++;
    }
    for (char c : t.toCharArray()) {
        cnts[c - 'a']--;
    }
    for (int cnt : cnts) {
        if (cnt != 0) {
            return false;
        }
    }
    return true;
}
```

**计算一组字符集合可以组成的回文字符串的最大长度**

[409. Longest Palindrome \(Easy\)](https://leetcode.com/problems/longest-palindrome/description/)

```text
Input : "abccccdd"
Output : 7
Explanation : One longest palindrome that can be built is "dccaccd", whose length is 7.
```

使用长度为 256 的整型数组来统计每个字符出现的个数，每个字符有偶数个可以用来构成回文字符串。

因为回文字符串最中间的那个字符可以单独出现，所以如果有单独的字符就把它放到最中间。

```text
public int longestPalindrome(String s) {
    int[] cnts = new int[256];
    for (char c : s.toCharArray()) {
        cnts[c]++;
    }
    int palindrome = 0;
    for (int cnt : cnts) {
        palindrome += (cnt / 2) * 2;
    }
    if (palindrome < s.length()) {
        palindrome++;   // 这个条件下 s 中一定有单个未使用的字符存在，可以把这个字符放到回文的最中间
    }
    return palindrome;
}
```

**字符串同构**

[205. Isomorphic Strings \(Easy\)](https://leetcode.com/problems/isomorphic-strings/description/)

```text
Given "egg", "add", return true.
Given "foo", "bar", return false.
Given "paper", "title", return true.
```

记录一个字符上次出现的位置，如果两个字符串中的字符上次出现的位置一样，那么就属于同构。

```text
public boolean isIsomorphic(String s, String t) {
    int[] preIndexOfS = new int[256];
    int[] preIndexOfT = new int[256];
    for (int i = 0; i < s.length(); i++) {
        char sc = s.charAt(i), tc = t.charAt(i);
        if (preIndexOfS[sc] != preIndexOfT[tc]) {
            return false;
        }
        preIndexOfS[sc] = i + 1;
        preIndexOfT[tc] = i + 1;
    }
    return true;
}
```

**回文子字符串**

[647. Palindromic Substrings \(Medium\)](https://leetcode.com/problems/palindromic-substrings/description/)

```text
Input: "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
```

从字符串的某一位开始，尝试着去扩展子字符串。

```text
private int cnt = 0;

public int countSubstrings(String s) {
    for (int i = 0; i < s.length(); i++) {
        extendSubstrings(s, i, i);     // 奇数长度
        extendSubstrings(s, i, i + 1); // 偶数长度
    }
    return cnt;
}

private void extendSubstrings(String s, int start, int end) {
    while (start >= 0 && end < s.length() && s.charAt(start) == s.charAt(end)) {
        start--;
        end++;
        cnt++;
    }
}
```

**判断一个整数是否是回文数**

[9. Palindrome Number \(Easy\)](https://leetcode.com/problems/palindrome-number/description/)

要求不能使用额外空间，也就不能将整数转换为字符串进行判断。

将整数分成左右两部分，右边那部分需要转置，然后判断这两部分是否相等。

```text
public boolean isPalindrome(int x) {
    if (x == 0) {
        return true;
    }
    if (x < 0 || x % 10 == 0) {
        return false;
    }
    int right = 0;
    while (x > right) {
        right = right * 10 + x % 10;
        x /= 10;
    }
    return x == right || x == right / 10;
}
```

**统计二进制字符串中连续 1 和连续 0 数量相同的子字符串个数**

[696. Count Binary Substrings \(Easy\)](https://leetcode.com/problems/count-binary-substrings/description/)

```text
Input: "00110011"
Output: 6
Explanation: There are 6 substrings that have equal number of consecutive 1's and 0's: "0011", "01", "1100", "10", "0011", and "01".
```

```text
public int countBinarySubstrings(String s) {
    int preLen = 0, curLen = 1, count = 0;
    for (int i = 1; i < s.length(); i++) {
        if (s.charAt(i) == s.charAt(i - 1)) {
            curLen++;
        } else {
            preLen = curLen;
            curLen = 1;
        }

        if (preLen >= curLen) {
            count++;
        }
    }
    return count;
}
```

**字符串循环移位包含**

[编程之美：3.1](https://github.com/CyC2018/Interview-Notebook/blob/master/notes/Leetcode%20%E9%A2%98%E8%A7%A3.md#)

```text
s1 = AABCD, s2 = CDAA
Return : true
```

给定两个字符串 s1 和 s2，要求判定 s2 是否能够被 s1 做循环移位得到的字符串包含。

s1 进行循环移位的结果是 s1s1 的子字符串，因此只要判断 s2 是否是 s1s1 的子字符串即可。

**字符串循环移位**

[编程之美：2.17](https://github.com/CyC2018/Interview-Notebook/blob/master/notes/Leetcode%20%E9%A2%98%E8%A7%A3.md#)

```text
s = "abcd123" k = 3
Return "123abcd"
```

将字符串向右循环移动 k 位。

将 abcd123 中的 abcd 和 123 单独逆序，得到 dcba321，然后对整个字符串进行逆序，得到 123abcd。

**字符串中单词的翻转**

[程序员代码面试指南](https://github.com/CyC2018/Interview-Notebook/blob/master/notes/Leetcode%20%E9%A2%98%E8%A7%A3.md#)

```text
s = "I am a student"
return "student a am I"
```

将每个单词逆序，然后将整个字符串逆序。

## 栈和队列

## 哈希表

哈希表使用 O\(N\) 空间复杂度存储数据，从而能够以 O\(1\) 时间复杂度求解问题。

Java 中的 **HashSet** 用于存储一个集合，可以查找元素是否在集合中。

如果元素有穷，并且范围不大，那么可以用一个布尔数组来存储一个元素是否存在。例如对于只有小写字符的元素，就可以用一个长度为 26 的布尔数组来存储一个字符集合，使得空间复杂度降低为 O\(1\)。

Java 中的 **HashMap** 主要用于映射关系，从而把两个元素联系起来。

在对一个内容进行压缩或者其它转换时，利用 HashMap 可以把原始内容和转换后的内容联系起来。例如在一个简化 url 的系统中[Leetcdoe : 535. Encode and Decode TinyURL \(Medium\)](https://leetcode.com/problems/encode-and-decode-tinyurl/description/)，利用 HashMap 就可以存储精简后的 url 到原始 url 的映射，使得不仅可以显示简化的 url，也可以根据简化的 url 得到原始 url 从而定位到正确的资源。

HashMap 也可以用来对元素进行计数统计，此时键为元素，值为计数。和 HashSet 类似，如果元素有穷并且范围不大，可以用整型数组来进行统计。

**数组中的两个数和为给定值**

[1. Two Sum \(Easy\)](https://leetcode.com/problems/two-sum/description/)

可以先对数组进行排序，然后使用双指针方法或者二分查找方法。这样做的时间复杂度为 O\(NlogN\)，空间复杂度为 O\(1\)。

用 HashMap 存储数组元素和索引的映射，在访问到 nums\[i\] 时，判断 HashMap 中是否存在 target - nums\[i\]，如果存在说明 target - nums\[i\] 所在的索引和 i 就是要找的两个数。该方法的时间复杂度为 O\(N\)，空间复杂度为 O\(N\)，使用空间来换取时间。

```text
public int[] twoSum(int[] nums, int target) {
    HashMap indexForNum = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        if (indexForNum.containsKey(target - nums[i])) {
            return new int[]{indexForNum.get(target - nums[i]), i};
        } else {
            indexForNum.put(nums[i], i);
        }
    }
    return null;
}
```

**判断数组是否含有重复元素**

[217. Contains Duplicate \(Easy\)](https://leetcode.com/problems/contains-duplicate/description/)

```text
public boolean containsDuplicate(int[] nums) {
    Set set = new HashSet<>();
    for (int num : nums) {
        set.add(num);
    }
    return set.size() < nums.length;
}
```

**最长和谐序列**

[594. Longest Harmonious Subsequence \(Easy\)](https://leetcode.com/problems/longest-harmonious-subsequence/description/)

```text
Input: [1,3,2,2,5,2,3,7]
Output: 5
Explanation: The longest harmonious subsequence is [3,2,2,2,3].
```

和谐序列中最大数和最小数只差正好为 1，应该注意的是序列的元素不一定是数组的连续元素。

```text
public int findLHS(int[] nums) {
    Map countForNum = new HashMap<>();
    for (int num : nums) {
        countForNum.put(num, countForNum.getOrDefault(num, 0) + 1);
    }
    int longest = 0;
    for (int num : countForNum.keySet()) {
        if (countForNum.containsKey(num + 1)) {
            longest = Math.max(longest, countForNum.get(num + 1) + countForNum.get(num));
        }
    }
    return longest;
}
```

**最长连续序列**

[128. Longest Consecutive Sequence \(Hard\)](https://leetcode.com/problems/longest-consecutive-sequence/description/)

```text
Given [100, 4, 200, 1, 3, 2],
The longest consecutive elements sequence is [1, 2, 3, 4]. Return its length: 4.
```

要求以 O\(N\) 的时间复杂度求解。

```text
public int longestConsecutive(int[] nums) {
    Map countForNum = new HashMap<>();
    for (int num : nums) {
        countForNum.put(num, 1);
    }
    for (int num : nums) {
        forward(countForNum, num);
    }
    return maxCount(countForNum);
}

private int forward(Map countForNum, int num) {
    if (!countForNum.containsKey(num)) {
        return 0;
    }
    int cnt = countForNum.get(num);
    if (cnt > 1) {
        return cnt;
    }
    cnt = forward(countForNum, num + 1) + 1;
    countForNum.put(num, cnt);
    return cnt;
}

private int maxCount(Map countForNum) {
    int max = 0;
    for (int num : countForNum.keySet()) {
        max = Math.max(max, countForNum.get(num));
    }
    return max;
}
```

## 贪心算法

一般什么时候需要用到贪心，其实就是在题目推导比较难解，但是直观思维却比较简单。比如经典的排课问题，就是使用贪心，先进行排序，再进行选择，贪心算法也时常用来求近似解。

所以一般解法可以考虑为，先排序，再根据条件求结果。证明的过程是非常难的，所以我们一般不会讨论证明

贪心思想 贪心思想保证每次操作都是局部最优的，并且最后得到的结果是全局最优的。

> 455.分发饼干： 假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。对每个孩子 i ，都有一个胃口值 gi ，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 j ，都有一个尺寸 sj 。如果 sj &gt;= gi ，我们可以将这个饼干 j 分配给孩子 i ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。
>
> 注意：
>
> 你可以假设胃口值为正。 一个小朋友最多只能拥有一块饼干。
>
> 示例 1:
>
> 输入: \[1,2,3\], \[1,1\]
>
> 输出: 1
>
> 解释: 你有三个孩子和两块小饼干，3个孩子的胃口值分别是：1,2,3。 虽然你有两块小饼干，由于他们的尺寸都是1，你只能让胃口值是1的孩子满足。 所以你应该输出1。 示例 2:
>
> 输入: \[1,2\], \[1,2,3\]
>
> 输出: 2
>
> 解释: 你有两个孩子和三块小饼干，2个孩子的胃口值分别是1,2。 你拥有的饼干数量和尺寸都足以让所有孩子满足。 所以你应该输出2.

由于题目想让尽量多的孩子满足胃口值，所以应该先用量小的饼干满足胃口小的。这样得到的结果是最优的。

```text
public int findContentChildren(int[] g, int[] s) {
        int count = 0;
        Arrays.sort(g);
        Arrays.sort(s);
        int i = 0,j = 0;
        while (i < g.length && j < s.length) {
            if (g[i] <= s[j]) {
                i ++;
                j ++;
                count ++;
            }else {
                j ++;
            }
        }
        return count;
    }
复制代码
```

> 1. 无重叠区间：给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。
>
> 注意:
>
> 可以认为区间的终点总是大于它的起点。 区间 \[1,2\] 和 \[2,3\] 的边界相互“接触”，但没有相互重叠。 示例 1:
>
> 输入: \[ \[1,2\], \[2,3\], \[3,4\], \[1,3\] \]
>
> 输出: 1
>
> 解释: 移除 \[1,3\] 后，剩下的区间没有重叠。 示例 2:
>
> 输入: \[ \[1,2\], \[1,2\], \[1,2\] \]
>
> 输出: 2
>
> 解释: 你需要移除两个 \[1,2\] 来使剩下的区间没有重叠。 示例 3:
>
> 输入: \[ \[1,2\], \[2,3\] \]
>
> 输出: 0
>
> 解释: 你不需要移除任何区间，因为它们已经是无重叠的了。

本题类似于课程排课，我们应该让课程结束时间最早的先排课，这样可以让排课最大化，并且需要让课程结束的时间小于下一节课程开始的时间。并且\[1,2\]\[2,3\]不算课程重叠。

所以我们的想法是，根据数组的第二位进行排序，也就是按照课程的结束时间排序，然后依次寻找不重叠的区间，然后用总个数减去不重叠的区间，剩下的就是要删除的区间。

不过，要注意的是，不重叠的区间并不一定是连续的，如果1和2区间重叠了，还要判断1和3是否重叠，直到找到不重叠的区间，再从3区间开始找下一个区间。

```text
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
import java.util.*;
class Solution {
    public int eraseOverlapIntervals(Interval[] intervals) {
        int len = intervals.length;
        if (len <= 1)return 0;
        Arrays.sort(intervals, (a,b) -> a.end - b.end);
        int count = 1;
        int end = intervals[0].end;
        for (int i = 1;i < intervals.length;i ++) {
            if (intervals[i].start < end) {
                continue;
            }
            count ++;
            end = intervals[i].end;
        }
        return len - count;
    }
}
复制代码
```

本题要注意的点有几个：

1 需要用一个值标识起始值的end，然后再往后找一个符合条件的end。由于是顺序查找，所以只需要一个变量i。并且使用end标识起始元素。

2 默认的count应该为1，因为自己本身就是不重叠的。所以找到其他不重叠的区域，使用n-count才对。

> 1. 用最少数量的箭引爆气球 在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球直径的开始和结束坐标。由于它是水平的，所以y坐标并不重要，因此只要知道开始和结束的x坐标就足够了。开始坐标总是小于结束坐标。平面内最多存在104个气球。
>
> 一支弓箭可以沿着x轴从不同点完全垂直地射出。在坐标x处射出一支箭，若有一个气球的直径的开始和结束坐标为 xstart，xend， 且满足 xstart ≤ x ≤ xend，则该气球会被引爆。可以射出的弓箭的数量没有限制。 弓箭一旦被射出之后，可以无限地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。
>
> Example:
>
> 输入: \[\[10,16\], \[2,8\], \[1,6\], \[7,12\]\]
>
> 输出: 2
>
> 解释: 对于该样例，我们可以在x = 6（射爆\[2,8\],\[1,6\]两个气球）和 x = 11（射爆另外两个气球）。

```text
import java.util.*;
class Solution {
    public int findMinArrowShots(int[][] points) {
        if (points.length <= 1){
            return points.length;
        }
        Arrays.sort(points, (a, b) -> a[1] - b[1]);
        int end = points[0][1];
        int cnt = 1;
        for (int i = 1;i < points.length;i ++) {
            if (points[i][0] <= end) {
                continue;
            }
            end = points[i][1];
            cnt ++; 
        }
        return cnt;
    }
}
复制代码
```

和上一题类似，要注意的地方是： 1.本题是求不重叠区域的个数，而上一题是求要删除重叠区域的个数。 2.本题中\[1,2\]\[2,3\]也算是重叠区域

> 1. 根据身高重建队列
>
> 这题思路不直观，跳过

1. 划分字母区间

> 字符串 S 由小写字母组成。我们要把这个字符串划分为尽可能多的片段，同一个字母只会出现在其中的一个片段。返回一个表示每个字符串片段的长度的列表。
>
> 示例 1:
>
> 输入: S = "ababcbacadefegdehijhklij" 输出: \[9,7,8\] 解释: 划分结果为 "ababcbaca", "defegde", "hijhklij"。 每个字母最多出现在一个片段中。 像 "ababcbacadefegde", "hijhklij" 的划分是错误的，因为划分的片段数较少。 注意:
>
> S的长度在\[1, 500\]之间。 S只包含小写字母'a'到'z'。

本题的思路是，先把每个字母的最后一位找出来，存在数组里，然后从头开始找到这样一个字符串，对于字符串中的每个字母，它出现的最后一个字母已经包含在整个字符串内。

```text
import java.util.*;
class Solution {
    public List<Integer> partitionLabels(String S) {
        int []arr = new int[26];
        List<Integer> list = new ArrayList<>();
        for (int i = 0;i < S.length();i ++) {
            arr[S.charAt(i) - 'a'] = i;
        }
        int start = 0;
        int end = arr[S.charAt(0) - 'a'];
        for (int i = 0;i < S.length();i ++) {
           end =  Math.max(arr[S.charAt(i) - 'a'], end);     
           if (i < end) {
               continue;
           }else {
               list.add(end - start + 1);
               start = i + 1;
           }
        }
        return list;
    }
}
复制代码
```

本题要点：

1.要使用一个数组存储每个字母的最后出现位置。 通过x - 'a'的方式得到其下标。

2.由于需要每一次截取的长度，所以用start和end来表示，可以用于保存长度。

> 1. 种花问题 假设你有一个很长的花坛，一部分地块种植了花，另一部分却没有。可是，花卉不能种植在相邻的地块上，它们会争夺水源，两者都会死去。
>
> 给定一个花坛（表示为一个数组包含0和1，其中0表示没种植花，1表示种植了花），和一个数 n 。能否在不打破种植规则的情况下种入 n 朵花？能则返回True，不能则返回False。
>
> 示例 1:
>
> 输入: flowerbed = \[1,0,0,0,1\], n = 1 输出: True 示例 2:
>
> 输入: flowerbed = \[1,0,0,0,1\], n = 2 输出: False 注意:
>
> 数组内已种好的花不会违反种植规则。 输入的数组长度范围为 \[1, 20000\]。 n 是非负整数，且不会超过输入数组的大小。

思路：算出花坛中一共有几个空位，看看是否大于等于花的数量

```text
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        int cnt = 0;
        if (flowerbed.length == 1 && flowerbed[0] == 0) {
            return n <= 1;
        }
        if (flowerbed.length >= 2) {
            if (flowerbed[0] == 0 && flowerbed[1] == 0) {
                flowerbed[0] = 1;
                cnt ++;
            }
            if (flowerbed[flowerbed.length - 1] == 0 && flowerbed[flowerbed.length - 2] == 0) {
                flowerbed[flowerbed.length - 1] = 1;
                cnt ++;
            }
        }
        for (int i = 1;i < flowerbed.length - 1;) {
            if (flowerbed[i - 1] == 0 && flowerbed[i] == 0 && flowerbed[i + 1] == 0 ) {
                cnt ++;
                flowerbed[i] = 1;
                i = i + 2;
            }else {
                i ++;
            }
        }
        return cnt >= n;
    }
}
复制代码
```

注意点：

1从头到尾找到符合0 0 0情况的个数。

2注意数组两边的特殊情况处理 0 0。当长度大于1时处理即可。

3。处理长度为1时的数组

> 1. 判断子序列
>
> 给定字符串 s 和 t ，判断 s 是否为 t 的子序列。
>
> 你可以认为 s 和 t 中仅包含英文小写字母。字符串 t 可能会很长（长度 ~= 500,000），而 s 是个短字符串（长度 &lt;=100）。
>
> 字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。
>
> 示例 1: s = "abc", t = "ahbgdc"
>
> 返回 true.
>
> 示例 2: s = "axc", t = "ahbgdc"
>
> 返回 false.

解析：本题我刚开始想的办法是使用dp求出LCS最长公共子序列，判断长度是否等于t的长度，结果超时了。事实证明我想太多了。

只需要按顺序查找t的字母是否都在s中即可，当然，要注意查找时候的下标移动，否则也是O（N2）的复杂度

DP解法：超时

```text
import java.util.*;
class Solution {
    public boolean isSubsequence(String s, String t) {
        return LCS(s,t);
    }
    public boolean LCS(String s, String t) {
        int [][]dp = new int[s.length() + 1][t.length() + 1];
        for (int i = 1;i <= s.length();i ++) {
            for (int j = 1;j <= t.length();j ++) {
                if (s.charAt(i - 1) == t.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        int len = dp[s.length()][t.length()];
        return len == s.length();
    }
}
复制代码
```

正解： 巧用indexOf方法indexOf\(c,index + 1\)来找到从index + 1开始的c字母。

```text
import java.util.*;
class Solution {
    public boolean isSubsequence(String s, String t) {
        int index = -1;
        for (int i = 0;i < s.length();i ++) {
            index = t.indexOf(s.charAt(i), index + 1);
            if (index == -1) {
                return false;
            }
        }
        return true;
    }
}
复制代码
```

> 1. 非递减数列 这题暂时没有想到比较好的方法

> 给定一个长度为 n 的整数数组，你的任务是判断在最多改变 1 个元素的情况下，该数组能否变成一个非递减数列。
>
> 我们是这样定义一个非递减数列的： 对于数组中所有的 i \(1 &lt;= i &lt; n\)，满足 array\[i\] &lt;= array\[i + 1\]。
>
> 示例 1:
>
> 输入: \[4,2,3\] 输出: True 解释: 你可以通过把第一个4变成1来使得它成为一个非递减数列。 示例 2:
>
> 输入: \[4,2,1\] 输出: False 解释: 你不能在只改变一个元素的情况下将其变为非递减数列。

> 1. 买卖股票的最佳时机 II 题意：
>
> 给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
>
> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
>
> 示例 1:
>
> 输入: \[7,1,5,3,6,4\] 输出: 7 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。 随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。 示例 2:
>
> 输入: \[1,2,3,4,5\] 输出: 4 解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。 注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。 因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。 示例 3:
>
> 输入: \[7,6,4,3,1\] 输出: 0 解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

题意：只要出现价差为正时就买入，这样一定是最赚的，注意本题中同一天可以进行卖出后再进行买入。

对于 \[a, b, c, d\]，如果有 a &lt;= b &lt;= c &lt;= d ，那么最大收益为 d - a。而 d - a = \(d - c\) + \(c - b\) + \(b - a\) ，因此当访问到一个 prices\[i\] 且 prices\[i\] - prices\[i-1\] &gt; 0，那么就把 prices\[i\] - prices\[i-1\] 添加到收益中，从而在局部最优的情况下也保证全局最优。

```text
class Solution {
    public int maxProfit(int[] prices) {
        int buy = 0;
        int sell = 1;
        int cnt = 0;
        while(buy < sell && sell < prices.length) {
            if(prices[sell] > prices[buy]) {
                cnt += prices[sell] - prices[buy];
            }   
            buy = sell;
            sell = buy + 1;
        }
        return cnt;
    }
}
复制代码
```

## 双指针

双指针 双指针主要用于遍历数组，两个指针指向不同的元素，从而协同完成任务。

双指针其实一般不会抽取出来单独作为一种算法，因为数组中经常会用到，而且我们熟悉的二分查找也使用了双指针。

二分查找

> 1. 两数之和 II - 输入有序数组
>
> 给定一个已按照升序排列 的有序数组，找到两个数使得它们相加之和等于目标数。
>
> 函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2。
>
> 说明:
>
> 返回的下标值（index1 和 index2）不是从零开始的。 你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。 示例:
>
> 输入: numbers = \[2, 7, 11, 15\], target = 9 输出: \[1,2\] 解释: 2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。

这题基本操作了。

```text
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0,right = numbers.length - 1;
        int []arr = new int[2];
        while (left < right) {
            if (numbers[left] + numbers[right] < target) {
                left ++;
            }else if (numbers[left] + numbers[right] > target) {
                right --;
            }else {
                arr[0] = left + 1;
                arr[1] = right + 1;
                return arr;
            }
        }
        return arr;
    }
}
复制代码
```

> 1. 平方数之和
>
> 给定一个非负整数 c ，你要判断是否存在两个整数 a 和 b，使得 a2 + b2 = c。
>
> 示例1:
>
> 输入: 5 输出: True 解释: 1 \* 1 + 2 \* 2 = 5
>
> 示例2:
>
> 输入: 3 输出: False

基操

```text
import java.util.*;
class Solution {
    public boolean judgeSquareSum(int c) {
        double n = Math.sqrt(c);
        for (double i = 0;i <= n;i ++) {
            double diff = c - i * i;
            int j = (int) Math.sqrt(diff);
            if (j * j == diff) {
                return true;
            }
        }
        return false;
    }
}
复制代码
```

1. 反转字符串中的元音字母 编写一个函数，以字符串作为输入，反转该字符串中的元音字母。

示例 1： 给定 s = "hello", 返回 "holle".

示例 2： 给定 s = "leetcode", 返回 "leotcede".

注意: 元音字母不包括 "y".

快排思想进行交换即可

```text
import java.util.*;
class Solution {
    public String reverseVowels(String s) {
        char[] arr = s.toCharArray();
        int left = 0,right = s.length() - 1;
        while (left < right){
            while (left < right && !isVowels(arr[left])) {
                left ++;
            }
            while (left < right && !isVowels(arr[right])) {
                right --;
            }
            char temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left ++;
            right --;
        }
        return String.valueOf(arr);
    }
    public boolean isVowels(char c) {
        char[]arr = {'a', 'i', 'e', 'u', 'o', 'A', 'I', 'E', 'U', 'O'};
        for (int k = 0;k < arr.length;k ++) {
            if (c == arr[k]) {
                return true;
            }
        }
        return false;
    }
}
复制代码
```

> 1. 验证回文字符串 Ⅱ
>
> 给定一个非空字符串 s，最多删除一个字符。判断是否能成为回文字符串。
>
> 示例 1:
>
> 输入: "aba" 输出: True 示例 2:
>
> 输入: "abca" 输出: True 解释: 你可以删除c字符。 注意:
>
> 字符串只包含从 a-z 的小写字母。字符串的最大长度是50000。

在验证回文的基础上加上一步，当遇到不符合要求的字符时，再往前走一步即可。当然机会只有一次。

本题可能遇到一个问题，如果直接用while循环写的话，会遇到两种情况，一种是左边加一，一种是右边减一。只要一种情况满足即可。所以我们要另外写一个判断函数，然后用\|\|来表示两种情况即可。

```text
class Solution {
    public boolean validPalindrome(String s) {
        int left = 0,right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left) == s.charAt(right)) {
                left ++;
                right --;
            }else {
                return valid(s, left + 1,right) || valid(s, left, right - 1);
            }
        }
        return true;
    }
    
    public boolean valid(String s, int i, int j) {
        int left = i,right = j;        
        while (left < right) {
            if (s.charAt(left) == s.charAt(right)) {
                left ++;
                right --;
            }
            else return false;
        }
        return true;
    }
}
复制代码
```

> 1. 合并两个有序数组
>
> 这题给的用例有毒，不谈。
>
> 1. 环形链表
>
> 剑指offer 使用双指针，一个指针每次移动一个节点，一个指针每次移动两个节点，如果存在环，那么这两个指针一定会相遇。

> 1. 通过删除字母匹配到字典里最长单词 给定一个字符串和一个字符串字典，找到字典里面最长的字符串，该字符串可以通过删除给定字符串的某些字符来得到。如果答案不止一个，返回长度最长且字典顺序最小的字符串。如果答案不存在，则返回空字符串。
>
> 示例 1:
>
> 输入: s = "abpcplea", d = \["ale","apple","monkey","plea"\]
>
> 输出: "apple" 示例 2:
>
> 输入: s = "abpcplea", d = \["a","b","c"\]
>
> 输出: "a" 说明:
>
> 所有输入的字符串只包含小写字母。 字典的大小不会超过 1000。 所有输入的字符串长度不会超过 1000。

解析：本题的双指针不是指左右指针了，而是分别扫描两个字符串所用的指针。

由于题目要求先按照长度排序再按照字典序排序，于是使用比较器可以实现该逻辑，然后再一一匹配即可。

```text
import java.util.*;
class Solution {
    public String findLongestWord(String s, List<String> d) {
        Collections.sort(d, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                if (o1.length() != o2.length()) {
                    return o2.length() - o1.length();
                } else {
                    return o1.compareTo(o2);
                }
            }
        });
        for (String str : d) {
            int i = 0,j = 0;
            while (i < s.length() && j < str.length()) {
                if (s.charAt(i) == str.charAt(j)) {
                    i ++;
                    j ++;
                }else {
                    i ++;
                }
            }
            if (j == str.length()) {
                return str;
            }
        }
        return "";     
    }
}
复制代码
```

## 排序

排序

> 快速选择 一般用于求解 Kth Element 问题，可以在 O\(N\) 时间复杂度，O\(1\) 空间复杂度完成求解工作。
>
> 与快速排序一样，快速选择一般需要先打乱数组，否则最坏情况下时间复杂度为 O\(N2\)。
>
> 堆排序
>
> 堆排序用于求解 TopK Elements 问题，通过维护一个大小为 K 的堆，堆中的元素就是 TopK Elements。当然它也可以用于求解 Kth Element 问题，堆顶元素就是 Kth Element。快速选择也可以求解 TopK Elements 问题，因为找到 Kth Element 之后，再遍历一次数组，所有小于等于 Kth Element 的元素都是 TopK Elements。可以看到，快速选择和堆排序都可以求解 Kth Element 和 TopK Elements 问题。

```text
排序 ：时间复杂度 O(NlogN)，空间复杂度 O(1)

public int findKthLargest(int[] nums, int k) {
    Arrays.sort(nums);
    return nums[nums.length - k];
}

堆排序 ：时间复杂度 O(NlogK)，空间复杂度 O(K)。
每次插入一个元素，当元素超过k个时，弹出顶部的最小值，当元素push完以后，剩下的元素就是前k大的元素，堆顶元素就是第K大的元素。

public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> pq = new PriorityQueue<>(); // 小顶堆
    for (int val : nums) {
        pq.add(val);
        if (pq.size() > k) // 维护堆的大小为 K
            pq.poll();
    }
    return pq.peek();
}

快速选择（也可以认为是快速排序的partition加上二分的算法）

利用partition函数求出一个数的最终位置，再通过二分来逼近第k个位置，算法结论表明该算法的时间复杂度是O（N)

class Solution {
public int findKthLargest(int[] nums, int k) {
    k = nums.length - k;
    int l = 0, r = nums.length - 1;
    while (l < r) {
        int pos = partition(nums, l , r);
        if (pos == k) return nums[pos];
        else if (pos < k) {
            l = pos + 1;
        }else {
            r = pos - 1;
        }
    }
    return nums[k];
}
public int partition(int[] nums, int left, int right) {
    int l = left, r = right;
    int temp = nums[l];
    while (l < r) {
        while (l < r && nums[r] >= temp) {
            r --;
        }
        while (l < r && nums[l] <= temp) {
            l ++;
        }
        if (l < r) {
            int tmp = nums[l];
            nums[l] = nums[r];
            nums[r] = tmp;
        }
    }
    nums[left] = nums[l];
    nums[l] = temp;
    return l;
}
}
复制代码
```

桶排序

> 1. 前K个高频元素
>
> 给定一个非空的整数数组，返回其中出现频率前 k 高的元素。
>
> 例如，
>
> 给定数组 \[1,1,1,2,2,3\] , 和 k = 2，返回 \[1,2\]。
>
> 注意：
>
> 你可以假设给定的 k 总是合理的，1 ≤ k ≤ 数组中不相同的元素的个数。 你的算法的时间复杂度必须优于 O\(n log n\) , n 是数组的大小。

```text
解析：
设置若干个桶，每个桶存储出现频率相同的数，并且桶的下标代表桶中数出现的频率，即第 i 个桶中存储的数出现的频率为 i。把数都放到桶之后，从后向前遍历桶，最先得到的 k 个数就是出现频率最多的的 k 个数。

import java.util.*;
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i : nums) {
            if (map.containsKey(i)) {
                map.put(i, map.get(i) + 1);
            }else {
                map.put(i, 1);
            }
        }
        ArrayList<Integer>[] timesMap = new ArrayList[nums.length + 1];
        for (int key : map.keySet()) {
            int times = map.get(key);
            if (timesMap[times] == null) {
                timesMap[times] = new ArrayList<>();
                timesMap[times].add(key);
            }
            else {
                timesMap[times].add(key);            
            }
        }
        List<Integer> top = new ArrayList<Integer>();
        for (int i = timesMap.length - 1;i > 0 && top.size() < k;i --) {
            if (timesMap[i] != null) {
                top.addAll(timesMap[i]);
            }
        }
        return top;
    }
}

注意：

1本题的难点在于先用hashmap存储数据得到每个数的频率，再用数组存储每个频率对应哪些数。

2最后再通过频率数组的最后一位开始往前找，找到k个数为之，就是出现频率最高的k个数了。
复制代码
```

> 1. 根据字符出现频率排序
>
> 给定一个字符串，请将字符串里的字符按照出现的频率降序排列。

输入: "tree"

输出: "eert"

解释: 'e'出现两次，'r'和't'都只出现一次。 因此'e'必须出现在'r'和't'之前。此外，"eetr"也是一个有效的答案。

我下面这个写法只考虑了小写字母的情况，大写字母与其他字符没有考虑，是错误的。正确的做法还是应该用一个128长度的char数组 。因为char是1一个字节长度，也就是8位，2的8次方是256，考虑正数的话就是128。

上题使用map是因为32位整数太大，数组存不下，而本题char数组只需要长度为128即可，不用使用map。

错误解：

```text
public static String frequencySort(String s) {
        int []arr = new int[26];
        char []crr = s.toCharArray();
        for (char c : crr) {
            arr[c - 'a']++;
        }

        List<Character>[]times = new ArrayList[s.length() + 1];
        for (int i = 0;i < arr.length;i ++) {
            if (times[arr[i]] == null) {
                times[arr[i]] = new ArrayList<>();
                times[arr[i]].add((char) ('a' + i));
            }else {
                times[arr[i]].add((char) ('a' + i));
            }
        }
        StringBuilder sb = new StringBuilder();
        for (int i = times.length - 1;i > 0 ;i --) {
            if (times[i] != null) {
                for (char c : times[i]) {
                    int time = 0;
                    while (time < i) {
                        sb.append(c);
                        time ++;
                    }
                }
            }
        }
        return sb.toString();
    }
复制代码
```

正解：

```text
class Solution {
    public static String frequencySort(String s) {
        int []arr = new int[128];
        char []crr = s.toCharArray();
        for (char c : crr) {
            arr[c]++;
        }

        List<Character>[]times = new ArrayList[s.length() + 1];
        for (int i = 0;i < arr.length;i ++) {
            if (times[arr[i]] == null) {
                times[arr[i]] = new ArrayList<>();
                times[arr[i]].add((char) (i));
            }else {
                times[arr[i]].add((char) (i));
            }
        }
        StringBuilder sb = new StringBuilder();
        for (int i = times.length - 1;i > 0 ;i --) {
            if (times[i] != null) {
                for (char c : times[i]) {
                    int time = 0;
                    while (time < i) {
                        sb.append(c);
                        time ++;
                    }
                }
            }
        }
        return sb.toString();
    }
}
复制代码
```

> 1. 分类颜色 给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。
>
> 此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。
>
> 注意: 不能使用代码库中的排序函数来解决这道题。

> 进阶：
>
> 一个直观的解决方案是使用计数排序的两趟扫描算法。 首先，迭代计算出0、1 和 2 元素的个数，然后按照0、1、2的排序，重写当前数组。 你能想出一个仅使用常数空间的一趟扫描算法吗？

解析：本题的思路一个就是题目所说的计数排序，还有一个便是使用交换算法，设置三个下标，zero, one, two,分别表示0的结尾，1的结尾，2的结尾，并且在遍历过程中把0换到one前面，把2换到one后面，中间的就是1了。

```text
class Solution {
    public void sortColors(int[] nums) {
        if (nums.length <= 1)return;
        int zero = -1, one = 0,two = nums.length;
        while (one < two) {
            if (nums[one] == 0) {
                swap(nums, ++zero, one++);
            }else if (nums[one] == 2) {
                swap(nums, --two, one);
            }else {
                one ++;
            }
        }
    }
    public void swap(int []nums, int i, int j) {
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp; 
    }
}
复制代码
```

## 二分查找

正常实现

```text
public int binarySearch(int[] nums, int key) {
    int l = 0, h = nums.length - 1;
    while (l <= h) {
        int m = l + (h - l) / 2;
        if (nums[m] == key) {
            return m;
        } else if (nums[m] > key) {
            h = m - 1;
        } else {
            l = m + 1;
        }
    }
    return -1;
}
复制代码
```

> 时间复杂度
>
> 二分查找也称为折半查找，每次都能将查找区间减半，这种折半特性的算法时间复杂度都为 O\(logN\)。
>
> m 计算
>
> 有两种计算中值 m 的方式：
>
> m = \(l + h\) / 2 m = l + \(h - l\) / 2 l + h 可能出现加法溢出，最好使用第二种方式。
>
> 返回值
>
> 循环退出时如果仍然没有查找到 key，那么表示查找失败。可以有两种返回值：
>
> -1：以一个错误码表示没有查找到 key l：将 key 插入到 nums 中的正确位置

变种

题目：在一个有重复元素的数组中查找 key 的最左位置

如果是直接查找那么复杂度为O（n）所以可以采用二分优化

二分查找可以有很多变种，变种实现要注意边界值的判断。 例如在一个有重复元素的数组中查找 key 的最左位置的实现如下：

```text
public int binarySearch(int[] nums, int key) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] >= key) {
            h = m;
        } else {
            l = m + 1;
        }
    }
    return l;
}
复制代码
```

> 该实现和正常实现有以下不同：
>
> 循环条件为 l &lt; h h 的赋值表达式为 h = m 最后返回 l 而不是 -1 在 nums\[m\] &gt;= key 的情况下，可以推导出最左 key 位于 \[l, m\] 区间中，这是一个闭区间。h 的赋值表达式为 h = m，因为 m 位置也可能是解。
>
> 在 h 的赋值表达式为 h = mid 的情况下，如果循环条件为 l &lt;= h，那么会出现循环无法退出的情况，因此循环条件只能是 l &lt; h。以下演示了循环条件为 l &lt;= h 时循环无法退出的情况：

nums = {0, 1, 2}, key = 1 l m h 0 1 2 nums\[m\] &gt;= key 0 0 1 nums\[m\] &lt; key 1 1 1 nums\[m\] &gt;= key 1 1 1 nums\[m\] &gt;= key ... 当循环体退出时，不表示没有查找到 key，因此最后返回的结果不应该为 -1。为了验证有没有查找到，需要在调用端判断一下返回位置上的值和 key 是否相等

> 1. x 的平方根
>
> 实现 int sqrt\(int x\) 函数。
>
> 计算并返回 x 的平方根，其中 x 是非负整数。
>
> 由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。
>
> 示例 1:
>
> 输入: 4 输出: 2 示例 2:
>
> 输入: 8 输出: 2 说明: 8 的平方根是 2.82842..., 由于返回类型是整数，小数部分将被舍去。

一个数 x 的开方 sqrt 一定在 0 ~ x 之间，并且满足 sqrt == x / sqrt。可以利用二分查找在 0 ~ x 之间查找 sqrt。

对于 x = 8，它的开方是 2.82842...，最后应该返回 2 而不是 3。在循环条件为 l &lt;= h 并且循环退出时，h 总是比 l 小 1，也就是说 h = 2，l = 3，因此最后的返回值应该为 h 而不是 l。

```text
public int mySqrt(int x) {
    if (x <= 1) {
        return x;
    }
    int l = 1, h = x;
    while (l <= h) {
        int mid = l + (h - l) / 2;
        int sqrt = x / mid;
        if (sqrt == mid) {
            return mid;
        } else if (mid > sqrt) {
            h = mid - 1;
        } else {
            l = mid + 1;
        }
    }
    return h;
}
复制代码
```

注意：由于要取的值是比原值小的整数，所以等sqrt小于mid时，并且此时l &gt; h时说明h此时已经是最接近sqrt且比它小的值了。当然如果前面有相等的情况时已经返回了。 744. 寻找比目标字母大的最小字母

> 给定一个只包含小写字母的有序数组letters 和一个目标字母 target，寻找有序数组里面比目标字母大的最小字母。
>
> 数组里字母的顺序是循环的。举个例子，如果目标字母target = 'z' 并且有序数组为 letters = \['a', 'b'\]，则答案返回 'a'。
>
> 示例:
>
> 输入: letters = \["c", "f", "j"\] target = "a" 输出: "c"
>
> 输入: letters = \["c", "f", "j"\] target = "c" 输出: "f"
>
> 输入: letters = \["c", "f", "j"\] target = "d" 输出: "f"
>
> 输入: letters = \["c", "f", "j"\] target = "g" 输出: "j"
>
> 输入: letters = \["c", "f", "j"\] target = "j" 输出: "c"
>
> 输入: letters = \["c", "f", "j"\] target = "k" 输出: "c" 注:
>
> letters长度范围在\[2, 10000\]区间内。 letters 仅由小写字母组成，最少包含两个不同的字母。 目标字母target 是一个小写字母。

解析：使用二分查找逼近，找到字母后右边那个就是最小的，找不到的话返回结束位置的右边第一个字母。

注意： 1 与上一题相反，本题的要找的是比指定值大一点的数，所以此时l &gt; r满足时，l就是比指定值大一点的数了。

2 注意可能有连续重复的数字，所以一直往右找到一个数大于指定值

```text
class Solution {
    public char nextGreatestLetter(char[] letters, char target) {
        if (letters == null || letters.length == 0) return 'a';
        int l = 0,r = letters.length - 1;
        while (l <= r) {
            int m = l + (r - l)/2;
            if (letters[m] <= target ) {
                l = m + 1;
            }else {
                r = m - 1;
            }
        }

        if (l <= letters.length - 1) {
            return letters[l];
        }else {
            return letters[0];
        }

    }
}
复制代码
```

1. 有序数组中的单一元素

给定一个只包含整数的有序数组，每个元素都会出现两次，唯有一个数只会出现一次，找出这个数。

示例 1:

输入: \[1,1,2,3,3,4,4,8,8\] 输出: 2 示例 2:

输入: \[3,3,7,7,10,11,11\] 输出: 10 注意: 您的方案应该在 O\(log n\)时间复杂度和 O\(1\)空间复杂度中运行。

解析：本题其实可以用位运算做，但是限制了时间复杂度，所以考虑使用二分，这题我做不出来，可以参考下面答案

令 index 为 Single Element 在数组中的位置。如果 m 为偶数，并且 m + 1 &lt; index，那么 nums\[m\] == nums\[m + 1\]；m + 1 &gt;= index，那么 nums\[m\] != nums\[m + 1\]。

从上面的规律可以知道，如果 nums\[m\] == nums\[m + 1\]，那么 index 所在的数组位置为 \[m + 2, h\]，此时令 l = m + 2；如果 nums\[m\] != nums\[m + 1\]，那么 index 所在的数组位置为 \[l, m\]，此时令 h = m。

因为 h 的赋值表达式为 h = m，那么循环条件也就只能使用 l &lt; h 这种形式。

```text
public int singleNonDuplicate(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (m % 2 == 1) {
            m--;   // 保证 l/h/m 都在偶数位，使得查找区间大小一直都是奇数
        }
        if (nums[m] == nums[m + 1]) {
            l = m + 2;
        } else {
            h = m;
        }
    }
    return nums[l];
}
复制代码
```

1. 寻找旋转排序数组中的最小值

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

\( 例如，数组 \[0,1,2,4,5,6,7\] 可能变为 \[4,5,6,7,0,1,2\] \)。

请找出其中最小的元素。

你可以假设数组中不存在重复元素。

示例 1:

输入: \[3,4,5,1,2\] 输出: 1 示例 2:

输入: \[4,5,6,7,0,1,2\] 输出: 0

解析：比较经典的题目，正常情况下是顺序的，仅当arr\[i\] &gt; arr\[i + 1\]可以得知arr\[i + 1\]是最小值。 顺序扫描需要O（n），使用二分查找可以优化到Log2n

旋转数组的两个递增数组由最小值来划分。 所以对于l， m， r来说，如果arr\[m\] &lt; arr\[h\]，说明到m到h是有序部分，最小值应该在l到m之间。所以令r = m; 如果arr\[h\] &lt; arr\[m\]，说明最小值在m到h之间。所以令l = m + 1。 当l &gt; r时，说明nums\[m\] &gt; nums\[h\]已经到达终点，此时nums\[m + 1 \]就是最小值

```text
public int findMin(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] <= nums[h]) {
            h = m;
        } else {
            l = m + 1;
        }
    }
    return nums[l];
}
复制代码
```

> 1. 在排序数组中查找元素的第一个和最后一个位置
>
> 给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。
>
> 你的算法时间复杂度必须是 O\(log n\) 级别。
>
> 如果数组中不存在目标值，返回 \[-1, -1\]。
>
> 示例 1:
>
> 输入: nums = \[5,7,7,8,8,10\], target = 8 输出: \[3,4\] 示例 2:
>
> 输入: nums = \[5,7,7,8,8,10\], target = 6 输出: \[-1,-1\]

解析：参考别人的答案：

1 首先通过二分查找找到该数出现的最左边位置（与例题一样）

2 然后通过二分查找找到比该数大1的数出现的位置，如果不存在，则刚好在所求数右边一位，再减1即可。

3 边界条件判断

```text
public int[] searchRange(int[] nums, int target) {
    int first = binarySearch(nums, target);
    int last = binarySearch(nums, target + 1) - 1;
    if (first == nums.length || nums[first] != target) {
        return new int[]{-1, -1};
    } else {
        return new int[]{first, Math.max(first, last)};
    }
}

private int binarySearch(int[] nums, int target) {
    int l = 0, h = nums.length; // 注意 h 的初始值
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] >= target) {
            h = m;
        } else {
            l = m + 1;
        }
    }
    return l;
}
复制代码
```

## DFS和BFS，回溯

## 搜索

深度优先搜索和广度优先搜索广泛运用于树和图中，但是它们的应用远远不止如此。

### BFS

[![](data:image/svg+xml;utf8,<?xml%20version=%221.0%22?%3E%3Csvg%20xmlns=%22http://www.w3.org/2000/svg%22%20version=%221.1%22%20width=%22238%22%20height=%22258%22%3E%3C/svg%3E)](https://github.com/CyC2018/Interview-Notebook/blob/master/pics/4ff355cf-9a7f-4468-af43-e5b02038facc.jpg)

广度优先搜索的搜索过程有点像一层一层地进行遍历，每层遍历都以上一层遍历的结果作为起点，遍历一个距离能访问到的所有节点。需要注意的是，遍历过的节点不能再次被遍历。

第一层：

* 0 -&gt; {6,2,1,5};

第二层：

* 6 -&gt; {4}
* 2 -&gt; {}
* 1 -&gt; {}
* 5 -&gt; {3}

第三层：

* 4 -&gt; {}
* 3 -&gt; {}

可以看到，每一层遍历的节点都与根节点距离相同。设 di 表示第 i 个节点与根节点的距离，推导出一个结论：对于先遍历的节点 i 与后遍历的节点 j，有 di&lt;=dj。利用这个结论，可以求解最短路径等 **最优解** 问题：第一次遍历到目的节点，其所经过的路径为最短路径。应该注意的是，使用 BFS 只能求解无权图的最短路径。

在程序实现 BFS 时需要考虑以下问题：

* 队列：用来存储每一轮遍历得到的节点；
* 标记：对于遍历过的节点，应该将它标记，防止重复遍历。
* 
计算在网格中从原点到特定点的最短路径长度

\[\[1,1,0,1\], \[1,0,1,0\], \[1,1,1,1\], \[1,0,1,1\]\]

1 表示可以经过某个位置，求解从 \(0, 0\) 位置到 \(tr, tc\) 位置的最短路径长度。 2 由于每个点需要保存x坐标，y坐标以及长度，所以必须要用一个类将三个属性封装起来。 3 由于bfs每次只将距离加一，所以当位置抵达终点时，此时的距离就是最短路径了。

```text
private static class Position {
    int r, c, length;
    public Position(int r, int c, int length) {
        this.r = r;
        this.c = c;
        this.length = length;
    }
}

 public static int minPathLength(int[][] grids, int tr, int tc) {
        int[][] next = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
        int m = grids.length, n = grids[0].length;
        Queue<Position> queue = new LinkedList<>();
        queue.add(new Position(0, 0, 1));
        while (!queue.isEmpty()) {
            Position pos = queue.poll();
            for (int i = 0; i < 4; i++) {
                Position nextPos = new Position(pos.r + next[i][0], pos.c + next[i][1], pos.length + 1);
                if (nextPos.r < 0 || nextPos.r >= m || nextPos.c < 0 || nextPos.c >= n) continue;
                if (grids[nextPos.r][nextPos.c] != 1) continue;
                grids[nextPos.r][nextPos.c] = 0;
                if (nextPos.r == tr && nextPos.c == tc) return nextPos.length;
                queue.add(nextPos);
            }
        }
        return -1;
    }
复制代码
```

1. 完全平方数

> 组成整数的最小平方数数量
>
> 给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。

> 示例 1:
>
> 输入: n = 12 输出: 3 解释: 12 = 4 + 4 + 4. 示例 2:
>
> 输入: n = 13 输出: 2 解释: 13 = 4 + 9.

1 可以将每个整数看成图中的一个节点，如果两个整数之差为一个平方数，那么这两个整数所在的节点就有一条边。

2 要求解最小的平方数数量，就是求解从节点 n 到节点 0 的最短路径。

3 首先生成平方数序列放入数组，然后通过队列，每次减去一个平方数，把剩下的数加入队列，也就是通过bfs的方式，当此时的数刚好等于平方数，则满足题意，由于每次循环level加一，所以最后输出的level就是需要的平方数个数。

本题也可以用动态规划求解，在之后动态规划部分中会再次出现。

```text
public int numSquares(int n) {
    List<Integer> squares = generateSquares(n);
    Queue<Integer> queue = new LinkedList<>();
    boolean[] marked = new boolean[n + 1];
    queue.add(n);
    marked[n] = true;
    int level = 0;
    while (!queue.isEmpty()) {
        int size = queue.size();
        level++;
        while (size-- > 0) {
            int cur = queue.poll();
            for (int s : squares) {
                int next = cur - s;
                if (next < 0) {
                    break;
                }
                if (next == 0) {
                    return level;
                }
                if (marked[next]) {
                    continue;
                }
                marked[next] = true;
                queue.add(cur - s);
            }
        }
    }
    return n;
}

/**
 * 生成小于 n 的平方数序列
 * @return 1,4,9,...
 */
private List<Integer> generateSquares(int n) {
    List<Integer> squares = new ArrayList<>();
    int square = 1;
    int diff = 3;
    while (square <= n) {
        squares.add(square);
        square += diff;
        diff += 2;
    }
    return squares;
}
复制代码
```

1. 单词接龙

> 给定两个单词（beginWord 和 endWord）和一个字典，找到从 beginWord 到 endWord 的最短转换序列的长度。转换需遵循如下规则：
>
> 每次转换只能改变一个字母。 转换过程中的中间单词必须是字典中的单词。 说明:
>
> 如果不存在这样的转换序列，返回 0。 所有单词具有相同的长度。 所有单词只由小写字母组成。 字典中不存在重复的单词。 你可以假设 beginWord 和 endWord 是非空的，且二者不相同。 示例 1:
>
> 输入: beginWord = "hit", endWord = "cog", wordList = \["hot","dot","dog","lot","log","cog"\]
>
> 输出: 5
>
> 解释: 一个最短转换序列是 "hit" -&gt; "hot" -&gt; "dot" -&gt; "dog" -&gt; "cog", 返回它的长度 5。 示例 2:
>
> 输入: beginWord = "hit" endWord = "cog" wordList = \["hot","dot","dog","lot","log"\]
>
> 输出: 0

解释: endWord "cog" 不在字典中，所以无法进行转换。

找出一条从 beginWord 到 endWord 的最短路径，每次移动规定为改变一个字符，并且改变之后的字符串必须在 wordList 中。

单词台阶问题，亚马逊面试时考了。

这个参考别人的答案，我会加上解析。

```text
public int ladderLength(String beginWord, String endWord, List<String> wordList) {
    //注意此处把首个单词放到了list的最后面，所以start才会是N-1。别搞错了。
    wordList.add(beginWord);
    int N = wordList.size();
    int start = N - 1;
    int end = 0;
    while (end < N && !wordList.get(end).equals(endWord)) {
        end++;
    }
    if (end == N) {
        return 0;
    }
    List<Integer>[] graphic = buildGraphic(wordList);
    return getShortestPath(graphic, start, end);
}

本方法用于把每个单词开头的完整序列保存起来，以便让bfs过程中遍历到所有情况。

private List<Integer>[] buildGraphic(List<String> wordList) {
    int N = wordList.size();
    List<Integer>[] graphic = new List[N];
    for (int i = 0; i < N; i++) {
        graphic[i] = new ArrayList<>();
        for (int j = 0; j < N; j++) {
            if (isConnect(wordList.get(i), wordList.get(j))) {
                graphic[i].add(j);
            }
        }
    }
    return graphic;
}

本方法用于上面这个方法连接单词序列时，需要判断两个单词是否只需要一次改变即可，如果不满足要求，则跳过这个单词。

private boolean isConnect(String s1, String s2) {
    int diffCnt = 0;
    for (int i = 0; i < s1.length() && diffCnt <= 1; i++) {
        if (s1.charAt(i) != s2.charAt(i)) {
            diffCnt++;
        }
    }
    return diffCnt == 1;
}

这一步就是通过BFS进行单词序列连接了。
让初始所在位置入队，然后去遍历它能转变成的单词，接着进行bfs的遍历。

最终当next = end时，说明已经能到达最终位置了。所以此时的路径时最短的。每次出队都是一个路径，所以返回path即为最短路径长度。

private int getShortestPath(List<Integer>[] graphic, int start, int end) {
    Queue<Integer> queue = new LinkedList<>();
    boolean[] marked = new boolean[graphic.length];
    queue.add(start);
    marked[start] = true;
    int path = 1;
    while (!queue.isEmpty()) {
        int size = queue.size();
        path++;
        while (size-- > 0) {
            int cur = queue.poll();
            for (int next : graphic[cur]) {
                if (next == end) {
                    return path;
                }
                if (marked[next]) {
                    continue;
                }
                marked[next] = true;
                queue.add(next);
            }
        }
    }
    return 0;
}
复制代码
```

## DFS

广度优先搜索一层一层遍历，每一层得到的所有新节点，要用队列存储起来以备下一层遍历的时候再遍历。

而深度优先搜索在得到一个新节点时立马对新节点进行遍历：从节点 0 出发开始遍历，得到到新节点 6 时，立马对新节点 6 进行遍历，得到新节点 4；如此反复以这种方式遍历新节点，直到没有新节点了，此时返回。返回到根节点 0 的情况是，继续对根节点 0 进行遍历，得到新节点 2，然后继续以上步骤。

从一个节点出发，使用 DFS 对一个图进行遍历时，能够遍历到的节点都是从初始节点可达的，DFS 常用来求解这种 可达性 问题。

在程序实现 DFS 时需要考虑以下问题：

栈：用栈来保存当前节点信息，当遍历新节点返回时能够继续遍历当前节点。可以使用递归栈。 标记：和 BFS 一样同样需要对已经遍历过的节点进行标记。

1. 岛屿的最大面积

> 给定一个包含了一些 0 和 1的非空二维数组 grid , 一个 岛屿 是由四个方向 \(水平或垂直\) 的 1 \(代表土地\) 构成的组合。你可以假设二维矩阵的四个边缘都被水包围着。
>
> 找到给定的二维数组中最大的岛屿面积。\(如果没有岛屿，则返回面积为0。\)
>
> 示例 1:
>
> \[\[0,0,1,0,0,0,0,1,0,0,0,0,0\], \[0,0,0,0,0,0,0,1,1,1,0,0,0\], \[0,1,1,0,1,0,0,0,0,0,0,0,0\], \[0,1,0,0,1,1,0,0,1,0,1,0,0\], \[0,1,0,0,1,1,0,0,1,1,1,0,0\], \[0,0,0,0,0,0,0,0,0,0,1,0,0\], \[0,0,0,0,0,0,0,1,1,1,0,0,0\], \[0,0,0,0,0,0,0,1,1,0,0,0,0\]\] 对于上面这个给定矩阵应返回 6。注意答案不应该是11，因为岛屿只能包含水平或垂直的四个方向的‘1’。
>
> 示例 2:
>
> \[\[0,0,0,0,0,0,0,0\]\] 对于上面这个给定的矩阵, 返回 0。
>
> 注意: 给定的矩阵grid 的长度和宽度都不超过 50。

//只需要从每个1出发，然后遍历相连的所有1，得到总和，更新最大值即可。

```text
public static int maxAreaOfIsland(int[][] grid) {
        int [][]visit = new int[grid.length][grid[0].length];
        int max = 0;
        for (int i = 0;i < grid.length;i ++) {
            for (int j = 0;j < grid[0].length;j ++) {
                if (grid[i][j] == 1) {
                    max = Math.max(max, dfs(grid, i, j, visit, 0));
                }
            }
        }
        return max;
    }

    //通过递归进行了各个方向的可达性遍历，于是可以遍历到所有的1，然后更新最大值。
    
    public static int dfs(int [][]grid, int x, int y, int [][]visit, int count) {
        if (x < 0 || x > grid.length - 1 || y < 0 || y > grid[0].length - 1) {
            return count;
        }
        if (visit[x][y] == 1 || grid[x][y] == 0) {
            return count;
        }

        visit[x][y] = 1;
        count ++;

        count += dfs(grid, x + 1, y, visit, 0);
        count += dfs(grid, x - 1, y, visit, 0);
        count += dfs(grid, x, y + 1, visit, 0);
        count += dfs(grid, x, y - 1, visit, 0);
        return count;
    }
复制代码
```

1. 岛屿的个数

> 给定一个由 '1'（陆地）和 '0'（水）组成的的二维网格，计算岛屿的数量。一个岛被水包围，并且它是通过水平方向或垂直方向上相邻的陆地连接而成的。你可以假设网格的四个边均被水包围。
>
> 示例 1:
>
> 输入: 11110 11010 11000 00000
>
> 输出: 1 示例 2:
>
> 输入: 11000 11000 00100 00011
>
> 输出: 3

```text
public class 图的连通分量个数 {
    static int count = 0;
    public int findCircleNum(int[][] M) {
        count = 0;
        int []visit = new int[M.length];
        Arrays.fill(visit, 0);
        for (int i = 0;i < M.length;i ++) {
            if (visit[i] == 0) {
                dfs(M, i, visit);
                count ++;
            }
        }

        return count;
    }
    
    //每次访问把能到达的点标记为1，并且访问结束时计数加一。最终得到岛屿个数。
    public void dfs (int [][]M, int j, int []visit) {
        for (int i = 0;i < M.length;i ++) {
            if (M[j][i] == 1 && visit[i] == 0) {
                visit[i] = 1;
                dfs(M, i, visit);
            }
        }
    }

    
}
复制代码
```

1. 朋友圈

> 班上有 N 名学生。其中有些人是朋友，有些则不是。他们的友谊具有是传递性。如果已知 A 是 B 的朋友，B 是 C 的朋友，那么我们可以认为 A 也是 C 的朋友。所谓的朋友圈，是指所有朋友的集合。
>
> 给定一个 N \* N 的矩阵 M，表示班级中学生之间的朋友关系。如果M\[i\]\[j\] = 1，表示已知第 i 个和 j 个学生互为朋友关系，否则为不知道。你必须输出所有学生中的已知的朋友圈总数。
>
> 示例 1:
>
> 输入: \[\[1,1,0\], \[1,1,0\], \[0,0,1\]\] 输出: 2 说明：已知学生0和学生1互为朋友，他们在一个朋友圈。 第2个学生自己在一个朋友圈。所以返回2。 示例 2:
>
> 输入: \[\[1,1,0\], \[1,1,1\], \[0,1,1\]\] 输出: 1 说明：已知学生0和学生1互为朋友，学生1和学生2互为朋友，所以学生0和学生2也是朋友，所以他们三个在一个朋友圈，返回1。 注意：
>
> N 在\[1,200\]的范围内。 对于所有学生，有M\[i\]\[i\] = 1。 如果有M\[i\]\[j\] = 1，则有M\[j\]\[i\] = 1。

这题的答案是这样的：

```text
private int n;

public int findCircleNum(int[][] M) {
    n = M.length;
    int circleNum = 0;
    boolean[] hasVisited = new boolean[n];
    for (int i = 0; i < n; i++) {
        if (!hasVisited[i]) {
            dfs(M, i, hasVisited);
            circleNum++;
        }
    }
    return circleNum;
}

private void dfs(int[][] M, int i, boolean[] hasVisited) {
    hasVisited[i] = true;
    for (int k = 0; k < n; k++) {
        if (M[i][k] == 1 && !hasVisited[k]) {
            dfs(M, k, hasVisited);
        }
    }
}
复制代码
```

但是我的做法跟他一样，却会递归栈溢出，我只是把boolean判断换成了int判断，有点奇怪，还望指教。

```text
//    private static int n;
//    public static int findCircleNum(int[][] M) {
//        n = M.length;
//        int cnt = 0 ;
//        int []visit = new int[n];
//        for (int i = 0;i < M.length;i ++) {
//            if(visit[i] == 0)  {
//                dfs(M, visit, i);
//                cnt ++;
//            }
//        }
//        return cnt;
//    }
//    public static void dfs(int[][]M, int[] visit, int i) {
//        visit[i] = 1;
//        for (int j = 0;j < M.length;j ++) {
//            if(visit[j] == 0 && M[i][j] == 1)  {
//                dfs(M, visit, i);
//            }
//        }
//    }
复制代码
```

1. 被围绕的区域

> 给定一个二维的矩阵，包含 'X' 和 'O'（字母 O）。
>
> 找到所有被 'X' 围绕的区域，并将这些区域里所有的 'O' 用 'X' 填充。
>
> 示例:
>
> X X X X X O O X X X O X X O X X 运行你的函数后，矩阵变为：
>
> X X X X X X X X X X X X X O X X 解释:
>
> 被围绕的区间不会存在于边界上，换句话说，任何边界上的 'O' 都不会被填充为 'X'。 任何不在边界上，或不与边界上的 'O' 相连的 'O' 最终都会被填充为 'X'。如果两个元素在水平或垂直方向相邻，则称它们是“相连”的。

参考大佬答案：很有意思的解法

1 我们是要把X包围的O变成X但是有一个很麻烦的问题就是，如何判断O被X完全包住，这是非常难解决的。

2 于是换一种思路，把不被X包住的那些O找出来，剩下的不就是X了吗。

3 不被X包住的O，首先它们的起点一定是在边缘处，所以我们从边缘处找出一个O，然后从O出发，找到所有相连的O，把它们变成T（为了不跟里面的O混淆）

4 最后遍历一次棋盘，把T变成O，把O变成X，就搞定了。妙啊，妙啊。

```text
private int[][] direction = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
private int m, n;

public void solve(char[][] board) {
    if (board == null || board.length == 0) {
        return;
    }

    m = board.length;
    n = board[0].length;

    for (int i = 0; i < m; i++) {
        dfs(board, i, 0);
        dfs(board, i, n - 1);
    }
    for (int i = 0; i < n; i++) {
        dfs(board, 0, i);
        dfs(board, m - 1, i);
    }

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (board[i][j] == 'T') {
                board[i][j] = 'O';
            } else if (board[i][j] == 'O') {
                board[i][j] = 'X';
            }
        }
    }
}

private void dfs(char[][] board, int r, int c) {
    if (r < 0 || r >= m || c < 0 || c >= n || board[r][c] != 'O') {
        return;
    }
    board[r][c] = 'T';
    for (int[] d : direction) {
        dfs(board, r + d[0], c + d[1]);
    }
}
复制代码
```

417.能到达的太平洋和大西洋的区域

> 1. Pacific Atlantic Water Flow \(Medium\)
>
> Given the following 5x5 matrix:
>
> Pacific ~ ~ ~ ~ ~ ~ 1 2 2 3 \(5\) \* ~ 3 2 3 \(4\) \(4\) \* ~ 2 4 \(5\) 3 1 \* ~ \(6\) \(7\) 1 4 5 \* ~ \(5\) 1 1 2 4 \* \* \* \* \* \* Atlantic
>
> Return: \[\[0, 4\], \[1, 3\], \[1, 4\], \[2, 2\], \[3, 0\], \[3, 1\], \[4, 0\]\] \(positions with parentheses in above matrix\). 左边和上边是太平洋，右边和下边是大西洋，内部的数字代表海拔，海拔高的地方的水能够流到低的地方，求解水能够流到太平洋和大西洋的所有位置。

1 如果说上一题已经很有趣了，这一题可以说是更奇葩了。 力扣中国甚至没有翻译这道题。根据题意，我们要求的是水能流到太平洋和大西洋的所有点。

2 首先，在大西洋和 太平洋两边的水一定可以分别流入这两个海洋。 我们用一个数组canreach\[i\]\[j\]来表达能够流入到海洋。所以我么需要两个数组。

3 然后从海洋边上的水开始进行dfs，遇到海拔比自己高的水就把它也设置成canreach即可，于是我们就可以得到两个数组。最后遍历两个数组，都满足的点就是结果了。

```text
private int m, n;
private int[][] matrix;
private int[][] direction = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

public List<int[]> pacificAtlantic(int[][] matrix) {
    List<int[]> ret = new ArrayList<>();
    if (matrix == null || matrix.length == 0) {
        return ret;
    }

    m = matrix.length;
    n = matrix[0].length;
    this.matrix = matrix;
    boolean[][] canReachP = new boolean[m][n];
    boolean[][] canReachA = new boolean[m][n];

    for (int i = 0; i < m; i++) {
        dfs(i, 0, canReachP);
        dfs(i, n - 1, canReachA);
    }
    for (int i = 0; i < n; i++) {
        dfs(0, i, canReachP);
        dfs(m - 1, i, canReachA);
    }

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (canReachP[i][j] && canReachA[i][j]) {
                ret.add(new int[]{i, j});
            }
        }
    }

    return ret;
}

private void dfs(int r, int c, boolean[][] canReach) {
    if (canReach[r][c]) {
        return;
    }
    canReach[r][c] = true;
    for (int[] d : direction) {
        int nextR = d[0] + r;
        int nextC = d[1] + c;
        if (nextR < 0 || nextR >= m || nextC < 0 || nextC >= n
                || matrix[r][c] > matrix[nextR][nextC]) {

            continue;
        }
        dfs(nextR, nextC, canReach);
    }
}
复制代码
```

## Backtracking

Backtracking（回溯）属于 DFS。

普通 DFS 主要用在 可达性问题 ，这种问题只需要执行到特点的位置然后返回即可。 而 Backtracking 主要用于求解 排列组合 问题，例如有 { 'a','b','c' } 三个字符，求解所有由这三个字符排列得到的字符串，这种问题在执行到特定的位置返回之后还会继续执行求解过程。 因为 Backtracking 不是立即就返回，而要继续求解，因此在程序实现时，需要注意对元素的标记问题：

在访问一个新元素进入新的递归调用时，需要将新元素标记为已经访问，这样才能在继续递归调用时不用重复访问该元素； 但是在递归返回时，需要将元素标记为未访问，因为只需要保证在一个递归链中不同时访问一个元素，可以访问已经访问过但是不在当前递归链中的元素。

1. 电话号码的字母组合

> 给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。
>
> 给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![image](data:image/svg+xml;utf8,<?xml%20version=%221.0%22?%3E%3Csvg%20xmlns=%22http://www.w3.org/2000/svg%22%20version=%221.1%22%20width=%22200%22%20height=%22162%22%3E%3C/svg%3E)

> 示例:
>
> 输入："23" 输出：\["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"\].

解析：回溯法一般用于需要保存结果集的dfs，比如迷宫路径，走到头如果没有达到终点则回头。而一般的dfs走到头就开始尝试下一种情况，因为它没有保存结果集。

1 本题需要把数字键盘和字母做一个映射，使用数组是一个不错的办法。而使用hashmap可能会显得有点臃肿。

2 接着我们可以使用String或者Stringbuilder保存结果，由于string不会被改变，所以我们不需要维持其状态，直接递归即可。

而使用stringbuilder则需要在dfs前后维护相应变化。

```text
class Solution {
    public List<String> letterCombinations(String digits) {
        if (digits.equals("")) {
            return new ArrayList();
        }
        List<String> list = new ArrayList<>();
        String []arr = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        dfs(list, arr, new StringBuilder(), 0, digits);
        return list;
    }
    
    public void dfs(List<String> list, String []arr, StringBuilder sb, int cur, String digits) {
        if (cur == digits.length()) {
            list.add(sb.toString());
            return;
        }
        int num = Integer.parseInt(digits.substring(cur, cur + 1));
        
        for (int i = 0;i < arr[num].length();i ++) {
            sb.append(arr[num].charAt(i));
            dfs(list, arr, sb, cur + 1, digits);
            sb.deleteCharAt(sb.length() - 1);
        }
    }
}
复制代码
```

1. 复原IP地址

给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。

示例:

输入: "25525511135" 输出: \["255.255.11.135", "255.255.111.35"\]

解析：本题的解题思路不难，从左到右拼凑ip地址即可

但是需要判断每个段的ip是否有效，包括开头不能是0,长度&lt;=3,大小&lt;=255等。

下面是一个暴力解法，4重循环可以遍历所有情况。

```text
public List<String> restoreIpAddresses(String s) {
    int i,j,k,m;
    ArrayList <String> list = new ArrayList<>();
    int len = s.length();
    for (i = 1;i< 4 && i < len - 2;i ++) {
        for (j = i + 1;j < i + 4 && j < len - 1;j ++) {
            for (m = j + 1;m < j + 4 && m < len;m ++) {
                //substring后面的下标是不算在内的。
                String s1 = s.substring(0,i);
                String s2 = s.substring(i,j);
                String s3 = s.substring(j,m);
                String s4 = s.substring(m,len);
                if(isValid(s1) && isValid(s2) && isValid(s3) && isValid(s4))
                {
                    list.add(s1 + '.' + s2 + '.' + s3 + '.' + s4);
                }
            }
        }
    }
    return list;
}

public boolean isValid(String s) {
    if (s.length() == 0 || s.length() > 3 || s.charAt(0) == '0' && s.length() > 1
            || Integer.parseInt(s) > 255) {
        return false;
    }else return true;
}
复制代码
```

解析: 使用回溯法来做该题。首先从头开始，k代表ip的段数，s代表总长度，每次使用一个数字则s -= 1，直到s = 0并且k = 4时，符合题意，加入结果集。

```text
当然在for循环中，i 从0到2进行遍历。然后更新当前结果。

public List<String> restoreIpAddresses(String s) {
    List<String> addresses = new ArrayList<>();
    StringBuilder tempAddress = new StringBuilder();
    doRestore(0, tempAddress, addresses, s);
    return addresses;
}

private void doRestore(int k, StringBuilder tempAddress, List<String> addresses, String s) {
    if (k == 4 || s.length() == 0) {
        if (k == 4 && s.length() == 0) {
            addresses.add(tempAddress.toString());
        }
        return;
    }
    for (int i = 0; i < s.length() && i <= 2; i++) {
        if (i != 0 && s.charAt(0) == '0') {
            break;
        }
        String part = s.substring(0, i + 1);
        if (Integer.valueOf(part) <= 255) {
            if (tempAddress.length() != 0) {
                part = "." + part;
            }
            tempAddress.append(part);
            doRestore(k + 1, tempAddress, addresses, s.substring(i + 1));
            tempAddress.delete(tempAddress.length() - part.length(), tempAddress.length());
        }
    }
}
复制代码
```

1. 单词搜索

给定一个二维网格和一个单词，找出该单词是否存在于网格中。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

示例: board = \[ \['A','B','C','E'\], \['S','F','C','S'\], \['A','D','E','E'\] \]

给定 word = "ABCCED", 返回 true. 给定 word = "SEE", 返回 true. 给定 word = "ABCB", 返回 false.

本题使用dfs中的回溯法，主要思路也是递归。 但比较麻烦的一点是当遍历到满足条件的情况时，不应该继续其他递归分支了。有一个办法是使用for循环进行方向遍历，只要有一个分支满足就返回true。

```text
private final static int[][] direction = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
private int m;
private int n;

public boolean exist(char[][] board, String word) {
    if (word == null || word.length() == 0) {
        return true;
    }
    if (board == null || board.length == 0 || board[0].length == 0) {
        return false;
    }

    m = board.length;
    n = board[0].length;
    boolean[][] hasVisited = new boolean[m][n];

    for (int r = 0; r < m; r++) {
        for (int c = 0; c < n; c++) {
            if (backtracking(0, r, c, hasVisited, board, word)) {
                return true;
            }
        }
    }

    return false;
}

private boolean backtracking(int curLen, int r, int c, boolean[][] visited, final char[][] board, final String word) {
    if (curLen == word.length()) {
        return true;
    }
    if (r < 0 || r >= m || c < 0 || c >= n
            || board[r][c] != word.charAt(curLen) || visited[r][c]) {

        return false;
    }

    visited[r][c] = true;

    for (int[] d : direction) {
    //此处完成了剪枝，如果分支i满足则不会走后续分支。
        if (backtracking(curLen + 1, r + d[0], c + d[1], visited, board, word)) {
            return true;
        }
    }

    visited[r][c] = false;

    return false;
}
复制代码
```

解析：方法二：

首先使用dfs进行递归遍历，当满足条件时，直接抛出异常退出循环，脑洞比较大，但是确实可行，可以用于递归时的剪枝 。

但是实际上时间复杂度要比第一种方法大得多，这里只是提供一个终止递归的思路。

```text
class Solution {
    static class StopMsgException extends RuntimeException {
        
    }
    
    static boolean flag;
    
    public boolean exist(char[][] board, String word) {
        if (word.equals("")) {
            return true;
        }
        int [][]visit = new int[board.length][board[0].length];
        flag = false;
        try {
            for (int i = 0;i < board.length;i ++) {
                for (int j = 0;j < board[0].length;j ++) {
                    if (word.charAt(0) == board[i][j]) {
                        dfs(board, word, visit, i, j);
                    }
                }
            }
        } catch (StopMsgException e) {
            System.out.println(e);
        }
        return flag;
    }
    
    public void dfs(char[][] board, String word, int [][]visit, int i, int j) {
        if (word.equals("")) {
            flag = true;
            throw new StopMsgException();
        }
        if (i > board.length - 1 || i < 0 || j > board[0].length - 1 || j < 0) {
            return;
        }
        if (visit[i][j] == 1) {
            return;
        }
        
        if (word.charAt(0) == board[i][j]) {
            visit[i][j] = 1;
            //没有进行剪枝，效率比较低，于是在递归判断条件中进行剪枝，避免后续不必要的递归。
            
            dfs(board, word.length() == 1 ? "" : word.substring(1, word.length()), visit, i + 1, j);
            dfs(board, word.length() == 1 ? "" : word.substring(1, word.length()), visit, i - 1, j);
            dfs(board, word.length() == 1 ? "" : word.substring(1, word.length()), visit, i, j - 1);
            dfs(board, word.length() == 1 ? "" : word.substring(1, word.length()), visit, i, j + 1);
            visit[i][j] = 0;
        }
    }
}
复制代码
```

1. 全排列

> 给定一个没有重复数字的序列，返回其所有可能的全排列。
>
> 示例:
>
> 输入: \[1,2,3\] 输出: \[ \[1,2,3\], \[1,3,2\], \[2,1,3\], \[2,3,1\], \[3,1,2\], \[3,2,1\] \]

解析：经典而不失优雅。dfs中使用for循环遍历，visit和list记录状态进行回溯，满足条件时加入集合并返回。

```text
class Solution {
    static List<List<Integer>> Alllist = new ArrayList<>();
    public List<List<Integer>> permute(int[] nums) {
        Alllist.clear();
        List<Integer> list = new ArrayList<>();
        int []visit = new int[nums.length];
        dfs(list, nums, visit);
        return Alllist;
    }
    public void dfs(List<Integer> list, int []nums, int []visit) {
        if (list.size() == nums.length) {
            Alllist.add(new ArrayList(list));
            return;
        }
        
        for (int i = 0;i < nums.length;i ++) {
            if (visit[i] == 0) {
                visit[i] = 1;
                list.add(nums[i]);
                dfs(list, nums, visit);
                visit[i] = 0;
                list.remove(list.size() - 1);
            }
        }
    }
}
复制代码
```

1. 全排列 II

> 给定一个可包含重复数字的序列，返回所有不重复的全排列。
>
> 示例:
>
> 输入: \[1,1,2\] 输出: \[ \[1,1,2\], \[1,2,1\], \[2,1,1\] \]

```text
解析：本题在上一题的基础上加上了一个条件，数组中有重复数字，但是结果集返回的是不同的排列。
这就要求我们对相同数字做过滤了。

我们要明确的是，不重复排列，要求的是相同数字不能在同一次递归中出现在同一个位置。
比如 1 2 1 和1 2 1，这里开头的两个1可能分别对应数组下标的0和1，但只能取一个。

所以我们加了一个条件，当该数没有被访问过时，我们直接过滤掉所有重复的数，只把当前数作为本次递归的首位数。

class Solution {
        static List<List<Integer>> Alllist = new ArrayList<>();
        public List<List<Integer>> permuteUnique(int[] nums) {
            Alllist.clear();
            List<Integer> list = new ArrayList<>();
            int []visit = new int[nums.length];
            Arrays.sort(nums);
            dfs(list, nums, visit);
            return Alllist;
        }
        public void dfs(List<Integer> list, int []nums, int []visit) {
            if (list.size() == nums.length) {
                Alllist.add(new ArrayList(list));
                return;
            }
            
            for (int i = 0;i < nums.length;i ++) {
                if(i - 1 >= 0 && nums[i] == nums[i - 1] && visit[i - 1] == 0) {
                    continue;
                }
                if (visit[i] == 0) {
                    visit[i] = 1;
                    list.add(nums[i]);
                    dfs(list, nums, visit);
                    visit[i] = 0;
                    list.remove(list.size() - 1);
                }
            }
        }
    }
复制代码
```

1. 组合

给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。

示例:

输入: n = 4, k = 2 输出: \[ \[2,4\], \[3,4\], \[2,3\], \[1,2\], \[1,3\], \[1,4\], \]

解析:组合长度固定为k，不能有重复组合，所以我们规定顺序是从小到大进行组合，这样的话就不会有重复情况出现了。

```text
class Solution {
    static List<List<Integer>> Alllist = new ArrayList<>();
    public List<List<Integer>> combine(int n, int k) {
        Alllist.clear();
        List<Integer> list = new ArrayList<>();
        dfs(list, n, k, 1);
        return Alllist;
    }
    public void dfs(List<Integer> list, int n, int k, int cur) {
        if (list.size() == k) {
            Alllist.add(new ArrayList(list));
            return;
        }
        for (int i = cur;i <= n;i ++) {
            list.add(i);
            dfs(list, n, k, i + 1);
            list.remove(list.size() - 1);
        }
    }
}
复制代码
```

#### 39. 组合总和

> 给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
>
> candidates 中的数字可以无限制重复被选取。
>
> 说明：
>
> 所有数字（包括 target）都是正整数。 解集不能包含重复的组合。 示例 1:
>
> 输入: candidates = \[2,3,6,7\], target = 7, 所求解集为: \[ \[7\], \[2,2,3\] \] 示例 2:
>
> 输入: candidates = \[2,3,5\], target = 8, 所求解集为: \[ \[2,2,2,2\], \[2,3,3\], \[3,5\] \]

解析：由于组合中没有重复数字，并且每个数可以出现多次，所以我们依然可以按顺序进行组合，每次i取自己或比自己更大的数组下标即可。

```text
class Solution {
static List<List<Integer>> Alllist = new ArrayList<>();
public List<List<Integer>> combinationSum(int[] candidates, int target) {
    Alllist.clear();
    List<Integer> list = new ArrayList<>();
    Arrays.sort(candidates);
    dfs(list, candidates, 0, target, 0);
    return Alllist;
}
public void dfs(List<Integer> list, int [] candidates, int sum, int target, int cur) {
    if (sum == target) {
        Alllist.add(new ArrayList(list));
        return;
    }
    for (int i = cur;i < candidates.length;i ++) {
        if (sum + candidates[i] <= target) {
            list.add(candidates[i]);
            dfs(list, candidates, sum + candidates[i], target, i);
            list.remove(list.size() - 1);
        }
    }
}
复制代码
```

#### 40. 组合总和 II

> 给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
>
> candidates 中的每个数字在每个组合中只能使用一次。
>
> 说明：
>
> 所有数字（包括目标数）都是正整数。 解集不能包含重复的组合。 示例 1:
>
> 输入: candidates = \[10,1,2,7,6,1,5\], target = 8, 所求解集为: \[ \[1, 7\], \[1, 2, 5\], \[2, 6\], \[1, 1, 6\] \] 示例 2:
>
> 输入: candidates = \[2,5,2,1,2\], target = 5, 所求解集为: \[ \[1,2,2\], \[5\] \]

解析：本题类似排序的第二题，组合求和，但是组合不能重复，而数组中允许重复数字。

所以我们依然要过滤掉同一位置上的重复数字。

```text
class Solution {
    static List<List<Integer>> Alllist = new ArrayList<>();
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Alllist.clear();
        List<Integer> list = new ArrayList<>();
        int []visit = new int[candidates.length];
        Arrays.sort(candidates);
        dfs(list, candidates, 0, target, 0, visit);
        return Alllist;
    }
    public void dfs(List<Integer> list, int [] candidates, int sum, int target, int cur, int[] visit) {
        if (sum == target) {
            Alllist.add(new ArrayList(list));
            return;
        }
        for (int i = cur;i < candidates.length;i ++) {
            if (i - 1 >= 0 && candidates[i] == candidates[i - 1] && visit[i - 1] == 0) {
                continue;
            }
            if (sum + candidates[i] <= target) {
                visit[i] = 1;
                list.add(candidates[i]);
                dfs(list, candidates, sum + candidates[i], target, i + 1, visit);
                list.remove(list.size() - 1);
                visit[i] = 0;
            }
        }
    }
}
复制代码
```

#### 216. 组合总和 III

> 找出所有相加之和为 n 的 k 个数的组合。组合中只允许含有 1 - 9 的正整数，并且每种组合中不存在重复的数字。
>
> 说明：
>
> 所有数字都是正整数。 解集不能包含重复的组合。 示例 1:
>
> 输入: k = 3, n = 7 输出: \[\[1,2,4\]\] 示例 2:
>
> 输入: k = 3, n = 9 输出: \[\[1,2,6\], \[1,3,5\], \[2,3,4\]\]

解析：与前面类似，没啥难度

```text
class Solution {
    public List<List<Integer>> combinationSum3(int k, int n) {
        Alllist.clear();
        List<Integer> list = new ArrayList<>();
        dfs(list, 0, n, k, 1);
        return Alllist;
    }
    static List<List<Integer>> Alllist = new ArrayList<>();
    public void dfs(List<Integer> list, int sum, int n, int k, int cur) {
        if (sum == n && list.size() == k) {
            Alllist.add(new ArrayList(list));
            return;
        }
        for (int i = cur;i <= 9;i ++) {
            if (sum + i <= n && list.size() < k) {
                list.add(i);
                dfs(list, sum + i, n, k, i + 1);
                list.remove(list.size() - 1);
            }
        }
    }
}
复制代码
```

#### 78. 子集

> 给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。
>
> 说明：解集不能包含重复的子集。
>
> 示例:
>
> 输入: nums = \[1,2,3\] 输出: \[ \[3\], \[1\], \[2\], \[1,2,3\], \[1,3\], \[2,3\], \[1,2\], \[\] \]

解析：本题要注意的是，数字不重复，所以我们可以按顺序来查找子集，并且递归结束条件是当cur = nums.length。时结束。

```text
class Solution {
    List<List<Integer>> Alllist = new ArrayList<>();
    public List<List<Integer>> subsets(int[] nums) {
        List<Integer> list = new ArrayList<>();
        dfs(nums, 0, list);
        return Alllist;
    }
    
    public void dfs(int []nums, int cur, List<Integer> list) {
        if (cur <= nums.length) {
            Alllist.add(new ArrayList(list));
        } else {
            return;    
        }
        for (int i = cur;i < nums.length;i ++) {
            list.add(nums[i]);
            dfs(nums, i + 1, list);
            list.remove(list.size() - 1);
        }
    }
}
复制代码
```

#### 90. 子集 II

> 给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。
>
> 说明：解集不能包含重复的子集。
>
> 示例:
>
> 输入: \[1,2,2\] 输出: \[ \[2\], \[1\], \[1,2,2\], \[2,2\], \[1,2\], \[\] \]

```text
class Solution {
    List<List<Integer>> Alllist = new ArrayList<>();
    public List<List<Integer>> subsetsWithDup(int[] nums) {
            List<Integer> list = new ArrayList<>();
            Arrays.sort(nums);
            int []visit = new int[nums.length];
            dfs(nums, 0, visit, list);
            return Alllist;
    }
    public void dfs(int []nums, int cur, int []visit, List<Integer> list) {
            if (cur <= nums.length) {
                Alllist.add(new ArrayList(list));
            } else {
                return;    
            }
            for (int i = cur;i < nums.length;i ++) {
                if (i > 0 && visit[i - 1] == 0 && nums[i] == nums[i - 1]) {
                    continue;
                }
                if (visit[i] == 0) {
                    visit[i] = 1;
                    list.add(nums[i]);
                    dfs(nums, i + 1,visit, list);
                    list.remove(list.size() - 1);
                    visit[i] = 0;
                }
            }
    }
}
复制代码
```

#### 131. 分割回文串

> 给定一个字符串 s，将 s 分割成一些子串，使每个子串都是回文串。
>
> 返回 s 所有可能的分割方案。
>
> 示例:
>
> 输入: "aab" 输出: \[ \["aa","b"\], \["a","a","b"\] \]

解析： 这题其实也类似，只不过比较巧妙一点。

1 要把字符串分割成回文字符串组合，那么我们要存的其实还是所有的字符串，所以返回条件还是当cur = s.length\(\)。

2 字符串分割，首先在循环中遍历满足回文的第一个子串，在此基础上找下一个回文子串，达到了剪枝的目的。

```text
class Solution {
    List<List<String>> allList = new ArrayList<>();
    public List<List<String>> partition(String s) {
        List<String> list = new ArrayList<>();
        dfs(s, 0, list);
        return allList;
    }
    public void dfs(String s, int cur, List<String> list) {
        if (cur == s.length()) {
            allList.add(new ArrayList(list));
            return;
        }
        for (int i = cur;i < s.length();i ++) {
            String str = s.substring(cur, i + 1);
            if (legal(str)) {
                list.add(str);
                dfs(s, i + 1, list);
                list.remove(list.size() - 1);
            }
        }
    }
    public boolean legal(String s) {
        int i = 0,j = s.length() - 1;
        while (i < j) {
            if (s.charAt(i) == s.charAt(j)) {
                i ++;
                j --;
            }else {
                return false;
            }
        }
        return true;
    }
}
复制代码
```

## 动态规划

动态规划 递归和动态规划都是将原问题拆成多个子问题然后求解，他们之间最本质的区别是，动态规划保存了子问题的解，避免重复计算。

#### 第一大类：斐波那契数列型，选择n - 1或n - 2的一种

爬楼梯

#### 70. Climbing Stairs \(Easy\)

> 题目描述：有 N 阶楼梯，每次可以上一阶或者两阶，求有多少种上楼梯的方法。
>
> 定义一个数组 dp 存储上楼梯的方法数（为了方便讨论，数组下标从 1 开始），dp\[i\] 表示走到第 i 个楼梯的方法数目。第 i 个楼梯可以从第 i-1 和 i-2 个楼梯再走一步到达，走到第 i 个楼梯的方法数为走到第 i-1 和第 i-2 个楼梯的方法数之和。

dp\[N\] 即为所求。

考虑到 dp\[i\] 只与 dp\[i - 1\] 和 dp\[i - 2\] 有关，因此可以只用两个变量来存储 dp\[i - 1\] 和 dp\[i - 2\]，使得原来的 O\(N\) 空间复杂度优化为 O\(1\) 复杂度。

```text
public int climbStairs(int n) {
    if (n <= 2) {
        return n;
    }
    int pre2 = 1, pre1 = 2;
    for (int i = 2; i < n; i++) {
        int cur = pre1 + pre2;
        pre2 = pre1;
        pre1 = cur;
    }
    return pre1;
}
复制代码
```

#### 198. 打家劫舍

>

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

> 给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。
>
> 示例 1:
>
> 输入: \[1,2,3,1\] 输出: 4 解释: 偷窃 1 号房屋 \(金额 = 1\) ，然后偷窃 3 号房屋 \(金额 = 3\)。 偷窃到的最高金额 = 1 + 3 = 4 。

解析：注意抢劫的时候，对于 \[1,2,3,4\]，要么偷1，要么投2。 但是对于4来说，前面的偷法可以分为两种，要么偷1不偷2和3，要么偷2不偷3。

举个例子\[100,2,3,4\]偷4的时候，我们应该放弃2和3，直接偷100。同时我们不用再往后判断了，因为前面也考虑到了这种情况。

```text
public static int rob(int[] nums) {
    if (nums == null || nums.length == 0) return 0;
    if (nums.length == 1)return nums[0];
    if (nums.length == 2)return nums[0] > nums[1] ? nums[0] : nums[1];
    int []dp = new int[nums.length + 1];
    //dp代表最右只抢到第n家时的总钱数。
    dp[1] = nums[0];
    dp[2] = nums[0] > nums[1] ? nums[0] : nums[1];
    for (int i = 3;i <= nums.length;i ++) {
        dp[i] = Math.max(dp[i - 2] + nums[i - 1], dp[i - 1]);
    }
    return dp[nums.length];
}
复制代码
```

#### 213. 打家劫舍 II

> 你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都围成一圈，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。
>
> 给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。
>
> 示例 1:
>
> 输入: \[2,3,2\] 输出: 3 解释: 你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。 示例 2:
>
> 输入: \[1,2,3,1\] 输出: 4 解释: 你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。 偷窃到的最高金额 = 1 + 3 = 4 。

解析：环形区域主要考虑两种情况，第一家要抢的话，最后一家一定不能抢，第一家不抢的话，最后一家可以抢也可以不抢。然后根据上一题的做法使用dp即可。

```text
public static int rob(int[] nums) {

    if (nums == null || nums.length == 0) return 0;
    if (nums.length == 1)return nums[0];
    if (nums.length == 2)return nums[0] > nums[1] ? nums[0] : nums[1];
    int []dp = new int[nums.length + 1];
    //dp代表最右只抢到第n家时的总钱数。
    //如果抢了第一家
    dp[1] = nums[0];
    dp[2] = nums[0] > nums[1] ? nums[0] : nums[1];
    for (int i = 3;i < nums.length;i ++) {
        dp[i] = Math.max(dp[i - 2] + nums[i - 1], dp[i - 1]);
    }
    int max = dp[nums.length - 1];
    //如果不抢第一家
    dp[1] = 0;
    dp[2] = nums[1];
    for (int i = 3;i <= nums.length;i ++) {
        dp[i] = Math.max(dp[i - 2] + nums[i - 1], dp[i - 1]);
    }

    if (dp[nums.length]  > max)max = dp[nums.length];
    return max;
}
复制代码
```

> 母牛生产
>
> 程序员代码面试指南-P181
>
> 题目描述：假设农场中成熟的母牛每年都会生 1 头小母牛，并且永远不会死。第一年有 1 只小母牛，从第二年开始，母牛开始生小母牛。每只小母牛 3 年之后成熟又可以生小母牛。给定整数 N，求 N 年后牛的数量。
>
> 第 i 年成熟的牛的数量为：

> 信件错排
>
> 题目描述：有 N 个 信 和 信封，它们被打乱，求错误装信方式的数量。
>
> 定义一个数组 dp 存储错误方式数量，dp\[i\] 表示前 i 个信和信封的错误方式数量。假设第 i 个信装到第 j 个信封里面，而第 j 个信装到第 k 个信封里面。根据 i 和 k 是否相等，有两种情况：
>
> i==k，交换 i 和 k 的信后，它们的信和信封在正确的位置，但是其余 i-2 封信有 dp\[i-2\] 种错误装信的方式。由于 j 有 i-1 种取值，因此共有 \(i-1\)\*dp\[i-2\] 种错误装信方式。 i != k，交换 i 和 j 的信后，第 i 个信和信封在正确的位置，其余 i-1 封信有 dp\[i-1\] 种错误装信方式。由于 j 有 i-1 种取值，因此共有 \(i-1\)\*dp\[i-1\] 种错误装信方式。 综上所述，错误装信数量方式数量为：
>
> dp\[N\] 即为所求。

#### 第二大类：矩阵路径

求矩阵路径和，走法总数或者最短距离等

#### 64. 最小路径和

> 给定一个包含非负整数的 m x n 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。
>
> 说明：每次只能向下或者向右移动一步。
>
> 示例:
>
> 输入: \[ \[1,3,1\], \[1,5,1\], \[4,2,1\] \] 输出: 7 解释: 因为路径 1→3→1→1→1 的总和最小。

```text
class Solution {
    public int minPathSum(int[][] grid) {
        int [][]dp = new int[grid.length][grid[0].length];
        dp[0][0] = grid[0][0];
        for (int i = 1;i < grid.length;i ++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }
        for (int i = 1;i < grid[0].length;i ++) {
            dp[0][i] = dp[0][i - 1] + grid[0][i];
        }
        for (int i = 1;i < grid.length;i ++) {
            for (int j = 1;j < grid[0].length;j ++) {
                dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }            
        }
        return dp[grid.length - 1][grid[0].length - 1];
    }
}
复制代码
```

#### 62. 不同路径

> 一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。
>
> 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
>
> 问总共有多少条不同的路径？
>
> 例如，上图是一个7 x 3 的网格。有多少可能的路径？
>
> 说明：m 和 n 的值均不超过 100。
>
> 示例 1:
>
> 输入: m = 3, n = 2 输出: 3 解释: 从左上角开始，总共有 3 条路径可以到达右下角。
>
> 1. 向右 -&gt; 向右 -&gt; 向下
> 2. 向右 -&gt; 向下 -&gt; 向右
> 3. 向下 -&gt; 向右 -&gt; 向右 示例 2:
>
> 输入: m = 7, n = 3 输出: 28

```text
class Solution {
    public int uniquePaths(int m, int n) {
        int [][]dp = new int[m][n];
        for (int i = 0; i < m;i ++) {
            dp[i][0] = 1;
        }
        for (int i = 0; i < n;i ++) {
            dp[0][i] = 1;
        }
        for (int i = 1;i < m;i ++) {
            for (int j = 1;j < n;j ++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }        
        }
        return dp[m - 1][n - 1];
    }
}
复制代码
```

#### 第三大类：数组区间

#### 53. 最大子序和

> 给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
>
> 示例:
>
> 输入: \[-2,1,-3,4,-1,2,1,-5,4\], 输出: 6 解释: 连续子数组 \[4,-1,2,1\] 的和最大，为 6。 进阶:
>
> 如果你已经实现复杂度为 O\(n\) 的解法，尝试使用更为精妙的分治法求解。
>
> 解析：设置数组dp表示结尾为第i个数的最大子数组和是多少。然后方程是dp\[i\] = Math.max\(dp\[i - 1\] + nums\[i\], nums\[i\]\)
>
> 最后遍历一下dp数组找到最大子序和即可，

```text
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }
        if (nums.length == 1) {
            return nums[0];
        }
        int []dp = new int[nums.length];
        dp[0] = nums[0];
        for (int i = 1;i < nums.length;i ++) {
            dp[i] = Math.max(dp[i - 1] + nums[i], nums[i]);
        }
        int max = nums[0];
        for (int i = 0;i < dp.length;i ++) {
            max = Math.max(max, dp[i]);
        }
        return max;
    }
}
复制代码
```

#### 第四类：分割整数

#### 343. 整数拆分

> 给定一个正整数 n，将其拆分为至少两个正整数的和，并使这些整数的乘积最大化。 返回你可以获得的最大乘积。
>
> 例如，给定 n = 2，返回1（2 = 1 + 1）；给定 n = 10，返回36（10 = 3 + 3 + 4）。
>
> 注意：你可以假设 n 不小于2且不大于58。

参考答案：

解析：对于一个数来说，拆分包括两种情况，一种是把i拆成j和i - j，另一种是i拆成j与i - j这个数所能构成的组合。 所以方程就是dp\[i\] = Math.max\(dp\[i\], Math.max\(j \* dp\[i - j\], j \* \(i - j\)\)\);

由于dp代表的是数字n能够得到的最大乘积，为了满足上面这个式子，dp\[2\] = dp\[1\] \* 1，所以dp\[1\] = 1;

```text
public int integerBreak(int n) {
    int[] dp = new int[n + 1];
    dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        for (int j = 1; j <= i - 1; j++) {
            dp[i] = Math.max(dp[i], Math.max(j * dp[i - j], j * (i - j)));
        }
    }
    return dp[n];
}
复制代码
```

1. 完全平方数

> 给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。
>
> 示例 1:
>
> 输入: n = 12 输出: 3 解释: 12 = 4 + 4 + 4. 示例 2:
>
> 输入: n = 13 输出: 2 解释: 13 = 4 + 9.

解析：恕在下直言，有些DP的题目也是神仙题，确实不容易想到。dp代表的和为n的完全平方数所需的最少个数。

本题还可以用bfs来做，详细可参见上面的bfs部分。

dp\[1\] = 1。所以对于每个i,我们都要遍历所有小于等于它的平方数，以便找到所需个数最少的个数。

方程写作：对于每个数i有一个最小值dp\[i\] min = Math.min\(min, dp\[i - square\] + 1\); 最后dp\[n\]即为所求。

非常巧妙，值得学习和记忆。

```text
public int numSquares(int n) {
    List<Integer> squareList = generateSquareList(n);
    int[] dp = new int[n + 1];
    for (int i = 1; i <= n; i++) {
        int min = Integer.MAX_VALUE;
        for (int square : squareList) {
            if (square > i) {
                break;
            }
            min = Math.min(min, dp[i - square] + 1);
        }
        dp[i] = min;
    }
    return dp[n];
}

private List<Integer> generateSquareList(int n) {
    List<Integer> squareList = new ArrayList<>();
    int diff = 3;
    int square = 1;
    while (square <= n) {
        squareList.add(square);
        square += diff;
        diff += 2;
    }
    return squareList;
}
复制代码
```

#### 第五类：最长递增子序列

> 最长递增子序列 已知一个序列 {S1, S2,...,Sn} ，取出若干数组成新的序列 {Si1, Si2,..., Sim}，其中 i1、i2 ... im 保持递增，即新序列中各个数仍然保持原数列中的先后顺序，称新序列为原序列的一个 子序列 。
>
> 如果在子序列中，当下标 ix &gt; iy 时，Six &gt; Siy，称子序列为原序列的一个 递增子序列 。
>
> 定义一个数组 dp 存储最长递增子序列的长度，dp\[n\] 表示以 Sn 结尾的序列的最长递增子序列长度。对于一个递增子序列 {Si1, Si2,...,Sim}，如果 im &lt; n 并且 Sim &lt; Sn ，此时 {Si1, Si2,..., Sim, Sn} 为一个递增子序列，递增子序列的长度增加 1。满足上述条件的递增子序列中，长度最长的那个递增子序列就是要找的，在长度最长的递增子序列上加上 Sn 就构成了以 Sn 为结尾的最长递增子序列。因此 dp\[n\] = max{ dp\[i\]+1 \| Si &lt; Sn && i &lt; n} 。
>
> 因为在求 dp\[n\] 时可能无法找到一个满足条件的递增子序列，此时 {Sn} 就构成了递增子序列，需要对前面的求解方程做修改，令 dp\[n\] 最小为 1，即：

解析: dp数组代表以第i个元素作为序列结尾时的最大递增序列，由于之前的序列是可选的，所以我们遍历之前所有满足情况的点（也就是满足nums\[i\] &gt; nums\[j\]点），找到前面最长的递增序列即可。

转移方程是 dp\[i\] = Math.max\(dp\[j\] + 1, dp\[i\]\);

```text
class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length == 0)return 0;
        if (nums.length == 1)return 1;
        int []dp = new int[nums.length];
        dp[0] = 1;
        
        for (int i = 1;i < nums.length;i ++) {
            for (int j = 0;j < i;j ++) {
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[j] + 1, dp[i]);
                }else {
                    dp[i] = Math.max(dp[i], 1);
                }
            }
        }
        
        int max = 0;
        for (int i = 0;i < dp.length;i ++) {
            max = Math.max(max, dp[i]);
        }
        return max;
    }
}
复制代码
```

#### 第六类：最长公共子序列

> 最长公共子序列 对于两个子序列 S1 和 S2，找出它们最长的公共子序列。
>
> 定义一个二维数组 dp 用来存储最长公共子序列的长度，其中 dp\[i\]\[j\] 表示 S1 的前 i 个字符与 S2 的前 j 个字符最长公共子序列的长度。考虑 S1i 与 S2j 值是否相等，分为两种情况：
>
> 当 S1i==S2j 时，那么就能在 S1 的前 i-1 个字符与 S2 的前 j-1 个字符最长公共子序列的基础上再加上 S1i 这个值，最长公共子序列长度加 1 ，即 dp\[i\]\[j\] = dp\[i-1\]\[j-1\] + 1。 当 S1i != S2j 时，此时最长公共子序列为 S1 的前 i-1 个字符和 S2 的前 j 个字符最长公共子序列，与 S1 的前 i 个字符和 S2 的前 j-1 个字符最长公共子序列，它们的最大者，即 dp\[i\]\[j\] = max{ dp\[i-1\]\[j\], dp\[i\]\[j-1\] }。 综上，最长公共子序列的状态转移方程为：

```text
if (nums1[i - 1] == nums2[j - 1]) {
    dp[i][j] = dp[i - 1][j - 1] + 1;
} else {
    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
} 
复制代码
```

> 对于长度为 N 的序列 S1 和 长度为 M 的序列 S2，dp\[N\]\[M\] 就是序列 S1 和序列 S2 的最长公共子序列长度。
>
> 与最长递增子序列相比，最长公共子序列有以下不同点：
>
> 针对的是两个序列，求它们的最长公共子序列。 在最长递增子序列中，dp\[i\] 表示以 Si 为结尾的最长递增子序列长度，子序列必须包含 Si ；在最长公共子序列中，dp\[i\]\[j\] 表示 S1 中前 i 个字符与 S2 中前 j 个字符的最长公共子序列长度，不一定包含 S1i 和 S2j 。 在求最终解时，最长公共子序列中 dp\[N\]\[M\] 就是最终解，而最长递增子序列中 dp\[N\] 不是最终解，因为以 SN 为结尾的最长递增子序列不一定是整个序列最长递增子序列，需要遍历一遍 dp 数组找到最大者。

```text
public int lengthOfLCS(int[] nums1, int[] nums2) {
    int n1 = nums1.length, n2 = nums2.length;
    int[][] dp = new int[n1 + 1][n2 + 1];
    for (int i = 1; i <= n1; i++) {
        for (int j = 1; j <= n2; j++) {
            if (nums1[i - 1] == nums2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    return dp[n1][n2];
}
复制代码
```

#### 第七类：01背包

> 0-1 背包 有一个容量为 N 的背包，要用这个背包装下物品的价值最大，这些物品有两个属性：体积 w 和价值 v。
>
> 定义一个二维数组 dp 存储最大价值，其中 dp\[i\]\[j\] 表示前 i 件物品体积不超过 j 的情况下能达到的最大价值。设第 i 件物品体积为 w，价值为 v，根据第 i 件物品是否添加到背包中，可以分两种情况讨论：
>
> 第 i 件物品没添加到背包，总体积不超过 j 的前 i 件物品的最大价值就是总体积不超过 j 的前 i-1 件物品的最大价值，dp\[i\]\[j\] = dp\[i-1\]\[j\]。 第 i 件物品添加到背包中，dp\[i\]\[j\] = dp\[i-1\]\[j-w\] + v。 第 i 件物品可添加也可以不添加，取决于哪种情况下最大价值更大。
>
> 综上，0-1 背包的状态转移方程为：

```text
public int knapsack(int W, int N, int[] weights, int[] values) {
    int[][] dp = new int[N + 1][W + 1];
    for (int i = 1; i <= N; i++) {
        int w = weights[i - 1], v = values[i - 1];
        for (int j = 1; j <= W; j++) {
            if (j >= w) {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - w] + v);
            } else {
                dp[i][j] = dp[i - 1][j];
            }
        }
    }
    return dp[N][W];
}
复制代码
```

在程序实现时可以对 0-1 背包做优化。观察状态转移方程可以知道，前 i 件物品的状态仅由前 i-1 件物品的状态有关，因此可以将 dp 定义为一维数组，其中 dp\[j\] 既可以表示 dp\[i-1\]\[j\] 也可以表示 dp\[i\]\[j\]。此时，

因为 dp\[j-w\] 表示 dp\[i-1\]\[j-w\]，因此不能先求 dp\[i\]\[j-w\]，以防止将 dp\[i-1\]\[j-w\] 覆盖。也就是说要先计算 dp\[i\]\[j\] 再计算 dp\[i\]\[j-w\]，在程序实现时需要按倒序来循环求解。

public int knapsack\(int W, int N, int\[\] weights, int\[\] values\) { int\[\] dp = new int\[W + 1\]; for \(int i = 1; i &lt;= N; i++\) { int w = weights\[i - 1\], v = values\[i - 1\]; for \(int j = W; j &gt;= 1; j--\) { if \(j &gt;= w\) { dp\[j\] = Math.max\(dp\[j\], dp\[j - w\] + v\); } } } return dp\[W\]; } 无法使用贪心算法的解释

0-1 背包问题无法使用贪心算法来求解，也就是说不能按照先添加性价比最高的物品来达到最优，这是因为这种方式可能造成背包空间的浪费，从而无法达到最优。考虑下面的物品和一个容量为 5 的背包，如果先添加物品 0 再添加物品 1，那么只能存放的价值为 16，浪费了大小为 2 的空间。最优的方式是存放物品 1 和物品 2，价值为 22.

id w v v/w 0 1 6 6 1 2 10 5 2 3 12 4 变种

完全背包：物品数量为无限个

多重背包：物品数量有限制

多维费用背包：物品不仅有重量，还有体积，同时考虑这两种限制

其它：物品之间相互约束或者依赖

划分数组为和相等的两部分

1. Partition Equal Subset Sum \(Medium\)

> Input: \[1, 5, 11, 5\]
>
> Output: true
>
> Explanation: The array can be partitioned as \[1, 5, 5\] and \[11\]. 可以看成一个背包大小为 sum/2 的 0-1 背包问题。

```text
public boolean canPartition(int[] nums) {
    int sum = computeArraySum(nums);
    if (sum % 2 != 0) {
        return false;
    }
    int W = sum / 2;
    boolean[] dp = new boolean[W + 1];
    dp[0] = true;
    Arrays.sort(nums);
    for (int num : nums) {                 // 0-1 背包一个物品只能用一次
        for (int i = W; i >= num; i--) {   // 从后往前，先计算 dp[i] 再计算 dp[i-num]
            dp[i] = dp[i] || dp[i - num];
        }
    }
    return dp[W];
}

private int computeArraySum(int[] nums) {
    int sum = 0;
    for (int num : nums) {
        sum += num;
    }
    return sum;
}
复制代码
```

找零钱的方法数

1. Coin Change \(Medium\)

Example 1: coins = \[1, 2, 5\], amount = 11 return 3 \(11 = 5 + 5 + 1\)

Example 2: coins = \[2\], amount = 3 return -1. 题目描述：给一些面额的硬币，要求用这些硬币来组成给定面额的钱数，并且使得硬币数量最少。硬币可以重复使用。

物品：硬币 物品大小：面额 物品价值：数量 因为硬币可以重复使用，因此这是一个完全背包问题。

```text
public int coinChange(int[] coins, int amount) {
    if (coins == null || coins.length == 0) {
        return 0;
    }
    int[] minimum = new int[amount + 1];
    Arrays.fill(minimum, amount + 1);
    minimum[0] = 0;
    Arrays.sort(coins);
    for (int i = 1; i <= amount; i++) {
        for (int j = 0; j < coins.length && coins[j] <= i; j++) {
            minimum[i] = Math.min(minimum[i], minimum[i - coins[j]] + 1);
        }
    }
    return minimum[amount] > amount ? -1 : minimum[amount];
}
复制代码
```

#### 第八类：股票买卖

只能进行 k 次的股票交易

1. Best Time to Buy and Sell Stock IV \(Hard\)

```text
public int maxProfit(int k, int[] prices) {
    int n = prices.length;
    if (k >= n / 2) {   // 这种情况下该问题退化为普通的股票交易问题
        int maxProfit = 0;
        for (int i = 1; i < n; i++) {
            if (prices[i] > prices[i - 1]) {
                maxProfit += prices[i] - prices[i - 1];
            }
        }
        return maxProfit;
    }
    int[][] maxProfit = new int[k + 1][n];
    for (int i = 1; i <= k; i++) {
        int localMax = maxProfit[i - 1][0] - prices[0];
        for (int j = 1; j < n; j++) {
            maxProfit[i][j] = Math.max(maxProfit[i][j - 1], prices[j] + localMax);
            localMax = Math.max(localMax, maxProfit[i - 1][j] - prices[j]);
        }
    }
    return maxProfit[k][n - 1];
}
复制代码
```

只能进行两次的股票交易

1. Best Time to Buy and Sell Stock III \(Hard\)

```text
public int maxProfit(int[] prices) {
    int firstBuy = Integer.MIN_VALUE, firstSell = 0;
    int secondBuy = Integer.MIN_VALUE, secondSell = 0;
    for (int curPrice : prices) {
        if (firstBuy < -curPrice) {
            firstBuy = -curPrice;
        }
        if (firstSell < firstBuy + curPrice) {
            firstSell = firstBuy + curPrice;
        }
        if (secondBuy < firstSell - curPrice) {
            secondBuy = firstSell - curPrice;
        }
        if (secondSell < secondBuy + curPrice) {
            secondSell = secondBuy + curPrice;
        }
    }
    return secondSell;
}
复制代码
```

需要冷却期的股票交易

1. Best Time to Buy and Sell Stock with Cooldown\(Medium\)

题目描述：交易之后需要有一天的冷却时间。

```text
public int maxProfit(int[] prices) {
    if (prices == null || prices.length == 0) {
        return 0;
    }
    int N = prices.length;
    int[] buy = new int[N];
    int[] s1 = new int[N];
    int[] sell = new int[N];
    int[] s2 = new int[N];
    s1[0] = buy[0] = -prices[0];
    sell[0] = s2[0] = 0;
    for (int i = 1; i < N; i++) {
        buy[i] = s2[i - 1] - prices[i];
        s1[i] = Math.max(buy[i - 1], s1[i - 1]);
        sell[i] = Math.max(buy[i - 1], s1[i - 1]) + prices[i];
        s2[i] = Math.max(s2[i - 1], sell[i - 1]);
    }
    return Math.max(sell[N - 1], s2[N - 1]);
}
复制代码
```

需要交易费用的股票交易

1. Best Time to Buy and Sell Stock with Transaction Fee \(Medium\)

Input: prices = \[1, 3, 2, 8, 4, 9\], fee = 2 Output: 8 Explanation: The maximum profit can be achieved by: Buying at prices\[0\] = 1 Selling at prices\[3\] = 8 Buying at prices\[4\] = 4 Selling at prices\[5\] = 9 The total profit is \(\(8 - 1\) - 2\) + \(\(9 - 4\) - 2\) = 8. 题目描述：每交易一次，都要支付一定的费用。

```text
public int maxProfit(int[] prices, int fee) {
    int N = prices.length;
    int[] buy = new int[N];
    int[] s1 = new int[N];
    int[] sell = new int[N];
    int[] s2 = new int[N];
    s1[0] = buy[0] = -prices[0];
    sell[0] = s2[0] = 0;
    for (int i = 1; i < N; i++) {
        buy[i] = Math.max(sell[i - 1], s2[i - 1]) - prices[i];
        s1[i] = Math.max(buy[i - 1], s1[i - 1]);
        sell[i] = Math.max(buy[i - 1], s1[i - 1]) - fee + prices[i];
        s2[i] = Math.max(s2[i - 1], sell[i - 1]);
    }
    return Math.max(sell[N - 1], s2[N - 1]);
}
复制代码
```

买入和售出股票最大的收益

1. Best Time to Buy and Sell Stock \(Easy\)

题目描述：只进行一次交易。

只要记录前面的最小价格，将这个最小价格作为买入价格，然后将当前的价格作为售出价格，查看当前收益是不是最大收益。

```text
public int maxProfit(int[] prices) {
    int n = prices.length;
    if (n == 0) return 0;
    int soFarMin = prices[0];
    int max = 0;
    for (int i = 1; i < n; i++) {
        if (soFarMin > prices[i]) soFarMin = prices[i];
        else max = Math.max(max, prices[i] - soFarMin);
    }
    return max;
}
复制代码
```

#### 第九类：字符串编辑

删除两个字符串的字符使它们相等

1. Delete Operation for Two Strings \(Medium\)

Input: "sea", "eat" Output: 2 Explanation: You need one step to make "sea" to "ea" and another step to make "eat" to "ea". 可以转换为求两个字符串的最长公共子序列问题。

```text
public int minDistance(String word1, String word2) {
    int m = word1.length(), n = word2.length();
    int[][] dp = new int[m + 1][n + 1];
    for (int i = 0; i <= m; i++) {
        for (int j = 0; j <= n; j++) {
            if (i == 0 || j == 0) {
                continue;
            }
            if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
            }
        }
    }
    return m + n - 2 * dp[m][n];
}
复制代码
```

复制粘贴字符

1. 2 Keys Keyboard \(Medium\)

题目描述：最开始只有一个字符 A，问需要多少次操作能够得到 n 个字符 A，每次操作可以复制当前所有的字符，或者粘贴。

Input: 3 Output: 3 Explanation: Intitally, we have one character 'A'. In step 1, we use Copy All operation. In step 2, we use Paste operation to get 'AA'. In step 3, we use Paste operation to get 'AAA'.

```text
public int minSteps(int n) {
    if (n == 1) return 0;
    for (int i = 2; i <= Math.sqrt(n); i++) {
        if (n % i == 0) return i + minSteps(n / i);
    }
    return n;
}
public int minSteps(int n) {
    int[] dp = new int[n + 1];
    int h = (int) Math.sqrt(n);
    for (int i = 2; i <= n; i++) {
        dp[i] = i;
        for (int j = 2; j <= h; j++) {
            if (i % j == 0) {
                dp[i] = dp[j] + dp[i / j];
                break;
            }
        }
    }
    return dp[n];
}    
复制代码
```

## 位运算

## 数学

数学 素数 素数分解

每一个数都可以分解成素数的乘积，例如 84 = 22 \* 31 \* 50 \* 71 \* 110 \* 130 \* 170 \* …

整除

令 x = 2m0 \* 3m1 \* 5m2 \* 7m3 \* 11m4 \* …

令 y = 2n0 \* 3n1 \* 5n2 \* 7n3 \* 11n4 \* …

如果 x 整除 y（y mod x == 0），则对于所有 i，mi &lt;= ni。

最大公约数最小公倍数

x 和 y 的最大公约数为：gcd\(x,y\) = 2min\(m0,n0\) \* 3min\(m1,n1\) \* 5min\(m2,n2\) \* ...

x 和 y 的最小公倍数为：lcm\(x,y\) = 2max\(m0,n0\) \* 3max\(m1,n1\) \* 5max\(m2,n2\) \* ...

## 分治

1. 为运算表达式设计优先级

> 给定一个含有数字和运算符的字符串，为表达式添加括号，改变其运算优先级以求出不同的结果。你需要给出所有可能的组合的结果。有效的运算符号包含 +, - 以及 \* 。
>
> 示例 1:
>
> 输入: "2-1-1" 输出: \[0, 2\] 解释: \(\(2-1\)-1\) = 0 \(2-\(1-1\)\) = 2 示例 2:
>
> 输入: "2_3-4_5" 输出: \[-34, -14, -10, -10, 10\] 解释: \(2\*\(3-\(4_5\)\)\) = -34 \(\(2_3\)-\(4_5\)\) = -14 \(\(2_\(3-4\)\)_5\) = -10 \(2_\(\(3-4\)_5\)\) = -10 \(\(\(2_3\)-4\)\*5\) = 10

#### Tri

Trie，又称前缀树或字典树，用于判断字符串是否存在或者是否具有某种字符串前缀。

**实现一个 Trie**

[208. Implement Trie \(Prefix Tree\) \(Medium\)](https://leetcode.com/problems/implement-trie-prefix-tree/description/)

```text
class Trie {

    private class Node {
        Node[] childs = new Node[26];
        boolean isLeaf;
    }

    private Node root = new Node();

    public Trie() {
    }

    public void insert(String word) {
        insert(word, root);
    }

    private void insert(String word, Node node) {
        if (node == null) return;
        if (word.length() == 0) {
            node.isLeaf = true;
            return;
        }
        int index = indexForChar(word.charAt(0));
        if (node.childs[index] == null) {
            node.childs[index] = new Node();
        }
        insert(word.substring(1), node.childs[index]);
    }

    public boolean search(String word) {
        return search(word, root);
    }

    private boolean search(String word, Node node) {
        if (node == null) return false;
        if (word.length() == 0) return node.isLeaf;
        int index = indexForChar(word.charAt(0));
        return search(word.substring(1), node.childs[index]);
    }

    public boolean startsWith(String prefix) {
        return startWith(prefix, root);
    }

    private boolean startWith(String prefix, Node node) {
        if (node == null) return false;
        if (prefix.length() == 0) return true;
        int index = indexForChar(prefix.charAt(0));
        return startWith(prefix.substring(1), node.childs[index]);
    }

    private int indexForChar(char c) {
        return c - 'a';
    }
}
```

**实现一个 Trie，用来求前缀和**

[677. Map Sum Pairs \(Medium\)](https://leetcode.com/problems/map-sum-pairs/description/)

```text
Input: insert("apple", 3), Output: Null
Input: sum("ap"), Output: 3
Input: insert("app", 2), Output: Null
Input: sum("ap"), Output: 5
```

```text
class MapSum {

    private class Node {
        Node[] child = new Node[26];
        int value;
    }

    private Node root = new Node();

    public MapSum() {

    }

    public void insert(String key, int val) {
        insert(key, root, val);
    }

    private void insert(String key, Node node, int val) {
        if (node == null) return;
        if (key.length() == 0) {
            node.value = val;
            return;
        }
        int index = indexForChar(key.charAt(0));
        if (node.child[index] == null) {
            node.child[index] = new Node();
        }
        insert(key.substring(1), node.child[index], val);
    }

    public int sum(String prefix) {
        return sum(prefix, root);
    }

    private int sum(String prefix, Node node) {
        if (node == null) return 0;
        if (prefix.length() != 0) {
            int index = indexForChar(prefix.charAt(0));
            return sum(prefix.substring(1), node.child[index]);
        }
        int sum = node.value;
        for (Node child : node.child) {
            sum += sum(prefix, child);
        }
        return sum;
    }

    private int indexForChar(char c) {
        return c - 'a';
    }
}
```

## 图

#### 二分图

如果可以用两种颜色对图中的节点进行着色，并且保证相邻的节点颜色不同，那么这个图就是二分图。

**判断是否为二分图**

[785. Is Graph Bipartite? \(Medium\)](https://leetcode.com/problems/is-graph-bipartite/description/)

```text
Input: [[1,3], [0,2], [1,3], [0,2]]
Output: true
Explanation:
The graph looks like this:
0----1
|    |
|    |
3----2
We can divide the vertices into two groups: {0, 2} and {1, 3}.
```

```text
Example 2:
Input: [[1,2,3], [0,2], [0,1,3], [0,2]]
Output: false
Explanation:
The graph looks like this:
0----1
| \  |
|  \ |
3----2
We cannot find a way to divide the set of nodes into two independent subsets.
```

```text
public boolean isBipartite(int[][] graph) {
    int[] colors = new int[graph.length];
    Arrays.fill(colors, -1);
    for (int i = 0; i < graph.length; i++) {  // 处理图不是连通的情况
        if (colors[i] == -1 && !isBipartite(i, 0, colors, graph)) {
            return false;
        }
    }
    return true;
}

private boolean isBipartite(int curNode, int curColor, int[] colors, int[][] graph) {
    if (colors[curNode] != -1) {
        return colors[curNode] == curColor;
    }
    colors[curNode] = curColor;
    for (int nextNode : graph[curNode]) {
        if (!isBipartite(nextNode, 1 - curColor, colors, graph)) {
            return false;
        }
    }
    return true;
}
```

#### 拓扑排序

常用于在具有先序关系的任务规划中。

**课程安排的合法性**

[207. Course Schedule \(Medium\)](https://leetcode.com/problems/course-schedule/description/)

```text
2, [[1,0]]
return true
```

```text
2, [[1,0],[0,1]]
return false
```

题目描述：一个课程可能会先修课程，判断给定的先修课程规定是否合法。

本题不需要使用拓扑排序，只需要检测有向图是否存在环即可。

```text
public boolean canFinish(int numCourses, int[][] prerequisites) {
    List[] graphic = new List[numCourses];
    for (int i = 0; i < numCourses; i++) {
        graphic[i] = new ArrayList<>();
    }
    for (int[] pre : prerequisites) {
        graphic[pre[0]].add(pre[1]);
    }
    boolean[] globalMarked = new boolean[numCourses];
    boolean[] localMarked = new boolean[numCourses];
    for (int i = 0; i < numCourses; i++) {
        if (hasCycle(globalMarked, localMarked, graphic, i)) {
            return false;
        }
    }
    return true;
}

private boolean hasCycle(boolean[] globalMarked, boolean[] localMarked,
                         List[] graphic, int curNode) {

    if (localMarked[curNode]) {
        return true;
    }
    if (globalMarked[curNode]) {
        return false;
    }
    globalMarked[curNode] = true;
    localMarked[curNode] = true;
    for (int nextNode : graphic[curNode]) {
        if (hasCycle(globalMarked, localMarked, graphic, nextNode)) {
            return true;
        }
    }
    localMarked[curNode] = false;
    return false;
}
```

**课程安排的顺序**

[210. Course Schedule II \(Medium\)](https://leetcode.com/problems/course-schedule-ii/description/)

```text
4, [[1,0],[2,0],[3,1],[3,2]]
There are a total of 4 courses to take. To take course 3 you should have finished both courses 1 and 2\. Both courses 1 and 2 should be taken after you finished course 0\. So one correct course order is [0,1,2,3]. Another correct ordering is[0,2,1,3].
```

使用 DFS 来实现拓扑排序，使用一个栈存储后序遍历结果，这个栈的逆序结果就是拓扑排序结果。

证明：对于任何先序关系：v-&gt;w，后序遍历结果可以保证 w 先进入栈中，因此栈的逆序结果中 v 会在 w 之前。

```text
public int[] findOrder(int numCourses, int[][] prerequisites) {
    List[] graphic = new List[numCourses];
    for (int i = 0; i < numCourses; i++) {
        graphic[i] = new ArrayList<>();
    }
    for (int[] pre : prerequisites) {
        graphic[pre[0]].add(pre[1]);
    }
    Stack postOrder = new Stack<>();
    boolean[] globalMarked = new boolean[numCourses];
    boolean[] localMarked = new boolean[numCourses];
    for (int i = 0; i < numCourses; i++) {
        if (hasCycle(globalMarked, localMarked, graphic, i, postOrder)) {
            return new int[0];
        }
    }
    int[] orders = new int[numCourses];
    for (int i = numCourses - 1; i >= 0; i--) {
        orders[i] = postOrder.pop();
    }
    return orders;
}

private boolean hasCycle(boolean[] globalMarked, boolean[] localMarked, List[] graphic,
                         int curNode, Stack postOrder) {

    if (localMarked[curNode]) {
        return true;
    }
    if (globalMarked[curNode]) {
        return false;
    }
    globalMarked[curNode] = true;
    localMarked[curNode] = true;
    for (int nextNode : graphic[curNode]) {
        if (hasCycle(globalMarked, localMarked, graphic, nextNode, postOrder)) {
            return true;
        }
    }
    localMarked[curNode] = false;
    postOrder.push(curNode);
    return false;
}
```

#### 并查集

并查集可以动态地连通两个点，并且可以非常快速地判断两个点是否连通。

**冗余连接**

[684. Redundant Connection \(Medium\)](https://leetcode.com/problems/redundant-connection/description/)

```text
Input: [[1,2], [1,3], [2,3]]
Output: [2,3]
Explanation: The given undirected graph will be like this:
  1
 / \
2 - 3
```

题目描述：有一系列的边连成的图，找出一条边，移除它之后该图能够成为一棵树。

```text
public int[] findRedundantConnection(int[][] edges) {
    int N = edges.length;
    UF uf = new UF(N);
    for (int[] e : edges) {
        int u = e[0], v = e[1];
        if (uf.connect(u, v)) {
            return e;
        }
        uf.union(u, v);
    }
    return new int[]{-1, -1};
}

private class UF {
    private int[] id;

    UF(int N) {
        id = new int[N + 1];
        for (int i = 0; i < id.length; i++) {
            id[i] = i;
        }
    }

    void union(int u, int v) {
        int uID = find(u);
        int vID = find(v);
        if (uID == vID) {
            return;
        }
        for (int i = 0; i < id.length; i++) {
            if (id[i] == uID) {
                id[i] = vID;
            }
        }
    }

    int find(int p) {
        return id[p];
    }

    boolean connect(int u, int v) {
        return find(u) == find(v);
    }
}
```

## 位运算

**1. 基本原理**

0s 表示一串 0，1s 表示一串 1。

```text
x ^ 0s = x      x & 0s = 0      x | 0s = x
x ^ 1s = ~x     x & 1s = x      x | 1s = 1s
x ^ x = 0       x & x = x       x | x = x

复制代码
```

* 利用 x ^ 1s = ~x 的特点，可以将位级表示翻转；利用 x ^ x = 0 的特点，可以将三个数中重复的两个数去除，只留下另一个数。
* 利用 x & 0s = 0 和 x & 1s = x 的特点，可以实现掩码操作。一个数 num 与 mask ：00111100 进行位与操作，只保留 num 中与 mask 的 1 部分相对应的位。
* 利用 x \| 0s = x 和 x \| 1s = 1s 的特点，可以实现设值操作。一个数 num 与 mask：00111100 进行位或操作，将 num 中与 mask 的 1 部分相对应的位都设置为 1。

位与运算技巧：

* n&\(n-1\) 去除 n 的位级表示中最低的那一位。例如对于二进制表示 10110 **100** ，减去 1 得到 10110**011**，这两个数相与得到 10110**000**。
* n&\(-n\) 得到 n 的位级表示中最低的那一位。-n 得到 n 的反码加 1，对于二进制表示 10110 **100** ，-n 得到 01001**100**，相与得到 00000**100**。
* n-n&\(~n+1\) 去除 n 的位级表示中最高的那一位。

移位运算：

* > > n 为算术右移，相当于除以 2n；
* > > > n 为无符号右移，左边会补上 0。
* &lt;&lt; n 为算术左移，相当于乘以 2n。

**2. mask 计算**

要获取 111111111，将 0 取反即可，~0。

要得到只有第 i 位为 1 的 mask，将 1 向左移动 i-1 位即可，1&lt;&lt;\(i-1\) 。例如 1&lt;&lt;4 得到只有第 5 位为 1 的 mask ：00010000。

要得到 1 到 i 位为 1 的 mask，1&lt;&lt;\(i+1\)-1 即可，例如将 1&lt;&lt;\(4+1\)-1 = 00010000-1 = 00001111。

要得到 1 到 i 位为 0 的 mask，只需将 1 到 i 位为 1 的 mask 取反，即 ~\(1&lt;&lt;\(i+1\)-1\)。

**3. Java 中的位操作**

```text
static int Integer.bitCount();           // 统计 1 的数量
static int Integer.highestOneBit();      // 获得最高位
static String toBinaryString(int i);     // 转换为二进制表示的字符串
```

**统计两个数的二进制表示有多少位不同**

[461. Hamming Distance \(Easy\)](https://leetcode.com/problems/hamming-distance/)

```text
Input: x = 1, y = 4

Output: 2

Explanation:
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑

The above arrows point to positions where the corresponding bits are different.
```

对两个数进行异或操作，位级表示不同的那一位为 1，统计有多少个 1 即可。

```text
public int hammingDistance(int x, int y) {
    int z = x ^ y;
    int cnt = 0;
    while(z != 0) {
        if ((z & 1) == 1) cnt++;
        z = z >> 1;
    }
    return cnt;
}
```

使用 z&\(z-1\) 去除 z 位级表示最低的那一位。

```text
public int hammingDistance(int x, int y) {
    int z = x ^ y;
    int cnt = 0;
    while (z != 0) {
        z &= (z - 1);
        cnt++;
    }
    return cnt;
}
```

可以使用 Integer.bitcount\(\) 来统计 1 个的个数。

```text
public int hammingDistance(int x, int y) {
    return Integer.bitCount(x ^ y);
}
```

**数组中唯一一个不重复的元素**

[136. Single Number \(Easy\)](https://leetcode.com/problems/single-number/description/)

```text
Input: [4,1,2,1,2]
Output: 4
```

两个相同的数异或的结果为 0，对所有数进行异或操作，最后的结果就是单独出现的那个数。

```text
public int singleNumber(int[] nums) {
    int ret = 0;
    for (int n : nums) ret = ret ^ n;
    return ret;
}
```

**找出数组中缺失的那个数**

[268. Missing Number \(Easy\)](https://leetcode.com/problems/missing-number/description/)

```text
Input: [3,0,1]
Output: 2
```

题目描述：数组元素在 0-n 之间，但是有一个数是缺失的，要求找到这个缺失的数。

```text
public int missingNumber(int[] nums) {
    int ret = 0;
    for (int i = 0; i < nums.length; i++) {
        ret = ret ^ i ^ nums[i];
    }
    return ret ^ nums.length;
}
```

**数组中不重复的两个元素**

[260. Single Number III \(Medium\)](https://leetcode.com/problems/single-number-iii/description/)

两个不相等的元素在位级表示上必定会有一位存在不同。

将数组的所有元素异或得到的结果为不存在重复的两个元素异或的结果。

diff &= -diff 得到出 diff 最右侧不为 0 的位，也就是不存在重复的两个元素在位级表示上最右侧不同的那一位，利用这一位就可以将两个元素区分开来。

```text
public int[] singleNumber(int[] nums) {
    int diff = 0;
    for (int num : nums) diff ^= num;
    diff &= -diff;  // 得到最右一位
    int[] ret = new int[2];
    for (int num : nums) {
        if ((num & diff) == 0) ret[0] ^= num;
        else ret[1] ^= num;
    }
    return ret;
}
```

**翻转一个数的比特位**

[190. Reverse Bits \(Easy\)](https://leetcode.com/problems/reverse-bits/description/)

```text
public int reverseBits(int n) {
    int ret = 0;
    for (int i = 0; i < 32; i++) {
        ret <<= 1;
        ret |= (n & 1);
        n >>>= 1;
    }
    return ret;
}
```

如果该函数需要被调用很多次，可以将 int 拆成 4 个 byte，然后缓存 byte 对应的比特位翻转，最后再拼接起来。

```text
private static Map cache = new HashMap<>();

public int reverseBits(int n) {
    int ret = 0;
    for (int i = 0; i < 4; i++) {
        ret <<= 8;
        ret |= reverseByte((byte) (n & 0b11111111));
        n >>= 8;
    }
    return ret;
}

private int reverseByte(byte b) {
    if (cache.containsKey(b)) return cache.get(b);
    int ret = 0;
    byte t = b;
    for (int i = 0; i < 8; i++) {
        ret <<= 1;
        ret |= t & 1;
        t >>= 1;
    }
    cache.put(b, ret);
    return ret;
}
```

**不用额外变量交换两个整数**

[程序员代码面试指南 ：P317](https://github.com/CyC2018/Interview-Notebook/blob/master/notes/Leetcode%20%E9%A2%98%E8%A7%A3.md#)

```text
a = a ^ b;
b = a ^ b;
a = a ^ b;
```

**判断一个数是不是 2 的 n 次方**

[231. Power of Two \(Easy\)](https://leetcode.com/problems/power-of-two/description/)

二进制表示只有一个 1 存在。

```text
public boolean isPowerOfTwo(int n) {
    return n > 0 && Integer.bitCount(n) == 1;
}
```

利用 1000 & 0111 == 0 这种性质，得到以下解法：

```text
public boolean isPowerOfTwo(int n) {
    return n > 0 && (n & (n - 1)) == 0;
}
```

**判断一个数是不是 4 的 n 次方**

[342. Power of Four \(Easy\)](https://leetcode.com/problems/power-of-four/)

这种数在二进制表示中有且只有一个奇数位为 1，例如 16（10000）。

```text
public boolean isPowerOfFour(int num) {
    return num > 0 && (num & (num - 1)) == 0 && (num & 0b01010101010101010101010101010101) != 0;
}
```

也可以使用正则表达式进行匹配。

```text
public boolean isPowerOfFour(int num) {
    return Integer.toString(num, 4).matches("10*");
}
```

**判断一个数的位级表示是否不会出现连续的 0 和 1**

[693. Binary Number with Alternating Bits \(Easy\)](https://leetcode.com/problems/binary-number-with-alternating-bits/description/)

```text
Input: 10
Output: True
Explanation:
The binary representation of 10 is: 1010.

Input: 11
Output: False
Explanation:
The binary representation of 11 is: 1011.
```

对于 1010 这种位级表示的数，把它向右移动 1 位得到 101，这两个数每个位都不同，因此异或得到的结果为 1111。

```text
public boolean hasAlternatingBits(int n) {
    int a = (n ^ (n >> 1));
    return (a & (a + 1)) == 0;
}
```

**求一个数的补码**

[476. Number Complement \(Easy\)](https://leetcode.com/problems/number-complement/description/)

```text
Input: 5
Output: 2
Explanation: The binary representation of 5 is 101 (no leading zero bits), and its complement is 010\. So you need to output 2.
```

题目描述：不考虑二进制表示中的首 0 部分。

对于 00000101，要求补码可以将它与 00000111 进行异或操作。那么问题就转换为求掩码 00000111。

```text
public int findComplement(int num) {
    if (num == 0) return 1;
    int mask = 1 << 30;
    while ((num & mask) == 0) mask >>= 1;
    mask = (mask << 1) - 1;
    return num ^ mask;
}
```

可以利用 Java 的 Integer.highestOneBit\(\) 方法来获得含有首 1 的数。

```text
public int findComplement(int num) {
    if (num == 0) return 1;
    int mask = Integer.highestOneBit(num);
    mask = (mask << 1) - 1;
    return num ^ mask;
}
```

对于 10000000 这样的数要扩展成 11111111，可以利用以下方法：

```text
mask |= mask >> 1    11000000
mask |= mask >> 2    11110000
mask |= mask >> 4    11111111
```

```text
public int findComplement(int num) {
    int mask = num;
    mask |= mask >> 1;
    mask |= mask >> 2;
    mask |= mask >> 4;
    mask |= mask >> 8;
    mask |= mask >> 16;
    return (mask ^ num);
}
```

**实现整数的加法**

[371. Sum of Two Integers \(Easy\)](https://leetcode.com/problems/sum-of-two-integers/description/)

a ^ b 表示没有考虑进位的情况下两数的和，\(a & b\) &lt;&lt; 1 就是进位。

递归会终止的原因是 \(a & b\) &lt;&lt; 1 最右边会多一个 0，那么继续递归，进位最右边的 0 会慢慢增多，最后进位会变为 0，递归终止。

```text
public int getSum(int a, int b) {
    return b == 0 ? a : getSum((a ^ b), (a & b) << 1);
}
```

**字符串数组最大乘积**

[318. Maximum Product of Word Lengths \(Medium\)](https://leetcode.com/problems/maximum-product-of-word-lengths/description/)

```text
Given ["abcw", "baz", "foo", "bar", "xtfn", "abcdef"]
Return 16
The two words can be "abcw", "xtfn".
```

题目描述：字符串数组的字符串只含有小写字符。求解字符串数组中两个字符串长度的最大乘积，要求这两个字符串不能含有相同字符。

本题主要问题是判断两个字符串是否含相同字符，由于字符串只含有小写字符，总共 26 位，因此可以用一个 32 位的整数来存储每个字符是否出现过。

```text
public int maxProduct(String[] words) {
    int n = words.length;
    int[] val = new int[n];
    for (int i = 0; i < n; i++) {
        for (char c : words[i].toCharArray()) {
            val[i] |= 1 << (c - 'a');
        }
    }
    int ret = 0;
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if ((val[i] & val[j]) == 0) {
                ret = Math.max(ret, words[i].length() * words[j].length());
            }
        }
    }
    return ret;
}
```

**统计从 0 ~ n 每个数的二进制表示中 1 的个数**

[338. Counting Bits \(Medium\)](https://leetcode.com/problems/counting-bits/description/)

对于数字 6\(110\)，它可以看成是 4\(100\) 再加一个 2\(10\)，因此 dp\[i\] = dp\[i&\(i-1\)\] + 1;

```text
public int[] countBits(int num) {
    int[] ret = new int[num + 1];
    for(int i = 1; i <= num; i++){
        ret[i] = ret[i&(i-1)] + 1;
    }
    return ret;
}
```

