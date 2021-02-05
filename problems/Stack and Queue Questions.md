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
  
## Queue
	
	public class LinkedList {
	    Node head;
	    public LinkedList(List<Integer> values) {
            Node previous = null;
            for (int val : values) {
               Node node= new Node(val,null);
               if (previous == null)
                    head= node;
                else {
                    previous.next = node;
                }
                previous = node;
	        }
	        this.head = head;
	    }
	    
	    @Override
	    public String toString() {
	        return head == null ? null : head.toString();
	    }
    
	   
	    public Node getHead() { return head; }
	}
	
	 public class Node {
      public int val;
	    public Node next;
	    
	    private Node(int val, Node next) {
	        this.val = val;
	        this.next = next;
	    }
	    
	    @Override
	    public String toString() {
	        List<String> p = new ArrayList<>();
	        Node curr = this;
	        while (curr != null) {
	            p.add(String.valueOf(curr.val));
	            curr =curr.next;
	        }
	        return p.toString();
	    }
    }


## 3.1 Three in One
**Q**: Describe how you could use a single array to implement three stacks

### ????
- create an array of 10 elements of whatever
- endLeft:have a pointer for the end of the first stack which grows from the left
- endRight:have a pointer for the end of the second stack which grows from the right
- end/startMiddle:have a pointer for the start and end of the middle stack which grows from the middle (can start at 1/3 point)

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
**Q**: 

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