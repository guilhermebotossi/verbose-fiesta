# Problem

A left rotation operation on an array shifts each of the array's elements unit to the left. For example, if left rotations are performed on array , then the array would become . Note that the lowest index item moves to the highest index in a rotation. This is called a circular array.

Given an array of integers and a number, , perform left rotations on the array. Return the updated array to be printed as a single line of space-separated integers.

Function Description

Complete the function rotLeft in the editor below.

rotLeft has the following parameter(s):

int a[n]: the array to rotate  
int d: the number of rotations

# Solution

```
    public static List<Integer> rotLeft(List<Integer> a, int rotations) {
        int size = a.size();
        int steps = rotations % size;
        List<Integer> rotatedList = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            int rotatedIndex = (i - steps + n) % n;// Circular Array Rotation
            rotatedList.add(a.get(rotatedIndex));
        }

        return rotatedList;
    }
```
