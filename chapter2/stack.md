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

