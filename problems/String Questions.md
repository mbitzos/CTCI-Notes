## 1.1 isUnique
**Q**: Implement an algorithm to determine if a string has all unique characters. What if you
cannot use additional data structures?

**A** <br />
**t: O(n) s: O(n**)

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
**A**<br/>
**t: O(n) s: O(1)**

- sort string and loop through, if multiple in same order return false, else it got through:return true
  
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

**A** <br/>
**t: O(n) s: O(n)**
  
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


## 1.3 URLify
**Q:** Write a method to replace all spaces in a string with '%20: You may assume that the string
has sufficient space at the end to hold the additional characters, and that you are given the "true"
length of the string. (Note: If implementing in Java, please use a character array so that you can
perform this operation in place.)

**A:** <br/>
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

### 1.4 Palindrome Permutations
Given a string, write a function to check if it is a permutation of a palindrome.
A palindrome is a word or phrase that is the same forwards and backwards. A permutation
is a rea rrangement of letters. The palindrome does not need to be limited to just dictionary words
NOTE: ignore case and spaces;

**A** <br/>
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

## 1.5 One Away:
**Q:** There are three types of edits that can be performed on strings: insert a character,
remove a character, or replace a character. Given two strings, write a function to check if they are
one edit (or zero edits) away

**A**:<br/>**t:O(3^s) s: O(1)**

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
          if (s.length() == i && p.length() == j) {
              return true;
          } else if (s.length() == i && p.length() != j) {
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

## 1.6 String Compression
**Q:** There Implement a method to perform basic string compression using the counts
of repeated characters. For example, the string aabcccccaaa would become a2b1c5a3. If the
"compressed" string would not become smaller than the original string, your method should return
the original string. You can assume the string has only uppercase and lowercase letters (a - z).

**A**<br/>**t: O(n) s: O(n)**

- Loop through the array, for each charcter just loop until we dont have any more of that character,keep track ofhow many and append to string builder
- Have a flag that checks for any compression (changed) so we can return the original string or not if no compression has occured

    public static String compress(String s) {
    StringBuilder builder = new StringBuilder();
    int i =0;
    boolean changed = false;
    while (i<s.length()) {
        int amount = 0;
            char previous = s.charAt(i);
            amount++;
            builder.append(String.valueOf(previous));
            i++;
            
            while (i <s.length() && s.charAt(i) == previous) {amount++;i++;}
            if (amount > 1) 
                changed = true;
            builder.append(amount);
    }
    return changed ? builder.toString() : s;
    }

## 1.9 String Rotation
**Q**: Assume you have a method isSubst ring which checks if one word is a substring
of another. Given two strings, 51 and 52, write code to check if 52 is a rotation of 51 using only one
call to isSubstring (e.g., "waterbottle" is a rotation of"erbottlewat").

**A**:<br/> **t: O(n^2) s: O(1)**

- Loop through each character in one string, and in that loop do a while (chars are the same, using % to wrap around for the one string)
- if it gets to the end of that while loop we return true, else skip and move on

      public static boolean isSubstring(String s, String q) {
        if (s.equals(q)) return true;
        if (s.length() != q.length()) return false;
        
        int length = s.length();
        for (int i =0;i<length;i++) {
            int j = 0;
            while (j<length&& s.charAt((i+j) % length) == q.charAt(j)) {
                
                j++;
                if (j == length) return true;
            }
        }
        return false;
      }