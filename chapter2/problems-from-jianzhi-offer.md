# Problems from 'JianZhi Offer'

### 1.二维数组中的查找

在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**思路**

类似于二分查找，根据题目，如果拿数组中任意一个元素与目标数值进行比较，如果该元素小于目标数值，那么目标数值一定是在该元素的下方或右方，如果大于目标数值，那么目标数值一定在该元素的上方或者左方。 对于二分查找来说，每次比较只能移动一个指针，在二维数组的查找中，两个指针是一个上下方向移动，一个是左右方向移动。两个指针可以从同一个角出发。 假设我们从左上角出发，也就是row=0 和 col=0，如果元素小于目标数值，我们会将row往下移或着col往右移，这样，被屏蔽的区域可能会是目标元素所在的区域。比如row+=1，那么第一行除左上角以外的元素就被忽略了，如果col+=1，那么第一列出左上角以外的元素就被忽略了。因此这样是不可行的。所以本题从右上角出发寻找解题思路。

**代码**

```python
class Solution:
    def Find(self, target, array):
        rows = len(array)-1
        cols = len(array[0]) - 1
        i = rows
        j = 0
        while j<=cols and i>=0:
            if target<array[i][j]:
                i -= 1
            elif target>array[i][j]:
                j += 1
            else:
                return True
        return False
```

### 2.替换空格

请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

**代码**

```python
class Solution:
    # s 源字符串
    def replaceSpace(self, s):
        s_ = ''
        for j in s:
            if j == ' ':
                s_ = s_ + '%20'
            else:
                s_ = s_ + j
        return s_
```

### 3.从尾到头打印链表

输入一个链表，按链表值从尾到头的顺序返回一个ArrayList。

**思路：**使用栈从头到尾push链表的元素，然后pop所有的元素到一个list中并返回。

**代码**

```python
class Solution:
    def printListFromTailToHead(self, listNode):
        if not listNode:
            return []
        p = listNode
        stack = []
        res = []
        while p:
            stack.append(p.val)
            p = p.next
        for i in range(len(stack)-1,-1,-1):
             res.append(stack[i])
        return res
```

### 4.重建二叉树

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

**思路：**先序遍历和中序遍历的关系，先序遍历的第一个值是根节点的值。在中序遍历中，根节点左边的值是左子树，右边的值是右子树上的值。

**代码**

```python
class Solution:
    # 返回构造的TreeNode根节点
    def reConstructBinaryTree(self, pre, tin):
        if not pre:
            return None
        root = TreeNode(pre[0])
        n = tin.index(root.val)    #找到根节点在中序遍历中的下标
        root.left = self.reConstructBinaryTree(pre[1:n+1],tin[:n])
        root.right = self.reConstructBinaryTree(pre[n+1:],tin[n+1:])
        return root
```

### 5.用两个栈实现一个队列

用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

**思路**

定义两个stack，分别是stack1和stack2，队列的push和pop是在两侧的，push操作很简单，只需要在stack1上操作，而pop操作时，先将stack1的所有元素push到stack2中，然后stack2的pop返回的元素即为目标元素，然后把stack2中的所有元素再push到stack1中。

**代码**

```python
class Solution:
    def __init__(self):
        self.stack1 = []
        self.stack2 = []
    def push(self, node):
        self.stack1.append(node)
    def pop(self):
        if not self.stack1:
            return None
        while self.stack1:
            self.stack2.append(self.stack1.pop())
        res = self.stack2.pop()
        while self.stack2:
            self.stack1.append(self.stack2.pop())
        return res
```

### 6.旋转数组中的最小数字

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

**思路：**这个题很简单，题目说的不明白，意思是一个递增排序的数组做了一次旋转，给你旋转后的数组，找到最小元素。输入{3,4,5,1,2}输出1。

两个方法：1.遍历数组元素，如果前一个元素大于后一个元素，则找到了最小的元素。如果前一个一直小于后一个元素，说明没有旋转，返回第一个元素。

2.二分查找，如果中间元素位于递增元素，那么中间元素&gt;最右边元素，最小元素在后半部分。否则，最小元素在前半部分。

**代码**

1.时间复杂度O\(n\)

```python
class Solution:
    def minNumberInRotateArray(self, rotateArray):
        if not rotateArray:
            return 0
        for i in range(len(rotateArray)-1):
            if rotateArray[i] > rotateArray[i+1]:
                return rotateArray[i+1]
        return rotateArray[0]
```

2.二分查找时间复杂度O\(logn\)

```python
class Solution:
    def minNumberInRotateArray(self, rotateArray): 
        if not rotateArray:
            return 0
        l = 0
        r = len(rotateArray) - 1
        while l < r:
            mid = (l + r)/2
            if rotateArray[mid] > rotateArray[r]:
                l = mid+1
            else:
                r = mid
        return rotateArray[l]
```

### 7.斐波那契数列

要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0），n&lt;=39。

**思路：**菲波那切数列：F\(1\)=1，F\(2\)=1, F\(n\)=F\(n-1\)+F\(n-2\)（n&gt;=3，n∈N\*）

只需定义两个整型变量，b表示后面的一个数字，a表示前面的数字即可。每次进行的变换是： a,b = b,a+b

**代码**

```python
class Solution:
    def Fibonacci(self, n):
        if n <= 0:
            return 0
        a = b = 1
        for i in range(2,n):
            a,b = b,a+b
        return b
```

### 8.跳台阶

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

**思路：**典型的动态规划问题，对于第n阶台阶来说，有两种办法，一种是爬一个台阶，到第n-1阶；第二种是爬两个台阶，到第n-2阶。

得出动态规划递推式： ![\[&#x516C;&#x5F0F;\]](https://www.zhihu.com/equation?tex=F%28n%29%3DF%28n-1%29%2BF%28n-2%29)

**代码**

```python
class Solution:
    def jumpFloor(self, number):
        if number < 0:  return 0
        if number == 1:  return 1
        if number == 2:  return 2
        result = [1, 2]
        for i in range(2,number):
            result.append(result[i-1] + result[i-2])
        return result[-1]
```

### 9.变态跳台阶

一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

**思路：**n=0时,f\(n\)=0；n=1时,f\(n\)=1；n=2时,f\(n\)=2；假设到了n级台阶，我们可以n-1级一步跳上来，也可以不经过n-1级跳上来，所以f\(n\)=2\*f\(n-1\)。

推公式也能得出：

n = n时：f\(n\) = f\(n-1\)+f\(n-2\)+...+f\(n-\(n-1\)\) + f\(n-n\) = f\(0\) + f\(1\) + f\(2\) + ... + f\(n-1\)

由于f\(n-1\) = f\(0\)+f\(1\)+f\(2\)+ ... + f\(\(n-1\)-1\) = f\(0\) + f\(1\) + f\(2\) + f\(3\) + ... + f\(n-2\)

所以f\(n\) = f\(n-1\)+f\(n-1\)=2\*f\(n-1\)

**代码**

```python
class Solution:
    def jumpFloorII(self, number):
        if number <= 0:  return 0
        if number == 1: return 1
        if number == 2: return 2
        result = [1,2]
        for i in range(2,number):
            result.append(2*result[-1])
        return result[-1]
```

### 10.矩阵覆盖

我们可以用2\*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2\*1的小矩形无重叠地覆盖一个2\*n的大矩形，总共有多少种方法？

**思路：**n = 1: f\(n\) = 1; n=2 : f\(n\) = 2;

假设到了n，那么上一步就有两种情况，在n-1的时候，竖放一个矩形，或着是在n-2时，横放两个矩形（这里不能竖放两个矩形，因为放一个就变成了n-1，那样情况就重复了），所以总数是f\(n\)=f\(n-1\)+f\(n-2\)。时间复杂度O\(n\)。和跳台阶题一样。

**代码**

```python
class Solution:
    def rectCover(self, number):
        if number <= 0: return 0
        if number == 1: return 1
        if number == 2: return 2
        result = [1,2]
        for i in range(2,number):
            result.append(result[-1]+result[-2])
        return result[-1]
```

### 11.二进制中1的个数

输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。例如，9表示1001，因此输入9，输出2。

**思路：**如果整数不等于0，那么该整数的二进制表示中至少有1位是1。

先假设这个数的最右边一位是1，那么该数减去1后，最右边一位变成了0，其他位不变。

再假设最后一位不是1而是0，而最右边的1在第m位，那么该数减去1，第m位变成0，m右边的位变成1，m之前的位不变。

上面两种情况总结，一个整数减去1，都是把最右边的1变成0，如果它后面还有0，那么0变成1。那么我们把一个整数减去1,与该整数做位运算，相当于把最右边的1变成了0，比如1100与1011做位与运算，得到1000。那么一个整数中有多少个1就可以做多少次这样的运算。

**代码**

```python
class Solution:
    def NumberOf1(self, n):
        count = 0
        while n:
            n = (n - 1) & n
            count = count + 1
        return count
```

### 12.数值的整数次方

给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

**代码：**考虑base=0/exponent=0/exponent&lt;0的情况 。

```python
class Solution:
    def Power(self, base, exponent):
        if exponent==0: return 1
        if base==0: return 0
        temp = 1
        if exponent<0:
            abs_exponent = -exponent
            while abs_exponent>0:
                temp = temp*base
                abs_exponent = abs_exponent-1
            return 1/temp
        while exponent>0:
            temp = temp*base
            exponent = exponent-1
        return temp
```

### 13.调整数组顺序使奇数位于偶数前面

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

**代码：**

```python
#借助两个辅助的空间
class Solution:
    def reOrderArray(self, array):
        res1 = []
        res2 = []
        for i in array:
            if i%2==1:
                res1.append(i)
            else:
                res2.append(i)
        array = res1 + res2
        return array
```

不借助额外的空间，循环一次。p1和p2两个指针，p1指向第一个元素，p2指向第二个元素，p1找到第一个偶数，p2找到第一个奇数，\[p1,p2-1\]整体往后移一位，将奇数插到p1位置，然后p1+1。

```python
class Solution:
    def reOrderArray(self, array):
        p1 = 0
        while p1 < len(array):
            while p1 < len(array) and array[p1]%2 == 1:
                p1 += 1
            p2 = p1+1
            while p2 < len(array) and array[p2]%2 == 0:
                p2 += 1
            if p2 < len(array):
                array.insert(p1,array.pop(p2))
                p1 += 1
            else:
                break
        return array
```

### 14.链表中的倒数第K个节点

输入一个链表，输出该链表中倒数第k个结点。

**思路：**假设链表中的节点数大于等于k个，那么一定会存在倒数第k个节点，首先使用一个快指针先往前走k步，然后两个指针每次走一步，两个指针之间始终有k的距离，当快指针走到末尾时，慢指针所在的位置就是倒数第k个节点。

**代码**

```python
class Solution:
    def FindKthToTail(self, head, k):
        if not head or k <=0:
            return None
        p = q = head
        t = 0
        while p and t < k:
            p = p.next
            t = t+1
        if t < k:
            return None
        while p != None:
            p = p.next
            q = q.next
        return q
```

### 15.反转链表

```python
class Solution(object):
    def reverseList(self, head):
        if not head:
            return None
        p = head
        q = head.next
        while q:
            head.next = q.next
            q.next = p
            p = q
            q = head.next
        return p
```

### 16.合并两个排序的链表

输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

```python
class Solution(object):
    def mergeTwoLists(self, l1, l2):
        cur = head =ListNode(0)
        while l1 and l2:
            if l1.val <= l2.val:
                cur.next = l1
                l1 = l1.next
            else:
                cur.next = l2
                l2 = l2.next
            cur = cur.next
        cur.next = l1 or l2
        return head.next
```

### 17.树的子结构

输入两棵二叉树A，B，判断B是不是A的子结构。（空树不是任意一个树的子结构）

**思路：**采用递归的思路，单独定义一个函数判断B是不是从当前A的根节点开始的子树，这里判断是不是子树也需要一个递归的判断。如果是，则返回True，如果不是，再判断B是不是从当前A的根节点的左子节点或右子节点开始的子树。

```python
class Solution:
    def HasSubtree(self, pRoot1, pRoot2):
        if not pRoot1 or not pRoot2:
            return False
        result = False
        if pRoot1.val == pRoot2.val:
            result = self.isSubTree(pRoot1,pRoot2)
        if not result:
            result = self.HasSubtree(pRoot1.left,pRoot2)
        if not result:
            result = self.HasSubtree(pRoot1.right,pRoot2)
        return result
    def isSubTree(self,pRoot1,pRoot2):
        if not pRoot2:
            return True
        if not pRoot1:
            return False
        if pRoot1.val != pRoot2.val:
            return False
        return self.isSubTree(pRoot1.left,pRoot2.left) and self.isSubTree(pRoot1.right,pRoot2.right)
```

### 18.二叉树的镜像

操作给定的二叉树，将其变换为源二叉树的镜像。

```text
二叉树的镜像定义：源二叉树 
    	    8
    	   /  \
    	  6   10
    	 / \  / \
    	5  7 9 11
    	镜像二叉树
    	    8
    	   /  \
    	  10   6
    	 / \  / \
    	11 9 7  5
```

**代码：**交换左右结点

```python
class Solution(object):
    def invertTree(self, root):
        if not root:
            return None
        root.left,root.right = self.invertTree(root.right),self.invertTree(root.left)
        return root
```

### 19.顺时针打印矩阵

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

**思路：**输出第一行后逆时针翻转矩阵。

**代码**

```python
class Solution:
    # matrix类型为二维列表，需要返回列表
    def printMatrix(self, matrix):
        result = []
        while(matrix):
            result += matrix.pop(0)
            if not matrix:
                break
            matrix = self.turn(matrix)
        return result
    def turn(self,matrix):
        num_r = len(matrix)
        num_c = len(matrix[0])
        newmat = []
        for i in range(num_c):
            newmat2 = []
            for j in range(num_r):
                newmat2.append(matrix[j][i])
            newmat.append(newmat2)
        newmat.reverse()
        return newmat
```

### 20.包含min函数的栈

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。

```python
class Solution:
    def __init__(self):
        self.stack = []
        self.minStack = []
    def push(self, node):
        self.stack.append(node)
        if not self.minStack or node < self.minStack[-1]:
            self.minStack.append(node)            #保存最小元素到栈顶
        else:
            self.minStack.append(self.minStack[-1])
    def pop(self):
        if self.stack:
            self.stack.pop()
            self.minStack.pop()
    def top(self):
        if self.stack:
            return self.stack[-1]
        else:
            return None
    def min(self):
        if self.minStack:
            return self.minStack[-1]
        else:
            return None
```

### 21.栈的压入、弹出

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

**思路：**栈的压入顺序是指1,2,3,4,5是依次push到栈的，但并不是说只有push的过程，也可能有pop的操作，比如push 1，2，3，4之后，把4pop出去，然后再push5，再pop5，然后依次pop3,2,1。弹出序列是指每次pop出去的元素都是当时栈顶的元素。

那么就可以构造一个辅助栈来判断弹出序列是不是和压栈序列对应。首先遍历压栈序列的元素push到辅助栈，判断是不是弹出序列的首元素，如果是，则弹出序列pop首元素（指针后移），如果不是，则继续push，再接着判断；直到遍历完了压栈序列，如果辅助栈或者弹出序列为空，则返回True，否则返回False

**代码**

```python
class Solution:
    def IsPopOrder(self, pushV, popV):
        stack = []
        for i in pushV:
            stack.append(i)
            while stack and stack[-1] == popV[0]:
                stack.pop()
                popV.pop(0)
        if not stack:
            return True
        else:
            return False
```

### 22.从上到下打印二叉树

从上往下打印出二叉树的每个节点，同层节点从左至右打印。

```python
#二叉树的层次遍历
class Solution:
    def PrintFromTopToBottom(self, root):
        if not root:
            return []
        stack = [root]
        res = []
        while stack:
            temp = stack
            stack = []
            for node in temp:
                res.append(node.val)
                if node.left:
                    stack.append(node.left)
                if node.right:
                    stack.append(node.right)
        return res
```

### 23.二叉搜索树的后序遍历序列

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

**思路：**递归判断。如果序列的长度小于2，那一定是后序遍历的结果。根据BST和后序遍历的性质，遍历结果的最后一个一定是根节点，那么序列中前面一部分小于根节点的数是左子树，后一部分是右子树，递归进行判断。

**代码**

```python
class Solution:
    def VerifySquenceOfBST(self, sequence):
        if len(sequence) == 0:
            return False
        return self.subVerifySquenceOfBST(sequence)
    def subVerifySquenceOfBST(self, sequence):
        if len(sequence) <= 2:
            return True
        flag = sequence[-1]
        index = 0
        while sequence[index] < flag:
               index += 1
        j = index
        while j < len(sequence)-1:
            if sequence[j] > flag:
                j += 1
            else:
                return False
        return self.subVerifySquenceOfBST(sequence[:index]) and self.subVerifySquenceOfBST(sequence[index:-1])
```

### 24.二叉树中和为某一值的路径

输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。\(注意: 在返回值的list中，数组长度大的数组靠前\)

**思路：**定义一个子函数，输入的是当前的根节点、当前的路径以及还需要满足的数值，同时在子函数中运用回溯的方法进行判断。

**代码**

```python
class Solution:
    # 返回二维列表，内部每个列表表示找到的路径
    def FindPath(self, root, expectNumber):
        self.res = []
        if not root:
            return []
        self.subPath(root,[],expectNumber)
        return self.res
    def subPath(self, root, path,number):
        if not root.left and not root.right:
            if number == root.val:
                self.res.append(path+[root.val])
        if root.left:
            self.subPath(root.left,path+[root.val],number-root.val)
        if root.right:
            self.subPath(root.right,path+[root.val],number-root.val)  
```

### 25.复杂链表的复制

输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

```python
#     def __init__(self, x):
#         self.label = x
#         self.next = None
#         self.random = None
class Solution:
    # 返回 RandomListNode
    def Clone(self, pHead):
        if pHead == None:
            return None
        # 新建一个链表的头结点
        newhead = RandomListNode(pHead.label)    
        # 令头结点的random等于原来头节点的random
        newhead.random = pHead.random  
        # 令头结点的next等于原来头结点的next (这一步可以省略，直接让newhead.next等于递归后的pHead.next)         
        newhead.next = pHead.next
        # 递归处理newhead.next
        newhead.next = self.Clone(pHead.next)
        return newhead
```

### 26.二叉搜索树与双向链表

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

思路：题目很难理解，在网上找了个图。中序遍历，从中序遍历得到的第一个节点开始构造链表，定义两个辅助节点listHead\(链表头节点\)、listTail\(链表尾节点\)。listHead用于记录链表的头节点，用于最后算法的返回；listTail用于定位当前需要更改指向的节点。![](https://pic4.zhimg.com/80/v2-624d0f675872f2798eddb365a3b99c1b_hd.jpg)![](https://pic1.zhimg.com/80/v2-390f27ecd0f32dc4d515d1adc8fa8d08_hd.jpg)

```python
class Solution:
    def __init__(self):
        self.listHead = None
        self.listTail = None
    def Convert(self, pRootOfTree):
        if pRootOfTree == None:
            return None
        self.Convert(pRootOfTree.left)
        if self.listHead == None:
            self.listHead = pRootOfTree
            self.listTail = pRootOfTree
        else:
            self.listTail.right = pRootOfTree
            pRootOfTree.left = self.listTail
            self.listTail = pRootOfTree
        self.Convert(pRootOfTree.right)
        return self.listHead
```

### 27.字符串的排列

输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

**思路：**递归。把字符串分为两个部分： 字符串的第一个字符，第一个字符后面的所有字符。1.求所有可能出现在第一个位置的字符，用索引遍历。2.求第一个字符以后的所有字符的全排列。将后面的字符又分成第一个字符以及剩余字符。

```python
class Solution:
    def Permutation(self, ss):
        if len(ss)==0 or len(ss)==1:
            return ss
        res = []
        self.helper(ss, res, '')
        return sorted(list(set(res)))
    def helper(self, ss, res, path):
        if not ss:
            res.append(path)
        else:
            for i in range(len(ss)):
                self.helper(ss[:i]+ss[i+1:],res,path+ss[i])
```

### 28.数组中出现次数超过一半的数字

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

解法1：对数组进行排序，如果该数存在，那么就是排序数组中间的数，判断这个数的个数是否大于一半，如果是，返回这个数，否则返回0。

```python
class Solution:
    def MoreThanHalfNum_Solution(self, numbers):
        count = 0
        numbers.sort()
        res = numbers[len(numbers)/2]
        for i in numbers:
            if i == res:
                count +=1
        if count >len(numbers)/2:
            return res
        else:
            return 0
```

解法2：假设第一个数字是众数，遍历数组，若元素 == 当前众数res则count += 1，否则count -= 1;

在下次count == 0时，意味着当前众数res的数量为已遍历元素一半；这种情况下，剩余数组众数仍等于原数组众数（因为最坏的情况是已遍历数组中一半是数组众数，一半是非众数）。

因此，在每次count == 0时，记录当前数字为当前众数，当遍历完整个数组时，留下的res一定为整个数组的众数（最坏情况是在最后一个元素才找到众数，前面的count全部抵消）。

最后验证这个数是否出现了一半以上。

```python
class Solution:
    def MoreThanHalfNum_Solution(self, numbers):
        res=numbers[0]
        count = 0
        for i in numbers:
            if res == i:
                count +=1 
            else:
                count -=1 
            if count < 0:
                res = i 
                count = 1
        count2 = 0
        for i in numbers:
            if res == i:
                count += 1
        if count > len(numbers)/2:
            return res
        else: return 0
```

### 29.最小的K个数

输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

```python
#堆排序
class Solution:
    def GetLeastNumbers_Solution(self, tinput, k):
        # write code here
        if len(tinput) < k or k==0:
            return []
        self.buildHeap(tinput[:k],k)
        for i in range(k,len(tinput)):
            if tinput[i] > self.heap[0]:
                continue
            else:
                self.heap[0] = tinput[i]
                self.perceDown(0,k)
        return sorted(self.heap)
        
    def buildHeap(self,tinput,k):
        self.heap = tinput
        for i in range(k//2,-1,-1):
            self.perceDown(i,k)
            
    def perceDown(self,i,k):
        temp = self.heap[i]
        while (2 * i + 1) < k:
            child = 2 * i + 1
            if (child < k - 1) and self.heap[child] < self.heap[child+1]:
                child = child + 1
            if temp < self.heap[child]:
                self.heap[i] = self.heap[child]
                i = child
            else:
                break
        self.heap[i] = temp  
```

### 30.连续子数组的最大和

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例:

输入: \[-2,1,-3,4,-1,2,1,-5,4\],

输出: 6

解释: 连续子数组 \[4,-1,2,1\] 的和最大，为 6。

**思路：**如果数组里所有的整数都是负数，那么选择最大的数即可，因为越累加越小。

正负数都有的情况，需要两个变量，一个是global\_max,从全局来看，每次最大的是什么组合，另一个是local\_max，和global\_max相比，更新global\_max。

**代码**

```python
class Solution(object):
    def maxSubArray(self, nums):

        if max(nums) < 0:
            return max(nums)
     
        local_max, global_max = 0, 0
        for i in nums:
            local_max = max(0, local_max + i)
            global_max = max(global_max, local_max)
        return global_max
```

### 31.从1到n的整数中1出现的个数

比如，1-13中，1出现6次，分别是1，10，11，12，13。

```python
class Solution:
    def NumberOf1Between1AndN_Solution(self, n):
        count = 0
        for i in range(1,n+1):
            j = i
            while j > 0:
                if j%10 == 1:
                    count += 1
                j = j/10
        return count
```

### 32.把数组排成最小的数

输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

思路： 根据题目的要求，两个数字m和n能拼接称数字mn和nm。如果mn&lt;nm，也就是m应该拍在n的前面，我们定义此时m小于n；反之，如果nm&lt;mn，我们定义n小于m。如果mn=nm,m等于n。

```python
class Solution:
    def PrintMinNumber(self, numbers):
        if not len(numbers):
            return ""
        arr = [str(x) for x in numbers]
        arr.sort(lambda x,y:cmp(x+y,y+x))
        return int("".join(arr))
```

### 33.丑数

把只包含质因子2、3和5的数称作丑数。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

**思路：**动态规划的解法，首先确保数组里的已有的丑数是排好序的，同时要维护三个索引。

**代码：**

```python
class Solution:
    def GetUglyNumber_Solution(self, index):
        if index<=0:
            return 0
        res = [1]
        a, b, c = 0
        while(len(res) < index):
            nextMin = min(res[a] * 2,res[b] * 3,res[c] * 5)
            res.append(nextMin)
            while res[a] * 2 <= nextMin:
                a += 1
            while res[b] * 3 <= nextMin:
                b += 1
            while res[c] * 5 <= nextMin:
                c += 1
        return res[-1]
```

### 34.第一个只出现一次的字符

在一个字符串\(0&lt;=字符串长度&lt;=10000，全部由字母组成\)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）。

**思路：**创建哈希表，下标为ACII值，值为出现次数。

**代码**

```python
class Solution:
    def FirstNotRepeatingChar(self, s):
        #建立哈希表,有256个字符，于是创建一个长度为256的列表
        ls=[0]*256
        #遍历字符串,下标为ASCII值,值为次数
        for i in s:
            ls[ord(i)]+=1   #ord()函数以一个字符作为参数，返回对应的ASCII数值
        for j in s:
            if ls[ord(j)]==1:
                return s.index(j)
                break
        return -1
```

### 35.数组中的逆序对

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

```python
#使用归并排序的思路求解
class Solution:
    def InversePairs(self, data):
        if len(data) > 1:
            mid = len(data) / 2
            left_half = data[:mid]
            right_half = data[mid:]
            left_count = self.InversePairs(left_half)%1000000007
            right_count = self.InversePairs(right_half)%1000000007
            i,j,k,count = len(left_half)-1,len(right_half)-1,len(data)-1,0
            while i >= 0 and j >= 0:
                if left_half[i] < right_half[j]:
                    data[k] = right_half[j]
                    j = j - 1
                    k = k - 1
                else:
                    data[k] = left_half[i]
                    count += (j+1)
                    i = i - 1
                    k = k - 1
            while i >= 0:
                data[k] = left_half[i]
                k = k - 1
                i = i - 1
            while j>=0:
                data[k] = right_half[j]
                k = k - 1
                j = j - 1
            return (count + left_count + right_count)%1000000007
        else:
            return 0
```

### 36.两个链表的第一个公共结点

\(leetcode160\) 编写一个程序，找到两个单链表相交的起始节点。

如下面的两个链表**：**![](https://pic3.zhimg.com/80/v2-8af605d15e7445b13a7cc4e4cd73f14a_hd.jpg)

在节点 c1 开始相交。

**注意：**

* 如果两个链表没有交点，返回 `null`.
* 在返回结果后，两个链表仍须保持原有的结构。
* 可假定整个链表结构中没有循环。
* 程序尽量满足 O\(n\) 时间复杂度，且仅用 O\(1\) 内存。

**分析**

设置两个指针，一个从headA开始遍历，遍历完headA再遍历headB，另一个从headB开始遍历，遍历完headB再遍历headA，如果有交点，两个指针会同时遍历到交点处。

**代码**

```python
class Solution(object):
    def getIntersectionNode(self, headA, headB):
        p1 = headA
        p2 = headB
        while p1 != p2:
            if p1 == None:
                p1 = headB
            else:
                p1 = p1.next
            if p2 == None:
                p2 = headA
            else:
                p2 = p2.next
        return p2
```

### 37.统计一个数字在排序数组中的出现的次数

思路：考虑数组为空的情况，直接返回0；用二分查找法，找到i和j的位置。

```python
class Solution:
    def GetNumberOfK(self, data, k):
        if len(data) == 0:
            return 0
        i = 0
        j = len(data) - 1
        while i < j and data[i] != data[j]:
            if data[i] < k:
                i += 1
            if data[j] > k:
                j -= 1
        if data[i] != k: 
            return 0
        return j-i+1
```

### 38.二叉树的深度

\(同leetcode104\)输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

**示例：**  
给定二叉树 `[3,9,20,null,null,15,7]`，

```text
    3
   / \
  9  20
    /  \
   15   7
```

返回它的最大深度 3 。

**思路**

递归的方法，比较左边路径和右边路径哪边最长，选择最长的一边路径，加上root结点本身的长度。

**代码**

```python
class Solution(object):
    def maxDepth(self, root):
        if root is None:
            return 0
        else:
            return max(self.maxDepth(root.left),self.maxDepth(root.right))+1
```

### 39.平衡二叉树

（同leetcode110）输入一个二叉树，判断是否是平衡二叉树。

> 平衡二叉树：一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过1。

**示例 :**

给定二叉树 `[3,9,20,null,null,15,7]`

```text
    3
   / \
  9  20
    /  \
   15   7
```

返回 `true` 。

**思路**

利用104题中判断二叉树最大深度的函数，左子树和右子树的深度差小于等于1即为平衡二叉树。

**代码**

```python
class Solution(object):
    def isBalanced(self, root):
        if root == None:
            return True
        elif abs(self.height(root.left)-self.height(root.right))>1:
            return False
        else:
            return self.isBalanced(root.left) and self.isBalanced(root.right)
    
    def height(self,root):
        if root == None:
            return 0
        else:
            return max(self.height(root.left),self.height(root.right))+ 1
```

### 40.数组中只出现一次的数字

一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

**思路：**如果数组中只有一个数字出现了一次，对数组所有数求一次异或，两个相同的数的异或是0。  
那么如果数组中有两个数出现了一次，其他出现了两次，将这数组分成两个子数组，这两个数字分别出现在这两个子数组中，那么就转换成了前面所说的求异或的问题。那么怎么分呢，这里的思路是根据要求的这两个数的异或之后最右边不为1的这一位进行划分的。

**代码**

```python
class Solution:
    # 返回[a,b] 其中ab是出现一次的两个数字
    def FindNumsAppearOnce(self, array):
        res = 0
        for i in array:
            res ^= i
        splitBit = 1
        while splitBit & res == 0:
            splitBit = splitBit << 1
        res1 = 0
        res2 = 0
        for i in array:
            if i & splitBit == 0:
                res1 ^= i
            else:
                res2 ^= i
        return [res1,res2]
```

### 41.和为S的连续正数序列

输出所有和为S的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序。

思路：维护两个指针，一个指针指向这个连续正数序列的开始，一个指向连续正数序列的结束，判断当前的序列和与目标的关系，不断更新这两个指针的位置。

代码：

```python
class Solution:
    def FindContinuousSequence(self, tsum):
        res = []
        i = 1
        j = 2
        curSum = i + j
        while i <= tsum/2:
            if curSum == tsum:
                res.append(range(i,j+1))
                j = j + 1
                curSum += j
            elif curSum > tsum:
                curSum -= i
                i += 1
            else:
                j += 1
                curSum += j
        return res
```

### 42.和为S的两个数字

输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

思路：由于是排好序的数组，因此对于和相等的两个数来说，相互之间的差别越大，那么乘积越小，因此我们使用两个指针，一个从前往后遍历，另一个从后往前遍历数组即可。

代码：

```python
class Solution:
    def FindNumbersWithSum(self, array, tsum):
        if len(array)<2:
            return []
        i = 0
        j = len(array)-1
        while i < j:
            if array[i]+array[j] > tsum:
                j -= 1
            elif array[i]+array[j] < tsum:
                i += 1
            else:
                return [array[i],array[j]]
        return []
```

### 43.左旋转字符串

对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。

思路：分割法。

```python
class Solution:
    def LeftRotateString(self, s, n):
        m = len(s)
        res1 = s[n:m]
        res2 = s[0:n]
        res = res1+res2
        return res
```

### 44.翻转单词顺序列

例如，“student. a am I”翻转为“I am a student.”。

思路：按空格切分为数组，依次入栈，再出栈\(用空格连接\)

```python
class Solution:
    def ReverseSentence(self, s):
        if s is None or len(s) == 0:
            return s
        stack = []
        for i in s.split(' '): #split()通过指定分隔符对字符串进行切片
            stack.append(i)
        res = ""
        while len(stack) > 0:
            res += stack.pop() + " "
        res = res[:-1]
        return res
```

### 45.扑克牌顺子

一副扑克牌,里面有2个大王，2个小王，从中随机抽出5张牌，如果牌能组成顺子就输出true，否则就输出false。为了方便起见，大小王是0，大小王可以当作任何数字。

**思路：**

1、将数组排序 ；2、统计数组中0的个数，即判断大小王的个数；3、统计数组中相邻数字之间的空缺总数，如果空缺数小于等于大小王的个数，可以组成顺子，否则不行。如果数组中出现了对子，那么一定是不可以组成顺子的。

**代码：**

```python
class Solution:
    def IsContinuous(self, numbers):
        if not numbers:
            return False
        numbers.sort()
        zeros = 0
        while numbers[zeros]==0:
            zeros = zeros + 1
        for i in range(zeros,len(numbers)-1):
            if numbers[i+1] == numbers[i] or (numbers[i+1] - numbers[i] - 1) > zeros:
                return False
            else:
                zeros -= (numbers[i+1]-numbers[i]-1)
        return True
```

### 46.孩子们的游戏（圆圈中最后剩下的数）

游戏是这样的：首先，让小朋友们围成一个大圈。然后，他随机指定一个数m，让编号为0的小朋友开始报数。每次喊到m-1的那个小朋友要出列，不再回到圈中，从他的下一个小朋友开始，继续0...m-1报数....这样下去....直到剩下最后一个小朋友获胜，获胜的小朋友编号多少？\(注：小朋友的编号是从0到n-1\)

```python
class Solution:
    def LastRemaining_Solution(self, n, m):
        if not n and not m :
            return -1
        res = range(n)
        i = 0
        while len(res)>1:
            i = (m+i-1)%len(res)
            res.pop(i)
        return res[0]
```

### 47.求1+2+3+...+n

求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

**思路：**将加法问题转化为递归进行求解即可。

**代码：**

```python
class Solution:
    def __init__(self):
        self.sum = 0
    def Sum_Solution(self, n):
        if n<=0:
            return 0
        self.getSum(n)
        return self.sum
    def getSum(self,n):
        self.sum+=n
        n = n - 1
        return n>0 and self.getSum(n)
```

### 48.不用加减乘除做加法

写一个函数，求两个整数之和，要求在函数体内不得使用+、-、\*、/四则运算符号。

**思路：**

对数字做运算，除了加减乘除外，还有**位运算**，位运算是针对二进制的，二进制的运算有“三步走”策略：

例如5的二进制是101，17的二进制10001。  
第一步：各位相加但不计进位，得到的结果是10100。  
第二步：计算进位值，只在最后一位相加时产生一个进位，结果是二进制10。   
第三步：把前两步的结果相加，得到的结果是10110。转换成十进制正好是22。

接着把二进制的加法用位运算替代：  
（1）不考虑进位对每一位相加，0加0、1加1的结果都是0，1加0、0加1的结果都是1。这和异或运算相同。（2）考虑进位，只有1加1的时候产生进位。 位与运算只有两个数都是1的时候结果为1。考虑成两个数都做位与运算，然后向左移一位。（3）相加的过程依然重复前面两步，直到不产生进位为止。

```python
#当一个正数和一个负数相加时，陷入死循环
class Solution:
    def Add(self, num1, num2):
        while num2!=0:
            sum = num1^num2
            carry = (num1&num2)<<1
            num1 = sum
            num2 = carry
        return num1
```

当一个正数和一个负数相加时，陷入死循环。实际上，在进行负数的按位加法时，有可能发生在最高位还要向前进一位的情形，正常来说，这种进位因为超出了一个int可以表示的最大位数，应该舍去才能得到正确的结果。因此，对于Java，c，c++这样写是正确的。而对于Python，却有点不同。

在早期版本中如Python2.7中，整数的有int和long两个类型。int类型是一个固定位数的数；long则是一个理论上可以存储无限大数的数据类型。当数大到可能溢出时，为了避免溢出，python会把int转化为long。而Python3.x之后整数只有一个可以放任意大数的int了。可是无论哪种，都是采用了特殊的方法实现了不会溢出的大整数。 所以会使程序无限的算下去，这也是Python效率低的一个原因。（python2和python3都有这个问题。）  
已经知道了右移过程中大整数的自动转化，导致变不成0，那么只需要在移动的过程中加一下判断就行了，把craay的值和0xFFFFFFFF做一下比较就可以了，具体代码如下所示。

```python
class Solution:
    def Add(self, num1, num2):
        while num2:
            sum = num1 ^ num2
            carry = 0xFFFFFFFF&(num1 & num2)<<1
            carry = -(~(carry - 1) & 0xFFFFFFFF) if carry > 0x7FFFFFFF else carry
            num1 = sum
            num2 = carry
        return num1
```

### 49.把字符串转换成整数

将一个字符串转换成一个整数\(实现Integer.valueOf\(string\)的功能，但是string不符合数字要求时返回0\)，要求不能使用字符串转换整数的库函数。 数值为0或者字符串不是一个合法的数值则返回0。

输入描述:

```text
输入一个字符串,包括数字字母符号,可以为空
```

输出描述:

```text
如果是合法的数值表达则返回该数字，否则返回0
```

示例1：

```text
输入：+2147483647 ，输出：2147483647；输入：1a33，输出：0。
```

**代码：**

```python
class Solution:
    def StrToInt(self, str):
        str = str.strip()
        if not str:
            return 0
        number, flag = 0, 1
        #符号位的判断是否有正负号
        if str[0] == '-':
            str = str[1:]
            flag = -1
        elif str[0] == '+':
            str = str[1:]
        #遍历除+，-以外的所有字符，如果遇到非数字，则直接返回0
        for c in str:
            if c >= '0' and c <= '9':
                number = 10*number + int(c)
            else:
                return 0
        number = flag * number
        return number
```

### 50.数组中重复的数字

在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

**思路：**一个简单的方法是先排序再查找，时间复杂度是O\(nlogn\)。还可以用哈希表来解决，遍历每个数字，每扫描到一个数字可以用O\(1\)的时间来判断哈希表中是否包含了这个数字，如果没有包含，则加到哈希表，如果包含了，就找到了一个重复的数字。时间复杂度O\(n\)。

我们注意到数组中的数字都在0~n-1范围内，如果这个数组中没有重复的数字，那么当数组排序后数字i在下标i的位置，由于数组中有重复的数字，有些位置可能存在多个数字，同时有些位置可能没有数字。遍历数组，当扫描到下标为i 的数字m时，首先看这个数字是否等于i，如果是，继续扫描，如果不是，拿它和第m个数字进行比较。如果它和第m个数字相等，就找到了一个重复的数字，如果不相等，就交换两个数字。继续比较。

```python
class Solution:
    # 这里要特别注意~找到任意重复的一个值并赋值到duplication[0]
    # 函数返回True/False
    def duplicate(self, numbers, duplication):
        for i in range(len(numbers)):
            while numbers[i] != i:
                m = numbers[i]
                if numbers[m] == numbers[i]:
                    duplication[0] = m
                    return True
                else:
                    numbers[i] = numbers[m]
                    numbers[m] = m
        return False
```

### 51.构建乘积数组

给定一个数组A\[0,1,...,n-1\],请构建一个数组B\[0,1,...,n-1\],其中B中的元素B\[i\]=A\[0\]\*A\[1\]\*...\*A\[i-1\]\*A\[i+1\]\*...\*A\[n-1\]。不能使用除法。

**思路：**如果没有不能使用除法的限制，可以直接用累乘的结果除以A\[i\]。由于题目有限制，一种直观的解法是连乘n-1个数字，但时间复杂度是O\(n^2\)。可以把B\[i\]=A\[0\]\*A\[1\]\*...\*A\[i-1\]\*A\[i+1\]\*...\*A\[n-1\]分成A\[0\]\*A\[1\]\*...\*A\[i-1\]和A\[i+1\]\*...\*A\[n-1\]两部分的乘积。

```python
class Solution:
    def multiply(self, A):
        C,D = [],[]
        for i in range(len(A)):
            if i == 0:
                C.append(1)
            else:
                C.append(C[i-1]*A[i-1])
        for i in range(len(A)):
            if i == 0:
                D.append(1)
            else:
                D.append(D[i-1]*A[len(A)-i])
        D = D[::-1]
        B = []
        for i in range(len(A)):
            B.append(C[i]*D[i])
        return B
```

### 52.正则表达式匹配

请实现一个函数用来匹配包括'.'和'\*'的正则表达式。模式中的字符'.'表示任意一个字符，而'\*'表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab\*ac\*a"匹配，但是与"aa.a"和"ab\*a"均不匹配。

**思路：**如果 s和pattern都为空，匹配成功。

当模式中的第二个字符不是`*`时：（1）如果字符串第一个字符和模式中的第一个字符相匹配，那么字符串和模式都后移一个字符，然后匹配剩余的；（2）如果字符串第一个字符和模式中的第一个字符相不匹配，直接返回false。

而当模式中的第二个字符是`*`时：（1）模式后移2字符，相当于`x*`被忽略；（2）字符串后移1字符，模式后移2字符。

**代码**

```python
class Solution:
    # s, pattern都是字符串
    def match(self, s, pattern):
        if s == pattern:
            return True
        if len(pattern)>1 and pattern[1] == '*':
            if s and (s[0]==pattern[0] or pattern[0] == '.'):
                return self.match(s,pattern[2:]) or self.match(s[1:],pattern)
            else:
                return self.match(s,pattern[2:])
        elif s and pattern and (s[0] == pattern[0] or pattern[0]=='.'):
            return self.match(s[1:],pattern[1:])
        return False
```

### 53.表示数值的字符串

请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但是"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。

**思路：**数字的格式可以用A\[.\[B\]\]\[E\|eC\]或者.B\[E\|eC\]表示，其中A和C都是整数（可以有符号也可以没有），B是一个无符号数。

如果遍历到e或E，那么之前不能有e或E，并且e或E不能在末尾；

如果遍历到小数点,那么之前不能有小数点，并且之前不能有e或E；

如果遍历到正负号，那么如果之前有正负号，只能够出现在e或E的后面，如果之前没符号，那么符号只能出现在第一位，或者出现在e或E的后面；

如果遍历到不是上面所有的符号和0~9，返回False。

**代码：**

```python
class Solution:
    # s字符串
    def isNumeric(self, s):
        hasE = False
        hasDot = False
        hasSign = False
        for i in range(len(s)):
            if s[i] == 'e' or s[i] == 'E':
                if hasE or i == len(s) - 1:
                    return False
                hasE = True
            elif s[i] == '.':
                if hasDot or hasE:
                    return False
                hasDot = True
            elif s[i] == '+' or s[i] == '-':
                if hasSign and s[i - 1] != 'e' and s[i - 1] != 'E':
                    return False
                if not hasSign:
                    if i != 0 and s[i - 1] != 'e' and s[i - 1] != 'E':
                        return False
                hasSign = True
            else:
                if s[i] < '0' or s[i] > '9':
                    return False
        return True
```

### 54.字符流中第一个不重复的字符

请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。

```text
如果当前字符流没有存在出现一次的字符，返回#字符。
```

**思路：**用一个字典保存下出现过的字符，以及字符出现的次数。

除保存出现的字符之外，我们用一个字符数组保存出现过程字符顺序，如果不保存插入的char的话，我们可以遍历ascii码中的字符。

**代码：**

```python
class Solution:
    # 返回对应char
    def __init__(self):
        self.s=''
        self.dict1={}
    def FirstAppearingOnce(self):
        for i in self.s:
            if self.dict1[i]==1:
                return i
        return '#'
    def Insert(self, char):
        self.s=self.s+char
        if char in self.dict1:
            self.dict1[char]=self.dict1[char]+1
        else:
            self.dict1[char]=1
```

### 55.链表中环的入口节点

给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

**思路：**快慢指针，快指针一次走两步，慢指针一次走一步。如果链表中存在环，且环中假设有n个节点，那么当两个指针相遇时，快的指针刚好比慢的指针多走了环中节点的个数，即n步。从另一个角度想，快的指针比慢的指针多走了慢的指针走过的步数，也是n步。相遇后，快指针再从头开始走，快慢指针再次相遇时，所指位置就是入口。

**代码：**

```python
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None
class Solution:
    def EntryNodeOfLoop(self, pHead):
        if pHead==None or pHead.next==None or pHead.next.next==None:
            return None
        low=pHead.next
        fast=pHead.next.next
        while low!=fast:
            if fast.next==None or fast.next.next==None:
                return None
            low=low.next
            fast=fast.next.next
        fast=pHead
        while low!=fast:
            low=low.next
            fast=fast.next
        return fast
```

### 56.删除链表中重复的结点

在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1-&gt;2-&gt;3-&gt;3-&gt;4-&gt;4-&gt;5 处理后为 1-&gt;2-&gt;5。\(leetcode82\)

**思路**

1.设置一个虚拟头结点，设置两个指针，pre指向虚拟头结点，cur指向头结点。![](https://pic1.zhimg.com/80/v2-448a6dd66aecf42d947820b649dfd990_hd.jpg)

2.判断下一个节点的值和cur的值是否相等，若相等cur后移，直到下个节点的值和cur的值不同。![](https://pic1.zhimg.com/80/v2-247ec1ffc075c6d806bed5ab9352aea8_hd.jpg)

3.此时执行pre.next= cur.next。![](https://pic1.zhimg.com/80/v2-3c8ed7ab91d62b3dd78e907e78800d94_hd.jpg)

4.继续走直到结尾.![](https://pic3.zhimg.com/80/v2-1ac54a5fa5dd85d7ac4b72d5b17eb59a_hd.jpg)

**代码**

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None
class Solution(object):
    def deleteDuplicates(self, head):
        dummy = ListNode(-1)
        dummy.next = head
        pre = dummy
        cur = head
        while cur:
            while cur.next and cur.val == cur.next.val:
                cur = cur.next
            if pre.next == cur:
                pre = pre.next
            else:
                pre.next = cur.next
            cur = cur.next
        return dummy.next
```

### 57.二叉树的下一个结点

给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

**思路：**如下图所示，二叉树的中序遍历序列是{d,b,h,e,i,a,f,c,g}。![](https://pic1.zhimg.com/80/v2-c24fbfeb07d85f63cbbe8e084297d52c_hd.jpg)

1、如果该节点有右子树，那么它的下一个节点就是它的右子树的最左侧子节点；

2、如果该节点没有右子树且是父节点的左子树，那么下一节点就是父节点；

3、如果该节点没有右子树且是父节点的右子树，比如i节点，那么我们往上找父节点，找到一个节点满足： 它是它的父节点的左子树的节点。

**代码：**

```python
# class TreeLinkNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
#         self.next = None
class Solution:
    def GetNext(self, pNode):
        if not pNode:
            return None
        if pNode.right:
            res = pNode.right
            while res.left:
                res = res.left
            return res
        while pNode.next:
            tmp = pNode.next
            if tmp.left == pNode:
                return tmp
            pNode = tmp
        return None
```

### 58.对称的二叉树

请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。（leetcode101题）

例如，二叉树 `[1,2,2,3,4,4,3]` 是对称的。

```text
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

**思路**

递归的思想，首先判断头结点是否为空。然后将根节点的左右两个节点假设成两个独立的树，如果左右两个树都为空，返回True。然后看左子树的左结点和右子树的右结点、左子树的右结点和右子树的左结点是否相同，都相同返回True.

**代码**

```python
class Solution:
    def isSymmetrical(self, pRoot):
        if pRoot is None:
            return True
        return self.isSymmetricTree(pRoot.left,pRoot.right)
    def isSymmetricTree(self,left,right):
        if left is None and right is None:
            return True
        if left is None or right is None or left.val != right.val:
            return False
        return self.isSymmetricTree(left.left,right.right) and self.isSymmetricTree(left.right,right.left)
```

### 59.把二叉树打印成多行

从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。（leetcode102题）

给定二叉树: `[3,9,20,null,null,15,7]`,

```text
    3
   / \
  9  20
    /  \
   15   7
```

返回其层次遍历结果：

```text
[
  [3],
  [9,20],
  [15,7]
]
```

**思路**

用队列实现，root为空，返回空；队列不为空，记下此时队列中的节点个数temp，temp个节点出队列的同时，记录节点值，并把节点的左右子节点加入队列中。

**代码**

```python
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    def Print(self, pRoot):
        queue = [pRoot]
        res = []
        if not pRoot:
            return []
        while queue:
            templist = []
            templen = len(queue)
            for i in range(templen):
                temp = queue.pop(0)
                templist.append(temp.val)
                if temp.left:
                    queue.append(temp.left)
                if temp.right:
                    queue.append(temp.right)
            res.append(templist)
        return res
```

### 60.按之字形顺序打印二叉树

请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

例如：  
给定二叉树 `[3,9,20,null,null,15,7]`,

```text
    3
   / \
  9  20
    /  \
   15   7
```

返回锯齿形层次遍历如下：

```text
[
  [3],
  [20,9],
  [15,7]
]
```

**思路**

在上一题的基础上，加上flag标志判断，若flag为负表示偶数行，从右往左遍历。

**代码**

```python
class Solution:
    def Print(self, pRoot):
        queue = [pRoot]
        res = []
        flag = 1  #判断flag是否为负，如果为负表示偶数行，从右往左遍历
        if not pRoot:
            return []
        while queue:
            templist = []
            templen = len(queue)
            for i in range(templen):
                temp = queue.pop(0)
                templist.append(temp.val)
                if temp.left:
                    queue.append(temp.left)
                if temp.right:
                    queue.append(temp.right)
            if flag == -1:
                templist = templist[::-1]     #反转
            res.append(templist)
            flag *= -1
        return res
```

### 61.二叉搜索树的第K个节点

给定一棵二叉搜索树，请找出其中的第k小的结点。例如，（5，3，7，2，4，6，8）中，按结点数值大小顺序第三小结点的值为4。![](https://pic1.zhimg.com/80/v2-0abd8e8252a99bb70edf0b0864bb0e64_hd.png)

**思路：**如果是按中序遍历二叉搜索树的话，遍历的结果是递增排序的。所以只需要中序遍历就很容易找到第K个节点。

```python
class Solution:
    def __init__(self):
        self.my_list = []  # 用于保存中序遍历的序列的结点
    def in_order(self, pRoot):  # 中序遍历二叉树，并把结果保存到my_list中
        if not pRoot:
            return None
        if pRoot.left:
            self.in_order(pRoot.left)
        self.my_list.append(pRoot)
        if pRoot.right:
            self.in_order(pRoot.right)
    def KthNode(self, pRoot, k):
        self.in_order(pRoot)
        if k <= 0 or k > len(self.my_list):  # 越界
            return None
        return self.my_list[k - 1]
```

### 62.滑动窗口的最大值

给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}；针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个：{\[2,3,4\],2,6,2,5,1}，{2,\[3,4,2\],6,2,5,1}，{2,3,\[4,2,6\],2,5,1}，{2,3,4,\[2,6,2\],5,1}，{2,3,4,2,\[6,2,5\],1}，{2,3,4,2,6,\[2,5,1\]}。

**思路1：**求每次窗口的最大值，时间复杂度O\(n\*size\)。

```python
class Solution:
    def maxInWindows(self, num, size):
        res = []
        i = 0
        while size > 0 and i + size - 1 < len(num):
            res.append(max(num[i:i + size]))
            i += 1
        return res
```

**思路2：**双向队列，queue存入num的位置，时间复杂度O\(n\)

```python
class Solution:
    def maxInWindows(self, num, size):
        queue = []
        res = []
        i = 0
        while size>0 and i<len(num):
            if len(queue)>0 and i-size+1 > queue[0]: #若最大值queue[0]位置过期 则弹出 
                queue.pop(0)
            while len(queue)>0 and num[queue[-1]]<num[i]: #每次弹出所有比num[i]的数字
                queue.pop()
            queue.append(i)
            if i>=size-1:
                res.append(num[queue[0]])
            i += 1
        return res
```

### 64.矩阵中的路径

请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则之后不能再次进入这个格子。 例如，在下面的3 X 4 矩阵中包含一条字符串"bfce"的路径，但是矩阵中不包含"abfb"路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。

```text
a  b  t  g
c  f  c  s
j  d  e  h
```

**思路：**回溯法。首先，遍历矩阵中的格子，直到找到一个和字符串第一个字符相同的格子，把这个格子作为这个路径进入矩阵的起点。除了在边界的格子之外，其他各自都有4个相邻的格子。如果矩阵中的某个格子的字符不是ch，那么这个格子不可能处在路径上的第i个位置。如果路径上的第i个字符正好是ch，那么往相邻的格子寻找路径上的第i+1个字符。当在矩阵中定位了路径中前n个字符的位置之后，在与第n个字符对应的格子的周围都没有找到第n+1个字符，说明第n个字符不对，这个时候只要在路径上回到第n-1个字符，重新定位第n个字符即可。

　　由于路径不能重复进入矩阵的格子，因此需要定义一个和矩阵相同大小的布尔值矩阵，作为当前已经找过的路径是否经过某个格子的标识。 当矩阵中坐标为（row,col）的格子和路径字符串中相应的字符一样时，从4个相邻的格子\(row,col-1\),\(row-1,col\),\(row,col+1\)以及\(row+1,col\)中去定位路径字符串中下一个字符如果4个相邻的格子都没有匹配字符串中下一个的字符，表明当前路径字符串中字符在矩阵中的定位不正确，我们需要回到前一个，然后重新定位。一直重复这个过程，直到路径字符串上所有字符都在矩阵中找到合适的位置。

```python
class Solution:
    def dfs(self,matrix,flag,rows,cols,r,c,s):
        if s=='':
            return True
        dx = [-1,1,0,0]
        dy = [0,0,-1,1]   # 利用两个数组，来实现对每个格子周围格子的访问
        for k in range(4):
            x = dx[k] + r
            y = dy[k] + c
            if x >= 0 and x < rows and y >= 0 and y < cols and flag[x][y] and matrix[x*cols+y]==s[0]:
                flag[x][y]=False   # 修改当前格子的标识
                if self.dfs(matrix,flag[:],rows,cols, x, y,s[1:]):   # 递归
                    return True
                flag[x][y]=True   
                # 如果上一个判断条件返回的是False，那么就说明这个格子目前还不是路径上的格子，再把当前格子的标识修改回来。
        return False
    def hasPath(self, matrix, rows, cols, path):
        if path == '':
            return True
        flag = [[True for c in range(cols)] for r in range(rows)]  # 定义一个表示矩阵
        for r in range(rows):   
        # 对这个矩阵中的元素进行遍历，不断找路径进入矩阵的起点，直到以某个格子为起点找到整个路径为止。
            for c in range(cols):
                if matrix[r*cols+c] == path[0]:
                    flag[r][c] = False
                    if self.dfs(matrix,flag[:],rows,cols, r, c,path[1:]):
                        return True
                    flag[r][c] = True
        return False
```

### 65.机器人的运动范围

地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？

**思路：**还是利用递归对矩阵进行深度优先搜索，从（0,0）位置出发，每成功走一步标记当前位置为true,然后从当前位置往四个方向探索，返回1 + 4 个方向的探索值之和。判断当前节点是否可达的标准为：当前节点在矩阵内；当前节点未被访问过；当前节点满足K的限制。

```python
class Solution:
    def __init__(self):  # 机器人可以倒回来，但不能重复计数。
        self.count = 0
    def movingCount(self, threshold, rows, cols):
        flag = [[1 for i in range(cols)] for j in range(rows)]
        self.findWay(flag,0,0,threshold)  # 从（0，0）开始走
        return self.count
    def findWay(self,flag,i,j,k):
        if i >= 0 and j >= 0 and i < len(flag) and j < len(flag[0]) and sum(list(map(int,str(i)))) + sum(list(map(int,str(j)))) <= k and flag[i][j] == 1:
            flag[i][j] = 0
            self.count += 1
            self.findWay(flag,i-1,j,k)
            self.findWay(flag,i+1,j,k)
            self.findWay(flag,i,j-1,k)
            self.findWay(flag,i,j+1,k)
```

