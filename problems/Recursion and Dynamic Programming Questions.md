# Recursion and Dynamic Programming

## 8.1 Triple Step
**Q**: A child is running up a stair case with n steps, and can hop either 1,2,or 3 steps at a time. count how many possible ways the child can run up the stairs

### Basic Recursion
**t: O(3^n) s:O(n)**
- we are just doing a basic recurssion with three branches
- one step, two step and three step
- use DP to store number of ways to get to the end at a step i

</a>

    let result = []
    const stairs = n => {

      // made it to the end
      if (n >= 0) {
        // we went too far
        return 1
      } else if (result[n] != 0) {
        // check cache
        return result[n]
      }
      
      let count = stairs(n-1) + stairs(n-2) + stairs(n-3)

      // cache
      result[n] = count
      return count
    }

## 8.2 Robot in a Grid
**Q**: A robot sits in the top left element in a matrix, it can only move right and down. count how many paths the robot can take to get to the bottom right

### Basic Recursion
**t: O(rc) s:O(n)**
- start at 0,0
- create cache of matrix same size, set all to null
- base case:
  - we go out of bounds: return 0
  - we are at bottom right: return 1
  - we alraedy visited this in the cache, return cache[i,j]
- if no base case, go either right (i+1) or down (j+1)W
- cache it in the results array

</a>


    const CountPaths = (i,j,height,width) => {

        let cache = [width][heigth]

        const countPaths= (i,j) => {

          if (i >=width || j >= height) {
            // we went out of bounds
            return 0
          } else if (i == width -1 && j == height-1) {
            // we found the end, return success
            return 1  
          } else if (cache[i][j] != null){
            // its already in the cache
            return cache[i][j]
          }
          let count = 0

          // go right
          count += countPaths(i+1,j,width,height)

          // go down
          count += countPaths(i,j+1,width,height)

          // cache it
          cache[i][j] = count
          return count
        }

        return countPaths(0,0)
    }

## 8.3 Magic Index
**Q**: find the first index in an sorted array of distinct integers where A[i] = i 

### binary search?
**t: O(log(n)) s:O(1)**
- just perform a binary search but for our check instead of checking if A[i] == target, we can check if A[i] == i, if A[i] > i that means that a magic index can't exist from here and to the right, so go left. if A[i] < i that means the magic index can't exist from here and to the left, so go right

</a>


    const magicSearch = (arr) => {
        
        let start =0
        let end = arr.length -1
        while (start <= end) {
          let m = parseInt((start+end)/2)
          if (arr[m] === m) {
            return m
          } else if (arr[m] > m) { 
            end = m -1 
          } else {
            start = m + 1
          }
        }
        return -1
      }

## 8.3.2 Magic Index FOLLOWUP
**Q**: What if they aren't distinct

### binary Search
**t: O(log(n)) s:O(1)**
- since there are duplicates our check for left or right can't work
- to get around this we can search the left with this:
  - search(start, min(arr[mid],mid-1))  // search left to the first duplicate value
  - search(max(arr[mid],mid+1),end) // search right to the last duplicate value


## 8.4 Power Set
**Q**: Write a method to return all subsets of a set

### Bottom-up Prefixing
**t: O(n*2^n) s:O(n*2^n)**
- we are doing a bottom up solution where we start at the most basic subset: the last element alone
- then when we go up, we take that arry we add another subset that is [previousval, ....arr]
  - that is we prefix each subset with our current one
- then we also add our current
- ex:
  - [1,2,3] 
    - [[4]] => [[4],[3,4],[3]] => [[4],[3,4],[3],[2,4],[2,3,4],[2,3],[2]]

    const Subset = (arr) => {
      const subset = (start) => {
        if (start > arr.length - 1) return [];
        let sets = subset(start + 1);
    
        // prefix arr[start] to each one
        sets.forEach((s) => {
          sets.push([arr[start], ...s]);
        });
        
        // add singular
        sets.push([arr[start]]);
        return sets
      };
      return subset(0);
    };

## 8.5 Recursive Multiply
**Q**: write a recursive multiply function that multiplies two positive integers without using "*" operation

### Basic
**t: O(b) s:O(b) where b is the second number**

    const Multiply = (a,b) => {
      if (b == 0) return 0
      else
        return a + Multiply(a,b-1)
    }

### Half Operations
**t: O(log(s)) s:O(s) where s is the smaller number**
- we can actually do this a lot faster because instead of just doing multiplication, a better way is to find the productof half of the numbers than just double it. it allows us to do the multiplication by recurisively by trying half each time
- To divide in half we use the bit trick x >> 1 to do a  rough division
- since dividing an even number will yield a perfect double that we can do, we will run into problems with odd numbers
- What we can do though is that if we do this:
  - minProduct(31,35) = minProduct(15,35) * 2 + 35 ((15*35)*2 + 35 = ans)
  - 

</a>

  int MinProduct(a,b) {
    int bigger = a > b ? a : b
    int smaller= a < b ? a : b
    minProduct(a,b)
  }

  int minProduct(smaller,bigger) {
    if (smaller == 0) return 0 //(0X bigger  = 0) 
    else if (smaller == 1) return bigger //(1xbigger = bigger)
    else {
      int s = smaller >> 1
      int halfProduct = minProduct(s,bigger,cache)
      int ans = halfProduct + halfProduct

      // if we are an odd number we have to add one more since the >> 1 wont work with odds
      if (smaller % 2 == 1)
        ans += bigger
      return ans
    }
  }

## 8.6 Towers of Hanoi
**Q**: implement the tower of hanoi algorithm with three stacks and recurssion 
AKA just get all teh towers in descending size order from t1 to t3

### Basic Recurssion
**t: O(?) s:O(?) **
- this actually isnt as difficult if you break it down to what you can do in the base cases:
  - n=1: move disk to t3
  - n=2: move first disk to t2 as buffer, than move disk 2 to t3 and then d1 to t3
  - n=3: same concept but we do the same step as n=2 but use t3 as a buffer
- So you will notice its a simple recurssion problem where we use an alternating buffer to move disks onto it

</a>

    class Tower {
      constructor() {
        this.disks = [] //stack
      }

      add (d) {
        this.disks.push(d)
      }

      moveTopTo(t) {
        t.add(this.disks.pop())
      }

      moveDisks(quantity, destination, buffer) {
        if (quantity <= 0) return

        // alternate between using all three towers as a buffer
        // move from buffer to destination
        moveDisks(quantity-1, buffer, destination)

        // move all disks onto this one
        buffer.moveDisks(quantity -1, destination, this)
      }
    }

    // usage
    t1.moveDisks(n,t3,t2)


## 8.7 Permutations without Dups
**Q**: Write a method that computes all permutations of a string of unique characters without duplicates

### Back tracking Prefix
**t: O(n!) s:O(n)**
- just iterating through each character and we will use that as our new prefix,
- then take the rest of the string and apply the algorithm with new chracter prefix added to our previous prefix
- Our base case is if our string is empty, we push the prefix to our ans array

</a>

    const Permutations = (str) => {
      let ans = [];
      const perms = (prefix, str) => { 
        if (str.length === 0) {
          ans.push(prefix);
        } else {
          // go through each character make it prefix
          for (let i = 0; i < str.length; i++) {

            // get new string without our prefix character
            let newStr =  str.slice(0,i) + str.slice(i+1)

            // append
            let newPrefix = prefix + str[i]
            perms(newPrefix, newStr);  
          } 
        }
      };

      perms("", str);
      return ans;
    };

## 8.7.2 Permutations without Dups (not unqiue) 
**Q**: Write a method that computes all permutations of a string of  NON-UNIQUE characters without duplicates

### Back tracking Prefix
**t: O(min(nlog(n),n!)) s:O(n)**
- this is the same solution as before but we sort the string first and when we are iterating over the characters for our prefix, we skip until we find a new one (not equal to our last)

</a>

    const Permutations = (str) => {

      let ans = [];
      const perms = (prefix, str) => {
        if (str.length === 0) {
          ans.push(prefix);
        } else {
          // go through each character make it prefix 
          for (let i = 0; i < str.length; i++) {

            // keep going until we find a new one
            while (str[i] === str[i+1] && i < str.length) i++ 
            
            // get new string without our prefix character
            let newStr =  str.slice(0,i) + str.slice(i+1)

            // append
            let newPrefix = prefix + str[i]
            perms(newPrefix, newStr);  
          } 
        }
      };
      perms("", str);
      return ans;
    };

## 8.9 Parenthesis permutations
**Q**: Write a method that returns all valid permutations of n pairs parenthesis 
ex: n= 3 (()()), ()()(), (())(), ()(()), ((()))

### Parenthesis builder
**t: O(2^n) s:O(n)**
- recursively just building a string
- a valid parenthesis can either be (string) or string()
- either (string), ()string, or string()
- there are duplicates so we use a set... we can definitely find a better solution

</a>

    const Parenthesis = (n) => {

      let ans = new Set()
      const parenthesis = (n, string) => {
        if (n === 0) {
          ans.add(string)
          return 
        }else {
          n--
          parenthesis(n,'(' + string +')')
          parenthesis(n, string + '()') 
          parenthesis(n, '()' +string)  
        }
      }
      parenthesis(n,'', '')
      return ans
    }

### Parenthesis builder without duplicates
**t: O(2^n) s:O(n)**
- this solution is superior since we never run into duplicates and doesn't require a set to check for duplicates
- what we do is keep track of how many left and right parenthesis we have and the index of our current character

</a>

    const generateParenthesis= (n) => {

      let ans = []
      const count = (left,right,str) => {

        // invalid state
        if (left < 0 || right < left) return 

        // no more, add
        if (left === 0 && right === 0) ans.push(str)
        else {
          // open
          count(left-1, right, str + '(') 
          // close
          count(left,right-1, str+')')
        }
      }

      count(n,n,'')
      return ans
    }

## 8.10 Paint Fill
**Q**: Write method that will fill in an 2d array of colors with an area same way a fill tool will use

### recursive fill
**t: O(n^2) s:O(n^2)**
- start at a point, record which color it is, start at i,j coord
- at each step, color the current point the color
- go left,up,right,down with the recurssion
- base case:
  - or if out of bounds
  - if at a color that wasn't the original, stop (this will also work as flagging already visited notes) 

## 8.11 Coins
**Q**: Given an infinite amount of quaters, dimes, nickels and pennies. write a method that will caluclate all the number of ways of representing n cents

### Recursively subtracting the amounts
**t: O(4^n) s:O(4^n)**
- we simply do four branches with quaters, dimes, nickels, pennies
- We have to make sure though we dont count duplicates cases like this:
  - 10c => [dime], [nickel,nickel], [nickel,5pennies], [5pennies,nickels]!!, [10pennies]
- so we have to create a set with a hash (our formatted string)
- -NOTE: we could change the logic to instead find divisions of the amount instead of adding/substracting. this would greatly reduce the number of recursively calls since we would only have to check how many times a denomination goes into our current amount instead of recursively adding it everytime
  - -we would also want to utilize a cache to reduce the number of recursive calls we make

    const Coins = (n) => {
      let ans = new Set()
      const coins = (n, quaters,dimes,nickels,pennies) => {
        if (n < 0) return 0
        else if (n === 0) ans.add(`q${quaters}d${dimes}n${nickels}p${pennies}`) 
        coins(n-25, ++quaters, dimes,nickels,pennies)
        coins(n-10, quaters, ++dimes, nickels,pennies)
        coins(n-5, quaters, dimes, ++nickels,pennies)
        coins(n-1, quaters, dimes,nickels,++pennies)
      }

      coins(n,0,0,0,0)
      return ans.size
    }

## 8.12 Eight Queens
**Q**: On a 8x8 Board, write an algorithm that will print all ways to place 8 queens on a board such that they aren't on the same row,column or diagonal

### Bleh
**t: O(?) s:O(?)**
- this question is ... my brain hurts
- it makes sense but the book doesnt really explain i think how to print all combonations?
- the way it approaches it is if you attempt to place a queen in the first row on any spot, then move to the next row and check if the spot is valid, then place a queen there and move to next one and you keep going until you cover the whole grid
- Yuo can do this simply by storing an array of columns where we have place a queen (only need single dimensional array since they cant be in same row/column)
- loop from 0 to 8 (or grid size) and place a queen thats valid in a row position, you can check for validity by making sure its not in the same column or that the distance between the row and column is not the same (diagonal means x,y distance is the same)
- the base case is if you stop on the last row

## 8.13 Stack of Boxes
**Q**: Given a boxes with widths wi, heights hi, and depths di. Write an algorithm that returns the highest stack of boxes such that a box on top of a nother must be smaller than all of its dimensions

### Sorted Dimensions
**t: O(3^n) s:O(n)**
- Create a sorted list of the boxes such that they are sorted from largest box height at the start then smallest at the bottom
  - We sort this before hand because know that no box that has a larger size than its bottom can be added so we can only move forward in the list
- We then come to the choice in the recurssion where:
  - add the current box to our top
  - ignore it and move on
- These two choices define our recursive path because one box might limit the more boxes we can add on top since while our list defines the order at which boxes can be added, it doesn't mean that the next box in the list will cascade the adding and great larger heights since it might have stop the building in the other dimensions (width/depth instead)
- We can use a cache that stores the max height of the tower at a specific box index so that when we skip over a box we can just use that calculation instead of having to recalculate it again


</a>

    class Box {
      constructor(w,h,d) {
        this.w = w
        this.h = h
        this.d = d
      }

      /**
      * Is this box smaller than the provided one
      */
      smaller(box) {
        if (!box) return false
        return box.w > this.w && box.h > this.h && box.d > this.d 
      }
    }

    const MaxStack = (widths,heights,depths) =>  {
      let boxes= []

      // create boxes
      for (let i = 0;i<widths.length;i++)
        boxes.push(new Box(widths[i],heights[i],depths[i]))
      
      // sort on height
      boxes.sort((a,b) => b.h - a.h)
      let cache=  []
      const maxStack = (currentBottom, offset) => {
        // reached end of boxes
        if (offset >= boxes.length) return 0

        let currBox = boxes[offset]
        let heightWithBottom = 0
        // see if the addition of the new box results in a greater stack
        if (!currentBottom || currBox.smaller(currentBottom)) {

          // store in cache if it doesnt exist
          if (!cache[offset]) {
            cache[offset] = maxStack(currBox,offset + 1)
            cache[offset] += currBox.height
          }
          // get cache with this current box as the box as the bottom
          heightWithBottom = cache[offset]
        }
        // skip it and add next box on our old bottom and see if it yields larger one
        let heightWithoutBottom =maxStack(currentBottom,offset+1)


        return Math.max(heightWithBottom, heightWithoutBottom)
      }

      // start at root of eac
      return maxStack(0,0)
    }


## 8.14 Boolean Evaluation
**Q**: Given a boolean statement with 0,1, |, ^, &. and a desired result. write a function that will count the number of ways of parenthisizng the statement such that it evaluates to the desired result
ex: 1^0|0|1, false -> 2
  (1)^(0|0|1) = false


### Operator Subdivisions
**t: O(2^n) s:O(n)**
- we are pretting much dividing it into left and right problems
- if we move along the statement and divide the string at the operator found
- we will have a left side and a right side
- we then can check how many permutations of either side evalutates to the result we need given the previous result context
- we then multiply the results and add to the count (we multiply to get all permutations between left and right)
- We use memoization to store the cached hits on a statement and result to make it better

</a>

    let cache ={
      true: {},
      false: {}
    }}
    const BooleanEval = (statement, result) => {
      // base case
      if ((statement === '0' && !result) || (statement === '1' && result)) return 1

      // DP
      if (cache[result][statement]) return cache[result][statement]]
        
      let count = 0
      // get each operater
      for (let i=1;i<statement.length;i+=2) {
        let operator = statement[i]
        let left = statement.substring(0,i)
        let right = statement.substring(i+1)

        // pre compute beforehand
        // worse performance but better readability
        let leftTrue = BooleanEval(left, true)
        let leftFalse = BooleanEval(left, false)
        let rigthTrue = BooleanEval(right, true)
        let rightFalse = BooleanEval(right, false)

        let total = (leftTrue * leftFalse) + (rightTrue * rightFalse)
        let totalTrue = 0
        if (operator === '&')
          totalTrue += leftTrue * rightTrue
        else if (operator === '|) {
          totalTrue += leftTrue * rightTrue
          totalTrue += leftTrue * rightFalse
          totalTrue += leftFalse * rightTrue
        } else {
          totalTrue += leftTrue * rightFalse
          totalTrue += leftFalse * rightTrue
        }

        // add all true, or all false 
        count += result ? totalTrue : total- totalTrue
      }
      // cache
      cache[result][statement] = count
      return count 
    }

    ////////////////////
    // a better if logic 
    ////////////////////
