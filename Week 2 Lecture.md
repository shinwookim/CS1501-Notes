# Week 2: Search Problem, ADT Tree
## Recap: Boggle Game
Recall the Boggle Game. Given a 4x4 board of letters, we want to find all words with at least 3 adjacent letters.
- Adjacent letters are horizontally, vertically, or diagonally neighboring
- Any cube in the board can only be used once per word, but can be used fo multiple words

We came up with the pseudocode to solve the boggle game:
```Pseudo
void traverse(row and column of current cell, word string so far) {
   for each of the eight directions {
       if neighbor down the direction is a in the board and hasn’t been used {
          append neighbor’s letter to word string and mark neighbor             
          as used
          if word string a word with 3+ letters
             add word string to set of solutions
          if word string is a prefix
             traverse(row and column of neighbor, word string)
           delete last letter of word string and mark neighbor as unused
        }
     }
}
```

We also discussed how the search space could be modeled as a *tree*. In this tree, each node represents one call to `traverse()` except the root node.

Notice that with recursive algorithms, it is difficult to determine the the run-time using the frequency and cost technique due to the recursive calls. 

However, we know that in the worst case, the backtracking algorithm must visit each node in the search space tree. And at each node, the non-recursive part of traverse executes. So, we can use the number of nodes in the search tree as a lower bound for the worst-case runtime: $\text{worst-case runtime}=\text{number of nodes} \times \text{work per node (non-recursive part of traverse)}$

#### Calculating the Number of Nodes
So let's first calculate the number of nodes.
![Tree Analysis.png](Week2/Tree_Analysis.png)
**Maximum Number of Nodes** (Worst Case) $= 1 +16 \times (1+8+8^2+8^3+...+8^{15})$
We can use theta($\theta$) notation to simplify: Maximum Number of Nodes = $1+\theta(\text{largest term})=1+16\times\theta(8^{15})$.

In terms of board size($n$), we get: **Maximum Number of Nodes**$=1+n\times\theta(8^{n-1})=\theta(n\times 8^{n-1})=\theta({n\times 8^n})$  

Thus, in the worst-case, the run-time of backtracking for Boggle is $\Omega(n\times 8^n)$, where $n$ is the board size (number of cells). If the non-recursive work is constant ($O(1)$), then the worst-case runtime would be $O(n\times 8^n)$. Clearly, the worst-case runtime of backtracking algorithm is exponential. (Although **pruning** has practical savings in runtime, the runtime is still exponential).

### Speeding Up the Non-Recursive Work
So how can we make the non-recursive work(dictionary lookup for prefix/words) faster ($O(1)$)? There are many things we could do such as using a hash table, or a *tree*, but first let's discuss the time needed to append and delete letters from the word string.
#### String v. StringBuilder
When we are constructing the words over the course of recursion, we will need to build up / tear down strings. When we move down the tree, we are adding a new character to the current string. When we bactrack, we must remove the most recent character. (Think pushing/popping a string stack).

If we use a stack, `push` and `pop` are generally $\theta(1)$ except when we need to resize. However, that cost can be **amoritzed**.

So how could we implement this? We could *try* using a `String` to hold the current word string. However, Java `String`s are immutable which means everytime we make changes to the `String`, it must create a new object and allocate it to the variables which makes it essentially $\theta(n)$ where $n$ is the `length()` of the String.
```Java
s = new String("Here is a basic string");
s = s + "this operation allocates and initializes all over again";
```

So using a `String` is not a good idea, but we can use the `StringBuilder` to overcome this. `append()` and `deleteCharAt()` can be used to `push` and `pop` which now runs at $\theta(1)$. However, we still need to account for resizing (we must amortize it).
	- Note that the `StringBuffer` can also be used for this purpose. The `StringBuffer` is synchornized while the `StringBuilder` is not

## Searching Problem
A common problem in computer science is the searching problem. If we have:
+ a large *dynamic* set of data items in the form of `(key, value)` pairs
	+ Dynamic means we can add or remove from the set
+ input size($n$) for the number of pairs
	+ with key size assumed constant
We want to see if the *target key* exists in the set or not. If the key is not found, we can return `NULL`, `false`, or some other value to signify that key was not found.

### Symbol Table ADT
To solve this problem, we are going to create a new abstract data type, which we will call the **Symbol Table ADT**. Our ADT has a set of (key, value) pairs and the operations: `insert`, `search`, `delete`. Its name comes from earlier use in compilers.

We can implement the symbol table using: **Unsorted Array**, **Sorted Array**, **Unsorted LinkedList**, **Sorted LinkedList**, **Hash Table**.
|Implementation|Runtime for Insert|Runtime for search|Runtime for delete|
|------------------------------|---------------------------------------------|-------------------------|-------------------------|
|Unsorted Array|$O(1)$ - Insert at end (Amortized)|$O(N)$ - Linear Search|$O(N)$ - $O(N)$ to search for the element, then shift if necessary (Amortized)|
|Sorted Array|$O(N)$ - Find correct place, then shift|$O(\log{N})$ - Binary Search|$O(N)$ - Binary Search, then shift if necessary|
|Unsorted Linked List|$O(1)$ - Add at front|$O(N)$ - Linear Search|$O(N)$ - Linear Search|
|Sorted Linked List|$O(N)$ - Linear Search|$O(N)$ - Linear Search|$O(N)$ - Linear Search|
|Hash Table|Average: $O(1)$|Average: $O(1)$|Average: $O(1)$|

## Tree ADT
Arrays and LinkedLists are linear structures, but what if we used a non-linear data structure like a **tree**?
![Tree_Terminology.png](Assets/Week2/Tree_Terminology.png)
A **Binary Tree** is a tree where each node has at most 2 children.
![BinaryTree.png](Assets/Week2/BinaryTree.png)
A **full binary tree** is a tree in which every node other than the leaves has two children. A **complete binary tree** is a binary tree in which every level, except possibly the last, is completely filled, and all nodes are as far left as possible. So all full trees are complete binary tree.

Note that the number of nodes in a full tree is: $1+2+4+8+...=\sum_{i=0}^{h-1}2^i=\theta(2^{h-1})$ where $h$ is the height of the tree. This also implies that a complete tree has at maximum of $\theta(2^{h-1})$ nodes.

### Implementing a Tree
Let's first declare our Tree as a (generic) interface:
```Java
public interface TreeInterface<T> {
	public T getRootData() throws EmptyTreeException;
	public int getHeight() throws EmptyTreeException;
	public int getNumberOfNodes() throws EmptyTreeException;
	public boolean isEmpty();
	public void levelOrderTraverse();
	public void clear();
}
public class EmptyTreeException extends Exception {
	public EmptyTreeException(String reason){
		super(reason);
	}
}
```
#### Binary Tree Implementation
```Java
public interface BinaryTreeInterface<T> extends TreeInterface<T> {
	public void buildTree(T rootData); //Build a tree with just one root
	public void buildTree(T rootData, BinaryTreeInterface<T> left, BinaryTreeInterface<T> right); // Build a tree with root, left subtree, and right subtree
}
```
We'll first implement the Binary Tree using a linked structure. We'll have a pointer to a root node, and each node will point to the next node. So first, let's define the node:
```Java
public class BinaryNode<T> {
	private T data; // Points to Data
	private BinaryNode<T> left;  // Points to left node (root of left subtree)
	private BinaryNode<T> right; // Points to right node (root of right subtree)
	/*
	* Construcor (Default)
	*/
	public BinaryNode(T data){
		this(data, null, null);
	}
	/*
	* Construcor (Parametized)
	*/
	public BinaryNode(T data, BinaryNode<T> left, BinaryNode<T> right){
		this.data = data;
		this.left = left;
		this.right = right;
	}
	/*
	* Setters and Getters
	*/
	public void setData(T data){
		this.data = data;
		}
		
	public T getData(){
	    return data;
	}
	
	public void setLeftChild(BinaryNode<T> left){
		this.left = left;
	}
	
	public BinaryNode<T> getLeftChild(){
		return left;
	}
	
	public void setRightChild(BinaryNode<T> right){
		this.right = right;
	}
	
	public BinaryNode<T> getRightChild(){
		return right;
	  }
	/*
	* Check if it has childs
	*/
	public boolean hasLeftChild(){
		return left != null;
	}
	
	public boolean hasRightChild(){
		return right != null;
	}
	/* How do we get the number of nodes?
	 * 1. Traverse & Count all nodes
	 * 2. Use recursion on the subtree.
	 */
	public int getNumberOfNodes(){
		int result = 1;
		if(left != null){
			result += left.getNumberOfNodes();
		}
		if(right != null){
			result += right.getNumberOfNodes();
		}
		return result;
	}
	// Similarly, use recursion
	// Another way to implement is to parameterize the rootNode and pass in the children
	public int getHeight(){
		int max = 0;
		if(left != null){
			max = left.getHeight();
		}
		if(right != null){
			if(right.getHeight() > max){
				max = right.getHeight();
			}
		}
		return 1 + max;
	}
	// Again, recursion
	// Note that this is a shallow copy because the data is pointing to the same data.
	public BinaryNode<T> copy(){
		BinaryNode<T> newRoot = new BinaryNode<>(data);
		if(left != null){
			newRoot.left = left.copy();
		}
		if(right != null){
			newRoot.right = right.copy();
		}
		return newRoot;
	}
}
```
Now that we have the node, let's build the actual tree.
```Java
public class BinaryTree < T > implements BinaryTreeInterface < T > {
	private BinaryNode < T > root;
	// Implementation Here
}
```
Notice that we are implementing a `BinaryTreeInterface` which means we'll have to implement all methods of the Interface.

Our default constructor just sets the `root` to `null`
```Java
 public BinaryTree() {
	root = null;
}
```
1. The `setRoot(BinaryNode <T> root)` sets the root node to the given node
2. The `getRoot()` method returns the root
3. The `getRootData()` method returns the data of the root
4. The `getHeight()` methods returns the height of the tree. Recall that the `BinaryNode` class already has a `getHeight()` method, so we can simplify our implementation by using it.
5. The `getNumberOfNodes()` methods returns the height of the tree. Recall that the `BinaryNode` class already has a `getNumberOfNodes()` method, so we can simplify our implementation by using it.
6. `isEmpty()` returns boolean value depending on when the tree is empty. Notice that if the tree is empty, the `root` will be `null`
7. Conversely, we can `empty()` a tree by setting the `root` to `null`. (Root will be garbage collected, including the subtrees)
```Java
protected void setRoot(BinaryNode < T > root) {
	this.root = root;
}
protected BinaryNode < T > getRoot() {
	return root;
}
public T getRootData() throws EmptyTreeException {
	// We must account for the special case when the root is null (empty tree).
	if (root == null)
		throw new EmptyTreeException("Empty tree");
	return root.getData();
}
public int getHeight() throws EmptyTreeException {
	if (root == null)
		throw new EmptyTreeException("Empty tree");
	// We use the `getHeight()` method of the BinaryNode class.
	return root.getHeight();
}
public int getNumberOfNodes() throws EmptyTreeException {
	if (root == null)
		throw new EmptyTreeException("Empty tree");
	// We use the `getNumberOfNodes()` method of the BinaryNode class.
	return root.getNumberOfNodes();
}
public boolean isEmpty() {
	return root == null;
}
public void clear() {
	root = null;
}
```
#### Building the Tree
But how do we actually build a tree? We can use the `buildTree()` method.
```Java
private void privateBuildTree(T rootData, BinaryTree <T> leftTree, BinaryTree <T> rightTree) {
	root = new BinaryNode <> (rootData);
	if ((leftTree != null) && (!leftTree.isEmpty())) {
		root.setLeftChild(leftTree.getRoot());
	}
	if ((rightTree != null) && (!rightTree.isEmpty())) {
		if (leftTree == rightTree) {
			root.setRightChild(rightTree.getRoot().copy());
		} else {
			root.setRightChild(rightTree.getRoot());
		}
	}
	if ((leftTree != null) && (leftTree != this)) {
		leftTree.clear();
	}
	if ((rightTree != null) && (rightTree != this)) {
		rightTree.clear();
	}
}
```
`privateBuildTree(T rootData, BinaryTree <T> leftTree, BinaryTree <T> rightTree)` does three things:
1. Create a new `BinaryNode` which will act as our `root` (`root = new BinaryNode <> (rootData);`)
2. Link the `root` to the left subtree (if it is not `null`)  - `root.left = leftTree.root;`
3. Link the `root` to the right subtree (if it is not `null`) - `root.right = rightTree.root;`
However we must also act to prevent client direct access to our subtrees (to ensure encapsulation). We don't want the client to be able to directly access our subtrees. For example, if the client envokes `privateBuildTree(data, treeA, treeB)`, the shouldn't be able to reference the left subtree with `treeA` and the right subtree with `treeB` after the `privateBuildTree` ends.

To do this, we can try setting `treeA = null;` and `treeB = null;`. However, this approach will not work because we can't access `treeA` and `treeB` from within the method (they our outside variables). What if we set `leftTree = rightTree = null;`? This has no effect on client access.

Instead we can set the root node of each subtree to be null: (`leftTree.root = null; rightTree.root = null;`). In this case, `treeA` and `treeB` will still be able to access the original `BinaryTree` objects, they will not be able to access the new `BinaryNode` classes and thus won't be able to access the new tree. Note that this can be done easily using the `clear()` method.

We now must consider the special case when `treeA == treeB`. This means that the `leftTree` and `rightTree` will point to the same BinaryTree. If we are not careful, the `root`'s left child and right childs will point to the same single node. Thus, it will not be a valid tree (In a tree every node must have a single parent). So how can we handle this case? We can copy the subtree, assign the left to the original, and right to the copy.

Another special case may be if `treeA == this` or `treeB == this`. If we are not careful, when we call `clear()`, we will destory the tree we are calling. Thus we must make a comparison to ensure we are not `clear()`ing our own tree.

Note that `privateBuildTree(...)` can be called from multiple methods:
```Java
public BinaryTree(T rootData) {
	privateBuildTree(rootData, null, null);
}
public BinaryTree(T rootData, BinaryTree < T > leftTree,
	BinaryTree < T > rightTree) {
	privateBuildTree(rootData, leftTree, rightTree);
}    
public void buildTree(T rootData) {
	privateBuildTree(rootData, null, null);
}    
public void buildTree(T rootData,
	BinaryTreeInterface < T > left,
	BinaryTreeInterface < T > right) {
	privateBuildTree(rootData,(BinaryTree<T>)left,(BinaryTree<T>)right);
}    
```

 #### Traversing the Tree
 In a linear data structure, it is very easy to traverse it from start to end using a typical interation. However, for a non-linear data structures, there are multiple methods to implement traversal. In a general binary tree there is: **Pre-order, in-order, post-order, level-order** traversal.
+ Pre-order Traversal: We visit root before we visit the root's subtree
	+ Think Left-touch
```Java
public void preorderTraverse() {
	preorderTraverse(root);
}
private void preorderTraverse(BinaryNode < T > root) {
	if (root != null) {
		//visit the root
		System.out.println(root.getData());
		//left
		preorderTraverse(root.getLeftChild());
		//right
		preorderTraverse(root.getRightChild());
	}
}
```
- In-order Traversal: We visit the left subtree, root, then right subtree
	- Think bottom-touch
```Java
public void inorderTraverse() {
	inorderTraverse(root);
}
private void inorderTraverse(BinaryNode < T > root) {
	if (root != null) {
		//left
		inorderTraverse(root.getLeftChild());
		//visit the root
		System.out.println(root.getData());
		//right
		inorderTraverse(root.getRightChild());
		//have you seen about time?
	}
}
```
 * Post-order Traversal: We visit the left subtree, right subtree, then root 
	 * Think Right-touch
```Java
public void postorderTraverse() {
	postorderTraverse(root);
}
private void postorderTraverse(BinaryNode < T > root) {
	if (root != null) {
		//left
		postorderTraverse(root.getLeftChild());
		//right
		postorderTraverse(root.getRightChild());
		//visit the root
		System.out.println(root.getData());
	}
}
```
 * Level-order Traversal: We begin at the root and visit nodes one level at a time. (Implementation to come later - See Breadth-First Search of Graphs).

Note that we call a recursive function which recurses on the last line, to be **tail-recursive**. This is important because many compilers can automatically convert tail-recursive functions into iterative functions(which are often faster).

The run-time of traversal will clearly be linear time.