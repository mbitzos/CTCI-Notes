- [5.1 Insertion](#51-insertion)
  - [Splice](#splice)
- [5.2 Binary to string](#52-binary-to-string)
  - [Im skipping this](#im-skipping-this)
- [5.3 Flip Bit to win](#53-flip-bit-to-win)
  - [Iterative tracker](#iterative-tracker)
  - [Optimized Iterative tracker](#optimized-iterative-tracker)
- [5.4 Next Number](#54-next-number)
  - [Brute Force Search until we find](#brute-force-search-until-we-find)
  - [Bit Manipulation to get next number](#bit-manipulation-to-get-next-number)
- [5.5 Debugger](#55-debugger)
- [5.6 Conversion](#56-conversion)
  - [XOR Count](#xor-count)
- [5.7 Pairwse Swap](#57-pairwse-swap)
  - [Magic number](#magic-number)
- [5.8 Draw Line](#58-draw-line)
  - [Bitshifting -1s](#bitshifting--1s)

## 5.1 Insertion
**Q**: Given two 32 bit numbers M and N, and two bit positions i, j. Write an algo that inserts N into M at bit i and goes until bit j
ex: M = 100000000 N = 10011, i = 2, j = 6
output => 10[10011]00

### Splice
**t: O(1) s:(1)**
- take -1 and bit shift it j amount 
  - ex: 111111111 j = 6 => 1111000000
- take -1 and bit shift it i amount
  - ex: 111111111 i =2 => 1111111100
- & those two to get all zeros between i and j and the rest 1
- & that with your M to clear all bits in M from i -> j
- take your N, bit shift it i amount
  - ex:  N= 10011  i=2 => 1001100
- | the the bit shfited N and the masked num

</a>

    let mask = (-1 << i) && (-1 << j)
    let val = N << i
    return (num & mask) | val

## 5.2 Binary to string
**Q**: Given a real number between 0 and 1 (ex:0.72) pass in as a double, print the binary representation. If the number can't display ERROR

### Im skipping this

## 5.3 Flip Bit to win
**Q**: you have an integer and you can set one bit to 1. write an algo to find the longest sequence of 1's in that number that you can create

### Iterative tracker
**t: O(n^2) s: O(1) where number of bits in the integer is the sequence in bits**
- have three vars: max, curr, and pointer to last zero found (-1 if not found)
- loop through each bit (we can do this by arthmitically shifting)
- if we at a zero:
  - if (last zero found != -1) 
    - check to see if our current is higher than our max, then set if so
    - reset curr = 0
    - set i = lastZeroFound -1  // we need to go back and try
  - else
    - lastZeroFound = i
    - curr++
- else
  - curr++
- at the end return (curr > max) ? curr : max
  
### Optimized Iterative tracker
**t: O(n) s: O(1) where number of bits in the integer is the sequence in bits**
- i had the right idea above but instead of having to simply go back to the last zero or have to keep track of the last zero found, what we can do is hold the previous lenght of 1's sequences
- and use that to check out max. we dont need to go back to each time we reset because we are simply restarting the counter everytime we reach a zero and hold our previous sequence. Since we can only swap one 0 bit, we only need to store the last sequence (if we could hold more bits, we would need to hold the last two somehow)
- while (a != 0)
    - if i == 1
      - ++currentLength
    - else
      - previousLength = (a >> 2) == 0 : currentLength // if its the end reset, else continue 
      - currentLength = 0 // reset current counter
  - max=  max(max, previouslength + currentlength+1)
    a >>>= 1  // go to next bit


## 5.4 Next Number
**Q**: Given a positive number print the next smallest and next largest integer that contain the same number of 1 bits as the num

### Brute Force Search until we find
**t: O(1) s: O(1)**
**NOTE: we can argue constant time since we will be looping a fixed number each time**
- get how many ones exist in the number first
- to find the nextHigh do a while loop where we check if the number of ones in our current max is equal to the ones in our number, if not increment, else stop, this is our max
- to find our nextLowest its the same thing but we are decrementing instead

</a>

    getOnes(num) {
      let ones=0
      for (int i =0;i<32;i++)
        ones += getBits(num,i)  // add 0 or 1
      return ones
    }

    getBit(num, i) {
      return numm & (1 << i)
    }

    getNext(num) {
      let ones = getOnes(num)
      let high = num +1
      while (getOnes(high) != length) high++
      let low = num -1
      while (getOnes(low) != length) low--

      return [high,low]
    }

### Bit Manipulation to get next number
**t: O(1) s: O(1)**
- Next Largest Number:
  - To get the next largest number we have to understand this principle of the question:
    - The number of 1s must be the same, so what this means is one bit needs to change from 0 -> 1 and another needs to go from 1 -> 0 (this is because this will give the next highest with same 1s)
      - additionally the flip from 0 -> 1 MUST be to the left of 1 -> 0 to be higher
    - So what we will do is find the first 0 thats not a trailing zero at the start and convert that to a 1
      - ex: 010100 < 011100 
    - You'll notice that now we have too many 1's and not enough 0's
    - So this means we needs to flip a 0 to a 1
      - We know that from before this flip must be to the right so it must be to the right of the first non trailing zero (this will be labelled as bit **p**)
    - We also need to flip this bit such that it makes the smallest change so that we find the NEXT highest
    - What we can do is just clear all bits to the right, then write (c1 -1) 1's to the far right (where c1 is the number of 1's to the right the first flip)
    - ex: 10110 = 22  => 1[1]110 = 30, then => 11[001] => 11001 = 25
    - this last step can be done by:
      - a = 1<< p // all zeros except 1 in first bit
      - b = a- 1 // all zeros except for p ones
      - mask = ~b // all ones except for p zeros
      - n &= mask // clears rightmost p bits
      - a = 1 << (c1-1) // 0s with a 1 at positon c1-1
      - b =a -1 // 0s with 1s from 0 to c1-2
      - n |= a  // inserts 1s at positions 0 through c1-2
- Next Smallest Number:
  - We do the same thing but we search for the first trailing 1 and flip it from a 1 to a 0
  - then instead of clearing and setting c1-1 1's to the right, we write c1 +1 1's to the far left as close to the first bit:
    - ex: 101001 = 41 => 10[0]001 => 100100 =36
    - We can do this last step by:
      - a = ~0 // all 1s
      - b = a << (p+1)    // 1s followed by p+1 zeros
      - n &= b      // clears bits 0 to p
      - a = 1 << (c1+1)   //0s with 1 at position (c1+1)
      - b = a -1        // 0s followed by c1+1 ones
      - c=  b<< (c0-1) // c1+1 ones followed by c0-1 zeros
      - n |= c

## 5.5 Debugger
**Q**: Explain what this does: ((n & (n-1)) == 0)
**A**: Returns if n is the power of 2 or 0 (or like i said before as at most 1 bit that is 1)
- n & (n-1) means they both have unique bits that are 1 (none overlapping)
- n-1 is the same as n except its trailing 0s are 1s and the next 1 after that is 0
- if n = xyz1000, n -1 = xyz0111
- so if n & (n-1) == 0 that means xyz MUST be 0's
- so this checks if n == 0...0001000..0
- must have exactly one 1

## 5.6 Conversion
**Q**:  Write a algorithm that returns how many bits are needed to flip to convert A to integer B

### XOR Count
**t: O(1) s:(1)**
- We can XOR A and B to get a bitnumber that only contains bits of 1 where they are different
- Then we iterate through that numbers bit and get how many 1s it has (thats how many bits need to be flipped)

</a>

    return getOnes(A^B) // from 5.3

## 5.7 Pairwse Swap
**Q**: Write a function that can swap the odd bits with even bits, as few instructions as possible

### Magic number
**t: O(1) s: O(1)**
- We can use the number 1431655765 as a mask (01010101010101010)
- num & mask to get all odd bits 
- num & (mask << 1) to get all even bits (NOTE we are left shifting because right shifting would overflow)
- Then we can just do (maskOdd >> 1 | maskEven << 1) which will swap all odd and all even

## 5.8 Draw Line
**Q**: A monochrome screen is stored as a single array of bytes, allowing 8 consecutive pixels to be sotred in one byte. screen has width w, where w is divisible by 8 (no bytes split across lines). the height of the screen can be derived from the lenght of the array and width. implement an algorithm that draws a horizontal line from (x1,y) to (x2,y)


### Bitshifting -1s
**t: O(1) s: O(1)**
- First we need to find the start and end byte we are going to be making modifications to
- we can do this by geting bytesPerRow = width/8
- start = y * bytesPerRow + (Math.floor(8/x1))  // which byte our x1 is in
- end = start + Math.floor(8/x2)
- we can than loop through all bytes between 0...start, and end...bytes.length-1 and set to 0 (000000)
- then we can set all bytes between start+1 and end -1 to -1 (1111111)
- now comes the part where we have to do bit wise operations to only color certain number of pixels on the end points
- We do the following:
  - start: 
    - -1 >> (x1 % 8)  // logically shift right -1 (111111...) to have the left bits be zero (result => 00000111111...)
  - end:
    -  -1 << (x2 % 8) // logically shift left -1 (1111...) to have the right bits be zero (result => ....1111100000)
- We have to make sure that during our checks we aren't going out of bounds (for the start+1,end-1 check also have to make sure that does that cross into eachother, can be achieved with simple for loop)

</a>

    let bytesPerRow = width/8
    let start = y * bytesPerRow + Math.floor(8/x1)
    let end = start  Math.floor(8/x2)

    for (let i =0;i<bytes.length;i++) {
      if (i == start) {
        bytes[i] = -1 >> (x1 % 8)
      } else if (i == end) {
        bytes[i] = -1 << (x2 % 8)
      } else if (i >= start+1 && i < end -1)
        bytes[i] = -1
      else {
        bytes[0] = 0
      }
    }


    // rest of bytes will always be 
    for (let i=0;i<start;i++)
      bytes[i] = 0
    for (let i=end+1;i<bytes.length-1;i++)
      bytes[i] = 0
    
    // inbetween is always going to be full so set to -1 (111111)
    for (let i = start+1;i<end-1;i++)
      bytes[i] = -1