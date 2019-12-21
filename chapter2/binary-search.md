---
description: 普通版、2D版、万金油版、有重复元素版
---

# Binary Search

## Classic Version

每次缩小一半的查找范围，和中间值比较。大于中间值往左，小于中间值往右。通过L=mid+1 or R=mid-1改变LR边界值。

{% tabs %}
{% tab title="Python" %}
```python
def binary_search(nums, target):
 if not nums:
  return None
 left, right = 0, len(nums)-1
 while left<=right: #小于还是小于等于？可以用1个元素debug，循环都进不去，所以这里一定要小于等于
  mid=(left+right)/2
  if nums[mid]<target:
   left=mid+1 #必须+1， 因为还是用1个元素debug，如果数组里是5，找的是7，循环出不去
  elif nums[mid]>target:
   right=mid-1
  else 
   return mid
 return None 
```
{% endtab %}

{% tab title="Java" %}
```
//package 一般是一个名词，而且全小写、用点
//class 名词，首字母大写
//interface 名词，upper ammelce
class Solution(object):
    static int binarySearch(int[] array, int target){
        if (array==null){ //如果是0其实没关系 因为进不去循环，返回-1
        return -1;
        }
        int l = 0;
        int r = array.length-1;
        while (l<=r){
            int mid = l+(r-l)/2; //int越过32位的overflow 可能会变成负数，可能是一个奇怪的数，不会自动变成long
            if (array[mid]==target){
            return mid;
            }
            elif (array[mid]<target){
            left = mid+1;
            }
            else{
            right = mid-1;
            }
        }
      return -1;
    } 
```
{% endtab %}
{% endtabs %}

Time: O\(logn\)

## 2D Version

一个二维坐标可以和一维坐标联系，4\*4的matrix，（2，2）代表2\*4+2个元素比它小。index=row\_index\*m+col\_index.\(m: number of cols\).

所以，row\_index=index/m; col\_index=index%m.

第一个index是0，最后一个index是n\*m-1。所以初始化的left=0, right=n\*m-1.

```python
class Solution(object):
  def search(self, matrix, target):
    """
    input: int[][] matrix, int target
    return: int[]
    """
    # write your solution here
    if not matrix or len(matrix)==0:
      return [-1,-1]

    m, n = len(matrix), len(matrix[0])
    left, right= 0, m*n-1

    while left<=right:
      mid = (left+right)//2
      row = mid//n
      column = mid%n

      if matrix[row][column]==target:
        return [row, column]
      
      elif matrix[row][column]<target:
        left = mid+1
      
      else:
        right = mid-1
    
    return [-1,-1]
```

Time: O\(log\(n\*m\)\)



{% hint style="info" %}
1. 每次搜索都比上次少一半&lt;------&gt;所以我们必须保证每一次的搜索范围都在减小
2. 终止时剩下1～2个元素，才能确认target是否在这两个元素之中&lt;----&gt;淘汰元素时要保证target不会被淘汰
{% endhint %}



## Find Closest Element

eg \[1,2,5,9\] target=3, index=1, number=2

`错误示范` 

因为如果（0，1），剩两个数或者剩下一个数，都会在这里做死循环；违反了上面中的原则1，搜索空间并没有减小，因为line5的mid永远都是一个值。

不能让left和right之间没有元素，不然会让left和right来回传值, 所以while的条件必须要在left和right之中隔一个值

{% tabs %}
{% tab title="错误示范" %}
```python
def binary_search (nums, target):
    left=0
    right=len(nums)-1
    while left<=right:
        mid=(left+right)/2
        if nums[mid]>target:
            right=mid
        elif nums[mid]<target:
            left=mid
        else:
            return mid
return None
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
1. 本质是提前一步退出

   搜索空间只剩下两个元素，target和谁更近就是谁 

2. Post-processing 最后剩下LR还没有比较
{% endhint %}

`正确做法`

{% tabs %}
{% tab title="正确做法" %}
```python
class Solution(object):
  def closest(self, array, target):
    """
    input: int[] array, int target
    return: int
    """
    # write your solution here
    if not array or len(array)==0:
      return -1
    
    left, right = 0, len(array)-1

    while left<right-1:
      mid = (left+right)//2
      if array[mid]==target:
        return mid
      elif array[mid]<target:
        left = mid #不可以+1
      else:
        right = mid #不可以-1
    
    return left if (array[right]-target>target-array[left]) else right

#上面这种写法虽然没有+1-1 但是肯定不会有死循环，因为while循环条件不同了，循环内一定是三个元素，
#所以不会在两个数之间来回传值
```
{% endtab %}
{% endtabs %}

Time: O\(logn\)

## First Occurrence 

> 如果找不到，return-1 （面试时，注意和面试官沟通，回复-1还是None）

1. num\[mid\]&lt;target, 左半边不要了，一定在右边，可以+1\(left=mid+1\)
2. num\[mid\]&gt;target, 右半边不要了，一定在左边，但是含mid\(right=mid\)
3. num\[mid\]=target, 虽然找到了，但也有可能不是第一个，所以right=mid

```python
class Solution(object):
  def firstOccur(self, array, target):
    """
    input: int[] array, int target
    return: int
    """
    # write your solution here
    if not array or len(array)==0:
        return -1
    
    left, right = 0, len(array)-1
    
    while left<right-1:
      
      mid = (left+right)//2
      
      if array[mid]==target:
        right=mid
      elif array[mid]<target:
        left=mid+1
      elif array[mid]>target:
        right=mid-1
    
    if array[left]==target:
      return left
    if array[right]==target:
      return right

    return -1  
```

## Last Occurrence

和First Occurrence的区别在 

1. mid=target时往左还是往右: 右

2. Post-Processing的顺序先后：先检查右边

```python
class Solution(object):
  def lastOccur(self, array, target):
    """
    input: int[] array, int target
    return: int
    """
    # write your solution here
    if not array or len(array)==0:
        return -1
        
    left, right = 0, len(array)-1
    
    while left<right-1:
      
      mid = (left+right)//2
      
      if array[mid]==target:
        left = mid
      elif array[mid]<target:
        left = mid+1
      elif array[mid]>target:
        right = mid-1
    
    if array[right]==target:
        return right
    if array[left]==target:
        return left
    
    return -1
```

## K Closest to Sorted Array

log\(n\)+k

line 35其实根本不用abs 因为既然sorted了也就知道谁大谁小了

{% tabs %}
{% tab title="While循环条件1" %}
```python
class Solution(object):
  def kClosest(self, array, target, k):
    """
    input: int[] array, int target, int k
    return: int[]
    """
    # write your solution here
    res=[]
    if len(array)==0 or k==0:
      return res
    index=self.getIndex(array, target)
    l,r=index-1, index+1
    res.append(array[index])
    while len(res)<k and (l>=0 or r<len(array)):
      if r<len(array) and (l<0 or abs(array[l]-target)>abs(array[r]-target)):
        res.append(array[r])
        r+=1
      elif l>=0:
        res.append(array[l])
        l-=1
    return res
    
  

  def getIndex(self, array, target):
    left, right = 0,len(array)-1
    while left<right-1:
      mid=(left+right)//2
      if array[mid]==target:
        return mid
      elif array[mid]<target:
        left=mid
      else:
        right=mid
    return left if abs(array[left]-target)<abs(array[right]-target) else right
```
{% endtab %}

{% tab title="While循环条件2" %}
```python
class Solution(object):
  def kClosest(self, array, target, k):
    """
    input: int[] array, int target, int k
    return: int[]
    """
    # write your solution here
    if not array or len(array)==0 or k<0:
      return -1
    
    index = self.binarySearch(array, target)
    result = []
    if k>0:
      result.append(array[index])
    
    i,j= index-1, index+1
    
    while len(result)<k and (i>=0 or j<=len(array)-1):
      if i>=0 and (j>len(array)-1 or target-array[i]<array[j]-target):
        result.append(array[i])
        i-=1
      else:
        result.append(array[j])
        j+=1
    return result

  def binarySearch(self, array, target):
    left, right = 0, len(array)-1

    while left<right-1:
      mid = (left+right)//2
      if array[mid]==target:
        return mid
      elif array[mid]<target:
        left = mid
      else:
        right = mid
    
    return left if (array[right]-target>target-array[left]) else right
```
{% endtab %}
{% endtabs %}

在上面这种写法里，要先把数字自己放进去，left, number, right 三个数字这样的顺序出现在数组里，然后left、right依次左、右expand。  
左边expand的条件是，  
- 首先左边还没过界并且result里的值还有的剩   
- 其次满足接下来两个条件二选一 （1）右边到界了左边还没到 （2）左边的距离比右边的距离更小  
相似的，右边expand的条件是，  
- 首先右边还没过界并且result里的值还有的剩   
- 其次满足接下来两个条件二选一 （1）左边到界了右边还没到 （2）右边的距离比左边的距离更小

{% hint style="info" %}
要特别注意两个点

1）先append 再移动  
2）while循环里if和elif的判断的先后条件，因为很容易list index out of range
{% endhint %}

还可以这么写 看起来简单点

```python
class Solution(object):
  def kClosest(self, array, target, k):
    """
    input: int[] array, int target, int k
    return: int[]
    """
    # write your solution here
    if not array or len(array)==0 or k<0:
      return -1
    
    index = self.binarySearch(array, target)
    l,r=index,index+1
    result=[]
    for i in range(0,k):
      if l>=0 and (r>len(array)-1 or array[r]-target>target-array[l]):
        result.append(array[l])
        l-=1
      else:
        result.append(array[r])
        r+=1
    return result

  def binarySearch(self, array, target):
    left, right = 0, len(array)-1

    while left<right-1:
      mid = (left+right)//2
      if array[mid]==target:
        return mid
      elif array[mid]<target:
        left = mid
      else:
        right = mid
    
    return left if (array[right]-target>target-array[left]) else right
```

## Smallest Element that is Larger than Target

input = ssss eeee bbbb \(smaller, equal, bigger\) 本质是在找bigger里面的第一个

case1: if input\[m\]&lt;target\('s'\)              -&gt; l=m or l=m+1 both ok  
case2: if input\[m\]==target\('e'\)            -&gt; l=m or l=m+1 both ok  
case3: if input\[m\]&gt;target\('b'\)              -&gt; r=m 不可以-1

post-processing：先左后右

```python
class Solution(object):
  def smallestElementLargerThanTarget(self, array, target):
    """
    input: int[] array, int target
    return: int
    """
    # write your solution here
    if not array or len(array)==0:
      return -1
    
    left, right = 0, len(array)-1
    
    while left<right-1:
      mid=(left+right)//2
      if array[mid]==target:
        left=mid #+1 OK
      elif array[mid]>target:
        right=mid #cannot -1
      else:
        left=mid #+1 OK
    
    if array[left]>target:
      return left
    elif array[right]>target:
      return right
    
    return -1
```

## Sqrt（）

找一个最接近于平方根的整数, floor

#### 方法一：试

```python
def sqrt(n):
    val = 1
    while val*val<=n
        val+=1
    return val-1
```

Time O\( $$\sqrt(n)$$ \)

Space O\(1\)

#### 方法二：binary search

if mid\*mid&lt;n: go right \[mid, right\]

if mid\*mid&gt;n: go left \[left, mid\]

if mid\*mid==n: return mid

{% tabs %}
{% tab title="post-processing" %}
```python
class Solution(object):
  def sqrt(self, x):
    """
    input: int x
    return: int
    """
    # write your solution here
    if x<=1:
      return x

    left, right = 1, x//2
    while left<right-1:
      mid = (left+right)//2
      if mid*mid==x:
        return mid
      elif mid*mid>x:
        right=mid-1
      else:
        left=mid
    if right*right<=x:
      return right
    else:
      return left
```
{% endtab %}

{% tab title="classic" %}
```python
class Solution(object):
  def sqrt(self, x):
    """
    input: int x
    return: int
    """
    # write your solution here
    if x<=1:
      return x
    left = 1
    right = x//2
    
    while True:
      mid = (left+right)//2
      if mid>x//mid:
        right=mid-1
      elif mid<=x//mid and mid+1>x//(mid+1):
        return mid
      else:
        left = mid+1
```
{% endtab %}
{% endtabs %}

## Find Peak Element

```python
class Solution(object):
    def findPeak(self, array):
    """
    input: int[] array
    return: int
    """
        left, right = 0, len(array)-1
        while left<right-1:
            mid = (left+right)//2
            if array[mid]>array[mid-1] and array[mid]>array[mid+1]:
                return mid
            elif array[mid]<array[mid+1]:
                left=mid+1
            elif array[mid]>array[mid-1]:
                right=mid-1
        return left if array[left]>array[right] else right
```

## Search In Shifted Sorted Array I

```python
class Solution(object):
  def search(self, array, target):
    """
    input: int[] array, int target
    return: int
    """
    # write your solution here
    
    if not array or len(array)==0:
      return -1
    
    left, right = 0, len(array)-1

    while left<right-1:
      mid=(left+right)//2
      if array[mid]==target:
        return mid
      if array[left]<=array[mid]:
        if target<array[mid] and target>=array[left]:
          right=mid-1
        else:
          left=mid+1
      else:
        if target<=array[right] and target>array[mid]:
          left=mid+1
        else:
          right=mid-1
    
    if array[left]==target:
      return left
    elif array[right]==target:
      return right

    return -1
```

## First Bug Version

Pull Request有很多个版本，如果有一个version有bug，在version7发现了，快速找到这个有bug的version的第一个version

{% hint style="info" %}
Java那个版本的答案需要注意：因为一开始先找到n/2和n这个范围，如果bad version是第一个，那么在while除的时候有可能就直接让n=0了，这样left和right都是0，not valid.  所以要在while前面先判断n还没有到1
{% endhint %}

{% tabs %}
{% tab title="Python" %}
```python
class Solution:
  def findFirstBadVersion(n): 
    # write your solution here
    left, right = 1, n
    while left<right-1:
      mid = (left+right)//2
      if isBadVersion(mid):
        right=mid
      else:
        left=mid+1
    return left if isBadVersion(left) else right
```
{% endtab %}

{% tab title="Java" %}
```java
class Solution extends VersionControl {
  public int findFirstBadVersion(int n) {
    // write your solution here
    while ((n!=1) & (isBadVersion(n))){
      n/=2;
    }
    int left = n; 
    int right = n*2;

    while (left<right-1){
      int mid = (right+left)/2;
      if (isBadVersion(mid)){
        right=mid;
      }else{
        left=mid+1;
      }
    }
    return isBadVersion(left)? left:right;
  }
}
```
{% endtab %}
{% endtabs %}

## 新题: 虚拟数组

给一个已排好序的正整数数组，在首尾之间，不连续的部分可以看成是漏掉了一些数。这些漏掉的数可以组成一个虚拟的数组，要求给出一个序号k，返回虚拟数组的第k个数。 比如给定原数组：\[2,4,7,8,9,15\]，漏掉的数组成这样一个虚拟数组：\[3,5,6,10,11,12,13,14\]。若k=2，返回虚拟数组的第二个数“5”。

每次取数组中间位置mid的元素a\[mid\]，跟数组最右边的元素a\[right\]比较，求出k=\(a\[right\]-a\[mid\]\)-\(right-mid\) 这个k值就代表从mid到right之间有多少个hole。比较k和n的大小，如果k&lt;n就说明第n个hole在数组左半边，那么让n=n-k然后继续搜索左半边；否则的话第n个hole在数组右半边，就继续搜索右半边。直到最后left+1==right，直接返回a\[right\]-n就是最终要求的值

```python
def kth_missing_num(nums, k):
     
    missed = num_missing(nums)
    if missed < k: 
        raise ValueError("not that many missing numbers")
     
    start, end = 0, len(nums)-1
    while start + 1 != end:
        mid = (start+end)//2
        missed = num_missing(nums, start, mid)
        if missed >= k:
            end = mid
        else:
            start = mid 
            k = k - missed 
    return nums[start] + k
     
def num_missing(nums, start = 0, end = None):
    if end is None:
        end = len(nums) - 1
     
    return nums[end] - nums[start] - (end - start)
 
class TestMissingNumber(unittest.TestCase):
 
    def setUp(self):
        self.nums = [2,4,7,8,9,15]
        self.missings = [3,5,6,10,11,12,13,14]
    def tearDown(self):
        pass
    def testMissingNumber(self):
        for i, missed in enumerate(self.missings):
            self.assertEqual(kth_missing_num(self.nums, i+1), missed)
        with self.assertRaises(ValueError):
            kth_missing_num(self.nums, len(self.missings)+1)
```

## 高等难度

### K-th smallest in Two Sorted Arrays

1.1: 两个sorted array的median 两个array凑在一起的

  
1.2: 两个sorted array的第k小或者前k小的元素   
方法一：可以2-pointers的方法，谁小移谁，用O\(k\)的时间  
方法二：Binary Search 谁小删除谁 每一次搜索范围都是当前的k/2个元素 O\(logk\)

### Closest k Element 

用上面的方法来接着做  
1. Binary Search to find L and R   log\(n\)  
2. 此时有了两个array，left及left的左边都可以通过当前element和target的距离，这就是所谓的array A，同理，right及right的右边都是arrayB。就成了上面的这个题 log\(k\)就可以搞定

### Search In Unknown Sized Sorted Array

1. Find the end 倍增法
2. Binary Search

```python
class Solution(object):
  def search(self, dic, target):
    """
    input: Dictionary dic, int target
    return: int
    """
    # write your solution here
    start = 1
    while dic.get(start) and dic.get(start)<target:
      start*=2
  
    left, right = start//2, start
    while left<=right:
      mid = (left+right)//2
      if dic.get(mid) is None or dic.get(mid)>target:
        right = mid-1
      elif dic.get(mid)<target:
        left = mid+1
      else:
        return mid
    return -1
```

Time: O\(log 2\(first\_bug\_version\)\) 

## 实际工作中的binary search

任何object都可以被sorted，但是我们需要一个比较策略，比如口红可以用色号、RGB色度、16进制的数字、品牌的首字母、喜爱程度... 这些都是comparable

Generics: 范形 就像是外形看不出来色号的口红 

### 面试时：

1. Clarification:  - sorted? ascending？descending? - how you define your colors
2. Examples：过一个example
3. Solutions： Assumptions - xx的时候返回xx （比如不存在的时候返回-1还是null） - 如果遇到multiple该return哪个？（都可以） Input、Output: - input：int array， int target - output：int index Corner Case: - null: 没有array - empty: 有array但没元素 Algorithm: - Binary Search, 时间空间复杂度 call stack+ new出来的
4. Coding
5. 回头验证space & time complexity 
6. Test Case



要讲清楚：



退出循环的时候l和r在哪里（classic里是错开，提前一步时是）

