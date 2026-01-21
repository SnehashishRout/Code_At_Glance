**A.** Given string num representing a non-negative integer num, and an integer k, return the smallest possible integer after removing k digits from num.

```cpp
Example 1:

Input: num = "1432219", k = 3
Output: "1219"
Explanation: Remove the three digits 4, 3, and 2 to form the new number 1219 which is the smallest.
Example 2:

Input: num = "10200", k = 1
Output: "200"
Explanation: Remove the leading 1 and the number is 200. Note that the output must not contain leading zeroes.
Example 3:

Input: num = "10", k = 2
Output: "0"
Explanation: Remove all the digits from the number and it is left with nothing which is 0.
```

Solution : To make the number as small as possible, we want smaller digits to appear as early as they can. 
If a digit is larger than a digit that comes after it, keeping the larger digit will always make the number worse, 
because higher-place values matter more than later ones. 
Therefore, whenever we see a smaller digit to the right of a larger one, it is always optimal to remove the 
larger digit (as long as we still have removals left).
Thus we infer two things :
  * If a larger digit appears before a smaller one, keeping the larger digit will always make the number worse
  * Removing it early gives the smallest possible prefix

This intuition naturally leads to a greedy strategy using a monotonic increasing stack. As we scan the number from left to right, 
we maintain digits in increasing order. When the current digit is smaller than the top of the stack and we can still remove digits, 
we pop from the stack to eliminate the harmful larger digit. After processing all digits, if removals remain, we delete digits from the 
end since they contribute the least to making the number smaller. 
Finally, we remove leading zeros and return the result, or "0" if nothing remains.

```cpp
class Solution {
public:
    string removeKdigits(string num, int k) {
        stack<char> stk;
        stk.push(num[0]);
        int i = 1;
        while (i < num.length()) {
            while (!stk.empty() && num[i] < stk.top() && k > 0) {
                stk.pop();
                k--;
            }
            stk.push(num[i]);
            i++;
        }

        // we need to remove all the k digits so we remove the rest 
        // digits incase K is not 0 yet
        
        while (k > 0 && !stk.empty()) {
            stk.pop();
            k--;
        }

        string res = "";
        while (!stk.empty()) {
            res.push_back(stk.top());
            stk.pop();
        }
        reverse(res.begin(), res.end());

        i = 0;
        while (i < res.length() && res[i] == '0') {
            i++;
        }

        if (i == res.length())
            return "0";

        return res.substr(i);
    }
};
```
