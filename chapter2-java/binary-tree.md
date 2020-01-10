# Binary Tree

## General Tree 

又向，联通，无环

```java
class TreeNode {
    int key;
    List<TreeNode> children;
    public TreeNode(int key) {
        this.key = key;
        children = new ArrayList<TreeNode>();
    }
}

root //拎出来root就找到了整个树
```

## General Graph

```java
class GraphNode {
    int key;
    List<TreeNode> neighbors;
    public GraphNode(int key) {
        this.key = key;
        neighbors = new ArrayList<GraphNode>();
    }
}

List<GraphNode> //因为这个时候不能用一个root拎出来整张图了，需要一个list of node
//or adjacency list
//or adjacency matrix
```

A的neighbor里有B说明A到B有边，但是只有查了B才能知道B到A有没有边\(对于有向图\)

G = V + E 

Given V, Dense Graph的E是O\( $$V^2$$ \), Sparse Graph的E是O\(v\). 矩阵只适合node少的情况（sparse graph），而list适合node多的情况。

![](../.gitbook/assets/image%20%2826%29.png)

如果简化adj list，用list of list，这个list可以是array list, 也可以是linked list. 把vertex的index存下来，那么就可以用graph.get\(i\)拿到第i个node的 neighbor index 做算法题的时候，都是下面这种简化的。在实际工作中其实还是哟搞得上面的general graph，因为需要有value在

```java
List<List<Integer>> graph //也可以 Array<Array<Integer>> 如果知道长度
graph.get(0) //里的list的长度就是0的neighbor的个数
graph.get(1)

for (Integer i:graph.get(1)) {
    // i is a neighbor of node 1
}
```

外层list的顺序重要，里面的list的数量无所谓

## Iterative Traversal of Binary Tree

why iterative?

* from stack to heap
* for iterators

### Pre-Order

root进，pop栈顶并打印，右进，左进，pop栈顶并打印... 直到栈空了  
因为stack是先进后出，所以进stack时先进右后进左

Time: O\(n\)  
Space: heap上的那堆右节点 O\(height\)

```python

```

### In-Order 

关键是要知道什么时候左子树被打印完了 用一个helper，when helper is null, it means the left subtree of the root is finished, the root is the top element in the stack 当helper是null的时候，当前的左都打完了，这个时候可以放右边的node进helper；最后栈空了，但是helper不是null，这个时候就可以pop打印了。

helper的意义：下一个要看的节点 如果不是null就入栈             如果是null就证明当前栈的top（左边）被打印完了，可以pop了，pop完helper里是刚才pop的节点的 right child 直到left和right都是空的时候停止

```python

```

### Post-Order

#### 奇技淫巧 反过来pre-order

Time: O\(2n\)  
Space: O\(n\)

#### 正派做法 记录从哪里来的

如果prev是current的parent，这是第一次看到它；如果prev是current的left child，这是第二次看到它；如果prev是curr的right child，可以打印了

* root = stack.top
* if previous is null --&gt; going down \(left subtree\)
* if previous is current's parent --&gt; going down \(left subtree\)

go down: 有左，优先左；没有左，打印右； 都没有，打印自己并弹栈  
from left: 右或者打印自己并弹栈  
from right: 打印自己并弹栈  
  
偷看栈顶 从上下来，有左压左，有右压右，左右都无，打印自己   
从左上来，有右压右，无右打自己   
从右上来，打印自己

## Binary Search Tree

对任何一个节点，左边的所有数都比它小，右边所有都比它大 优势是search时每一步砍掉一半的子树，缩小搜索空间。

* search\(\)     worst case O\(n\) average\(logn\)
* insert\(\)       worst case O\(n\) average\(logn\)
* remove\(\)    worst case O\(n\) average\(logn\)

### Self-Balancing Binary Search Tree

为了解决BST不总是平衡的问题，有了两种特殊的树，自己维持self-balancing的状态，以保证以上三种操作的时间复杂度都是logn

* AVL Tree
* Red-Black Tree

Red-Black Tree

* in Java: TreeMap / TreeSet
* in C++: map/set

问题来了，重复node？那么就在Map里存一个value，也就是节点的数量。

### Search in BST

```java
class TreeNode {
    int key;
    TreeNode left;
    TreeNode right;
    public TreeNode(int key) {
        this.key = key;
    }
}

//recursion 尾递归 最后一步调用自身
public TreeNode searchInBST(TreeNode root, int value) {
    if (root==null || root.key==target) {  //没找到或者找到了的时候停止
        return root;
    }
    
    if (root.key<value) {
        return searchInBST(root.right, value);
    }else {
        return searchInBST(root.left, value);
    } 
}

//recursion 三目
public TreeNode biSearch(TreeNode root, int target) {
	if (root == null || root.key == target) {
		return root;
}
		
return biSearch(root.key > target ? root.left : root.right, target);


//iteration
public TreeNode searchInBST(TreeNode root, Integer value) {
   
   while (root!=null && root.key!=target) { //root==null || root.key==target
       if (root.key<value) {
            root=root.right;
        }else {
            root = root.left;
   }
   return root
} 
```

#### Tail Recursion 

最后一步_且仅在最后一步_调用自身 这种递归**很容易**写成iterative 这个写法的好处是先前存的所有stack都弹栈了。如果写成tail recursion的话 建议改写成iteration。

下面这个不是tail recursion，因为line 6这一行还在栈里。

```java
public static void preOrder(TreeNode root, List<Integer> result) {
    if (root == null) {
        return;
    }
    result.add(root.val);
    preorder(root.left, result);
    preorder(root.right, result); //其实是为了存储
}
```

`return isBST()&&isBST()`也不是tail recursion 因为最后一步是&&

另一个例子是求阶乘

```java
int factorial(int n) {
    if (n == 1){
        return 1;
    }
    return n*factorial(n-1);
}
```

```java
int tailFactorial(int n, int total) {
    if (n==1) {
        return total;
    }
    return tailFactorial(n-1,n*total);
}
```

### Insert in BST

应该return改了的tree的root node

```java
//recursion
TreeNode insertBST(TreeNode root, int target) {
	if (root==null) {
	return new TreeNode(target);               //只需要在base case里new出来一个就行，不要放在if外，不然每层都有一个
}
	if (target<root.key) {                            //1. 怎么往下走 问左右孩子要什么： 以它为root的subtree
	root.left=insertBST(root.left, target);					//2. 当前层做什么：挂
} else if (target>root.key) {                      //1. 怎么往下走 问左右孩子要什么： 以它为root的subtree
	root.right=insertBST(root.right, target);          //2. 当前层做什么：挂
} 
	return root;                                   //3. 给上层返回什么
} //超级重点: root.left = , root.right=

//iteration method 1 存下pre然后post-processing
TreeNode insertBST(TreeNode root, int target) {
	if (root==null) {       // root给的null，return这个新的node
	return new TreeNode(target);
	}
	TreeNode returnRoot = root;
	TreeNode pre = null;
	
	while (root!=null) {
		pre=root;
		if (root.key<target) {
			root = root.right;
		} else if (root.key>target){
			root = root.left;
		} else {
			return returnRoot;
		}
	}//root==null
	if (pre.key<target){
		pre.right = new TreeNode(target);
	} else if (pre.key>target){
		pre.left = new TreeNode(target);
	}
	return returnRoot;
}

//iteration method 2 往下看一眼
TreeNode insertBST(TreeNode root, int target) {
	if (root==null) {       // root给的null，return这个新的node
	return new TreeNode(target);
	}
	TreeNode returnRoot = root;
	
	while (root.key != target) {
		if (root.key < target) {
			if(root.right == null){
				root.right = new TreeNode(target);
				return returnRoot;
			}
			root = root.right;
		}else{
			if (root.left ==null) {
				root.left = new TreeNode(target);
				return returnRoot;
			}
			root = root.left;
		}
	}
	return returnRoot;
}

//tail recursion, remove redundant operation
public static TreeNode insert(TreeNode root, int target) {
	if (root==null) {       // root给的null，return这个新的node
	return new TreeNode(target);
	}
	helper(root, target);
	return root;
}	
	
public static void helper(TreeNode root, int target) {
	if (target == root.key) {
		return;
	} else if (target < root.key) {
			if (root.left == null) {
				root.left = new TreeNode(target);
			} else {
				helper(root.left, target);
			}
		} else {
				if (root.right == null) {
					root.right = new TreeNode(target);
				} else {
					helper(root.right, target);
					}
			}
}	
```

### Delete in BST

* leaf： 3.right=null
* only has child on one sight: 3.right = 3.right.right / 3.right = 3.right.left
* has children on both sides: 调右子树的最小值
* * 如果右节点没有children 它自己就是最小的   然后把该继承的（原来的left）都继承好
  * 如果右节点还有左子树 一直往左走，直到没有left为止 把最小的替换了 把最小的右子树接到原来的最小值位置

Time: O\(height\)  
Space: O\(height\)

```python
def __deleteMin(self, root):
        if not root.left:
            return root.right
        root.left = self.__deleteMin(root.left)
        return root
    
def __delete(self, root, key):
    if not root:
        return None
    if key < root.key:
        root.left = self.__delete(root.left, key)
    elif key > root.key:
        root.right = self.__delete(root.right, key)
    else: # root!=null and found it 
        if not root.right:
            return root.left  #report给了line 11或者line13，把return的value挂回去
        if not root.left:
            return root.right
        t = root
        root = self.__min(t.right)
        root.right = self.__deleteMin(t.right)
        root.left = t.left
    return root

def delete(self, key):
    self.__root = self.__delete(self.__root, key)
```



