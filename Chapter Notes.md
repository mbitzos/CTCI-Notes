# CTCI Notes

## Chapter 1: Arrays and Strings

### Hashtables
- are a create way to do fast lookup
- Built with arrays of linked lists (for hash clashes)

### Resizeable Arrays
- When adding an element to an array and its full, we double the size and copy elements to new doubled array
- time complexity is neglible

### JAVA: StringBuilder
- Added strings will take O(n) to copy each char in the array everytime
- Stringbuilders make it constant by creating a resizable array

## Chapter 2: Linked Lists
- Double linked lists give pointers for next/previous hmm

### Runner Technique
- Have two pointers that traverse the linked list at different speeds
- EXAMPLE:
  - If have an even linkedlist: a1->a2->a3->...an->b1->b2->b3->...b3
  - How can we rearrange it to: a1->b2->a2->b2->a3->b3->...an->bn
  - We can do this in O(n) also by having one pointer go one node at a time and the other 2
  - then when we have the second pointer reach the end we know the first as at the center
  - We can than use that marker to start inserting b1...bn interweaving it into a1...an

## Chapter 3: Stacks and Queues

## Chapter 4: Trees and Graphs

### Tree implementation

    class Node {
      constructor(val, children) {
        this.val = val
        this.children = children || []
      }
    }

    class Tree {
      /**
      * @param {Node} root 
      */
      constructor(root) {
        this.root = root
      }
    }


### Binary Trees
- has up to **2** children in each node

Implementation:


    class BinaryNode {
      constructor(val, left,right) {
        this.val = val
        this.left=left
        this.right=right
      }
    }

    class BinaryTree {
      /**
      * @param {Node} root 
      */
      constructor(root) {
        this.root = root
      }
    }

    /**
    * Util to generate a binary tree from json object (json = BinaryNode)
    const createBinaryTree = (json) => {
      
      
      const createNode = (json) => {
        if (!json) return
        return new BinaryNode(json.val,createNode(json.left),createNode(json.right))
      }

      return new BinaryTree(createNode(json))
    }

### Binary Tree Array implementation
Binary trees can be represented in an array where the following index calculations can be made to get the children/parents:

    parent: floor((i-1) /2)
    leftchild: i*2 + 1
    rightchild: i*2 + 2

### Binary Search Trees
- the descendents on the left are **all** lower than or equal to its parent
- the descendents on the right are **all** higher than its parent
  
### Balanced Trees
- imples that the tree is balanced enough on either side to have its find/insert operations be O(log(n)
- This doesnt necessarily mean perfectly equal (perfectly balanced trees)

### Complete Binary Tree
- all levels (except for last possibly) are filled with nodes
  - if the last level is not full, than it must be full from left to right (no gaps)

### Full Binary Tree
- all nodes have either 0 or 2 children
  
### Perfect Binary Tree
- all nodes have two children, its balanced perfectly and fully filled

### Binary Tree Traversals
**In order traversal:** visit the left branch, than curent, then right

    const inorderTraversal =(node) => {
      if (node) {
        inorderTraversal(node.left)
        visit(node.val)
        inorderTraversal(node.right)
      }
    }

**Pre-Order Traversal** visit the current before the children, then left to right
    const preorderTraversal =(node) => {
      if (node) {
        visit(node.val)
        inorderTraversal(node.left)
        inorderTraversal(node.right)
      }
    }

**Post-Order Traversal** visit the children left to right before parent
    const preorderTraversal =(node) => {
      if (node) {
        inorderTraversal(node.left)
        inorderTraversal(node.right)
        visit(node.val)
      }
    }

### Binary Heaps (Min-Heaps)
NOTE: Max heaps are essentially the same but elements are in descending order
- complete binary tree
- each node is smaller than its children
- The root is the smallest element in the tree
- two key operations:
  - insert:
    - We insert the element at the bottom and move it up (swap with parent) until its parent is larger or equal
      - O(log(n))
  - extractMin:
    - We return the root of the heap
    - To delete we:
      - Replace root with last element in heap and bubble down the element until its in the correct place
        - When deciding between left and right always swap with smaller child
      - O(log(n))
  
**Array Implementation**

    /**
    * Implements a min heap with arrays
    */
    class MinHeap {

      constructor() {
        this.heap =[]
      }

      insert(val) {

        // insert at bottom and bubble up
        this.heap.push(val)
        this.bubbleUp(this.heap.length-1)
      }

      getMin(remove) {
        let val =this.heap[0]

        // remove
        if (remove) {

          // put the min at end and cut off
          this.swap(0,this.heap.length-1)
          this.heap.splice(this.heap.length-1, 1)

          // correct the new min
          this.bubbleDown(0)
        }
        return val
      }


      swap(i,j) {
        let temp = this.heap[i]
        this.heap[i] = this.heap[j]
        this.heap[j] = temp
        return j
      }

      bubbleUp(i) {
        // keep going until we are larger than our parents or at root
        while (this.get(i) < this.getParent(i) && i !== 0) {
          i = this.swap(i,this.getParentIndex(i))
        }
      }
    
      bubbleDown(i) {    

        // keep going until we are larger than our kids 
        while ((this.get(i) > this.getRightChild(i) || this.get(i) > this.getLeftChild(i)) && i <= this.heap.length-1) { 
          let l =this.getLeftChildIndex(i)
          let r = this.getRightChildIndex(i)
          
          // take the smaller 
          if (this.get(r) === undefined || this.get(l) <= this.get(r))   
            i= this.swap(i,l)
          else 
            i = this.swap(i,r) 
          
        }
      }

      get(i) { 
        return this.heap[i] 
      } 
      getLeftChildIndex(i) {
        return 2*i +1 
      }
      getLeftChild(i) {
        return this.get(this.getLeftChildIndex(i))
      } 
      getRightChildIndex(i) {
        return 2*i +2
      }
      getRightChild(i) {
        return this.get(this.getRightChildIndex(i))
      }
      getParentIndex(i) {
        return Math.floor((i-1)/2)
      }
      getParent(i) {
        return this.get(this.getParentIndex(i))
      }

    }

### Tries (Prefix Trees)
- n-ary tree where each node represents a character, and branch down the tree represents a word
- special nodes are used to denote when there is a valid word. these exist at the end of the branchs but can be exist somewhere up the branch to denote a word within a word
  - example: Man]y] => man , many 
- These are used mainly to check for prefix or substring detection
  - As they can check if a string is a valid prefix in O(k) where k = length of string