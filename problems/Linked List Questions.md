- [Linked List Implementation](#linked-list-implementation)
  - [2.1 Remove Dups](#21-remove-dups)
    - [HashSet](#hashset)
  - [2.1.1 Remove Dups No Temporary Buffer ~ s: O(1)](#211-remove-dups-no-temporary-buffer--s-o1)
    - [Lookup](#lookup)
  - [2.2 Return Kth to Last](#22-return-kth-to-last)
    - [Offset Look ahead](#offset-look-ahead)
  - [2.3 Delete Middle Node](#23-delete-middle-node)
    - [Copy and Delete last](#copy-and-delete-last)
  - [2.4 Partition](#24-partition)
    - [Left and Right Pointers](#left-and-right-pointers)
  - [2.5 Sum lists](#25-sum-lists)
    - [Iterative Carry bit](#iterative-carry-bit)
    - [Recursive Carry bit](#recursive-carry-bit)
  - [2.6 Palindrome](#26-palindrome)
    - [Reverse Linked List and Check](#reverse-linked-list-and-check)
    - [Iterative Stack](#iterative-stack)
  - [2.7 Intersection](#27-intersection)
    - [HashSet](#hashset-1)
    - [Iterative Aligned Tail Checks](#iterative-aligned-tail-checks)
  - [2.8 Loop Detection](#28-loop-detection)
    - [Floyds Look Detection](#floyds-look-detection)

# Linked List Implementation
	
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


## 2.1 Remove Dups
**Q**: write code to remove duplicates from an unsorted linked list

### HashSet
**t: O(n) s: O(n)**
- use hashset to check if value already exist, if so delete it
        
      public static void removeDups(LinkedList list) {
          HashSet<Integer> set = new HashSet<Integer>();
          
          Node curr = list.head;
          Node prev = null;
          while (curr != null) {
              if (set.contains(curr.getVal())){
                  prev.next = curr.next;
              } else {
                  set.add(curr.getVal());
              }
              prev = curr;
              curr = curr.next;
          }
      }


## 2.1.1 Remove Dups No Temporary Buffer ~ s: O(1)
### Lookup
**t: O(n^2) s: O(1)**

- Just look ahead
- NOTE: if the first element is a dup, than set the head of the linkedlist to the next
    	
      public static void removeDups(LinkedList list) {
          
          Node curr = list.head;
          Node prev = null;
          while (curr != null) {
              int val = curr.val;
              Node c = curr.next;
              while (c != null) {
                  if (c.getVal() == val) {
                      if (prev == null) {
                          list.head = curr;
                      } else
                          prev.next= curr.next;
                      break;
                  }
                  c = c.next;
              }
              prev = curr;
              curr = curr.next;
          }
      }
	
## 2.2 Return Kth to Last
Q: Return the kth to last element in the linked list

### Offset Look ahead
**t: O(n) s: O(1)**
- We will have two pointers, one at the start and the other k ahead
- When the second gets to the end we know the first is k away from the back
  - return the first
- if the second pointer cant be made return -1

      public static int returnKth(LinkedList list, int k) {
          
          Node curr = list.head;
          Node ahead = getKAhead(curr, k);
          if (ahead == null) return -1;
          while (ahead != null) {
              ahead = ahead.next;
              curr = curr.next;
          }
          return curr.getVal();
      }
      
      public static Node getKAhead(Node curr, int k) {
          while (curr != null) {
              curr = curr.next;
              if (k == 0) return curr;
              k--;
          }
          return k == 0 ? null : curr;
      }

## 2.3 Delete Middle Node
Q: given only a node c that exists somewhere between the start and end (exclusive) of the list, delete it and make the list return what it should look like
  - ex: c = 3, 1 -> 2 -> 3 -> 5 -> 1
      - => 1 -> 2-> 5 -> 1

</a>

### Copy and Delete last
**t: O(n) s: O(1)**
- We are going to mimmick this
- Just replace our current value with the next
- If the next one is the last element, just remove it from the list (set curr.next = null)
- traverse list normally
- NOTE: Not sure if this is what they want, is the val property even exposed? i assume so

</a>

    public static void deleteNode(Node node) {
        
        Node curr = node;
        while (curr != null) {
            curr.val = curr.next.val;
            if (curr.next.next == null) {
                curr.next = null;
            }
            curr = curr.next;
        }
    }

## 2.4 Partition
Q: Write code to partition a linked list around a value "x", such that all values less than x are to the left, and all values >= to x are to the right

### Left and Right Pointers
**t: O(n) s: O(1)**
- We have four pointers:
  - Start of left partition
  - tail of left partition
  - start of right partition
  - tail of right partition
- NOTE: We can actually just do this with two pointers: head and tail, and just add to that
- We traverse list, if the curr.val < partition value, we append to left (or initialize it) then progress it
- else add/init the right partition and traverse it
- At the end we tell the right.next -> null since we know its at the end
- And we connect left -> right, unless left == null than we just return right

</a>

    public static Node partition(LinkedList list, int target) {
        Node right = null;
        Node left=  null;
        Node rightCurr = null;
        Node leftCurr=  null;
        Node curr= list.head;
        while (curr != null) {
            if (curr.val < target) {
                if (left == null){
                    left = leftCurr =curr;
                }else {
                      leftCurr.next = leftCurr = curr;
                }
            } else {
                if (right == null){
                    right = rightCurr = curr;
                }else {
                      rightCurr.next = rightCurr = curr;
                }
      
            }
            curr = curr.next;

        }

          if (rightCurr != null)
              rightCurr.next = null;
        if (leftCurr != null) {
            leftCurr.next =right;
            return left;
        } else {
            return right;
        }
    }

## 2.5 Sum lists
Q: You have two numbers where each digit in that number is a linked list node in REVERSE ORDER
ex: 2->6->7 => 762
return the addition of these two numbers in a linked list
ex 2->6->7 + 1->2->3 
  = 762 + 321 = 1083 => 3->8->0->1


### Iterative Carry bit
**t: O(n) s: O(1)**
- Just loop through the nodes, if the values > 10 than make it 0, and carry over a boolean that flags that we should add the nextone

FOLLOW UP: What if the linked lists represent the number in non reverse:
ex: 2->6->7 => 267
### Recursive Carry bit
-First we are going to make sure the lengths are equal by adding 0's to the shorter one

</a>

	public static void padWithZero(LinkedList a, int padding) {
	    if (padding == 0) return ;
	    
	    Node start = new Node(0, null);
	    Node curr = start;
	    padding--;
	    while (padding > 0) {
	        Node newNode= new Node(0,null);
	        curr.next = newNode;
	        curr = newNode;
	        padding--;
	    }
	    curr.next = a.head;
	    a.head = start;
	}

- Than we will recursively iterate and have it so the recrusive function returns either a 1 or 0
and set the current node val to the return + the vals added
- We do this with a custom class that holds the carry bit and node itself

</a>

	public static class NodeCarry {
	    public boolean carry;
	    public Node node;
	    public NodeCarry(Node node, boolean carry) {
	        this.node = node;
	        this.carry =carry;
	    }
	}

    public static NodeCarry add(Node a, Node b) {
	    if (a == null || b == null) return null;
	    
	    
        // get next one;
	    NodeCarry next= add(a.next,b.next);
	    
	    // add values initally
	    int val = a.val + b.val;
	    
	    
	    Node newNode = new Node(0,null);
	    // connect to next
	    if (next != null) {
	        newNode.next = next.node;
	        val += next.carry ? 1 : 0;
 	    }
 	    
 	    // calc carry bit
        boolean carry =false;
 	    if (val >= 10) {
 	        carry = true;
 	        val -= 10;
 	    }
 	    newNode.val = val;
	    
	    // return the node with a carry or not
	    return new NodeCarry(newNode,carry);
	}

- Than we make sure to remove the front zeros

</a>

    public static LinkedList add(LinkedList a, LinkedList b) {
        int aLength = a.getLength();
        int bLength = b.getLength();
        int diff = aLength - bLength;
        
        // pad smaller array with zeros
        if (diff < 0)
            padWithZero(a,-diff);
        else
            padWithZero(b,diff);
        NodeCarry carry = add(a.head, b.head);
        return removeFrontZeros(new LinkedList(carry == null ? null : carry.node));
    }

    public static LinkedList removeFrontZeros(LinkedList a) {
      
      Node curr= a.head;
      if (curr == null) return a;
      
      
      while (curr != null && curr.val == 0) {
          curr = curr.next;
      }
      return new LinkedList(curr);
    }



## 2.6 Palindrome
Q: Implement a function to determine if linked list is a palindrome

### Reverse Linked List and Check
**t: O(n) s: O(n)**
- We will create a reverse linked list, while we are doing this we can actually keep track of the length. we do this because in the final check we only need to go halfway
- So we create this:

</a>

  	// stores a list and its length
    public static class CountedLinkedList {
        public LinkedList list;
        public int length;
        public CountedLinkedList(LinkedList list, int length) {
            this.list = list;
            this.length = length;
        } 
    }

- then we create a reverse counted linked list like this:
  
</a>

	public static CountedLinkedList getReverse(LinkedList list) {
	    Node previous = null;
	    Node curr = new Node(list.head);
	    int count = 0;
	    while (curr.next != null) {
	        
	        Node next =curr.next == null ? null: new Node(curr.next);
	        curr.next = previous;
	        previous =curr;
	        curr =next;
	        count++;
	    }
	    if(curr!=null) 
	        curr.next = previous;
	        
	    return new CountedLinkedList(new LinkedList(curr),count);
	}

- then finally we go through the heads of both our lists and check if the values exist
- NOTE: we only go till half because if we can verify that the linkedlists are the same on their respective halves, than we know the other will be since the list is the reverse of the other

</a>

	
	public static boolean isPalindrome(LinkedList list) {
	    CountedLinkedList reverse = getReverse(list);
	    int length = (int) Math.floor(reverse.length/2.0f);
	    Node right = reverse.list.head;
	    Node left = list.head;
	    while (length > 0) {
	        if (right.val != left.val)
	            return false;
	        right = right.next;
	        left = left.next;
	        length--;
	    }
	    return true;
	}

### Iterative Stack
**t: O(n) s: O(n)**
- We iterative through the linked list with a slow and fast pointer (the fast pointer goes twice as fast). We use this method to end up with the slow pointer in the middle
- While the slow runner goes through the list, we will push the values onto a stack, this stores all values in the stack in reverse order
- We then just iterate through the rest of the list and pop the stack and check if equal, if ever not return false;
- NOTE: we have to cover for the case where the linked list is odd so the slow can skip the middle element

## 2.7 Intersection
Q: Given two singly linked list, if they intersection (meaning they share the same node by reference) return that node, else return null;


### HashSet
**t: O(min(a,b)) s: O(a+b) where a = first list length, b = second list length**

- Simply just go through each list, and check if the current node is already in the set, if so return that node
- if not add to list and progress
- if reached end of either list, than return null (no intersection)

</a>

    public static Node intersection(LinkedList l1, LinkedList l2) {
          HashSet<Node> visited = new HashSet<Node>();
          Node a = l1.head;
          Node b = l2.head;
          while (a != null && b != null) {
              if (visited.contains(a)) return a;
              if (visited.contains(b)) return b;
              visited.add(a);
              visited.add(b);
              a = a.next;
              b= b.next;
          }
          return null;
      }

### Iterative Aligned Tail Checks
**t: O(a+b) s: O(1) where a = first list length, b = second list length**
- We can check if the lists have an intersection first by going through them and checking if their tails are the same
- What we will do alongside that is check the length of these linkedlists
- We do this by creating a custom class: TailLength

</a>

	public static class TailLength {
	    public int length;
	    public Node tail;
	    
	    public TailLength(Node tail, int length) {
	        this.length = length;
	        this.tail = tail;
	    }
	}

With a method that gets this from a linkedlist:
**O(max(a,b))**

	public static TailLength getTailLength(LinkedList list) {
	    int length  =0;
	    Node curr = list.head;
	    while (curr != null) {
	        curr = curr.next;
	        length++;
	    }
	    return new TailLength(curr, length);
	}

Then what we can do is find the difference in length between the two, and whichever is longest we move traverse diff amount of nodes forward.

	public static Node getKthNode(Node head, int k) {
	    Node curr = head;
	    while(k > 0) {
	        curr=  curr.next;
	        k--;
	    }
	    return curr;
	}

What we are doing here is making it so we can search both linkedlists at the same time and have its tails aligned. This makes it so when we start iterating we can just do simple checks if the two current nodes are equal.
**O(min(a,b))**


	public static Node intersection(LinkedList l1, LinkedList l2) {
	    TailLength tail1 = getTailLength(l1);
	    TailLength  tail2 = getTailLength(l2);
	    
	    // if there is a intersection
	    if (tail1.tail == tail2.tail) {
	        int diff = tail1.length - tail2.length;
	        Node t1 = l1.head;
	        Node t2 = l2.head;
	        
	        // traverse diff forward, so they are tail aligned
	        if (diff < 0) {
	            t2 = getKthNode(t2,-diff);
	        } else {
	            t1 =getKthNode(t1,diff);
	        }
	        // traverse until we find the node thats equal
	        while (t1 != t2) {
	            t1 = t1.next;
	            t2 = t2.next;
	        }
	        return t1;
	    } else {
	        return null;
	    }
	}


## 2.8 Loop Detection
Q: Given a linked list, return the node of the begining of the loop


### Floyds Look Detection
**t: O(?) s: O(1)**
https://cs.stackexchange.com/questions/10360/floyds-cycle-detection-algorithm-determining-the-starting-point-of-cycle 
- Firstly we use will use the tortoise and hare algorithm to find this loop, where pointer A moves one node at a time, and pointer B moves two nodes at a time
  - So we can say that the distance travelled for B =  2* distance travelled of A
- Additionally for loops there are three segments:
  - x = the distance between start of list and start of loop
  - y = distance between start of loop and where the collision happens
  - z = the distance between collision and start of loop
- What we will find is that x == z:
  - 2 * dist(A) * C = dist(B) * C
    - where C = number of loops we pass, we can remove this since the distance is proportionate anyways
  - 2(x+y) = (x+y+z) + y
  - 2(x+y) = x+2y+z
  - x = z
- therefore, since the point at which the collision happened and the start of the loop is the same as the distance between the head of the list and the start of the loop we can do this:
- Set a pointer to the head and the collision, increment together by one until they collide. They will collide when the loop starts since they are equal in distance

</a>

	public static Node getLoopStart(LinkedList a) {
	    Node slow = a.head;
	    Node fast =a.head;
	    while (fast != null && fast.next != null) {
	        
	        slow = slow.next;
	        fast = fast.next.next;
	        if (slow == fast) {
	            break;
	        }
	    }
	    if (fast == null || fast.next == null) return null;
	    
	    slow = a.head;
	    while (slow != fast) {
	        slow =slow.next;
	        fast = fast.next;
	    }
	    return slow;
	}


