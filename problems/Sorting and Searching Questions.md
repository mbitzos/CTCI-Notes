## 10.1 Sorted Merge
**Q**: Given two sorted arrays A,B. B contains enough space at the end to have enough space for A, merge A into B and sort it

### Two Pointer Tails
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

## 10.3 Search in Rotated Array
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
        
    class Listy {
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

### Find first suitable end index
**t: O(log(n)) s:O(1)**
- similar to above BUT optimized to not have to find the exact lenght
- While we are finding the length, we can actually stop not just when we find the length but when we find a value greater than our value and just use our current index as the length
- Also instead of having to find the exact length like i did below, we can just go until we find a value thats -1, and than what we do is search the later half of that array (we can discard the first half because from our prevous index/2 iteration we knew the value was not larger (or it would have stopped)). then we modify our binary search to also check if our midpoint === -1 then go right (too far) 

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
**Q**: You are given a 20gb file that just has one string per line, how would you sort this?

### External Sort 
- Just divide up the file x mb each where x is the amount of memory we have. sort each chunk and store in disk (not memory). go back and traverse each chunk

## 10.7.1 Missing Int
**Q**: Given an input file with 4 billion non negative integers. Write an algorithm that will generate an integer than is not contained in the file. Assume you only have 1gb of memory available for the task


### Bit Vector 
**t: O(n) s: O(n) bits**
- Go through entire array of numbers, mark down each number in a bit vector so that the ith bit tells if the ith number has been used
- We can then go through this bit vector and just find the first 0 and return the number at i
- NOTE: We would do this with an array but since arrays have overhead and also holds bytes we would be 4X the space of a bit vector and go over the 1byte limit, where as now we only get to 4 billion bits which is 0.5gb
  

## 10.7.2 Missing Int
**Q**: Now assume you only have 10MB of memory, the file contains more no more than 1 billion unqiue non negative integers

### Blocks
- Over the entire range of positive numbers ( 2^32-1) we can divide that range into blocks of size X
- What we can do now is keep track of how many numbers are in each block 
- Our goal is to find the first block that does not have X numbers recorded in it (this assumes that there is a number in that range that was not found, thats our answer)
- then we can search over that block range in the input array and do the same technique found as above (with a bit vector)
- There are 4 steps:
  1. Find Block Size X:
       - X is found with the following calculation
       - arraySize = 2^31/blockSize <= 2^21
         - (and int is 4 bytes, this is how many elements we can fit with 10MB)
       - blockSize>= 2^31/2^21
       - blockSize>= 2^10
       - the upper limit to our range size is found by how large our bit vector  
         - 10mb  => 2^23 bytes => 2 ^26 bits
       - therefore: 2^10 <= blockSize <= 2^26
  2. Keep an array where ith element = the number of numbers we found in the ith block
       - blockSize will always be larger than this array so we dont have to worry about memory restrictions for this (blockSize >= blocks)
       - We can do this easily by just doing blocks[Math.floor(i/blockSize)]++
  3. Loop through each index in blocks and determine when the number is < blockSize
  4. Once we find the block we can construct a bitvector that is blockSize long and perform the same algorithm in 10.7.1

## 10.8 Find Duplicates
**Q**: You have an array with all numbers from 1 -> N where you dont know N and at most 32 000. The array may have duplicates, write an algo that prints all duplicates with only 4kb of memory

### Bit Vector
Go through the array and mark the ith bit in a bitvector if we come across the ith number
If the bit is already 1, then print


## 10.9 Sorted Matrix Search
**Q**: Given an N x M matrix, in which each row and each column is sorted in ascending order, write algo to find an element

### Binary Search
**O(log(m) *log(n)) s:(log(m) + log(n))** 
- This so too complicated to write the code out for lol
- but essentially you are use these two facts for the basis of the search:
  - at a[i,j] all values to the left and up are smaller or equal
  - at a[i,j] all values to the right and down and larger or equal
- So we can form a binary search from this where we will check the middle of a matrix quadrant
- have four tracking variables at each iteration of the recurssion
  - **top, left** => top left bounds
  - **bottom right** => bottom right bounds
- We can than track the two middle points (might end up being the same)
  - **middle1** => [floor((top+bottom)/2), floor((left+right)/2)]
  - **middle2** => [ceil((top+bottom)/2), ceil((left+right)/2)]
- For middle 1 we check:
  - if **middle >= target** we will traverse the quadrant defined between **a[top,left] and middle1**
- for middle 2 we check:
  - if **middle <= target** we will traverse the quadrant defined between **middle and a[bottom,right]**
- if neither of those return true we have to check the other two quadrants and perform the same search
  - **bottom left quadrant** defined by: **a[left,middle1[1]]** and **a[middle1[0],bottom]**
  - **top right quadrant** defined by: **a[middle2[0],top**] and **a[right,middle2[1]]**
- The base case is if our middle points go **out of our defined bounds** by top,left,right,bottom
- or if one of the **middles equals our target**

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
Peak Swapper
**t: O(n) s:O(1)**
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

### Sorted and swap
**t: O(nlog(n)) s: O(n)**
- Sort array first O(nlogn)
- then go through the array and then swap the ith element with the i+1th element, increment by 2 O(n)

### Peak Swapper
**t: O(n) s:O(1)**
- a peak is formed when it is surrounded by two values smaller than it
- The way we make a peak is by putting the largest value in the middle
  - ex: 1,2,3 => 1,3,2 or 1,0,2 => 1,2,0
- All we have to do now is just traverse the array two at a time (peak happens every other), and swap the number with the largest adjacent if its larger than itself

</a>

    const getLargestIndex = (arr,i) => {
      let left = i -1
      let right = i === arr.length-1 ? null : i+1

      let index

      // left or right
      if (right == null || arr[left] > arr[right])
        index = left
      else
        index = right
      
      return  arr[i] < arr[index] ? index : i
    }

    const swap = (arr,i,j) => {
      let temp = arr[i] 
      arr[i] = arr[j]
      arr[j] = temp
    }

    const PeakSort = arr => {

      for (let i=1;i<arr.length;i+=2) {
        
        let maxIndex = getLargestIndex(arr,i)
        if (maxIndex !== i) 
          swap(arr,i,maxIndex) 
      } 

    
    } 