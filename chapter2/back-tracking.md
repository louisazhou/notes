---
description: 另一种recursion
---

# Back Tracking

回溯法：多阶段决策问题（multistage decision problem），所解决的问题需要分成多阶段，每个阶段做一个具体的决策。可以采用递归的写法，更系统地一次性获取决策的组合。

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

从根节点走到叶子节点，走过的边形成的集合就是包含了每个阶段所做具体decision的集合，也就是一个answer set。相当于深度优先遍历，pre-order地遍历根到叶子的所有compatible路径。

Answer这个集合有且只有一个，内容却动态变化。非常重要！

current position做深度优先遍历

容易漏的点：如果没有answer remove，那么在下一个阶段的选择时还会记住上一个阶段的所有选择，结果就错了。在Back Tracking的时候一模一样的路径不走两次，每次选择时不记得之前的世界线。

## Permutation

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

n个数，n个阶段，所以 $$O(n^{n})$$ 

但其实没那么大，因为考虑到compatibility，这一定是一个overestimate。只要剪枝做得好，实际运行效率一定很高。

另外，在上面的实现中，用的是perm这个\[\]的append和pop的动态变化来做到上面pseudocode中的current\_position+1 

Alternatively

```python
def bt(perm, current_position, N, numbers, answers):
    if current_position==N:
        answers.append(perm[:])
    
    for num in nums:
        if num not in perm:
            perm.append(num)
            bt(perm, current_position+1, N, numbers, answers)
            perm.pop()
```

但是不管哪种方法，在test for compatibility的时候，这个检查都是O\(n\)的。如果想在这里优化，要么建一个Hashset，要么就复用输入的nums，去分析nums的每一个能放的位置，把能放的数字放进结果集的list里。

```python
class Solution(object):
    def bt(answers, current_position, N, nums):
        if current_position == N:
            answers.append(nums[:])
            return
        for i in xrange(current_position, len(nums)):
            nums[current_position], nums[i] = nums[i], nums[current_position]
            bt(answers, current_position+1, N, nums)
            nums[current_position], nums[i] = nums[i], nums[current_position]
            return
    
    def permute(self, nums):
        answers = []
        bt(answers, 0, len(nums), nums)
        return answers    
```

### Generate all permutations \(with duplicates\)

> Input: \[1,2,2\]
>
> Output:  
> \[1,2,2\]  
> \[2,1,2\]  
> \[2,2,1\]
>
> Note: \[1, 2a, 2b\] is the same as \[1, 2b, 2a\]

As compared to the above, we cannot use the same compatibility test

1. For the same stage, we could not use the same value twice.  Use a collection to store the choices we have tried so far **at a given stage**.
2. For the different stages, a value could be used if and only if the total number of time this value has occurred previously +1 &lt;= \# of time this value occurs in the input. Or in other words, the total time previously &lt; \# of time in the input. 

```python
def bt(answers, permutation, N, nums):
    if len(permutation) == N:
        answers.append(permutation[:])
        return
    used=set()
    for num in nums:
        if permutation.count(num)<nums.count(num) and num not in used:
            used.add(num)
            permutation.append(num)
            bt(answers, permutation, N, nums)
            permutation,pop()
    return

class Solution(object):
    def permuteUnique(self, nums):
        answers, permutation = [], []
        bt(answers, permutation, len(nums), nums)
        return answers
```

## Subset

### Generate all subsets from a set of distinct integers

$$2^{n}$$ 

3 important things for solving problem with backtracking:

1. Is this a multistage decision problem? \(get a single subset\)
2. What decision we will make for a given stage? \(pick or not pick this number\)
3. Is there any compatibility concern? \(no\) 

Difference between subset and permutation is, for permutation, at each stage we must choose some value, but for subsetting, omitting is also a valid option. The decision made at each stage is thus no longer 'which number to pick', with a total stage of N input; but rather, 'to pick or not' is the decision at each stage. 

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

### Generate all subsets \(with duplicates\)

If we decide not to pick a value for stage i, then for all the same values after stage i, we will make the same decision. 

> \[2a, 2b, 2c\]  
> \[N,   N,   N\]  
> \[Y,    N,   N\]  
> \[Y,    Y,    N\]  
> \[Y,    Y,    Y\]

code的实现里：可以先sort，于是所有一样的就挨在一起了

```python
def bt(answers, subset, current_position, N, nums):
    if current_stage==N:
        answers.append(subset[:])
        return
    subset.append(nums[current_position])
    bt(answers, subset, current_position+1, N, nums)
    subset.pop()
    i=current_position+1
    while i<len(nums) and nums[current_position] == nums[i]:
        i+=1
    bt(answers, subset, i, N, nums)
    return

class Solution(object):
    def subsetWithDup(self, nums):
        nums.sort()
        answer = []
        bt(answers, [], 0, len(nums), nums)
    return answers
```

## Numeration

### Generate all Valid Parentheses 

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses. 

1. Is this a multistage decision problem? \(get a single balanced sequence\)
2. What decision we will make for a given stage? \(left or right parentheses\)
3. Is there any compatibility concern? \(if there is no left bracket on its left to match the current \), then we cannot put the \) at the current position\). 

During backtracking, we could record the number of remaining \( and the number of remaining \); 

or number of used \( and the number of used \). used‘\(’==used‘\)’时不能放'\)', 只有在左括号的数量多于右括号时才能加右括号。

```python
def bt(answers, sequence, l, r):
    # l, r: the number of remaining parenthesis
    if r == 0:
        answers.append(''.join(sequence))
    if l>0:
        sequence.append('(')
        bt(answers, sequence, l-1, r)
        sequence.pop()
    if r>l:
        sequence.append(')')
        bt(answers, sequence, l, r-1)
        sequence.pop()
    return

class Solution(object):
    def generateParenthesis(self, n):
        answers, sequence = [], []
        bt(answers, sequence, n, n)
        return answers
```

Branching Factor, B, Height of the Tree, H; Time Complexity: $$O(B^{H})$$ 



### Combinations

Choose K from N. 

In a total of K stages, for a given stage, possible answers:

1. Choose number from 1...n  But!! \(Problem is, in the compatibility test, we need to make sure first the number we choose is distinctive from each other; second combination\(1,2\) and combination \(2,1\) are the same. How to rule out the second possibility?\)
2. Choose or not choose the corresponding number.  But!! \(Problem is, each selection has a different range\)
3. Choose number but with a predefined order. We can do this so that the current number we pick will always be larger than the previous one.  Yeah!!（This way, we don't need to consider compatibility）

```python
def bt(combs, comb, lower_bound, n, k):
    if len(comb)==k:
        combs.append(comb[:])
        return
    for curr_number in range(lower_bound, n+1):
        comb.append(curr_number)
        bt(combs, comb, curr_number+1, n, k)
        comb.pop()
        
def get_all_combs(n,k):
    combs, comb = [],[]
    bt(combs, comb, 1, n, k)
    return combs
```

### Factor Combinations

难点：阶段数不固定！但是一样的是，

N=f1\*\(N'\)=f1\*\(f2\*\(N''\)\)=... 直到N‘’‘=1 

需要注意的是，避免组合重复，所以在generation的时候要enforce an order。

```python
def bt(answers, comb, n):
    if n==1 and len(comb)>1:
        answers.append(comb[:])
        return
    for f in range(2 if not comb else comb[-1], n+1):
        if n%f--0:
            comb.append(f)
            bt(answers, comb, n/f)
            comb.pop()

class Solution(object):
    def getFactors(self, n):
        answers = []
        bt(answers, [], n)
        return answers
```

最后prefer branching factor更小的写法

因为如果n=a\*b, 那么一定有一个大于sqrtn一个小于sqrtn的，所以其实只需要for到sqrt，

```python
import math

def bt(answers, comb, n):
    if len(comb)>0:
        answers.append(comb + [n])
    for f in range (2 if not comb else comb[-1], int(math.sqrt(n))+1):
        if n%f==0:
            comb.append(f)
            bt(answers, comb, n/f)
            comb.pop()

class Solution(object):
    def getFactors(self, n):
        answers=[]
        bt(answers, [], n)
        return answers
```

上面的代码还可以while循环再简化

```python
def bt(answers, comb, s, n):
    while s*s <= n:
        if n%s==0
            answers.append(comb+[s, n/s])
            bt(answers, comb+[s], s, n/s)
        s+=1


class Solution(object):
    def getFactors(self, n):
        answers = []
        bt(answers, [], 2, n)
        return answers
```

