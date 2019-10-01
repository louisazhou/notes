---
description: 非常好的面试情境，啥都考了= =
---

# Probability, Sampling and Randomization with Python

## Probability

### Review: Conditional Probability

#### Chain Rule: 

P\(A\|B\)=P\(AB\)/P\(B\)  
P\(A,B,C\)=P\(A\|B,C\)\*P\(B,C\)

Example of Chain Rule: 抽彩票 无论次序，每个人抽中的概率都是1/N  
P\(1YES\)=1/N  
P\(2Yes\)=P\(1No\)\*P\(2Yes,1No\)=\(1-1/N\)\*1/\(N-1\)=1/N  


### Shuffling Algorithm

两种思路来想，N张牌，N!个permutation, 每个permutation出现的概率是 $$\frac{1}{N!}$$   
  
第二种思路，条件概率的角度 如果有4张牌，做洗牌，那么每张牌在第一个位置的概率都是1/4  
第二个位置1/4 （因为 在该张牌不出现在第一个位置上的前提下，这张牌出现在了第二个位置上 3/4\*1/3=1/4）  
...   
每张牌在每个位置出现的概率都是1/N

Python中如何做randomization？ 两个函数   
random.random\(\): uniformly distributed, \[0,1\)  
random.randomint\(x,y\):uniformly distributed, \[x, y\]

```python
def shuffle(array):
    l_len = len(array)
    for i in range (l_len-1, -1, -1):  #最后一个位置0或者1都可以，也就是自己要不要和自己swap
    #从最后一个位置开始一个个往前generate
        rand=random.randint(0,i)
        array[rand], array[i] = array[i], array[rand]
    return array
```

Time: O\(n\)              Space:O\(1\)

#### Similar Problems to Solve

* Select random K elements in the size n array \(return first k elements\)
* Get a k-permutation of the array of size n

## Sampling

### Unlimited Data Flow

How to sample for an unlimited data flow and we are required to return 1 random number among all numbers read so far, such that the probability of returning any element read so far is 1/n?

如果是limited data，我们就可以shuffle所有number，取第一个；  
对于unlimited，N未知，所以无法存下所有数字  
所以，我们可以用一个counter\(c\), sample\(s\), 既然无法存下所有data，那么不如只保留O\(1\)space的sample。  
第一步，读进一个s=a，c=1；那么random.randomint\(0,0\), 100% a会被留下。  
第二步，又读进一个s=b，c=2；那么抛一枚硬币（random.randomint\(0,1\)），正面时用b作为sample，背面时用a作为sample，50%、50%；a和b被保留概率各一半。注意，sample之后这个sample就定下来了，如果sample之后留下b，那么以后再call sample时就没有了第一次的a。  
第三步，再读进s=a，c=3；掷一没筛子，（random.randomint\(0,2\)），这个random的012分别对应着abc，这个数值选中后我们其实也是对一个sample size是N的数做sampling。我们需要保证这次sampling得到c的概率是1/3， P\(3,a\)=P\(3a, 2a\)=P\(3,a\|2,a\)\*P\(2,a\)=2/3\*1/2=1/3。  
所以实际，内存只存了一个数，也就是被sample出来的这个sample。当前的随机性保证了，只是牺牲了中间步骤的独立性。

#### Reservoir sampling 就是这个思想

{% code-tabs %}
{% code-tabs-item title="pseudo code" %}
```python
s = [] #unlimited data flow
solu=s[0]
i=1
while (True):
    i+=1
    r=random.randint(0,i-1)
    if r==0:
        solu=s[i-1]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

开k个格子，就能让每个item被return的概率为k/n

{% code-tabs %}
{% code-tabs-item title="pseudo code" %}
```python
s = [] #unlimited data flow
solu=s[0:100]
i=100
while (True):
    i+=1
    r=random.randint(0,i-1)
    if r<100:
        solu[r]=s[i-1]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Return a Random Largest Number's Index

比如data stream "1, 2, 5a, 3, 4, 3, 4, 5b". 如果所有数字都一样，那么这就和上面的题一模一样，因为我们只需要在现在的这些data里随便return一个；而现在：记录current\_max\(cur\_max\)记录当前最大值；继续maintain上面题目的s和c。  
所以我们只需要care当前最大的数的sample和count，如果stream进来的数字比当前max小，就直接忽略它。所以本质上，只需要在上面的基础上加一个对于current\_max的条件判断

```python
class generator(object):
    def __init__(self):
        self.max_value=float('-inf')
        self.index=-1
        self.sample=0
        sample.count=0
    
    def read(self, val):
        self.index += 1
        if val>self.max_value:
            self.count=1
            self.sample=self.index
            self.max_value=val
        elif val==self.max_value:
            self.count+=1
            r=random.randint(0, self.count-1)
            if r==0:
                self.sample=self.index
```

Time: O\(n\)              Space:O\(1\)

## Randomization

用random7生成random5：  
如果是6或者7，就直接删了； P\(0 can be returned\) = 1/7\*5/7=1/5

用random5生成random7：  
第一步：random5生成random25  
random\(5\)\*5 + random\(5\)  
第二步：random25的值只要大于20就重来，用这个数字%7.

```python
def random25():
    return 5*random5()+random5()

def random7():
    num=random25()
    while num>20:
        num=random25()
    return num%7
```

Time: O\(1\)              Space:O\(1\)         试验成功概率21/25

