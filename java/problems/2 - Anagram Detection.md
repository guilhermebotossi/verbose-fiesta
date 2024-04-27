# Problem
Write a function in Java that takes in two strings and determines whether they are anagrams of each other. Anagrams are words or phrases formed by rearranging the letters of another, such as "listen" and "silent".

For example:  
Input: "listen", "silent"  
Output: true

Input: "hello", "world"  
Output: false

Consider how you might approach this problem by comparing the characters in the strings. Good luck!

# Solution

```
public class Main {

    public static void main(String[] args) {
        System.out.println(checkForAnagram("hello", "holel")); 
    }

    public static boolean checkForAnagram(String str1, String str2){
        Map<Character, Integer> frequenciesStr1 = new HashMap<>();
        Map<Character, Integer> frequenciesStr2 = new HashMap<>();

        char[] chars1 = str1.toCharArray();
        char[] chars2 = str2.toCharArray();

        for(int i = 0; i < str1.length(); i++){
            frequenciesStr1.computeIfAbsent(chars1[i], k -> 0);
            frequenciesStr1.put(chars1[i], frequenciesStr1.get(chars1[i]) + 1);
 
            frequenciesStr2.computeIfAbsent(chars2[i], k -> 0);
            frequenciesStr2.put(chars2[i], frequenciesStr2.get(chars2[i]) + 1);
        }
        System.out.println(frequenciesStr1);
        System.out.println(frequenciesStr2);


        return frequenciesStr1.equals(frequenciesStr2);
    }
   
    
}
```