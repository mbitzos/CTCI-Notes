## 1.1 isUnique
**Q**: Implement an algorithm to determine if a string has all unique characters. What if you
cannot use additional data structures?

### PRE NOTES:
  -Ask interviewer if the string is constrained to ASCII or not

### HashSet
**t: O(n) s: O(n)**
- use hashset to see if a char is already visited
  
      private static boolean isUnique(String s) {
          HashSet<Character> set = new HashSet<Character>();
          for (char c : s.toCharArray()) {
              if (set.contains(c)) {
                  return false;
              }
              set.add(c);
          }
          return true;
      }


### Sorting
**t: O(n) s: O(1)**

- sort string and loop through
  - NOTE: might take more than s O(1) based on sorting algorith,
  - if multiple in same order return false
  - else it got through:return true
  
        private static boolean isUnique(String s) {
          char[] chars = s.toCharArray();
          Arrays.sort(chars);
          for (int i=1;i<chars.length;i++) {
              if (chars[i] == chars[i-1])
                  return false;
          }
          return true;
        }

    
## 1.2 Check Permutations
**Q**: Given two strings, write a method to decide if one is a permutation of the
other.

### PRE NOTES:
  - case sensitive?
  - white space sensitive?


### HashSet
**t: O(n) s: O(n)**
  
  -Go through each character in the first string and store its occurances in a hashmap
  -Iterate through second string and check if the occurances are the same


    private static boolean isSame(String s1, String s2) {
	    HashMap<Character, Integer> map = new HashMap<Character, Integer>();
	    for (char c : s1.toCharArray()) {
	        if (map.containsKey(c))
	            map.put(c,map.get(c)+1);
	        else    
	            map.put(c, 1);
	    }
	    for (char c: s2.toCharArray()) {
	        if (map.containsKey(c)) {
                if (map.get(c) == 0) {
                    return false;
                } else {
                     map.put(c,map.get(c)-1);
                     if (map.get(c) == 0)
                        map.remove(c);
                }
	        } else {
	            return false;
	        }
	    }
	    return map.keySet().size() == 0;
	}

### Sorted Array
**t: O(n log(n)) s: O(1)**

  - If they are the same with just different orderings
  - We can sort both strings chars and than see if the sorted strings are equal
    - Ex: "abbacus" "bacabsu"
    -  -> aabbcsu == aabbcsu 
    - Ex: "testab" "batreb"
    -  -> abestt != bbaert




## 1.3 URLify
**Q:** Write a method to replace all spaces in a string with '%20: You may assume that the string
has sufficient space at the end to hold the additional characters, and that you are given the "true"
length of the string. (Note: If implementing in Java, please use a character array so that you can
perform this operation in place.)

### Iterative
**t: O(n) s: O(n)**
  
- just insert %20 if we are at a space

      private static String urlify(String s, int length) {
          char[] c = new char[length];
          int j = 0;
          for (int i =0;i<length;i++) {
              if (s.charAt(j) == ' ') {
                  c[i]='%';i++;
                  c[i]='2';i++;
                  c[i]='0';
              } else {
                  c[i] = s.charAt(j);
              }
              j++;
          }
        
          return String.valueOf(c);
      }
### In Place Backwards Iterative
**t: O(n) s: O(1)**
  - just do the above but we can edit the char[] array in place
  - We do this by traversing the array backwards, one pointer starting at the true length and the other starting at the end of the word given to us in the array
    - we can check for trailing spaces by looping through and give index of last character that wasnt a space

          public static int getLength(char[] str) {
            int index =0;
            for (int i=0;i<str.length;i++) {
              if (str[i] != ' ')  index= i;
            return index;
          }
  - Then we will just go backwards and when the second pointer signals its a space we can add teh 0,2,% then move the first pointer along
  - we can wrwite this in place without overwrites since the first pointer will never surpass the second since it will always been behind if there are spaces (spaces*3 away initially)
  

## 1.4 Palindrome Permutations
Given a string, write a function to check if it is a permutation of a palindrome.
A palindrome is a word or phrase that is the same forwards and backwards. A permutation
is a rea rrangement of letters. The palindrome does not need to be limited to just dictionary words
NOTE: ignore case and spaces;

### HashSet
**t: O(n) s: O(n)**

- A palindrome in character count is defined as
  
      if (odd)  
          all characters will have two occurances, except one which either have 1 or 3
      else
          all characters will have two occurances
    
- just go through string and make mapping from c -> occurances
then make sure that map satisfies the above 

      private static boolean isPermutationOfPalindrome(String s) {   
        s = s.toLowerCase();
        int length =0;
        HashMap<Character, Integer> map = new HashMap<Character,Integer>();
        for (char c : s.toCharArray()) {
            if (c == ' ') continue;
            if (map.containsKey(c)) {
            
                  map.put(c,map.get(c)+1);
            } else {
                    map.put(c, 1); 
            }
            length++;
        } 
        boolean odd = length % 2 != 0;
        boolean foundMiddle = false;
        for (Integer i : map.values()) {
            if (i != 2) {
                if (odd) {
                    if (foundMiddle) {
                        return false;
                    } else {
                        foundMiddle = true;
                    }
                } else {
                    return false;
                }
            }
        }
        
        return !odd || foundMiddle;
      }  

### BIT VECTOR
**t: O(n) s: O(n)**

  TODO: Understand Bit Vectors
- We can actually dont need to check the counts at all, all we need to know is if the count is odd or not
- We can do that by creating a bit vector and essentially the count will be stored in a bit (0-1), 1 represents odd because even will do: 0->1 ->0. Then what we can do is check at the end if the bit array only contains at most 1 "1".
  - We do this by the following calculation:  bit_vector & (bit_vector - 1) == 0
  - Ex:
    - 00010000 -1 = 00001111
    - 00010000 & 00001111 == 0
    - 
    - 00101000 -1 = 00100111
    - 00101000 & 00100111 = 00001111 !== 0

## 1.5 One Away:
**Q:** There are three types of edits that can be performed on strings: insert a character,
remove a character, or replace a character. Given two strings, write a function to check if they are
one edit (or zero edits) away

### DUMB Brute Force Iterative Recurssion
**t:O(3^s) s: O(s)**
- Essentially check all one character edits in the strign and see if it equals it
- We will recurssively iterate through each character in both strings at once
- if we have a different character, we will will try to traverse forward by doing the following:
- only progress the original string (insertion)
- only prgress the other string (deletion)
- progress both (wrong character)
- make sure to pass in a flag that says we already editted
- else progress
- base case is if we reached the end of one of the strings
- if one reached and the other, return false, else return true;

      private static boolean check(int i, int j, String s, String p, int edits) {
          // go to end, return true
          if (s.length() == i && p.length() == j) {
              return true;
          } else if (s.length() == i && p.length() != j) { // one got to the end
                return false;
          } else if (s.length() != i && p.length() == j) {
                return false;
          } else {
              if (s.charAt(i) == p.charAt(j)) {
                  return check(i+1,j+1,s,p,edits);
              } else if (edits > 0) {
                    return check(i+1,j,s,p,edits-1) || check(i,j+1,s,p,edits-1) || check(i+1,j+1,s,p,edits-1);
              } else {
                  return false;
              }
          }
      }

### Iterative Comparision
**t: O(n) s: O(1)**
  - We have three checks:
    - oneReplace()
      - iterate and see if the strings are the same if one is differentj
    - oneInsert(String a, String b)
        - iterate and see if the strings are the same if one if different only at one stage
          - -than keep the indices going
    - NOTE: We can actually merge these methods together, but might not be readable but technically better code
  - At the start we check the string lengths
    - if (s.length < q.length)
      - that must mean something was deleted
      - return onInsert(s,q)
    - if (s.length > q.length)
      - that must mean something was added
        - return onInsert(q,s) 
        - NOTE: we can swap q,s here because a delete in one is inverse of add in the other
    - if (s.length == q.length)
      - something must be edited
      - return onReplace(s,q)


## 1.6 String Compression
**Q:** There Implement a method to perform basic string compression using the counts
of repeated characters. For example, the string aabcccccaaa would become a2b1c5a3. If the
"compressed" string would not become smaller than the original string, your method should return
the original string. You can assume the string has only uppercase and lowercase letters (a - z).

### While loop skip and counts
**t: O(n) s: O(n)**

- Loop through the array, for each charcter just loop until we dont have any more of that character,keep track ofhow many and append to string builder
- Have a flag that checks for any compression (changed) so we can return the original string or not if no compression has occured

      public static String compress(String s) {
        StringBuilder builder = new StringBuilder();
        int i =0;
        while (i<s.length()) {
            int amount = 0;
                char previous = s.charAt(i);
                amount++;
                builder.append(String.valueOf(previous));
                i++;
                
                while (i <s.length() && s.charAt(i) == previous) {amount++;i++;}
                builder.append(amount);
        }
        String ans = builder.toString();
        return ans.length < s ? ans : s;  // return original if compressed is shorter
      }

## 1.9 String Rotation
**Q**: Assume you have a method isSubstring which checks if one word is a substring
of another. Given two strings, s1 and s2, write code to check if s2 is a rotation of s1 using only one
call to isSubstring (e.g., "waterbottle" is a rotation of"erbottlewat").

### Big Brain Plays
**t: O(1) s: O(1)**
  - if s2 is a rotation of s1, than that MUST mean it will be a substring of s1+s1
    - Example:
    - s1 = waterbottle
    - s2 = erbottlewat
    - s1+s1 = wat|erbottlewat|erbottle
  
          public static boolean isRotation(String a, String b) {
              if (a.length() == b.length()) {
                    return isSubstring(a +a, b);
              } else {
                  return false;
              }
          }


