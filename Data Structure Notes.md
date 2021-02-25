# Data Structure Notes

- [Data Structure Notes](#data-structure-notes)
  - [Chapter 1: Arrays and Strings](#chapter-1-arrays-and-strings)
    - [Hashtables](#hashtables)
    - [Resizeable Arrays](#resizeable-arrays)
    - [JAVA: StringBuilder](#java-stringbuilder)
  - [Chapter 2: Linked Lists](#chapter-2-linked-lists)
    - [Runner Technique](#runner-technique)
  - [Chapter 3: Stacks and Queues](#chapter-3-stacks-and-queues)
  - [Chapter 4: Trees and Graphs](#chapter-4-trees-and-graphs)
    - [Tree implementation](#tree-implementation)
    - [Binary Trees](#binary-trees)
    - [Binary Tree Array implementation](#binary-tree-array-implementation)
    - [Binary Search Trees](#binary-search-trees)
    - [Balanced Trees](#balanced-trees)
    - [Complete Binary Tree](#complete-binary-tree)
    - [Full Binary Tree](#full-binary-tree)
    - [Perfect Binary Tree](#perfect-binary-tree)
    - [Binary Tree Traversals](#binary-tree-traversals)
    - [Binary Heaps (Min-Heaps)](#binary-heaps-min-heaps)
    - [Tries (Prefix Trees)](#tries-prefix-trees)
    - [Graphs](#graphs)
    - [Searches](#searches)
    - [Depth-First-Search (DFS)](#depth-first-search-dfs)
    - [Breadth-First-Search (BFS)](#breadth-first-search-bfs)
    - [Bidirectional Search](#bidirectional-search)

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

    // Util to generate a binary tree from json object (json = BinaryNode)
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

**Implementation**

    class TrieNode {
      constructor(val) {
        this.val = val;
        this.word = false;
        this.children = {};
        this.parent = null;
      }

      addChild(child) {
        this.children[child.val] = child;
        child.parent = this;
      }
      hasChildren() {
        return Object.keys(this.children).length > 0;
      }
    }
    class Trie {
      constructor() {
        this.root = new TrieNode(null);
      }

      // adds a word
      addWord(word) {
        let currentNode = this.root;
        for (let i = 0; i < word.length; i++) {
          if (currentNode.children[word[i]]) {
            currentNode = currentNode.children[word[i]];
          } else {
            // there are no more so we can just omit the if check
            for (let j = i; j < word.length; j++) {
              let newC = new TrieNode(word[j]);
              currentNode.addChild(newC);
              currentNode = newC;
            }
            break;
          }
        }
        // last node is a word by default
        currentNode.word = true;
      }

      // removes a word
      removeWord(word) {
        let currentNode = this.root;
        for (let i = 0; i < word.length; i++) {
          let char = word[i];
          if (currentNode.children[char]) {
            currentNode = currentNode.children[char];
          } else {
            // never existed?
            return;
          }
        }
        // wasnt a word?
        if (!currentNode.word) {
          return;
        }

        currentNode.word = false;

        // if we have children that means we are a prefix, dont delete
        if (currentNode.hasChildren()) {
          return;
        }

        // delete any characters trailing
        while (currentNode.parent != null && !currentNode.word) {
          delete currentNode.parent.children[currentNode.val];
          currentNode = currentNode.parent;
        }
      }

      // checks if the word exists
      exists(a) {
        let curr = this.root;
        for (let i = 0; i < a.length; i++) {
          let char = a[i];
          if (curr.children[char]) {
            curr = curr.children[char];
          } else {
            return false;
          }
        }
        console.log(curr);
        return curr.word;
      }

      // if this trie has this prefix
      hasPrefix(a, b) {
        let curr = this.root;
        for (let i = 0; i < a.length; i++) {
          let char = a[i];
          if (curr.children[char]) {
            curr = curr.children[char];
          } else {
            return false;
          }
        }
        return true;
      }
    }

### Graphs
There are two ways to represent graphs:

**Node/Graph**

    class Node {
      constructor(name) {
        this.name = name
        this.children = []
        this.childrenLookup = {}
      }
      addChild(child) {
        this.children.push(child)
        this.childrenLookup[child.name] = child
      }
    }

    class Graph {
      constructor(nodes) {
        this.nodes = nodes
        // create lookup by name
        this.nodeLookup = {}
        this.nodes.forEach(node => {this.nodeLookup[node.name]=node})
      }
    }

    // helper func to create a directed/undirected graph
    // n => Array of names
    // conn => Array of [source name,destination name]
    const createGraph= (n,c, directed) => {
      let nodes = n.map(n => new Node(n))
      let graph = new Graph(nodes)

      // add conn => [source,destination]
      c.forEach(conn => {
        let source = graph.nodeLookup[conn[0]]
        let destination =graph.nodeLookup[conn[1]]
        source.addChild(destination)

        // if this graph is undirected we can add bidirectional conns everytime
        if (!directed){
          destination.addChild(source)
        }
      });
      return graph
    }

**Adjacency Matrix**
NxN matrix where n[i,j]= connection from node[i] => node[j]

    // creates adj matrix for a directed/undirected graph
    // n => Array of names
    // conn => Array of [source name,destination name]
    const createAdjacencyMatrix = (n,c,directed) => {
      let nLengths= n.length
      let adjacencyMatrix = []
      n.forEach(() => {adjacencyMatrix.push(new Array(nLengths))})
      c.forEach(([s,d]) => {
        adjacencyMatrix[s][d] = true
        if (!directed) {
          adjacencyMatrix[d][s] = true
        }
      })
      return adjacencyMatrix
    }

### Searches
### Depth-First-Search (DFS)
**t: O(V+E) s: O(V) where V = vertices(nodes) and E = edges (connections)**
- Start at root
- traverse each branch fully for a node, (depth first).
- mark nodes as visited or not, and make sure to not traverse marked nodes
- Often used if we want to traverse every node in a graph, but doesnt really matter
- done recursively going into each nodes children
  
</a>

    const dfs = (graph) => {
      
      let visited = new Set()

      const traverseNode = (node) => {
        if (visited.has(node)) return
        visited.add(node)
        node.children.forEach(c => {
          traverseNode(c)
        })
      }
      traverseNode(graph.nodes[0]) 
    }

### Breadth-First-Search (BFS)
**t: O(V+E) s: O(V) where V = vertices(nodes) and E = edges (connections)**
- Start at root
- traverse each nodes connection fully (cover all immediate connections first)
- mark nodes as visited or not, and make sure to not traverse marked nodes
- Used to find shorted path as we try to stay as close to the original node as possible
- Done using a queue where new node children are added to a queue, this allows us to visit the breadth first
  
</a>

    const bfs = (graph) => {

      let queue= []
      let visited = new Set()
      queue.push(graph.nodes[0])
      while(queue.length > 0) {
        let node = queue.shift()
        visited.add(node)
        node.children.forEach(c =>{
          if (!visited.has(c)) {
            queue.push(c)
          }
        })
      }
    }

###  Bidirectional Search
- we run two BFS from source and destination
- if the searches collide we found the shortest path
  
</a>

    const bidirSearch  = (a,b) => {
      let firstQueue = []
      let secondQueue = []
      let firstVisited = new Set()
      let secondVisited = new Set()
      firstQueue.push(a)
      secondQueue.push(b)

      while (firstQueue.length>0 && secondQueue.length > 0) {
        let first= firstQueue.shift()
        firstVisited.add(first)
        if (secondVisited.has(first)) 
          return true
        console.log("visit first:", first.name)

        let second = secondQueue.shift()
        if (firstVisited.has(second))
          return true
        secondVisited.add(second)
        console.log("visit first:", second.name)

        first.children.forEach(c => {
          if (!firstVisited.has(c))
            firstQueue.push(c)
        })

        second.children.forEach(c => {
          if (!secondVisited.has(c))
            secondQueue.push(c)
        })

      }
      return false
    }

