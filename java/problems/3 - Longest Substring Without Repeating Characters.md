# Problem

Write a function in Java that takes a string as input and returns the length of the longest substring without repeating characters.

For example:

Input: "abcabcbb"  
Output: 3 (the longest substring without repeating characters is "abc")  
Input: "bbbbb"  
Output: 1 (the longest substring without repeating characters is "b")  
Try to devise an efficient algorithm that can find the length of the longest substring without repeating characters for any given input string. Good luck!

# Solution

```
    public static int findLongestSubstringWithoutRepeatingChars(String str){
        Set<Character> uniqueChars = new HashSet<>();
        int maxLength = 0;
        int start = 0;
        int end = 0;

        char[] chars = str.toCharArray();

        while (end < chars.length) {
            if (!uniqueChars.contains(chars[end])) {
                uniqueChars.add(chars[end]);
                maxLength = Math.max(maxLength, end - start + 1);
                end++;
            } else {
                uniqueChars.remove(chars[start]);
                start++;
            }
        }

        return maxLength;
    }   
```