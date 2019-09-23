---
description: 另一种recursion
---

# Back Tracking

回溯法：多阶段决策问题，所解决的问题需要分成多阶段，每个阶段做一个具体的决策。可以采用递归的写法，更系统地一次性获取决策的组合。

模板的pseudo code：

```python
def backtracking(answer, current_position, N, possible_decisions):
#anwer: list that should contain those compatible decisions we previously made. The size of it should be == current_position
#N: integer indicates the total number of steps to build our final answer
#current_position: Integer indicates the id of the step we are at right now. Starts at 0
#possible_decisions: a map that associates the id of a step to a collection of possible decisions we can make for that step.

if len(answer)==N:
    #base case, 得到了一个想解决的问题的解 
    ... #应该把它存下来

else:
    for decision in possible_decisions[current_position]:
        if is_compatible(answer, decision):
            answer.add(decision)
            backtracking(answer, current_position+1, N, possible_decisions)
            answer.remove(decision)
```

从根节点走到叶子节点，走过的边形成的集合就是包含了每个阶段所做具体decision的集合，也就是一个answer set。相当于深度优先遍历，遍历根到叶子的所有compatible路径。

Answer这个集合有且只有一个，内容却动态变化。非常重要！

current position做深度优先遍历

如果没有answer remove，那么在下一个阶段的选择时还会记住上一个阶段的所有选择。

## Solved by Backtracking

### Generate all permutations from Distinct numbers 

$$n!$$ 

3 important things for solving problem with backtracking:

1. Is this a multistage decision problem? \(get a single permutation\)
2. What decision we will make for a given stage? \(pick a number from input\)
3. Is there any compatibility concern? \(the number we pick at the current stage should not be the same as those we picked earlier\) 

```python
def bt(perms, perm, seq):
    if len(perm)==len(seq): #做完具体决策
        perms.append(perm[:])
        return
    for num in seq:
        if is_compatible(perm, num):
           perm.append(num)
           bt(perms, perm, seq)
           perm.pop(num)
             
def is_compatible(perm, num):
    return num not in perm

def permutations(seq):
    perms, perm = [], []
    bt(perms, perm, seq)
    return perms
```

### Generate all subsets from a set of distinct integers

$$2^{n}$$ 

3 important things for solving problem with backtracking:

1. Is this a multistage decision problem? \(get a single subset\)
2. What decision we will make for a given stage? \(pick or not pick this number\)
3. Is there any compatibility concern? \(no\) 

```python
def bt(subsets, subset, seq, curr_pos):
    if curr_pos==len(seq): #做完具体决策
        subsets.append(subset[:])
        return
    
       # Case 1: pick the number seq[currr_pos]
     subset.append(seq[curr_pos])
     bt(subsets, subset, seq, curr_pos+1)
     subset.pop()
       
       # Case 2: Not pick the number
     bt(subsets, subset, seq, curr_pos+1)        


def subset(seq):
    ss, s = [], [] ##ss means all subset s means a single valid subset
    bt(ss, s, seq, 0)
    return ss
```

### Generate all Valid Parentheses 

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses. 

1. Is this a multistage decision problem? \(get a single balanced sequence\)
2. What decision we will make for a given stage? \(left or right parentheses\)
3. Is there any compatibility concern? \(if there is no left bracket on its left to match the current \), then we cannot put the \) at the current position\). 

During backtracking, we could record the number of remaining \( and the number of remaining \); 

or number of used \( and the number of used \). 

used\(==used\)时不能放）

  right&lt;=left, max\(left\)=n\) 

只有在左括号的数量多于右括号时才能加右括号。



