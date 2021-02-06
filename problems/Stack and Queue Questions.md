# Implementation
## Stack
    class StackNode {
      
      constructor(val, previous) {
        this.val = val;
        this.previous = previous;
      }
    }
    class Stack {

      constructor() {
        this.top = null;
      }

      pop() {
        if (!this.top) return null
        let val = this.top.val
        this.top = this.top.previous     
        return val;
      }

      peek() {
        return this.top ? this.top.val : null
      }

      push(val) {
        this.top = new StackNode(val,this.top)
      }

      isEmpty() {
        return !this.top
      }
    }
  

## 3.1 Three in One
**Q**: Describe how you could use a single array to implement three stacks

### ????
- create an array of 10 elements of whatever
- endLeft:have a pointer for the end of the first stack which grows from the left
- endRight:have a pointer for the end of the second stack which grows from the right
- end/startMiddle:have a pointer for the start and end of the middle stack which grows from the middle (can start at 1/3 point)
- This questions veyr much just psuedocode for interview required

</a>

    addLeft(val)
      if endLeft == startMiddle
        growAtI(endLeft)
      endLeft++
      arr[endLeft] = val

    addMiddle(val) 
      if endMiddle == (lenght -endRight)
        growAtI(endMiddle)
      endMiddle++
      arr[endMiddle] = val
    
    addRight(val)
      if ((length -endRight) == endMiddle)
        growAtI(endMiddle)
      endRight++
      arr[endMiddle] = val

    peekLeft()
      return arr[endLeft]
    .... same

    popLeft()
      if (endLeft == 0) {
        return null
      } else {
        let val = peekLeft()
        endLeft--;
      }
    popRight()
      if (endRight == length-1)
        null
        return null
      else
        let val = peekRight()
        endRight--
      
    popMiddle()
      
      if (endMiddle == startMiddle)
        return null
      else
        let val= peekMiddle()
        endMiddle--
        return val

## 3.2 Stack Min
**Q**: Implement a stack that has a min() func that returns lowest value

### Min Stack
**t: O(1) s: O(n)**
- We create an extended Stack class (not shown below)
- In this class we have an min stack that keeps track of the min at each step of the stack
- When we push, we add to the min if lower than min peek or if its undefined
- When we pop, we pop the min stack as well if the values are equal
- NOTE: all editors online wouldnt let this work becaue of es6 bullshit

</a>

    class MinStack extends Stack {

      constructor() {
        super()
        this.min = new Stack()
      }

      getMin() {
        return this.min.peek()
      }

      pop() {
        let val = super.pop()
        if (val === this.getMin())
          this.min.pop()
        return val
      }

      push(val) {
        super.push(val)
        let min = this.min.peak()
        if (min === null || val <min)
        this.min.push(min)
      }

    }

## 3.3 Stack of Plates
**Q**: Create a stack of stacks that creates a new stack when a stack gets past a threshold

### Stack of Plates
**t: O(1) s: O(n)**
- We will need tomake a mod to the stack class:
  - add length property so we know how long it is
- We will create a stack of stacks, as well as an array where ith element is ith stack
- When we push, we check if we have any stacks at peak(), if check if the current one is full (check length)
  - if full, create new stack and add to that
  - else, add to current peak stasck
- When we pop, we pop from our peek(), if none just return null
  - if the stack we popped from is empty, we can than pop that stack from our master list
- We make sure to keep our array lookup, up to date
- for popAtI(i), we can just use the array lookup we have. NOTE: this will sometimes lead sometimes to our stacks being empty half way
- TODO: in the futre we can create a min-stack of each stacks length, and then when we wanna push we check which array has the smallest length and add to that,if the min is max than we can add a new array
- This can easily be done by createing another stack of stacks but each entry is the stack at that state that has the least amount of plates available


</a>

    class StackOfStacks {
      constructor(limit) {
        this.limit = limit
        this.stacks = new Stack()
        this.stackLookup = []
      }

      pop() {
        let laststack = this.stacks.peek()
        if (laststack) {
          let val =laststack.pop()
          if (laststack.isEmpty()) {
            this.stacks.pop()

            // remove last
            this.stackLookup.splice(this.stackLookup.length-1,1)
          }
          this.length--
          return val
        } else {
          return null
        }
      }


      peek() {
        let stack = this.stacks.peek()
        return stack ? stack.peek() : null
      }

      push(val) {
        let stack = this.stacks.peek()
        if (!stack) {
          let newStack = new Stack()
          this.stacks.push(newStack)
          stack = newStack
        }
        if (stack) {
          // if we are at limit
          if (stack.length === this.limit) {
            let newStack = new Stack();
            this.stacks.push(newStack)
            this.stackLookup.push(newStack)
            stack =newStack
          }
          stack.push(val)
          this.length++
        }

      }
      popAt(i) {
        let stack = this.stackLookup[i]
        if (stack) {
          let val = stack.pop()
          return val
        } else {
          return null
        }
      }

      isEmpty() {
        let peak = this.peak()
        return peak ? peak.isEmpty() : true
      }
    
    }

## 3.4 Queue Via Stacks
**Q**: Implement a queue with two stacks

### Two Stacks
- We use temp stacks
- When we queue we simply add to our main stack
- When we dequeue or peek we do this:
  - keep popping elements on a temp stack
  - if peeking:
    - return temp.peek()
  - if poping:
    - return temp.pop()
- an optimization we made is only transfering between the two stacks when we need to
- So when we do a lot of pops, we dont need to put the temp values back on the main
- When we are pushing make sure our temp stack is empty, if not do the transfer then

</a>

    class Queue {
      constructor() {
        this.stack = new Stack()
        this.temp = new Stack()
      }

      enqueue(val) {
        // if we have values in our temp we need to get them back
        if (!this.temp.isEmpty()) {
          this.copyTo(this.temp,this.stack)
        }
        this.stack.push(val)
      }

      dequeue() {
        return this.getLast(true);
      }

      peek() {
        return this.getLast(false)
      }

      getLast(remove) {

        // if our temp stack is empty we should try to get our values
        // from main stack
        if (this.temp.isEmpty())
          this.copyTo(this.stack,this.temp)

        // if we are removing, just delete the head of the temp stack
        return remove ? this.temp.pop() : this.temp.peek()
      }

      // copy elements from one stack to another in reverse order
      copyTo(stackA, stackB) {
        // go the very end
        while (!stackA.isEmpty()) {
          stackB.push(stackA.pop())
        }
      }

      isEmpty() {
        return this.stack.isEmpty()
      }
    }

## 3.5 Sort Stack
**Q**: Write a program to sort a stack such that the smallest items are on the top
You can use additonal stacks but not any other datatypes

### MinStack
**s: O(n^2) t: O(n)**
- We will have additional stack that holds a temp list of sorted elements where the top is the highest number
- We keep adding numbers onto that as long as they are larger
- If we find a number not larger, we set it to the side and move all elements in our temp stack back to the original array
- once that is done add the set aside number, this will now be the largest number in the temp stack
- repeat adding numbers from the main stack until it is empty
- once it is empty we push all elements on reverse temp stack back to our normal stack
- this will give us the inplace stack sorted with lowest elements from the top

</a>

    const sortStack = (stack) => {
      let reverseStack = new Stack()

      while (!stack.isEmpty()) {
        
        // keep stacking on top
        while (stack.peek() >= reverseStack.peek())
          reverseStack.push(stack.pop())

        // we have a new min
        if (!stack.isEmpty()) {
          let min = stack.pop()

          // set aside and move new sorted maxes to original array
          while(!reverseStack.isEmpty()) {
            stack.push(reverseStack.pop())
          }
          reverseStack.push(min)
        }
      }

      // transfer reverse to have min on top
      while(!reverseStack.isEmpty()) {
        stack.push(reverseStack.pop())
      }
    
    }
    

## 3.6 Animal Shelter
**Q**: Write a datastrcuture that holds dogs and cats in a FIFO order.
This DS should be able to do the two following methods:
  - enqueue() same as queue
  - dequeueAny() returns the oldest animal
  - dequeueDog() return oldest dog
  - dequeueCat() returns oldest cat
You may use a linked list

### Buh
- we have two queues one for dogs one for cats
  - queue is made with stacks (3.4)
- We made a custom class that holds arrived property
- When we enqueue we create an animal object and increase ticket number
- this ensures that we knwo which of the two queues holds the oldest animal

</a>

    class Animal {
      constructor(name,type, arrived) {
        this.name = name
        this.type = type
        this.arrived = arrived
      }

      toString() {
        return `${this.name}:${this.type}`
      }
    }
    class AnimalShelter {

      

      constructor() {
        this.dogQueue = new Queue()
        this.catQueue = new Queue()
        this.ticket = 0
      }

      enqueue(name,type) {
        let animal = new Animal(name,type, ++this.ticket)
        if (type === "dog") 
          this.dogQueue.enqueue(animal)
        else
          this.catQueue.enqueue(animal);
      }

      dequeueAny() {
        let dog = (this.dogQueue.peek() || {}).arrived
        let cat = (this.catQueue.peek() || {}).arrived
        if (dog < cat) {
          return this.dequeueDog()
        } else 
          return this.dequeueCat()
      }

      dequeueCat() {
        return this.catQueue.dequeue()
      }

      dequeueDog() {
        return this.dogQueue.dequeue()
      }
    }