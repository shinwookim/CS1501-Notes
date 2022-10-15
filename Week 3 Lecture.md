# Week 3: Binary Search Tree

## Binary Search Tree
Now let's talk about the Binary Search Tree. A Search Tree has the following property for each node in the tree:
1. The data of the node is larger than the data in all nodes in the node's left subtree
2. The data of the node is smaller than the data in all nodes in the node's right subtree
Clearly, the largest element will be the right-most element, and the smallest element will be the left-most element. So to find the smallest item, we must start at the root and move left until it is not possible; and to find the largest item, we must start at the root and move right until it is not possible. Additionally, the shape of the BST will vary depending on which order the elements are inserted in.

The Search Tree can be represented as an interface. Notice that these are the same operatios we need to implement a symbol table.
```Java
public interface SearchTreeInterface<T extends Comparable<?super T>> {
	public boolean contains(T entry);
	public T getEntry(T entry);
	public T add(T entry);
	public T remove(T entry);
}
```

### Adding to BST
How do we add a data item *entry* into a BST rooted at *root*?
We must consider the following cases:
1. `root.data.compareTo(entry) == 0`
2. `root.data.compareTo(entry) < 0` (Entry is larger)
3. `root.data.compareTo(entry) > 0` (Entry is smaller)

### Implementing the BST
Now we will implement the BST. Notice that the BST's element must be comparable (so we can use the `CompareTo()` method).
```Java
public class BinarySearchTree <T extends Comparable <?super T>> extends BinaryTree <T> implements SearchTreeInterface <T> {
	// Implementation
}
```
Our default constructor calls upon the `super()` and creates an empty `BinaryTree.` We can parametize this and set the root to our desired node.
```Java
public BinarySearchTree() {
	super();
}
public BinarySearchTree(T rootData) {
	super();
	setRoot(new BinaryNode < T > (rootData));
}
public BinarySearchTree(BinaryNode < T > root) {
	super(null);
setRoot(root); //Have you seen About Time?
}
```
When we are building the tree, we must now consider the search tree properties. In order to block attempts to build a tree without following the BST property, we will throw exceptions when the `buildTree()` —which are inherited from `BinaryTree`s—methods are called.
```Java
public void buildTree(T rootData) {
	throw new UnsupportedOperationException();
}
public void buildTree(T rootData, BinaryTreeInterface <T> left, BinaryTreeInterface <T> right) {
	throw new UnsupportedOperationException();
}
```
The `getEntry(T entry)` can search for the entry we are looking for by using the `findEntry(BinaryNode<T> root, T entry)` method. Similarly, `contains()` can be implemented by using `getEntry()` method.
```Java
public T getEntry(T entry) {
	return findEntry(getRoot(), entry);
}
public boolean contains(T entry) {
	return getEntry(entry) != null;
}
private T findEntry(BinaryNode <T> root, T entry) {
	T result = null;
	// If root is null, the tree is empty
	if (root != null) {
		int compResult = root.getData().compareTo(entry);
		if (compResult == 0) {
			result = root.getData();
		} else if (compResult < 0) {
			// Recurse on right subtree 
			result = findEntry(root.getRightChild(), entry);
		} else {
			// Recurse on left subtree
			result = findEntry(root.getLeftChild(), entry);
		}
	}
	return result;
}
```
Now, how do we add entries to our BST? We can define our `add(T entry)` method which will utilize a `private` helper method. It is usually a good idea to make our helper methods `private`. We don't want to expose our implementation details to the outside.
```Java
public T add(T entry) {
	T result = null;
	if (isEmpty()) {
		setRoot(new BinaryNode < > (entry));
	} else {
		result = addEntry(getRoot(), entry);
	}
	return result;
}
private T addEntry(BinaryNode <T> root, T entry) {
	T result = null;
	int comparison = root.getData().compareTo(entry);
	if (comparison == 0) {
		// If root == data --> We will replace the root, save the original to result and return it.
		result = root.getData();
		root.setData(entry);
	} else if (comparison < 0) {
		if (root.hasRightChild()) {
			result = addEntry(root.getRightChild(), entry);
		} else {
			root.setRightChild(new BinaryNode <>(entry));
		}
	} else {
		if (root.hasLeftChild()) {
			result = addEntry(root.getLeftChild(), entry);
		} else {
			root.setLeftChild(new BinaryNode <>(entry));
		}
	}
	// Note that we will return either the data we've inserted or the data that we've overwritten.
	return result;
}
```

### Removing from the BST
How can we remove an element from the BST? Deleting is simple if we are trying to remove a leaf. However, if we want to remove a node that is not a leaf, we must consider how to connect the children of the node we remove back to the tree.

Let's consider the possible cases:
1. Node to be deleted is a leaf
	* We can simply pass back `null` to the node's parent
2. Node to be deleted has one child
	* Pass back the child to the node's parent to adopt instead of the node
3. Node to be deleted has two children
	- If the node has two children, parents cannot adopt both. Instead we can try to replace the data in the node while still maintaining the serach tree properties. We can either:
		1. Replace node's data by its successor (largest item in left subtree)
		2. Replace node's data by its predecessor (smalles item in right subtree)
		3. Then, we delete the node that we selected in previous step (as long as it has at most one child) This is fine because the largest item in left subtree or the smalles item in right subtree can have at most one child

Deleting an item requires first to find the node with that item in the tree. Assuming we have already found that node, the `removeFromRoot(BinaryNode<T> root)` method returns a reference to the root of the tree after removing its root.
```Java
private BinaryNode <T> removeFromRoot(BinaryNode <T> root) {
	//handles the three cases
	if (root.hasLeftChild() && root.hasRightChild()) {
		// If the root has two children, we can replace the root's data with that of the largest item in it's subtree, remmove the largest item from the left subtree, and return root
		BinaryNode < T > largest = findLargest(root.getLeftChild());
		root.setData(largest.getData());
		root.setLeftChild(removeLargest(root.getLeftChild()));
	} else {
		// If the root has one child, it returns the node of the root of subtree
		if (root.hasLeftChild()) {
			root = root.getLeftChild();
		} else {
			root = root.getRightChild();
		}
	}
	// if root is the only node, returns null
	return root;
}
```
But, In the case of two children, how do we find the largest item in a BST? The `findLargest(BinaryNode<T> root)` method takes care of that. Since the largest item in a tree has to be a right child, we traverse down the right child, until we cannot.
```Java
 private BinaryNode <T> findLargest(BinaryNode <T> root) {
	if (root.hasRightChild()) {
		return findLargest(root.getRightChild());
		} else {
			return root;
		}
}
```
We can now use the `removeLargest(BinaryNode<T> root)` method to remove that item from the tree. Note that if the largest item is the root of the tree, we return its left child.
```Java
private BinaryNode < T > removeLargest(BinaryNode < T > root) {
	if (root.hasRightChild()) {
		root.setRightChild(removeLargest(root.getRightChild()));
	} else {
		root = root.getLeftChild();
	}
	return root;
}
```
Now, we need to find the node to delete. The `removeEntry(BinaryNode <T> root, T entry, ReturnObject item)` method returns the root of the BST after removing the node that contains entry if found. However, we also need to return the removed data item. To return multiple items, we can pass in a wrapper object(`ReturnObject`).
```Java
private BinaryNode <T> removeEntry(BinaryNode <T> root, T entry, ReturnObject item) {
	if (root != null) {
		int comparison = root.getData().compareTo(entry);
		if (comparison == 0) {
			item.set(root.getData());
			root = removeFromRoot(root);
		} else if (comparison < 0) {
			root.setRightChild(removeEntry(root.getRightChild(), entry, item));
		} else {
			root.setLeftChild(removeEntry(root.getLeftChild(), entry, item));
		}
	}
	return root;
}
private class ReturnObject {
	T item;
	private ReturnObject(T entry) {
		item = entry;
	}
	private void set(T entry) {
		item = entry;
	}
	private T get() {
		return item;
	}
}
```
Lastly, we can call the `remove(T entry)` to initiate the remove.
```Java
public T remove(T entry) {
	// T result = findEntry(entry);
	ReturnObject envelope = new ReturnObject(null);
	setRoot(removeEntry(getRoot(), entry, envelope));
	return envelope.get();
}
```
##### Summary
1. `remove(T)` creates an empty wrapper object and calls `removeEntry`, passing to it the entry to delete and the empty wrapper
2. `removeEntry` searches for *entry* by either moving left or right (depending on the comparisons between entry and the data potion of the current node)
3. Once the node that contains entry is found, the data insde the node is put inside the wrapper object.
4. Then, `removeFromRoot` is called on the found node
5. `removeFromRoot` removes the node (three cases) and may need to call `findLargest` and `removeLargest` on the left subtree of the node(if it has two children)
6. `removeFromRoot` returns the new root of the tree to `removeEntry`
7. `removeEntry` sets the right or left child if current node to the return value of `removeFromRoot` and returns the node after this modification
8. `removeEntry` will return back to remove the (possibly) new root of the tree after removing entry
9. `remove` sets the root of the tree to the reutrn value of `removeEntry` amd returns the entry inside the wrapper object


### Runtime of BST Operations
* For `add()`, the number of comparisons is equal to the depth of the new node and $0 \leq \text{depth}\leq\text{height}$.  
* For a **search miss**(searching for an item that is not in the list), the number of comparison is **equal to the depth** of the node if it were in the tree.
* For a **search hit**(searching for an item that is in the list), the number of comparison is **equal to the depth + 1**. The addtional comparison comes from the root of the 'tree' where the item is found.
* On average, the node depth in a BST is $O(\log(n))$ and occurs when we are inserting in random order. (Sedgewick 3.2 Proof). $n:=\text{number of data items}$
* In the worst case, node depth is $O(n)$ - if the tree is linearly shaped.
+ For a delete, it takes $O(\log n)$ on average to find the node; so, it also takes $O(\log n)$ to find and remove the largest node in a subtree. Therefore, it takes $O(\log n)$ on average, and $O(n)$ in the worst case.

