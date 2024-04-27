# Problem
Write a Java function that reverses a given string without using any in-built reverse functions.

For example:  
Input: "Hello"  
Output: "olleH"

This problem can be approached in various ways using Java. Good luck!

# Solution

```
    public static String reverseString(String str){
        return new StringBuilder(str).reverse().toString();
    }
```

or

```
public static String reverseString(String str){
        char[] chars = str.toCharArray();
        StringBuilder sb = new StringBuilder();
        for(int i = chars.length - 1; i >= 0 ; i--){
            sb.append(chars[i]);
        }

        return sb.toString();
    }
```