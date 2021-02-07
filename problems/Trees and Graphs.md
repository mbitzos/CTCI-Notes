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
**t: O(n) s: O(log(n)) where H = height of tree**
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
**t: O(n) s: O(log(n))**
- we recursively go down the tree verifying if: left.val <= node.val < right.val
- NOTE: We also have to pass in the trees min/value value because we need to make sure that numbers that larger/smaller than their parent, arent larger or smaller than the parents parent.
- ex:   20
  -   /    \                    
    10     30    is wrong, 25 > 10 which is good but > the max = 20 
      \ 
        25
  - so we just have to verify if:

        // continue recursively or bubble back false, return true at end of algo
        if (left.val < parent.val && left.val < min) // good
        else if (right.val > parent.val && right.val > max) // good
        return false
      
        traverseLeft(node.left, max = node.val, min =min)
        traverseRight(node.right, max= max, min = node.val)
  
  - NOTE: we can set the min to infinity and max to -infinity when we are dealing with edges that dont need to check the second part of the condition


## 4.6 Successor
**Q**: Write an algo that returns the next successor of a node in the binary search tree next in-order(left->current -> right)

### Farthest down left child on the right
**t: O(n) s: O(log(n))**
- To traverse an in order BST we need to go from left -> center -> right
- 1) So at a node, to find the next node we simply go to our right subtree and go furthest left in there
- Now what happens if we dont have a right subtree:
  - If we came on the left of our parent that means we now have to check our parent (recursively traverse up to our parent go to 1))
  - If we came on the right our parentt that means fully traversed the parents subtree so we have to traverse back up to the parent's parent
  - if we reached the very end of our traversal, and nothing yet that means we are at the very end and thus we need to return null

</a>

    inOrderSuccessor(node) {

      // null case
      if (!node) return null

      if (node.right)
        // far left
        node =node.right
        while (node != null)
            node =node.left
        return node
      else
          // keep going up until we find a parent where we are not coming from the right
          let q = node
          let x = q.parent
          while (x!= null && x.left != q) {
            q = x
            x = x.parent
          }
          return x

    }

## 4.7 Build Order
**Q**: given a list of projects and a list of dependencies (list of = [project A, prokect B] where B depends on A). output the build order of projects such that projects that are depended on other projects must be built before

### Iterative Graph no Children check
**t: O(P+D) s: O(P) where P = # of projects and D= # of build dependencies**
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

### Traversal without links to parent
**t: O(n) s: O(log(n))**
- start at the root
- search left and search right
  - if one is found in the left and one is found on the right: return this node
- else go to left if both in left
- else go to right if both in right
- if our current node is either one of the two nodes that means that one node is a parent of the other, so we return the parent of the current node we are on
- OPTIMIZATION: We can actually optimize this so that we dont have to recalculate the subtree coverage at each step, we can bubble up the response at each stage and determine it there while we recursively go into  it. Our new method covers(n, a,b) -> n=>node to check, a,b => nodes we're finding the common ancestor for. our returns will be:
  - a if the subtree includes only a and not b
  - b if the substree includes only b and not a
  - null if in neither
  - return commmon ancestor
    - the common ancestor is return when we find that our call to covers(left) and covers(right) returns different non-null value (a and b are at differen subtrees at this specific point)
  - NOTE: we have to build a custom Result object to specific distinguish when the node doesnt exist. We can ofc just search for a and b at the start to make sure they exist as it wont affect run time (one time calculation)

### With links to parent
**t: O(d) s:O(1) where d = depth**
- we just move up each node to their parents and check when they collide
- To ensure one doesnt pass the other, we calculate the depth at the start and move one to match the other, than from their we can move our parents up one at a time and check when collision happens
  
</a>

    commonAncestor = (a, b) => {
      let depthA = getDepth(a)
      let depthB = getDepth(b)
      if (depthA < depthB)
        a= moveUpN(a, depthB - depthA)
      else if (depthB < depthA)
        b= moveUpN(b, depthA - depthB)
      
      while (a !== b && a !== null && b !== null) {
        a = a.parent
        b= b.parent
      }
      return a == null || b== null ? null : a
    }


    // moves a node up depth amount
    moveUpN(n, depth) {
      while (depth >0 && n != null)
        n = n.parent
        depth--
       return n  
    }

    // gets the depth of a node in a tree
    getDepth =(a) => {
      let depth = 0
      while (a!= null) {
        a = a.parent
        depth++
      }
      return depth
    }

## 4.9 BST Sequences
**Q**: Given a BST that was formed by reading an array left to right, print all array combinations that would result in that binary tree

### Recursive Weaving
Since the only assumption about this is when we insert into a BST the children must inserted after its root. And the order of which they get inserted left or right does not matter and does not matter between other subtrees. So essentially we need to find all comboinations of how to insert into current subtree and weave it with its sibling, then recursively do that for every subtree.
<br> **Example weaving:**
  - arr1: {1,2}
  - arr2: {3,4}
  - weave: {1,2,3,4},{1,3,2,4},{1,3,4,2},{3,4,1,2},{3,1,4,2},{3,1,2,4}
    - We are recursevly finding all weaves of {2} + {3,4} prefixed with 1, and also all weaves of {1,2} + {4} prefixed with 3. So we can see how this is a recursive problem
    - Pseudo Code:
      - push prefixed elements down the recurssion, when {first} or {second} is empty, add {first} {second} to prefix and store in result
      - This requires us to remove elements from our {first} and {second}. we can use a linked list that makes it changes, then reverts them so that other recurssions dont have a modified linked list

**First Recurssion: Find all sequences of left and right**

    allSequences(node a) {
      // base case: return 
      if (a== null) return []
      let results = []
      let prefix = a.val
      let left = allSequences(a.left)
      let right = allSequences(a.right)
      for (l in left) {
        for (r in right) {
          // this happens in step 2, but jsut assume it works now to make more sense conceptually
          results.add(weaveList(l,r,prefix,[])) 
        }
      }
      return results
    }
**Second Recurssion: Weave all sequences**

    weaveAll(first, second, prefix, results) {

      // base case is we reach the end of either first or second
      if (first.length == 0 || second.length == 0) {
        // form the string
        return prefix.join() + first.join() + second.join()
      }

      // weave with the first being our prefix
      // we remove the first element of our list then add to the end of our prefix
      prefix.push(first.shift())
      weaveLists(first,second,prefix)
      first.insert(0,prefix.removeLast())  // reverse what we did so we dont alter list

      // weave with the second being our prefix
      // we remove the first element of our list then add to the end of our prefix
      prefix.push(second.shift())
      weaveLists(first,second,prefix)
      second.insert(0,prefix.removeLast())  // reverse what we did so we dont alter list
    }

    

**NOTE:** This problem is conceptually very difficult because we have two different cases of recurssion happening at the same time. BUT! if we just look at it that the two are independent (which they are) we can easily logic out what each should do. This means when we design the first recurssion we must TRUST that the second does what we expect it to do before we design it. So we can tackle the first recurssion without worrying about the weaving part. then we desigsn the weaving part independent of the original problem aka. just design a algorithm that weaves two arrays. dont worry about all the sequences that are being fed into it


## 4.10 Check subtree
**Q**: Given two binary trees b1 and b2 where b1 and b2 are very large and b1 is much larger than b2, check if b2 is a subtree of b1

### recursive Hash substring comparision
**t: O(n*m) s:(n+m) where n=size of t1, m= size of t2**
  - create a string representation of the traversal of each tree
  - We do this with pre-ordering so that the order of the trees are preserved
  - additionally we have to record the value of a missing child
    - so if we have a tree like this:

               1
            2/   \3
             \3    \4
    - The preorder hash will be: 12null334
  - then at the end we just check if t2 hash is a substring of t1 has (takes n*m :c )
  
### Recursive check (this actually is better)
**t: O(n + km) s: O(n+m) where n = size of t1, m = size of t2 and k= number of t2.root.val we have in t1
- iterate through each node in t1 and go down it checkinng against each node of t2
- if we find we dont match before the end cancel and return back to start of search ing t1
      

# 4.11 Random Node
**Q**: Design a binary search tree such that there is a method called: getRandomNode() that returns an equally weighted random chance to return any node

### Additional check
**t: O(n) s: O(n)**
- when adding/deleting nodes them just store in a set
- when we want to get a random node just do nodes[parseInt(Math.random() * nodes.length)] and return this takes O(n)

### Weight
**t: O(D) s: O(D) where D = depth**
- when we add/delete nodes, recursively bubble up the correct number of nodes the parent has
- When we want to get a random node we will start at the root and do a weighted random check between left and right and middle depending how many nodes exist
- for example: if we have left= 15 nodes and right=7 nodes we therefore have this weight average:
  - pick left = 15/(15+1+7) = 15/23
  - pick center = 1/23
  - pick right = 7/23
- If we pick a node that has left or right than we must try the check again
- OPTIMIZATION: dont keep generating random numbers, we can reuse the Math.random() call (doenst need to change)

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
**t: O(nlog(n)) s: O(log(n))**
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

### Traversal with path lookup [DP]
**t: O(n) s:O(log(n)**
- We will traverse down the tree once using DFS. We will use same concept of two-sum where we will store the current sum at that node with how many paths exist so far for that sum (this allows us to go through the tree once and gather all the paths that may exist within themselves since we have negatives)
- When we arrive at each node
  - increment the running sum by our current value
  - table[running_sum] = table[running_sum]++ (initialize to 1 if there is none)
  - then we take the difference between our current sum and the target value. and look that up in our table and initialize our "results" variable to that. if not exists set to 0
    - What this step does is collects all the number of paths down this branch that have resulted in the sum being zero: A good case of this happening is this: 
      - target = 8
      - 5 -> 3 -> 1 -> 2 -> -2
      - 5-> 3 -> 1 == 8 but also 5 -> 3 -> 1 -> 2 -> -2 == 8
  - Then what we do is go down the left and rights recursively and add the result to our main result
  - Once we return from those we have to:
    - table[running_sum] = table[running_sum]--
    - return the sum + left_sum +  right_sum (these are just the returns from going left/right)
  - We do this step because when we return we are assuming we are going down a different permutation of a path that does not include ourselve (recursively all children will be doing this too so essentially we are temporarily chaning the table during each recursion and then removing it. This could have be done with a copy of the table instead but its worst performance/space complexity)

</a>

    getPaths(graph,target) {
      return this.getPathsAtNode(graph.root, 0,target, {})
    }

    getPathsAtNode(root,currentsum, targetsum, lookup) {
      // base case
      if (!root) return 0

      currentsum += root.val
      lookup[currentsum] = (lookup[currentSum] || 0) + 1
      let res = lookup[currentsum-target] || 0

      // add 1 if we are at the target
      if (currentsum == target)
        res = 0

      // traverse left and right
      res += getPathsAtNode(root.left, curerntSum,target,{})
      res += getPathsAtNode(root.right, currentsum,target,{})

      lookup[currentSum] = lookup[currentSum] + 1

      // remove unecessary entries
      if (!lookup[currentSum])
        delete lookup[currentSum]

      return res
    }