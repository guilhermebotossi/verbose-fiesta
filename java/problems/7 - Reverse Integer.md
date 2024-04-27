# Problem

Write a function in Java that reverses the digits of an integer. For example:

Input: 123  
Output: 321  
Consider handling both positive and negative integers and take care not to overflow (if the reversed integer exceeds the 32-bit signed integer range). Aim for an efficient solution that reverses the integer without using strings or arrays.

Try devising an algorithm that reverses the digits of an integer while handling both positive and negative numbers. Good luck!

# Solution

```
public int reverseInt(int input) {
    long reversedNum = 0;
    long input_long = input;

                /**
                    -> Modding (%) the input int by 10 will extract off the rightmost digit. example: (1234 % 10) = 4
                    -> Multiplying an integer by 10 will "push it left" exposing a zero to the right of that number, example: (5 * 10) = 50
                    -> Dividing an integer by 10 will remove the rightmost digit. (75 / 10) = 7
                */
    while (input_long != 0) {
        reversedNum = reversedNum * 10 + input_long % 10;
        input_long = input_long / 10;
    }

    if (reversedNum > Integer.MAX_VALUE || reversedNum < Integer.MIN_VALUE) {
        throw new IllegalArgumentException();
    }
    return (int) reversedNum;
}
```