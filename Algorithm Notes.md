# Algorithms

## Chapter 5 - Bit Manipulation

### Bit Tricks
- x ^ 0s = x
- x ^ 1s = x
- x ^ x = 0s
- x & 0s = 0s
- x & 1s = x
- x & x= x
- x | 0s = x
- x | 1s = 1s
- x | x = x

### 2s compliment and Negative Numbers
- negative number =>  (~positivenumber + 1) prefixed with 1 bit
- ex: 7 => 0[111],  -7 => 1[000 + 001] => 1[001]

### Right Shifts
**Arithmetic >>**
- Shifts all bits to the right, and putting sign bits in the most significant bits
  - this will roughly divide by two
- ex: 1[101] >> 1 => 1[110]
- ex: 1[101] >> 2 => 1[111]

**Logical >>>**
- Shifts all bits to the right, and putting 0s in most significant bits
- ex: 111 >>> 1 => 011
- ex: 111 >>> 2 => 001

### Common Bit Methods
**Get Bit**

    // 1 << i => 0000...1....0000 where 1 is in the ith bit
    // & will remove all other bits except the first one
    // if the value is all 0s that means our bit was 0, else it was 1
    return num & (1 << i) != 0

**Set Bit**

NOTE: this means to set it to 1 lol

    // 1 << i => 0000...1....0000 where 1 is in the ith bit
    // | will make it so that all other values dont matter except for the ith bit
    // this guarentess that the ith bit gets set to 1 because x | 1 = 1
    return num | (1 << i)

**Clear Bit**

    // ~(1 << i) => 11111...0....1111 where 0 is in the ith bit
    // and will set the the ith bit to 0 because x & 0 = 0
    return num & ~(1 << i) 

**Clear all signficant bits till i (inclusive)**

    // 1 << i => 0000...1....0000 where 1 is in the ith bit
    // subtracting 1 will give us [...00000][111111....] where the last 0 is the ith bit
    // & the mask will set all values left of and including ith bit to 0
    let mask = (1 << i) -1
    return num & mask

**Clear all bits after i (inclusive)**

    // -1 = ...11111...
    // -1 << (i+1) == ...11110000   where there are i+1 0s (i+1 because we want inclusive)
    // & the mask will clear all the bits from i to the first bit
    let mask = (-1 << (i+1))
    return num & mask

**Update Bit**

    let bitVal = value ? 1 : 0

    // 1111...0....1111 where 0 is at the ith bit
    let mask = ~(1 << i)

    // num & mask will clear the bit
    // 1 << bitVal => 0000...[1/0]...0 where 1/0 is at the ith bit
    // | the two will essentially take the num with the ith bit cleared and set it to the the value bitshifted in (1 << bitVal)
    return (num & mask) | (1 << bitVal)

## Chapter 6 - Math and Logic Puzzles

## Chapter 7 - Object Oriented Design

- ask tons of questions about the problem
  - who what where when why
- figure out core objects, think business domain
- think about relationships between core objects
- think about actions within the system and how it affects each core object

### Singleton
  - a class that only has one instance
  - usually used for global static class 

### Factory Method
  - essentially creates an instance of a class, used ina centralized place that possibly needs to create different types of classes
  - 

## Chapter 8 - Recurssion and Dynamic Programming

## Chapter 9 - System Design and Scalability

### Handling the Questions
- communicate
- start broad
- convey your thoughts even if you are figuring it out
- acknowledge interview concerns
- be careful with assumptions, state them explicitly
- estimate when necessary
- drive the design process
  
### Design: Step by Step

**Step 1: Scope the Problem**
- scope out the problem so that you understand what you are designing and the extent at which things operate

**Step 2: Make Reasonable Assumptions**
- be smart about the assumptions you need to make, talk to your interviewer

**Step 3: Draw the Major Components**
- Use the white board, draw everything
  
**Step 4: Identify the Key Issues**
- bottlenecks and major challenges?
- take interviewers guidance and use it
  
**Step 5: Redesign for the Key issues**
- update design to accomadate for the key issues
  
### Algorithms that Scale: Step by Step
- instead of large scale system design they may ask you to design a single feature or algorithm with the intention of scale

**Step 1: Ask Questions**
**Step 2: Make believe**
- design firstly without performance or memory constraints
- This will help guide you to the general outline for solution
**Step 3: Get Real**
- go back and address the constraints to make it work now
**Step 4: Solve Problems**
- Solve the issues you address in **Step 3**
- you might be able to build upon the solution defined in step 2 or have to scrap it entirely
- iterative design, will allow you to address new issues
- you arent expected to design facebook over 30 mins
  - its normal to create a solution with flaws, its good to point them out to show you are aware
  

### Key Concepts

### Horizontal vs Vertical Scaling
- vertical = add more resources (more machines, more memory etc)
    - usually easier
- horizontal = increase number of nodes, more servers

### Load Balancer
- distribute load of a work so that each server/component is being optimized and one isnt being throttled

### Database Denormalization or NoSQL
- sql joins are very slow as system grows larger
- denormalization: adding redundant info into a db to speed up reads.
  - ex: you have a project that has a list of tasks, adding something like the project name to the task table so you can get all the info you need from just the task table
- or just use NoSQL to avoid all joins ever hehe

### Database Partitioning (Sharding)
- splitting the data across multiple machines while ensuring where data is still
- many systems use many different kinds
**Vertical Partitioning**
  - partitioning by feature (ex: social media; one for messages, one for contacts etc)
**Key/Hash-Based Partitioning**
- use an id or key to parition the data
- ex: allocate N nervers and put the data on mod(key,n) 
- issue: fixing number of servers and if we were to add more it would be very expensive
**Directory-Based Partitioning**
- have a lookup table where your data is partioned
- problem: single point of failure
- problem: lookup table is under constant load (aka bottleneck)

### Caching
- caching frequent things to avoid heavy repeated computations

### Asynchronous Processing & Queues
- move slow operations off thread or async
- use pre-processing (load data even if not 100% correct as its better to prevent stalling or long loading screens)
- alert users when the operation is done in the background
  
### Networking Metrics
- **bandwidth**: theoretical max of data through
- **throughput**: actual data through
- **latency**: how logn it takes data to go from A -> B

### MapReduce
- a program to process large amounts of data
- uses a mapping function to transform data into a k,v pair
- reduce takes a key and a set of associated values and "reduces" them somehow, emits a new key

### Considerations
- Failures: plan for system failure
- Availablility: how often a system operates
- Reliability: how prone the system is to be available
- Read Heavy vs Write Heavy
- Security: whew
- 

## Chapter 10: Sorting and Searching

### Bubble Sort t: O(n^2) s: O(1)
- start at begining array and swap current and next if larger
- repeat throughout the array

### Selection Sort Sort t: O(n^2) s: O(1)
- find smallest, put it in front
- find next smalest, put in frontof that
- repeat

### Merge Sort t: O(nlog(n)) s: ?
- Merge sort pretty much has two big steps:
  - Parition
    - Recursively partion the array into two halfs split at middle (log(n) comes from here)
    - Stop if we can't parition any more
  - Merge
    - For both halves we merge them together
    - traverse each array in order and set the curr index to whichever arrays element is the min
    - We can do this at this point we can assume they are sorted (because the
    - at the end we have to add any remaining elements to the array from the lefthandside
      - The right hand side is already in the arry from before inplace
      - We can just add to the end (keep going from our current) and still remaining the sort order because they are sorted and we already inserted all min elements from the right handside at this point (or the loop would not be done and the right hand side wouldnt be inplace [which it IS])
  </a>


    const MergeSort=  (arr) => {
      
      let helper = new Array(arr.length)

      const merge = (low,middle,high) => {

        for (let i=low;i<=high;i++)
          helper[i] = arr[i]

        let helperLeft = low
        let helperRight = middle + 1
        let current= low

        // traverse arrays together and set the min to the arry
        while (helperLeft <= middle && helperRight <= high){

          if (helper[helperLeft] <= helper[helperRight]) {
            arr[current] = helper[helperLeft]
            helperLeft++
          } else {
            arr[current] = helper[helperRight] 
            helperRight++
          }
          current++
        }

        // copy the remaining already sorted left (dont need to worry since it must be the largest values)
        // NOTE: we only have to copy the left since the right is already in place of the array
        // NOTE: this happens because the left hand side will always finish first always (becasuse we middle+1)
        let remaining = middle - helperLeft
        for (let i = 0;i<=remaining;i++) {
          arr[current + i] = helper[helperLeft + i]
        }
      }
      const mergeSort = (start,end) => { 

        // can't parition anymore (m == end)
        if (start < end) {

          // split in half recursively
          let m  = parseInt((start+end)/2) 
          mergeSort(start,m)
          mergeSort(m+1,end)
          merge(start,m,end)
        }
      }

      mergeSort(0,arr.length-1)
    }

### Quick Sort
**t: best:O(nlog(n)) worst: O(n^2) s: O(nlog(n))**
- quick sort is built upon this premise that we are going to guess a place we think a median exists in an array and than continually swap values such that all values to the left or smaller and all values to the right are larger
- When we do this we are actually finding the real partition point where this happens with a leftmost pointer
- When we partition the array we are left with two halves. we then recursively apply the quicksort algo to them if they arent zero length
- NOTE

</a>

    const QuickSort = arr => {
      
      const qs = (left,right) => {
        let p = partition(left,right)

        // check to make sure our parition isnt at the bounds, if not sort them
        if (left < p -1)
          qs(left,p-1)
        if (p < right)
          qs(p,right)
      }

      const partition = (left,right) => {

        // pick middle because its a good guess (we can actually pick anywhere between left and right, but its proven that the middle is the best)
        let pivot = arr[parseInt((left+right)/2)]
        while (left<= right) {

          // we try to find the first value on the left that should on the right
          while (arr[left] < pivot) left++

          // we try to find the first value on the right that should be on the left
          while (arr[right] > pivot) right--

          // swap if its valid
          if (left <= right) {
            swap(left,right)
            left++
            right--
          }
        }
        // left is our new partition point because everything before it is <= and everything after is great
        // this was done by contiually swapping
        return left
      }
      const swap = (i,j) => {
        let temp  =arr[i]
        arr[i] = arr[j]
        arr[j] = temp
      }
      qs(0,arr.length-1)
    }

### Binary Search
**t: O(log(n)) s: O(1)**
- only works on sorted arrays
- pick middle point in array, if value is larger than our target that means we have to search to the left, if not search right, if equal return that index
- repeat until you find it OR you search windows close in on themselves (no more indices to search)

</a>

    const BinarySearch = (arr,target)=>{

        let start = 0;
        let end = arr.length-1
        
        // if we cross it means we haven't found it (happens at m-1 or m+1)
        while (start <=end) {

          // set middle
          let m = parseInt((start+end)/2)
          
          // we found it
          if (arr[m] === target) return m
          else if (arr[m] > target) {
            // search between [start,m)
            end = m - 1
          } else
            // search between (m,end)
            start = m + 1
        }
        return -1

    }
