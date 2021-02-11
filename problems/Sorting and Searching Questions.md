## 10.1 Sorted Merge
**Q**: Given two sorted arrays A,B. B contains enough space at the end to have enough space for A, merge A into B and sort it

### Two Point Tails
**t: O(n) s: O(1)**
- Have a pointer at the end of B (B.length-1)
- Have two pointers on each array at the end, for B have it placed at the true length of B (we can do a while loop to find it from the back)
- NOTE: we must do at the end if we want to do inplace changes to B
  - The reason for this is because we can start writing elements to the back without worrying about overrites (the first pointer will never be ahead of the two pointers)

## 10.2 Group Anagrams

#### Sorted  Sorts
**t: O(n^2log(n)) s: O(n)**
- create copy of the array
- sort each string in the array (n*nlog(n)) and store in the copy
- sort the copied (sorted string) array. any changes you make, make to master list

## 10.3 Search in Sorted Array
**Q**: Given a sorted array that has been rotated an unknown number of times, return the target val index in the array. 
  - [1,2,3,4,5] -> [4,5,1,2,3] t = 2 ans = 3

### Two Step Binary Search
**t: O(log(n)) s: O(1)**
- Use a modified version of binary search to find the minimum value in the array, this is the index of the smallest number but also the number of rotations the array has undergone
- Then just do normal binary search such and use the rotation number with the % operation to find where we really are looking at, move window normally
	
</a>

    const search = (nums, target) => {
        
        
        let end = nums.length -1
        let start = 0
        
        // find the rotation
        while (start < end) {
            let m = parseInt((start+end)/2)
            // go right, it contains pivot
            if (nums[end] <= nums[m]) {
                start = m+1
            } else
                end = m
        }
        // the rotation point is the smallest number
        // this is found in the above loop
        let rotation = start  
        
        start = 0
        end = nums.length-1
        
        // use normal binary search, but take in consideration our real middle
        // the reason we can still set start/end to m is because we really just using our start/end as a moving window size
        // our realmid var is the one that really matters here
        while (start <= end) {
            let m = parseInt((start+end)/2)
            let realmid = (m+rotation) % nums.length    // wrap it around
            
            // we found it
            if (nums[realmid]== target) return realmid
            else if (nums[realmid] < target ) start = m + 1 // go right
            else end = m - 1    // go left
        }
        return -1
    }

## 10.4 Sorted Search, No size
**Q**: Given an array like datastructure that does not allow you to see the size, but allows you to get elementAT(i) at O(n) time,if out of bounds will return -1, if given this structure that contains sorted list of positive number of integers, return the index of the element we want to find

### Find Bounds 
**t: O(log(n)) s: O(1)**
- find the length of the array
  - we will do this by having an "incrementer" variable that adds to our current guess of the length
  - in a while loop we loop until the arr.at(length) == -1 and arr.at(length-1) !== -1 (aka. we found our length)
    - inside we add double our incrementor everytime (this should be O(log(n)))
    - we can also do a check to see if we already reached our target here by chance
- Then we just do a normal binary search with our length found
  
</a>
        
    class Linky {
      constructor(nums) {
        this.nums =nums
      }
      at(i) {
        return i >= this.nums.length ? -1 : this.nums[i]
      }
    }

    const unknownBinarySearch = (list,target) => {

      let length = 1, inc = 1
      while (!(list.at(length) === -1 && list.at(length-1) !== -1)) {
        inc *=2
        // why not just check lol
        if (list.at(length) === target) return length
        if (list.at(length + inc) == -1)
          inc = 1
        length += inc
      }

      let left= 0
      let right = length-1
      while (left<=right) {
        let m = parseInt((left+right)/2)
        if (list.at(m) === target) return m
        else if (list.at(m) > target) right = m -1
        else left =m + 1
      }
      return -1
    }

## 10.5 Sparse Search
**Q**: Given an a sorted array of strings that contain many empty strings interspersed, return element of the string we want to find 

### Find Valid Middle 
**t: O(n) s: O(1)**
- do normal binary search but when we are get a middle do the following checks:
  - if equal to target return middle
  - if middle === ''
    - search left and right between our original binary search window inclusively [start,end]
    - use that middle, try again
  - if middle < target
    - go right
  - else
    - go left
- The worst case scenario is when we have something like this:
  - [target,"","",......,""]
  - This will require us to linearly scan for a correct middle from halfway until we find it

</a>

    const binaryStringSearch = (arr, target) => {
      let left = 0;
      let right = arr.length - 1;
      let m = parseInt((left + right) / 2);
      let i = 0;
      while (left <= right && i++ < 100) {
        let comp = arr[m].localeCompare(target);

        // if we found it return
        if (comp === 0) return m;
        else if (arr[m] === "") {
          // if our middle is useless, search to find a useful one
          let leftM = m - 1;
          let rightM = m + 1;
          let previous = m;

          // go until we find a non empty string
          while (leftM >= left || rightM <= right) {
            // only assign if this is still in bounds
            if (leftM >= left && arr[leftM] !== "") {
              m = leftM;
              break;
            }

            // only assign if this is still in bounds
            if (rightM <= right && arr[rightM] !== "") {
              m = rightM;
              break;
            }
            leftM--;
            rightM++;
          }
          // we could not find anything (not sure how)
          if (m === previous) return -1;
        } else if (comp === -1) {
          // go right, we are smaller
          left = m + 1;
          m = parseInt((left + right) / 2);
        } else {
          // go left, we are larger
          right = m - 1;
          m = parseInt((left + right) / 2);
        }
      }
      return -1;
    }

## 10.6 Sort Big File
**Q**: You are given a 19gb file that just has one string per line, how would you sort this?

### Find 
**t: O(n) s: O(1)**

## 10.7 Missing Int
**Q**: Given an input file with 4 billion non negative integers. Write an algorithm that will generate an integer than is not contained in the file. Assume you only have 1gb of memory available for the task


### Bit Vector 
**t: O(n) s: O(n) bits**
- Go through entire array of numbers, mark down each number in a bit vector so that the ith bit tells if the ith number has been used
- We can then go through this bit vector and just find the first 0 and return the number at i
- NOTE: We would do this with an array but since arrays have overhead and also holds bytes we would be 4X the space of a bit vector and go over the 1byte limit, where as now we only get to 4 billion bits which is 0.5gb
  

## 10.7.2 Missing Int
**Q**: Now assume you only have 10MB of memory, the file contains more no more than 1 billion unqiue non negative integers

### Partitioned sorted arrays
Perform a modification of merge sort only sorted segements of the array
Then go through each partition with a pointer on each one and try to find a number that doesnt exist

## 10.8 Find Duplicates
**Q**: You have an array with all numbers from 1 -> N where you dont know N and at most 32 000. The array may have duplicates, write an algo that prints all duplicates with only 4kb of memory

### Bit Vector
Go through the array and mark the ith bit in a bitvector if we come across the ith number
If the bit is already 1, then print


## 10.9 Sorted Matrix Search
**Q**: Given an M x M matrix, in which each row and each column is sorted in ascending order, write algo to find an element

### ???

### 10.10 Rank from Stream
**Q**: You are reading integers into a stream. Implement a datastructure that has two methods:
track (x) that is called when you read in an integer
getRankOfNumber(x) which returns how many numbers are less than or equal (not including the instance of x itself)

### BST with counter
**track: t: O(log(n)) s: O(n)**
**getRankOf: t: O(log(n)) s: O(n)**
- Create a binary search tree that when we insert (in track(x)) keep track of how many right and left children we have (by incrementing whenever we go left/right on that node)
- track (x) => log(n)
- When we want to get the rank of a number we just do a binary search, while we are traversing we keep a track of children: if we go right, add all leftChildren of our current node. At the end if we found it, we return the counter of children + our current nodes.leftchildren

</a>

class BSTNode {
  constructor(val) {
    this.left = null
    this.right = null
    this.val = val
    this.leftChildren =0 
    this.rightChildren =0
  }
}

class BSTTracker {
  constructor() {
    let root = null
  }
  insert(x) {
    let node = new BSTNode(x)
    if (this.root == null)
      this.root = node
    else {
      let curr = this.root

      // will never get to end
      while (curr !== node) {
        
        if (x <= curr.val) {
          curr.leftChildren++
          // add to th left if not there
          if (!curr.left) {
            curr.left =node
          }

          // traverse
          curr = curr.left
        } else {

          curr.rightChildren++
          // add to right if not there
          if (!curr.right) {
            curr.right = node 
          }

          // traverse
          curr =curr.right
        }
      }
    }
  }

  getRankOfNumber(x) {
    let curr = this.root
    let children  =0
    while (curr != null && curr.val !== x) {
      if (x <= curr.val) {
        curr = curr.left
      } else {
        // if we are going rightt add all the children to our right (they are all smaller)
        children+= (curr.leftChildren + 1) // plus one because are counting the current node
        curr = curr.right
      }
    }
    // if we found the value return the # of left children and the previous ones
    return curr ? (children + curr.leftChildren) : 0
  }
}

### 10.11 Peaks and Valley
**Q**: You are reading integers into a stream. Implement a datastructure that has two methods:
track (x) that is called when you read in an integer
getRankOfNumber(x) which returns how many numbers are less than or equal (not including the instance of x itself)

### Peaks and Valleys
**t: O(n) s: O(n)**
- Create copy of the array so we can have reference to non swapped
- Create a lookup from val -> index in array
- have two pointers (left and right) that side on either end of array
- we are essentially traversing the array from left to right and creating this arry:
  - 1,n,2,n-2,3,n-3.....
- to do so we are swapping the number we want (stored in the lookup) with the value that sits in our current spot we want
- alternate between putting n-i, and i
- Keep doing this until both pointers reach eachother

</a>

    const peakSort = (arr) => {

      let start = 0
      let end = 0
      let lookup = {}

      // create a lookup from val where it actually sits
      arr.forEach((val,i) => {
        lookup[val] = i
      }) 
      
      let copy = [...arr]

      // puts val into i
      // does this by looking up where val is currently, then swapping it with the number that
      // sits there, updates the lookup table
      const insertAt = (val,i) => {
        let valIndex = lookup[val]
        let valAtI = arr[i]

        // swap
        arr[i] = val
        arr[valIndex] = valAtI

        // update index
        lookup[valAtI] = valIndex
        lookup[val] = i
      }
      
      while (start<=(arr.length-1-end)) {
        let curr = start +end

        if (start <= end) {
          // do start now
          insertAt(copy[start], curr)
          start++
        } else {
          // do end
          insertAt(copy[arr.length - 1 -end], curr)
          end++
        }
      }
    }
 