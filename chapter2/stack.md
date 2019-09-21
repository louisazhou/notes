---
description: Last in First Out     list就是一个天然的stack，不需要单独定义class。
---

# Stack

push 

pop

top

从左到右linear scan，需要不断回头看过去的元素时，使用stack

{% embed url="https://www.jianshu.com/p/78e3b21b842d" %}



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



## 算术表达式  逆波兰表达式

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

## 946 Validate Stack Sequences

## Implement a queue using 2 stacks

两个array，s1用来进元素，s2用来出元素，如果在s2是空的时候还要继续出元素，就把s1的倒进s2里再pop

```python
Class Queue:
    
    def __init__(self):
        self.s1 = []
        self.s2 = []
        self.head = None
    
    def enqueue(self, x):
        if not self.s1
            self.head = x
        self.s1.append(x)
    
    def dequeue(self):
        if not self.s2:
            while self.s1:
                self.s2.append(self.s1.pop())
        return self.s2.pop()
    
    def is_empty(self):
        return not self.s1 and not self.s2
    
    def peek(self):
        if self.s2:
            return self.s2[-1]
        return self.head
    
    def size(self):
        return len(self.s1)+len(self.s2)
```

Enqueue: Time O\(1\)

Dequeue: Time worst case O\(n\), amortized O\(1\)

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

Push x: stack.append\(x\)

Case 1: x&lt;getMin\(\): minStack.append\(x\)

Case2: x&gt;=getMin\(\): minStack.append\(getMin\(\)\)

Pop x: stack.pop\(\), minStack.pop\(\)



优化，可以只有数值有变时再存min

Push x: stack.append\(x\)

Case 1: x&lt;=getMin\(\): minStack.append\(x\)

Case2: x&gt;getMin\(\): Do nothing

Pop x: 

if stack\[-1\]==getMin\(\) 

      minStack.pop\(\)    

stack.pop\(\)

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



