---
description: Python 里的string是immutable的
---

# String

尽量避免做slicing

## Char Removal

### 删除student中的u和n

常见错误：

1. 如果有多个元素连续出现，index不注意维护就会删不干净。
2. 调用API的时候要注意时间复杂度 deleteCharAt, subString, string1+string2都

{% code title="错误做法" %}
```python
    
```
{% endcode %}

两个挡板，三个区域，**同向**而行。对于需要保留output相对顺序的题目，选择同向而行。

左挡板左侧，不包含左侧，是要留的结果  
左右之间，是不在乎的区域  
右挡板右侧，不包含右侧，是正在探索的需求

Case 1: a\[j\] should be kept: a\[i\]=a\[j\], i++, j++  
Case 2: a\[j\] should not be kept: j++

```python
class Solution(object):
  def remove(self, input, t):
    """
    input: string input, string t
    return: string
    """
    # write your solution here
    slow, fast = 0, 0
    toremove = set(t)
    array = list(input)
    for fast in range(len(array)):
      if array[fast] not in toremove:
        array[slow]=array[fast]
        slow+=1
    return ''.join(array[:slow])
```

j 快指针，右边是未访问的元素，左边是已经访问的元素；

i 慢指针，左边是要返回的字符；

快慢指针之间\[i, j-1\]是不返回的元素。

initialization：i=0, j=0

str\[j\]是否该被保留 

a = list\(str\)

Case1: 保留str\[j\], a\[i\]=str\[j\], i+=1. j+=1;

Case2: 不保留str\[j\], j+=1

Termination Condition: j&gt;=len\(str\)

```python
def remove(str):
    lst = list(str)
    i,j = 0,0
    while j<len(lst):
        if lst[j] not in ['u', 'n']
            lst[i] = lst[j]
            i+=1
        j+=1
        return ''.join(lst[:i])
    print(remove("student"))
```

Time O\(n\)

Space O\(n\)

```python
def remove(str):
    lst = []
    j = 0
    while j<len(str):
        if str[j] not in ['u', 'n']
            lst.append(str[j])
        j+=1
        return ''.join(lst)
    print(remove("student"))
```

Time O\(n\)



Space O\(n\)

相比之下第一种更好，因为第二种里的append不一定是一个O\(1\)的操作。在Python中可变长度的容器，增加元素时要开辟新的空间（一般是double size），只是amortized O\(1\)。



### 删除句中的多余空格

原始输入空格比较多，把开头多余的空格、结尾的空格去掉，中间的只留1个空格。

Case 1: if a\[f\]==' ':  
- case 1.1 s==0, don't copy  
- case 1.2 s!=0,     if a\[s-1\]!=’ ‘，copy  
                                if a\[s-1\]==' '. don't copy

Case 2: if a\[f\]!=' ': copy

Post-processing: **s&gt;0** and a\[s-1\] ==' ', then s-=1

```python
class Solution(object):
  def removeSpaces(self, input):
    """
    input: string input
    return: string
    """
    # write your solution here
    if not input:
      return input
    array = list(input)
    slow = 0
    for fast in range (len(array)):
      if array[fast]==' ' and (fast==0 or array[fast-1]==' '):
        continue
      array[slow]=array[fast]
      slow+=1
    if (slow>0 and array[slow-1]==' '):
      slow-=1
    return ''.join(array[:slow])
```

initialization：i=0, j=0

Case 1:  a\[j\]==' ' and i==0, ignore 

Case 2:  a\[j\]!=' ' , keep

Case 3:  a\[j\]==' ' , and i!=0, and a\[i-1\]!=' ', keep

Case 4: a\[j\]==' ' , and i!=0, and a\[i-1\]==' ', ignore 

Termination: j=n

Post-processing: i&gt;0 and a\[i-1\] ==' ', then i-=1

```python
def remove_spaces(str):
    if not str:
        return str
    lst = list(str)
    i,j = 0,0
    while j < len(lst):
    ## Case 2 and Case 3
        if lst[j]!=' ' or (i!=0 and lst[i-1]!=' '):
            lst[i]=lst[j]
            i+=1
        j+=1
    if i>0 and lst[i-1] ==' ':
        i-=1
    return ''.join(lst[:i])
```

Time O\(n\)

Space O\(n\)

```python
def remove_spaces(str):
    if not str:
        return str
    lst = []
    for fast in xrange(len(lst)):
        if str[fast]==' ' and (fast==0 or lst[fast-1]==' '):
            continue
        lst.append(str[fast])
    if len(lst)>0 and lst[-1] ==' ':
        lst.pop()
    return ''.join(lst)
```



## Char De-duplication 

第一个字符，不论是否重复，都一定会被保留，所以initialization时，可以都从1开始。

Case 1: a\[j\]==a\[i-1\], ignore \(j+=1\)

Case 2: a\[j\]!=a\[i-1\], keep \(a\[i\]=a\[j\], i+=1, j+=1\)

```python
def remove_duplicate(str):
    if not str or len(str)<2:
        return str
    lst = list(str)
    slow, fast = 1, 1
    while fast<len(lst):
        if lst[fast]!=lst[slow-1]:
            lst[slow]=lst[fast]
            slow+=1
        fast+=1
    return ''.join(lst[:slow])
```

```python
def remove_duplicate(str):
    if not str or len(str)<2:
        return str
    fast = 0
    lst = []
    while fast<len(lst):
        if lst[fast]==0 or lst[-1]!=str[fast]:
            lst.append(str[fast])
        fast+=1
    return ''.join(lst[:slow])
```

```python
class Solution(object):
  def deDup(self, input):
    """
    input: string input
    return: string
    """
    # write your solution here
    if not input:
      return input
    array = list(input)
    slow = 0
    for fast in range (len(array)):
      if fast==0 or array[fast]!=array[slow-1]:
        array[slow]=array[fast]
        slow+=1
    return ''.join(array[:slow])
```

变形题1 如果可以允许两个一样的，那需要修改的只有2行：line5的初始值2，2； 以及line7的slow-2

变形题2 消消乐 维护一个stack，和栈顶一样就pop，最后返回所有在stack里的元素

fast = 0

stack = \[\]

Case 1: a\[fast\]!=stack.top\(\), stack.push\(a\[fast\]\), fast+=1

Case 2: a\[fast\]==stack.top\(\), keep moving fast until a\[fast\]!=stack.top\(\), stack.pop\(\)

要非常清楚fast本身的物理含义，在case2里，如果我们最终是要保留一个重复元素，那么就不需要pop\(\) 

```python
class Solution(object):
  def deDup(self, input):
    """
    input: string input
    return: string
    """
    # write your solution here
    if not input or len(input)<1:
        return input
    lst = []
    fast = 0
    while fast < len(input):
        if len(lst)>0 and input[fast]==lst[-1]: #易错点 lst[-1]但是忘了>0的前提
            while fast<len(input) and lst[-1]==input[fast]: #易错点 fast++的过程中可能比input本身还大 
                fast+=1
                fast+=1
            lst.pop()
        else:
            lst.append(input[fast])
            fast+=1
    return ''.join(lst)
```

同理，也可以再定义一个slow pointer，借助双指针实现inplace的操作。  

f fast: linear scan pointer \(fast and after are not processed\)

s slow: all letters to the left of s \(not including s\) are processed letters that should be kept. 

Initialization: slow=fast=1

Case 1: a\[f\]!=a\[s-1\], then a\[s\]=a\[f\], s++, f++;

Case 2: a\[f\]==a\[s-1\], then repeatedly f++ until a\[f\]!=a\[s-1\], s-=1

Return a\[0:s\] \(不包含s\) 

如果输入是一个list，那么空间复杂度就是O\(1\); 如果是个string，因为它immutable，先转换成string，空间复杂度是O\(n\)

## Reverse a string

#### iterative 双指针，头尾，相向，互换

```python
def reverse_helper(lst, left, right):
    while left<right:
        lst[left], lst[right] = lst[right], lst[left]
        left += 1
        right -= 1
```

#### recursive 虚线框中间

```python

```

#### reverse句子

相当于reverse两次，call reverse这个helper，第一次 reverse整个list，第二次reverse单词，j放在j-1，然后再call helper

```python
def reverse_helper(lst, left, right):
    while left<right:
        lst[left], lst[right] = lst[right], lst[left]
        left += 1
        right -= 1

def reverse_string(string):
    lst = list(string)
    reverse_helper(lst, 0, len(lst)-1)
    i = 0
    left = i
    right = i
    while i < len(lst):
        if i==len(lst)-1 or lst[i+1]==' ';
            right = i
            reverse_helper(lst, left, right)
            left = i + 2
        i += 1
    return ''.join(lst)
```

#### reverse right-hand side by two positions

abcdef --&gt; efabcd

step 1: reverse the whole word,

step 2: fe \| dcba 分别反转

## Substring

检查一个字符串是否是另一个字符串的子串，返回字符串开始的位置

* Brute-force  O\(m\*n\)

```python
class Solution:
    def strstr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype int
        """
        if not needle:
            return 0
        
        for h in xrange(len(haystack)-len(needle)+1): #[l,r)
            for n in xrange(len(needle)):
                if haystack[h+n]!=needle[n]:
                    break
            else: # when the for look terminates naturally (without early break)
                return h
        
        return -1
```

这里注意for和else搭配的用法，这是为了避免引入一个flag变量记录状态。如果正常退出，也就是没有执行break，else会执行；如果执行break，else不执行。这样在内部的for正常退出时h的位置就是match的字符串在原串的起始位置。

虽然乍一看这是一个O\(m\*n\)的复杂度，但是在早年word之类的Ctrl+F查找时都是这套brute force。这是因为对于英文文本来说，只要前面几个字符匹配了，大概率这就是一个词，实际的时间复杂度其实更接近于O\(m+n\)

* Rabin-Karp：  Avg Case O\(m-n\)， worst case：O\(m\*n\) 把字符串的比较转化成26进制数的比较

比如，16进制表示FBA=15\*16^2+11\*16^1+10\*16^0

如果用26进制数来表示abcd，那么a\*26^3+2\*26^2+3\*26^1+4\*26^0;

取模: 加速计算、保证时间复杂度是常数，避免大数比较

```python
Class RabinKarp:
    def __init__(self):
        self.base = 26
        self.mod = 997
    def strstr(self, haystack, needle):
        if len(needdle)>len(haystack):
            return -1
        hay_hash, nhash = 0,0
        power = 1
        for i in range(len(needle)):
            power = power*self.base%self.mod if i!=0 else 1
            hay_hash = (hay_hash * self.base + ord(haystack[i])%self.mod
            nhash = (nhash*self.base+ord(needle[i]))%self.mod
        for i in range(len(needle), len(haystack)):
            if hay_hash ==nhash and needle == haystack[i-len(needle):i]:
                return i-len(needle)
            hay_hash -= (power*ord(haystack[i-len(needle)]))%self.mod
            if hay_hash <0:
                hay_hash+=self.mod
            hay_hash=(hay_hash*self.base+ord(haystack[i]))%self.mod
        if hay_hash==nhash and needle ==haystack[len(haystack)-len(needle)]:
            return len(haystack) - len(needle)
        return -1
    
rk=RabinKarp()
print(rk.strstr('bacbabababacaab', 'abaca'))
```

```python
class Solution(object):
    def strStr(self, haystack, needle):
        """
        :type haystack: str
        :type needle: str
        :rtype: int
        """
        if not needle:
            return 0
        if len(haystack)<len(needle):
            return -1
        Q, W = 10**9+0, 256
        HW = 1
        
        for i in xrange(1, len(needle)):
            HW = (HW*W)%Q
        needle_hash, haystack_hash = 0,0
        
        for i in xrange(len(needle)):
            needle_hash = (W*needle_hash+ord(needle[i]))%Q
            haystack_hash = (W*haystack_hash+ord(haystack[i]))%Q
        
        if needle_hash == haystack_hash:
            return 0
        
        for i in xrange(len(needle), len(haystack)):
            haystack_hash = (haystack_hash -HW*ord(haystack[i-len(needle)])%Q+Q)%Q
            haystack_hash = (haystack_hash*W+ord(haystack[i]))%Q
            
            if needle_hash==haystack_hash
                return i -len(needle)+1
        
        return -1
```

## Char Replacement

Example: student --&gt; stuXXt, studendendent --&gt;stuXXXXXXt 

slow: all letters to the left of slow \(not including slow\) are the final results  
fast: current index; s1: 'den', s2: 'XX'

student  
s  
 f

#### case 1: from s1.length &gt; = s2. length 替换后变短

case 1: if f!='d', copy, f++, s++  
case 2: if f points to 'den', copy s2, s+=s2.length fast+=s1.lenth

#### case 2: s1.length &lt; s2. length 替换后变长

这个时候替换成XXXX 

step 1: 过一遍string，数一下s1字符串出现了多少次\(比如2次\)；  
step 2: enlarge the string 2\*\(s2.length-s1.length\);

s t u d e n t d e n t  _\_ \_  
                                    s  
                              f_

step 3: 把slow放在最后，f放在真正的string最后，slow的右边，不含slow都是最后结果，该保留的；fast是现在的index。  
出来的条件是f和s都离开了整个string。



## Shuffling

### ABCD1234--&gt;A1B2C3D4

ABCD1234--&gt; AB12CD34--&gt;A1B2C3D4

### A1B2C3D4--&gt;ABCD1234

> merge sort, 左右两边都sort好了，所以谁小移谁

          A1B2\|C3D4  
      /                        \  
AB12                     CD34  
            ABCD1234

12 CD 先换成 DC21，再内部交换一次CD12

### ABCDEFG1234567--&gt;A1B2C3D4E5F6G7

要让chunk1和chunk3同size：ABCD1234EFG567          
要为每一个步骤求mid

size = right-left-1  
mid = left+size/2  
lm = left+size/4  
rm = left+size\*3/4

### ABCDE1234567--&gt;A1B2C3D4E567

当后面的67没有，接着做

### ABCDEFG12345--&gt;A1B2C3D4E5FG

ABCDE12345FG  
54321GF --&gt;12345FG

## Permutation with dups

### if no dups, swap-swap

### if with dups

如果还是swap-swap的思路，那只要是换过来一样的，在hashset看到了，就不换了。这个判断需要加在进入for循环之前；另外里面加一个if判断 如果在里面，continue；把该加的加了

Time: O\(n!\*n\)  
Space: O\(n^2\)

## Encoding/Decoding

### aaaabcc--&gt;a4b1c2 run-length encoding, requires in-place

{% hint style="info" %}
注意2种case
{% endhint %}

slow: 左侧不包含都是结果  
fast: 当前

看f后面的已经不是f指向的时候了，总结之前的。但是注意这又有可能会overwrite到后面的一个index，如果string越变越长、越变越短... 

Step 1: from left to right: we only deal with the cases where the letter repeats &gt;=2 times; the case that appeared only once are simply copied. In the meantime, we need to count how many times a letter only repeats once.  eg. count=1   
Step 2: enlarge the string by 'count', and from right to left: do similar steps. 

## Sliding window

### longest substring that each char occurs at most k times

\[L\(slow\), R\(fast\)\]: each char occurs at most k times within this window  
HashMap: &lt;char, count&gt;

BDEFGADE  
\| \|  
~~&lt;B,1&gt;, &lt;D, 1&gt;,~~ &lt;E, 1&gt;, &lt;F, 1&gt;, &lt;G, 1&gt;, &lt;A, 1&gt;, ~~&lt;D, 1&gt;~~   

global max =6

1. when to move fast index: at most k times within the table \(no value exceeds k times\) 
2. when to move slow index: when there is a value &gt; k, keep slow++ until there is no value &gt; k
3. when to update the final result: when we do fast ++, as long as \(fast-slow+1\)&gt;global\_max 

### find all anagrams of a substring s2 in a long string s1 

string s2 = aabc  
            s1 = zzzzcdebcaabcyywwww

{% hint style="info" %}
这里有重复字母，所以不能用XOR来做。因为a XOR a 的时候得到的是0，这个时候不知道是aa的0还是bb的零
{% endhint %}

Fixed-Size sliding window 一个指针都行  
HashMap &lt;char, count&gt;

Step 1: initialize the hashmap, put s2 in hashmap  
Step 2: run sliding window on s1, 出现一个里面的count减一个，目标是都是0

Time = O\(m\*n\)

#### how to optimize to O\(m+n\)

用type\_of\_char\_to\_match来替代 每当有一个字母后面的数字是零，意味着type\_of\_char\_to\_match可以减一了，不用每次都对着hashmap查，而是对着数字查了。

### flip at most k times to convert from 0 to 1, find the longest subarray that contains all 1

同上面的at most k times, 只需要0 at most k times 

\_\_

