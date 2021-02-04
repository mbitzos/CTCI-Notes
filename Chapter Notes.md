# CTCI Notes

## Arrays and Strings

  ### Hashtables
  - are a create way to do fast lookup
  - Built with arrays of linked lists (for hash clashes)

  ### Resizeable Arrays
  - When adding an element to an array and its full, we double the size and copy elements to new doubled array
  - time complexity is neglible

  ### JAVA: StringBuilder
  - Added strings will take O(n) to copy each char in the array everytime
 - Stringbuilders make it constant by creating a resizable array

## Linked Lists
  - Double linked lists give pointers for next/previous hmm

  ### Runner Technique
  - Have two pointers that traverse the linked list at different speeds
  - EXAMPLE:
    - If have an even linkedlist: a1->a2->a3->...an->b1->b2->b3->...b3
    - How can we rearrange it to: a1->b2->a2->b2->a3->b3->...an->bn
    - We can do this in O(n) also by having one pointer go one node at a time and the other 2
    - then when we have the second pointer reach the end we know the first as at the center
    - We can than use that marker to start inserting b1...bn interweaving it into a1...an

