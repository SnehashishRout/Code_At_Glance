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
**2. Jump Game I**
You are given an integer array nums. You are initially positioned at the array's first index, and each element in the array represents your maximum jump length at that position.  
Return true if you can reach the last index, or false otherwise.

Input: nums = [2,3,1,1,4]  
Output: true  
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.

Input: nums = [3,2,1,0,4]  
Output: false  
Explanation: You will always arrive at index 3 no matter what. Its maximum jump length is 0, which makes it impossible to reach the last index.  

Soluntion : This can be appraoched using DP as we can go on exploring all possible scenarios. The DP solution :

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        vector<int> dp(nums.size(), -1);
        return dfs(nums, 0, dp);
    }

    bool dfs(vector<int>& nums, int i, vector<int> &dp) {
        if(i == nums.size() - 1)
            return true;
        if(i >= nums.size())
            return false;
        
        if(dp[i] != -1)
            return dp[i];

        bool res = false;
        for(int j=1; j<=nums[i]; j++){
            if(j + i <= nums.size()-1)
                res = res || dfs(nums, j + i, dp);
        }

        return dp[i] = res;
    }
};
```
But we can go on with a greedy appraoch where we start from the end and see if we can reach it from any previous index and if we are then our goal becomes that index and see if we can reach the goal using any previous index and so on. So our goal keep shifiting and if our goal becomes the starting index that means we can reach the last index from the start.

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int goal = nums.size() - 1;
        for(int i = nums.size() - 2; i>=0 ; i--){
            if( i + nums[i] >= goal)
                goal = i;
        }
        return goal == 0;
    }
};
```
**3. Jump Game II**
You are given a 0-indexed array of integers nums of length n. You are initially positioned at index 0.  
Each element nums[i] represents the maximum length of a forward jump from index i. In other words, if you are at index i, you can jump to any index (i + j) where:
0 <= j <= nums[i] and  
i + j < n
Return the minimum number of jumps to reach index n - 1. The test cases are generated such that you can reach index n - 1.  
Input: nums = [2,3,1,1,4]  
Output: 2  
Explanation: The minimum number of jumps to reach the last index is 2. Jump 1 step from index 0 to 1, then 3 steps to the last index.  

Input: nums = [2,3,0,1,4]  
Output: 2

Approach : This problem can be approcahed in DP way where we keep on exploring all possibilites and keep track of minimum in the way.

```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        vector<int> dp(nums.size(), -1);
        return dfs(nums, 0, dp);
    }

    int dfs(vector<int>& nums, int i, vector<int> &dp) {
        if(i == nums.size() - 1)
            return 0;
        if(dp[i] != -1)
            return dp[i];

        int res = INT_MAX - 1;
        for(int j = 1; j<=nums[i]; j++){
            if(i + j < nums.size())
                res = min(res, dfs(nums, i + j, dp) + 1);
        }
        return dp[i] = res;
    }
};
```

But this is O(N^2) Time Complexity and usinf Greedy Algorithm we can reduce it to O(N). Here we can see that when we start from Origin we can see that when we take maximum jump we can reach all the positions within that max jump position and thus we can keep a jump count of all those that come within that range. So we maintain a range where we have a common jump count and then traverse each positon and go on calculating the maximum we can reach from any of the positions within that range and that becomes the farthest end of the next range. And this way we can continue until farthest reachable limit of any range becomes equal to last index. 

```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        int l=0, r=0;
        int jump = 0;
        while(r < nums.size() - 1) {
            int farthest = 0;
            for(int i=l; i<=r; i++){
                farthest = max(farthest, i + nums[i]);
            }
            l = r + 1;
            r = farthest;
            jump ++;
        }
        return jump;
    }
};
```
