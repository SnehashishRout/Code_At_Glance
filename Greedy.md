**Greedy Algo**

**1.** Given a string s containing only three types of characters: '(', ')' and '*', return true if s is valid.

The following rules define a valid string:

Any left parenthesis '(' must have a corresponding right parenthesis ')'.
Any right parenthesis ')' must have a corresponding left parenthesis '('.
Left parenthesis '(' must go before the corresponding right parenthesis ')'.
'*' could be treated as a single right parenthesis ')' or a single left parenthesis '(' or an empty string "".

Input: s = "()"  
Output: true  
Example 2:

Input: s = "(*)"  
Output: true  
Example 3:

Input: s = "(*))"  
Output: true

Solution : The problem is just a variant of the string containing ( and ) and need to find the valid one. All we do is maintain a count of ( and increment it by if we encounter '(' and decrement if ')'. We return false immediately if we encounter a ')' when count is 0 or another way to say it is if anytime count becomes negative which denotes there is ')' for which there was no previous '('.
But the current condition that * can be '(' or ')' or empty brings out that we need to explore all 3 scenarios when we encounter * so it is becomes a recursive problem which can be optimized by DP Memoization. 
But this problem specifically can be optimized using Greedy also. So we know count can vary in 3 possible directions for * depending upon what do we choose it to be. So instead of single value we can maintain a range of values which can count can have. So we maintain 2 variables , lmin and lmax that is the min and max values count can have while iterating a particualr index of string. So if [lmin, lmax] = [0, 2] it means till that point in the string count can have any vsalue from 0 to 2.
So we follow simple rules :
1. If s[i] == '(' we increment the lmin,lmax by 1
2. If s[i] == ')' we decrement the lmin,lmax by 1
3. if s[i] == '*' we decrement lmin by 1 and increment lmax by 1 because * can be ')' in which case the minimum value can be one less than the current one and it can also be '(' so max value can be 1 more than the current lmax.
4. At any point if our lmin becomes -ve we make lmin = 0 because we cant have lmin -ve and this -ve scenario occurs if ')' is more than '(' which can never be a valid string.
5. At any point if our lmin becomes -ve we return false which means we are considering a value of * which is ')' and that is inturn making the whole expression invalid as no. of ')' exceeds '(' .

Memoized Recursive Soln :
```cpp
class Solution {
public:
    bool checkValidString(string s) {
        int n = s.size();
        vector<vector<int>> memo(n + 1, vector<int>(n + 1, -1));
        return dfs(s, 0, 0, memo);
    }

    bool dfs(string& s, int i, int open, vector<vector<int>>& memo) {
        if (open < 0) return false; // More ')' than '('
        if (i == s.length()) return open == 0;

        if (memo[i][open] != -1) return memo[i][open];

        bool res = false;

        if (s[i] == '(') {
            res = dfs(s, i + 1, open + 1, memo);
        } else if (s[i] == ')') {
            res = dfs(s, i + 1, open - 1, memo);
        } else { // '*'
            res = dfs(s, i + 1, open + 1, memo) ||  // Treat as '('
                  dfs(s, i + 1, open - 1, memo) ||  // Treat as ')'
                  dfs(s, i + 1, open, memo);        // Treat as ''
        }

        memo[i][open] = res;
        return res;
    }
};
```

Greedy Approach :

```cpp
public:
    bool checkValidString(string s) {
        int lmin = 0, lmax = 0;
        for(int i=0; i<s.length(); i++) {
            if(s[i] == '(') {
                lmin++;
                lmax++;
            } else if(s[i] == ')') {
                lmin--;
                lmax--;
            } else {
                lmin--;
                lmax++;
            }
            if(lmin < 0)
                lmin = 0;
            if(lmax < 0)
                return false;
        }
        return lmin == 0;
    }
```
