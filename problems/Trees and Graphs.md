## 4.1 Route between nodes
**Q**: Create algo that returns if there exists a route between two nodes in a **directed** graph exists

### BFS
**t: O(V+E) s: O(E)**
- Just start a BFS on the first node and whenever we visit a node check if its the target
- We could also use a bidirectional search to reduce time complexity by a bit (still same Big O) 
  

## 4.2 Minimal Tree 
**Q**: Create algo that given a sorted unique list of numbers, creates a binary tree that has the smallest possible height

### Binary search tree
**t: O(n) s: O(n)**
- create a binary search tree by recursively partitioning the array into halves, where the center is the parent node, and the left and right paritions are the left/right nodes

</a>

    let arr = [0,1,2,3,4,5,6,7,8,9,10]


    const createRoot= (i,j) => {
      // return single node
      if (i===j) return new BinaryNode(arr[i])
      // if we went past eachother, skip
      else if (i > j) return 

      // create node
      let center = Math.floor((i+j)/2) 
      return new BinaryNode(arr[center], createRoot(i,center-1), createRoot(center+1,j))
    }

    let tree = new BinaryTree(createRoot(0,arr.length-1))     


## 4.3 List of Depths
**Q**: given a binary Tree, design an algorithm which creates a linked list of all nodes at each depth

### BFS with Depth increase flag
**t: O(n) s: O(n)**
- We will use the BFS technique to traverse left and right nodes first
- For the queue we will hold a special datatype that holds the value, and a flag that flags if we want to increase the depth as well as allow our child to do so as well
- This flag essentially is given to the right children only, UNLESS we dont have any more right children then our left child is given the flag
- Honestly this kind of works like how king and queens work, how you only give the queen power if there are no sons or whatever. 
- We also have an array of linked lists that hold this
  

  
    const getListOfDepths = (tree) => {

        let lists = []  
        let queue = []

        // adds to queue with our custom datatype
        const addToQueue = (val, increase) => {
          queue.push({
            val, increase
          })
        }

        // start with root
        addToQueue(tree.root, true)
        let depth  =0
        while (queue.length > 0) {
          let {val:parent,increase} = queue.shift()
          console.log(parent,depth)

          // init depth
          if (!lists[depth])
            lists[depth] = new LinkedList()

          
          // add to depth
          lists[depth].add(parent.val) 
          if (increase) {
            depth++
          }
          // give to left if our parent is given priviledge to increase and we have no more right
          if (parent.left)
            addToQueue(parent.left,increase && !parent.right)

          // always pass down to the right
          if (parent.right)
            addToQueue(parent.right,increase)
        }

        return lists 

    }

## 4.4 Check Balanced
**Q**: Create algo that checks if a binary tree is balanced: means a tree such that the heights of the two subtrees of any node never differ by more than one

### Recursively validate and count
**t: O(n) s: O(1) where H = height of tree**
- just recursively go down the tree and check the size of the left and right node tree
- We can do this simply by doing:

</a>

    check(node)
        if (node === null) return 0
        let count = 1 // count ourselves
        let left=  check(node.left)
        let right = check(node.right)

        // if either of the trees report an imbalance or we are an imbalance
        if (left== -1 || right == -1 || Math.abs(left-right) > 1) return -1
        else
          // return total count
          return count + left + right 

## 4.5 Validate BST
**Q**: Write algo that verifies if BT is a BST

### Recurssively validate
**t: O(n) s: O(1)**
- we recursively go down the tree verifying if: left.val <= node.val < right.val
- if not we recursively bubble up false,
  - else we return true at the end as everything went through fine


## 4.6 Successor
**Q**: Write an algo that returns the next successor of a node in the binary search tree (the next highest node)

### Farthest down left child on the right
**t: O(n) s: O(1)**
- The way a binary tree is structure is that all nodes to the left are smaller and all nodes to the left are larger. If we are to find the next highest node this means that it exists to the right.
- The next highest node now exists between that node and the original node aka the furthest to the left, return the current traversed node if no more left children exist
  - NOTE: if no right tree exists, then just take the immediate left node

</a>

    if (!node.right) return node.left
    else
      let curr= node.right
      while (curr.left) curr =curr.left
      return curr

## 4.7 Build Order
**Q**: given a list of projects and a list of dependencies (list of = [project A, prokect B] where B depends on A). output the build order of projects such that projects that are depended on other projects must be built before

### Iterative Graph no Children check
**t: O(n^2) s: O(n)**
- first we great a directed graph where a connnection from a -> b represents that a depends on b
- we do a while until the build length === node length
  - in the loop we iterate over all the nodes, all nodes that have zero children (means no project depends on it), we remove it from the list of nodes and connections
  - if during this loop we dont remove any nodes, we throw an error saying the build order is impossible since we will be stuck in a loop

</a>

    const getBuildOrder= graph => {
      let build =[]
      let nodes = new Set(graph.nodes)
      while (build.length < graph.nodes.length) {

        let removed = false
        // go through all nodes
        for(const node of nodes) {

          // update children to only have the nodes
          node.children = node.children.filter(c => nodes.has(c))

          // if we have no income that means this depends on nothing, add to build
          if (node.children.length === 0) {
            build.push(node.name)
            nodes.delete(node)
            
            // flag we did something
            removed = true
          } 
        }
        // if we went through all the nodes and didnt find any
        if (!removed) 
          throw new Error("Build order doenst exist")
      } 
      return build
    }

## 4.8 Common Ancestor
**Q**: given a binary tree and two nodes in it, find the closest common ancestor node that these two nodes share

### Array Binary search
**t: O(n^2) s: O(1)**
- start at the root
- search left and search right
  - if one is found in the left and one is found on the right: return this node
- else go to left if both in left
- else go to right if both in right
- if our current node is either one of the two nodes that means that one node is a parent of the other, so we return the parent of the current node we are on

## 4.9 BST Sequences
**Q**: Given a BST that was formed by reading an array left to right, print all array combinations that would result in that binary tree

### ???????

## 4.10 Check subtree
**Q**: Given two binary trees b1 and b2 where b1 and b2 are very large and b1 is much larger than b2, check if b2 is a subtree of b1

### Recursive check
**t: O(a^2+b^2) s: O(1) where a = size of b1 and b= size of b2** 
- iterate through each node of b1 and try to see if it matches b2

### recursive Hash ending comparision
**t: O(log(aH-bH)) s:(2^a) where a = size of b1, aH=depth of b1, bG = depth of b2**
- iterate through b1, at each node create a hash string for the subtree. we can do this recursively where the format will be:
  - hash(left) + 'curr.val'+hash(right)   // NOTE we use '' to make sure 10 and 1,0 are different
  - When we make a hash also keep track of the depth for that hash
- Go through the b2 subtree and calculate the hash for the whole tree
- Traverse b1 from the root, if the current node's hash ends with the hash of b2
  - traverse down
  - else ignore
- Traverse until we find it OR we reach a depth thats greater than b2.hash.depth

# 4.11 Random Node
**Q**: Design a binary search tree such that there is a method called: getRandomNode() that returns an equally weighted random chance to return any node

### Additional check
**t: O(1) s: O(n)**
- when adding/deleting nodes them just store in a set
- when we want to get a random node just do nodes[parseInt(Math.random() * nodes.length)] and return 

### Weight
**t: O(1) s: O(n)**
- when we add/delete nodes, recursively bubble up the correct number of nodes the parent has
- When we want to get a random node we will start at the root and do a weighted random check between left and right and middle depending how many nodes exist
- for example: if we have left= 15 nodes and right=7 nodes we therefore have this weight average:
  - pick left = 15/(15+1+7) = 15/23
  - pick center = 1/23
  - pick right = 7/23
- If we pick a node that has left or right than we must try the check again

</a>


    const getRandom = (node) => {
      // arrry containing [weight,node to return]
      // use defaults in case left or right doesnt exists
      let probs = [[(node.left || {}).count || 0,node.left], [(node.right ||{}).count || 0,node.count]]

      // aggregate the counts (start at 1 cus we include the center)
      let count = probs.reduce((pre,curr) => pre += curr[0], 1)

      // get the random
      let random = parseInt(Math.random() * count)

      // base case we hit the center
      if (random <= 1) return node

      let prob = 0

      // fancy way to check probability while keeping the weight of the previous
      for (const [weight,n] in probs) {
        prob += weight
        // if we hit this,return the prob for this tree
        if(random <= prob)
          return getRandom(n) 
      }
    }


# 4.12 Paths with Sums
**Q**: return how many paths in a binary tree exist where the values sumed equal a number. it need not start or end at a root

### Staggered Window
**t: O(n^2) s: O(1)**
- Start at root, traverse each branch recursively adding the branches
- then go down one node at a time doing the same thing
- for both of these operations if our sum is equal to the number than add to the path but keep going (since we have negatives we must check if further it still worths)
- NOTE: We can probably use some DP to save on comp time since instead of contiually checking the same subtree values, we can just keep it stored ina  lookup array and use that

</a>

    const getPathsAtRoot = (root, targetValue)  => {

      // if we reached the end return 0
      if (root == null) return 0

      let paths  =0

      // try here
      paths += getPaths(root, targetValue)

      // move the start point of tree to left and right
      paths += getPathsAtRoot(root.left,targetValue)
      paths += getPathsAtRoot(root.right, targetValue)

      return paths
    }

    const getPaths = (node,target) => {
      if (!node) return 0
      let paths = 0
      target -= node

      // if the target is reached add a path, but keep going since negatives exist
      if (target === 0)
        paths++
      
      // get paths for branches
      paths += getPaths(node.left,target)
      paths += getPaths(node.right,target)
      return paths
    }