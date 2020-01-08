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

![](../.gitbook/assets/image%20%2825%29.png)

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

## Traversal of Binary Tree

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
    
    if (root.key==value) {
        return root;
    }else if (root.key<value) {
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

最后一步调用自身 这种递归**很容易**写成iterative 这个写法的好处是先前存的所有stack都弹栈了

相反 return isBST\(\)&&isBST\(\)不是tail recursion 因为最后一步是&&

另一个例子是求阶乘

### Insert in BST

应该return改了的tree的root node

```java
//recursion
TreeNode insertBST(TreeNode root, int key) {
	if (root==null) {
	return new TreeNode(key);               //只需要在base case里new出来一个就行，不要放在if外，不然每层都有一个
}
	if (key<root.key) {                            //1. 怎么往下走
	root.left=insertBST(root.left, key);					//2. 当前层做什么：挂
} else if (key>root.key) {                      //1. 怎么往下走
	root.right=insertBST(root.right, key);          //2. 当前层做什么：挂
} 
	return root;                                   //3. 给上层返回什么
}

//iteration
TreeNode insertBST(TreeNode root, int key) {
	if (root==null) {       // root给的null，return这个新的node
	return new TreeNode(key);
	}
	
	
}
```

超级重点: root.left = , root.right=

