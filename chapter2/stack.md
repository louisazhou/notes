# Stack

Last in First Out

list就是一个天然的stack，不需要单独定义class。

push 

pop

## 例题：括号匹配

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



## 例题： 算术表达式 

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

## 例题：946 Validate Stack Sequences

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

## Implement a stack/queue with Max API 

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

