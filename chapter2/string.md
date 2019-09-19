---
description: Python 里的string是immutable的
---

# String

尽量避免做slicing

## Char Removal

### 删除student中的u和n

常见错误：数组越界；如果有多个元素连续出现，删不干净。 更好的：2 pointers。

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



### Char De-duplication 

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



变形题1 如果可以允许两个一样的，那需要修改的只有2行：line5的初始值2，2； 以及line7的slow-2

变形题2 消消乐 维护一个stack，和栈顶一样就pop，最后返回所有在stack里的元素

fast = 0

stack = \[\]

Case 1: a\[fast\]!=stack.top\(\), stack.push\(a\[fast\]\), fast+=1

Case 2: a\[fast\]==stack.top\(\), keep moving fast until a\[fast\]!=stack.top\(\), stack.pop\(\)

要非常清楚fast本身的物理含义，在case2里，如果我们最终是要保留一个重复元素，那么就不需要pop\(\) 

```python
def remove_duplicate(str):
    if not str or len(str)<1:
        return str
    lst = []
    fast = 0
    while fast < len(str):
     #print fast, lst 
        c = str[fast]
        if len(lst)>0 and str[fast]==lst[-1]
            while fast<len(str) and lst[-1]==str[fast]:
                fast+=1
            lst.pop()
        else:
            lst.append(str[fast])
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

### Reverse a string

相当于reverse两次，call reverse这个helper，第一次 reverse整个list，第二次，j放在j-1，然后再call helper

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
    while i< len(lst):
        if i==len(lst)-1 or lst[i+1]==' ';
            right = i
            reverse_helper(lst, left, right)
            left = i + 2
        i += 1
    return ''.join(lst)
```



### Substring

检查一个字符串是否是另一个字符串的子串，返回字符串开始的位置

1. Brute-force  O\(m\*n\)
2. Robin-Karp： Avg Case O\(m-n\)， worst case：O\(m\*n\) 把字符串的比较转化成26进制数的比较

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

