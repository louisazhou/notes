---
description: Last in First Out     list就是一个天然的stack，不需要单独定义class。
---

# Stack

push 

pop

top

从左到右linear scan，需要不断回头看过去的元素时，使用stack

{% embed url="https://www.jianshu.com/p/78e3b21b842d" %}

{% code title="用array实现stack 全都是one-liner 好无聊" %}
```python
class Solution:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.queue=[]

    def push(self, x):
        """
        Push element x onto stack.
        """
        self.queue.append(x)
        

    def pop(self):
        """
        Removes the element on top of the stack and returns that element.
        """
        return self.queue.pop() if self.queue else None

        

    def top(self):
        """
        Get the top element.
        """
        return self.queue[-1] if self.queue else None

    def isEmpty(self):
        """
        Returns whether the stack is empty.
        """
        return not self.queue
```
{% endcode %}

常见题型：

1. Deduplication and Repeatedly deduplication, Leetcode 71, simplify path 
2. 作为辅助实现更高级的data structure: two stack -&gt; queue, min stack; max stack
3. 表达式计算、decode
4. 单调栈 eg. Histogram中找到最大的长方形

## 括号匹配

```python
def isValid(seq):
    s=[]
    for c in seq:
        if c in '([{':
            s.append(c)
        elif c==')' and (s and s[-1]=='('):
            s.pop()
        ...
```

或者我们用dict吧...

```python
def isValid(seq):
    left_bracket = []
    matching_bracket = {'{':'}', '[':']', '(': ')'}
    for b in brackets:
        if b in matching_bracket:
            left_bracket.append(b)
        elif not left_bracket or matching_bracket[left_bracket[-1]]!=b:
            return False
        else:
            left_bracket.pop()
        return not reft_bracket
```

{: brace-bracket/curly-bracket

\[: square bracket

\(: parenthesis 



## 简易计算器（+-）

本质是遇到‘）‘后把之前存的数字和operator拿出来做个计算，push回stack，直到stack空为止

```python
import operator
def arithmetic_expression_evaluation(terms):
  operands=[]
  operators=[]
  ops = {'+': operator.add, '-': operator.sub, '*': operator.mul, '/': operator.truediv}
  for term in terms:
    if term == '(':
      continue
    elif term == ')':
      right, left = operands.pop(), operator.pop()
      operands.append(ops[operators.pop])(left, right))
    elif term in ops:
      operators.append(term)
    else:
      operands.append(int(term))
  return operands[0]
```

{% tabs %}
{% tab title="queue&stack review里 从string输入的版本" %}
```python
def tokenize(s):
  from_num, num = False, 0
  for c in s:
    if c.isdigit():
      from_num = True
      num = 10*num+int(c)
    else:
      if from_num:
        yield num
        from_num, num = False, 0
      if not c.isspace():
        yield c
  if from_num:
    yield num

import operator
def arithmetic_expression_evaluation(terms):
  operands=[0]
  operators=['+']
  ops = {'+': operator.add, '-': operator.sub, '*': operator.mul, '/': operator.truediv}
  for term in terms:
    if term == '(':
      operators.append('+')
      ooperands.append(0)
    elif term == ')':
      right, left = operands.pop(), operator.pop()
      operands.append(ops[operators.pop])(left, right))
    elif term in ops:
      operators.append(term)
    else:
      operands.append(ops[operators.pop()](operands.pop(), term)
  return operands[-1]
```
{% endtab %}
{% endtabs %}

## 逆波兰表达式



## Decode

遇到【时把之前的内容暂存起来，保护现场，遇到】时把stack的内容释放出来，恢复现场。

四个变量，cnt， string，cnt\_stack, str\_stack

遇到 \[ : push cnt, string to stack, 重置cnt, string

遇到 \] : pop from stack

```python
def decodeString(s): 
    str_stack = []
    cnt_stack = []
    string = ""
    i = 0
    
    for i in range (len(s)): 
        if s[i].isdigit(): 
            cnt = int(s[i]) 
        elif s[i].isalpha(): 
            string += s[i] 
        elif s[i] == '[':
            cnt_stack.append(cnt)
            str_stack.append(string)
            string = ""
        else:
            cnt = cnt_stack.pop()
            last_ret = str_stack.pop()
            string = last_ret + string * cnt #这个string相加是O(n)的操作
            
    return string
```

$$O(n^2)$$ 

## 946 Validate Stack Sequences \(with distinct values\)

input: pushed=\[1,2,3,4,5\], popped = \[4,5,3,2,1\]  
output: True

```python
class Solution():
    def validateStackSequences(self, pushed, popped):
        s, next_consumed = [], 0
        for item in popped:
            while (not s or s[-1]!=item) and next_consumed<len(pushed):
                s.append(pushed[next_consumed])
                next_consumed += 1
            if s[-1]!=item:
                break
            s.pop()
        return not s
```

## Implement a queue using 2 stacks

两个array，s1用来进元素，s2用来出元素，如果在s2是空的时候还要继续出元素，就把s1的倒进s2里再pop 可以把它想象成底端对底端，中间有一个隐形的墙  
      --&gt; 6 5 \]\[4 3 2 1 --&gt;

stack1: to store new elements coming in \(enqueue\)  
- stack1.push\(\)  
stack2: to help with dequeue  
- case 1: if stack2 is NOT empty: stack2.pop\(\)  
- case 2: if stack2 is empty:  
     - move all elements from stack1 to stack2 one by one  
     - stack2.pop\(\)

```python
class Solution(object):
  def __init__(self):
    """
    Initialize your data structure here.
    """
    self.s1 = []
    self.s2 = []


  def poll(self):
    """
    return : int
    """
    self.move()
    return self.s2.pop() if self.s2 else None


  def offer(self, element):
    """
    input : int element
    """
    self.s1.append(element)


  def peek(self):
    """
    return : int 
    """
    self.move()
    return self.s2[-1] if self.s2 else None
    

  def size(self):
    """
    return : int 
    """
    return len(self.s1)+len(self.s2)


  def isEmpty(self):
    """
    return : boolean
    """
    return not self.s1 and not self.s2
  
  def move(self):
    if not self.s2:
      while self.s1:
        self.s2.append(self.s1.pop())
```

Enqueue: Time O\(1\)

Dequeue: Time worst case O\(n\), amortized O\(1\)

{% hint style="info" %}
amortized time vs average time:

amortized: amortize all operations within input （不管是什么样的input）  
average: average over all possible inputs \(和输入输出有关系 不能保证某一种情况\)
{% endhint %}

## Implement a stack using 2 queues

only 2 APIs available: enqueue, dequeue  
enqueue\(\):  
dequeue\(\):   
- if the queue is not empty, return the first element  
- if the queue is empty, return null  
stack \[ 1 2 3 4  
Q1 &lt;--    &lt;--  
Q2 &lt;--    &lt;--  
如果Q1没空，就把Q1塞到Q2，如果空了，返回最后的那个

```java
class Solution {
    private Queue<Integer> q1;
    private Queue<Integer> q2;
    
    /** Initialize your data structure here. */
    public Solution() {
      q1 = new ArrayDeque<>();
      q2 = new ArrayDeque<>();
    }

    /** Push element x onto stack. */
    public void push(int x) {
       q1.offer(x); 
    }

    /** Removes the element on top of the stack and returns that element. */
    public Integer pop() {
        Integer prev=q1.poll();
        Integer curr=q1.poll();
        while (curr!=null) {
          q2.offer(prev);
          prev=curr;
          curr=q1.poll();
        }
        Queue<Integer> tmp = q1;
        q1 = q2;
        q2 = tmp;
        return prev;
    }

    /** Get the top element. */
    public Integer top() {
        Integer ret = pop();
        if (ret!=null){
          q1.offer(ret);
        }
        return ret;
    }

    /** Returns whether the stack is empty. */
    public boolean isEmpty() {
       return top()==null;
    }
}
```

## Implement a stack using 1 queue

如果此时多了一个size\(\)的API  
stack \[ 1 2 3 4  
Q &lt;--    &lt;--

做size-1次：拿出来再从尾部塞回去

## Implement a stack with Max API 

Solution 1: Brute Force, O\(n\), iterate each element in the stack to find the max

Solution 2: Trade space for time

Current top of stack \(x, x\_max\)

New value y:

push: if y&gt;x\_max, store \(y, y\) else, store \(y, x\_max\)

pop: tmp=lst.pop\(\), return tmp\[0\]

```python
Class stack:

    def __init__(self):
        self.stack=[]
        
    def is_empty(self):
        return len(self.stack)==0
        
    def max(self):
        if not self.is_empty():
            return self.stack[len(self.stack)-1][1]
        raise Exception('max(): empty stack')
        
    def push(self, x):
        tmp = x
        if not self.is_empty():
            tmp = max(tmp, self.max())
        self.stack.append((x,tmp))
    
    def pop(self):
        if self.is_empty():
            raise Exception('pop(): empty stack')
        elem = self.stack.pop()
        return elem[0]
```

Time: O\(1\)

Space: O\(n\)

同理，也可以实现getmin

## Min Stack

### 两个stack

stack1 \(input\) 3 1 4 0  
stack2 \(min\)   3 1 1 0

Push x: stack.append\(x\)

Case 1: x&lt;getMin\(\): minStack.append\(x\)

Case2: x&gt;=getMin\(\): minStack.append\(getMin\(\)\)

Pop x: stack.pop\(\), minStack.pop\(\)

Time O\(1\)  
Space O\(n\)

```python
class Solution(object):

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack = []
        
    def push(self, x):
        """
        input : int x
        return : 
        """
        tmp = x
        if self.stack:
          tmp = min(tmp, self.min())
        self.stack.append((x, tmp))
        
    def pop(self):
        """
        return : int
        """
        if not self.stack:
          return -1 
        else:
          tmp = self.stack.pop()
          return tmp[0]

        
    def top(self):
        """
        return : int
        """
        return -1 if not self.stack else self.stack[-1][0]
        
    def min(self):
        """
        return : int
        """
        return -1 if not self.stack else self.stack[-1][1]
```

### 优化 如果有很多duplicate

> CART 如果有size就可以用tuple

#### 加counter &lt;1, 100&gt; 代表有100个1

#### 第一次出现它时里面有几个元素 &lt;1,2&gt; 出现1的时候是第二个位置

也可以用3个stack，不是存tuple而是又加一个stack来存位置



Follow up: duplicate element

优化，可以只有数值有变时再存min

Push x: stack.append\(x\)

Case 1: x&lt;=getMin\(\): minStack.append\(x\)

Case2: x&gt;getMin\(\): Do nothing

Pop x: 

if stack\[-1\]==getMin\(\) 

      minStack.pop\(\)    

stack.pop\(\)

## Implement a deque with multiple stacks

left XXXXXXXXXXXXXXXXXXXXXXXXX right  
l.add\(\)                                                            r.add\(\)  
l.remove\(\)                                                     r.remove\(\)

### 2 stacks           

  \]s1 s2\[  
s1: simulate the left end of the deque  
s2: simulate the right end of the deque  
left.add\(\): s1.push\(\)                                                          O\(1\)  
right.add\(\): s2.push\(\)                                                          O\(1\)  
left.remove\(\):   
- if s1 is not empty, just stack1.pop\(\)                                                          O\(1\)  
- if s2 is empty,   
       - move all elements from s2 to s1                                                          O\(n\) worst  
       - stack1.pop\(\)  
right.remove\(\): similar 

$$
\begin{array}{ll}{\text { L. remove }()} & {-2 n+1} \\ {\text { R.remove() }} & {-2(n-1)+1} \\ {\text { L. remove() }} & {-2(n-2)+1} \\ {\text { R.remove() }} & {-2(n-3)+1} \\ {\text { L. remove() }} & {-} \\ {\text { R.remove() }} & {-} \\ {\text { L. remove() }} & {-} \\ {\text { R.remove() }} & {-\cdots}\end{array}
$$

$$
\text { Amortized time }=\frac{2^{*}(n+(n-1)+(n-2)+\ldots+1)+n}{n}=\frac{n^{*}(n-1)+n}{n}=(n-1)+1=n
$$

How to speed up the remove\(\) operation?

### 3 stacks

4 3 2 1 \]s1 s2\[        
s3 buffer \[ 8 7 6 5   
  
两个stack的时候腹背受敌，只要一边空了，这个操作就非常expensive。如果能只挪一半的元素，不管left.remove还是right.remove就都不怕了。借助s3这个buffer，让左右两边均等。

8 7 6 5  \]s1 s2\[ 4 3 2 1  
s3 buffer \[

```python
class Solution:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.left = []
        self.right = []
        self.buffer = []

    def isEmpty(self):
        """
        Return true if the deque is empty otherwise return false
        """
        return False if len(self.left)+len(self.right)+len(self.buffer)!=0 else True

    def size(self):
        """
        return Size of deque
        """
        return len(self.left)+len(self.right)

    def offerFirst(self, x):
        """
        Offer x to the first position of the deque
        """
        self.left.append(x)

    def offerLast(self, x):
        """
        Offer x to the last position of the deque
        """
        self.right.append(x)

    def peekFirst(self):
        """
        Peek the first element of the deque.
        Return None if the deque is empty.
        """
        if not self.left: 
          self.move(self.right, self.left)
        return self.left[-1] if self.left else None

    def peekLast(self):
        """
        Peek the last element of the deque.
        Return None if the deque is empty.
        """
        if not self.right:
          self.move(self.left, self.right)
        return self.right[-1] if self.right else None

    def pollFirst(self):
        """
        Poll the first element of the deque.
        Return None if the deque is empty.
        """
        if not self.left: 
          self.move(self.right, self.left)
        return self.left.pop() if self.left else None

    def pollLast(self):
        """
        Poll the last element of the deque.
        Return None if the deque is empty.
        """
        if not self.right: 
          self.move(self.left, self.right)
        return self.right.pop() if self.right else None
    
    def move(self, fromS, toS):
        """
        helper function to serve as a buffer; move half of the element from fromS to toS
        """
        if not fromS:
          return
        
        halfsize = len(fromS)//2
        
        while halfsize>0: #这个太坑爹了 没有等号！！不然一个元素的时候要玩完
          self.buffer.append(fromS.pop())
          halfsize-=1
        
        while fromS:
          toS.append(fromS.pop())
        
        while self.buffer:
          fromS.append(self.buffer.pop())
```

$$
\begin{array}{l}{\text { 1st call Right. remove(): }} \\ {-\quad n / 2 \text { stack 1. pop }()} \\ {\quad- \text { n/2 stack 3. push()}} \\ {-\quad \text { n/2 stack 1. push()}} \\ {\text { - n/2 stack 1. push() }} \\ {\text { - n/2 stack 3. pop() }} \\ {\text { - n/2 stack 1. push() }} \\ {\text { - n/2 stack 1. push() }} \\ {\text { - stack 2. pop() }} \\ {\text { total 3 } n+1 \text { stack operations }}\end{array}
$$

$$
\text { Amortized time }=\frac{(3 n+1)+1 *(n / 2-1)}{n / 2}=\frac{3.5^{*} n}{0.5 * n}=7=O(1)
$$

## Sort Numbers with 2/3 Stacks

### 3 stacks:

s1 input   \[ 1 3 2 4  
s2 buffer \[                                   global\_min  
s3 output \[

> one sentence: use selection sort to sort  
> data structure: record globalmin when buffering elements from s1 to s2; when s1 is empty, put the global\_min in s3, put all but s2 back to s1.  
> algorithm \(high level--detail\):

### 2 stacks:

s1 input   \[ 1 3 2 4  
s2 left, output\| right, buffer \[                                    global\_min

> output: put global\_min back   
> buffer:

过去得到的 global\_min一定小于当前轮的 global\_min

方法一：while stack2.size\(\)&gt;stack2.initial\_size\_before\_this\_iteration  
方法二：while stack2.top\(\)&gt;=global\_min  
keep popping back to s1 

### Follow Up: duplicate element: add a counter 

s1 input   \[ 1 3 2 4  
s2 left, output\| right, buffer \[                                    global\_min      counter

```java
public class Solution {
  public void sort(LinkedList<Integer> s1) {
    LinkedList<Integer> s2 = new LinkedList<Integer>();
    // Write your solution here.
    if (s1==null || s1.size()<=1){
      return;
    }
    sort (s1, s2);
  }

  public void sort(Deque<Integer> input, Deque<Integer> buffer) {
    while (!input.isEmpty()) {
      int global_min = Integer.MAX_VALUE;
      int counter = 0;
      while (!input.isEmpty()) {
        if (input.peekFirst()<global_min){
          global_min=input.peekFirst();
          counter=1;
        }else if (input.peekFirst()==global_min) {
          counter++;
        }
        buffer.offerFirst(input.pollFirst());
      }
      while (!buffer.isEmpty() && buffer.peekFirst() >=global_min) {
        int tmp = buffer.pollFirst();
        if (tmp!=global_min) {
          input.offerFirst(tmp);
        }
      }
      while (counter-->0) {
        buffer.offerFirst(global_min);
      }
      }
    
    while (!buffer.isEmpty()){
      input.offerFirst(buffer.pollFirst());
    }
    }

  }
```

## 3 stacks 实现merge sort

## Leetcode 1003 合法字符串

和括号匹配的问题一样，遇到c时匹配并清空

```python
def isValid(S):
    if not S:
        return False 
    key = 'abc'
    stack = []
    
    for x in S:
        if x!= 'c':
            stack.append(x)
        else:
            if len(stack)< 2:
                return False 
            if key == ''.join([stack[-2], stack[-1], x]):
                stack.pop()
                stack.pop()
            else:
                return False
    
    return len(stack)==0
```

Time: O\(n\)

Space: O\(n\)

## Leetcode 856 表达式求值

（）has score 1

AB has score A+B

\(A\) has score 2\*A

```python
def scoreofparenthesis (S):
    stack = [0]
    for x in S:
        if x == '(':
            stack.append(0)
        else:
            top = stack.pop()
            if top == 0:
                stack[-1] += 1
            else:
                stack[-1] = top*2 + stack[-1]
     return stack[0]                
```

```python
def evaluate(seq):
    s=[]
    for c in seq:
        if c== '(':
            s.append(c)
        else:
            acc=0
            while s and s[-1]!='(':
                acc += s.pop()
            s.pop()  #pop the (
            s.append(max(acc*2, 1))  #when acc=0
    return sum(s) #because ()() s=[1,1]
```

