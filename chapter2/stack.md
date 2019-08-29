# Stack

Last in First Out

list就是一个天然的stack，不需要单独定义class。

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



## 例题： 判断算术表达式



