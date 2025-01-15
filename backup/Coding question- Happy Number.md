# Desc

Write an algorithm to determine if a number is happy.

A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.

```
Input:19
Output:true
Explanation:

19 is a happy number

    1^2 + 9^2 = 82
    8^2 + 2^2 = 68
    6^2 + 8^2 = 100
    1^2 + 0^2 + 0^2 = 1

Input:5
Output:false
Explanation:

5 is not a happy number

25->29->85->89->145->42->20->4->16->37->58->89
89 appears again.
```

# Memo

What makes this problem interesting is: why the result will not go infinitely large and there loops never ends

## Some intuitive:

1. 3 digits number, the square sum will be no larger than 243
2. starting from 4 digits number, the square sum digits drops
3. If we keep going, it will drop to <= 3 digits

|number | square sum |
|------------|-------------------|
| 9             | 81                 |
| 99          | 162               |
|999         |  243              |
|9999       | 324              |
|99999     | 405              |
|999999   | 486              |

## Math:

Assume $a_1, a_2, \dots a_m$ are m digits, we can prove the squared sum is no larger than n.

$$
\begin{align}
\text{squaredSum}(n) &= \sum_{i=1}^m a_i^2 <= m * 9^2 \\
n = \overline{a_1a_2 \dots a_m} >= 10^{m-1} \\
\lim_{m \to \infty} \frac{\text{squaredSum}(n)}{n} = 0
\end{align}
$$

# Solution

We can use slow fast pointer to find the loop:

```python
public class Solution {
    public int squareSum(int n) {
        int sum = 0;
        while(n > 0){
            int digit = n % 10;
            sum += digit * digit;
            n /= 10;
        }
        return sum;
    }

    public boolean isHappy(int n) {
        int slow = n, fast = squareSum(n);
        while (slow != fast){
            slow = squareSum(slow);
            fast = squareSum(squareSum(fast));
        };
        return slow == 1;
    }
}
```