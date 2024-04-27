# Problem

Write a function in Java that takes an array of integers and a target integer. The function should find two numbers in the array that add up to the target and return their indices. Assume that each input would have exactly one solution, and you may not use the same element twice.

For example:

Input: [2, 7, 11, 15], target = 9  
Output: [0, 1] (Indices of the elements that add up to the target)  
Try to devise an algorithm that can efficiently find the two numbers in the array that sum up to the given target. Good luck!

# Solution

```
    public static String twoSum(int[] arr, int target) {
        Map<Integer, Integer> map = new HashMap<>();

        for (int i = 0; i < arr.length; i++) {
            int complement = target - arr[i];
            if (map.containsKey(complement)) {
                return String.format("[%d,%d]", map.get(complement), i);
            }
            map.put(arr[i], i);
        }

        return "No two elements add up to the target.";
    }
```