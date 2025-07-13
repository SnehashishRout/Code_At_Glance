**1. Dynamic Programming 1D**

**A.** Given an integer array nums, return true if you can partition the array into two subsets such that 
       the sum of the elements in both subsets is equal or false otherwise.

Input: nums = [1,5,11,5]
Output: true
Explanation: The array can be partitioned as [1, 5, 5] and [11].

Input: nums = [1,2,3,5]
Output: false
Explanation: The array cannot be partitioned into equal sum subsets.

Solution : The Best way to approach this is to observe that the problem can be reduced to check if a subsequence sum can be equal to (Array_sum / 2)
So the brute force approach will be to traverse the whole array and check for each one if the target sum can be achieved by including the element
or by no including the element. So the Problem becomes O(2^n) complexity.

The above can be optimized by observibg that there are lot of repeating subproblems and we can memoize that. So if a particular sum value can be achieved by a particular value can be achieved by either inlcluding/excluding that element. 
Similarly it can be approached using bottom-up approach by maintaining a 2d dp array with rows as numbers in the array and columns as the possible sums from 0 to total_sum/2 ( = target) .

<pre>class Solution {
public:
    int target = sum / 2;
        int n = nums.size();
        vector<vector<bool>> dp(n + 1, vector<bool>(target + 1, false));
        
        for (int i = 0; i <= n; i++) {
            dp[i][0] = true; 
        }

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= target; j++) {
                if (nums[i - 1] <= j) {
                    dp[i][j] = dp[i - 1][j] || 
                               dp[i - 1][j - nums[i - 1]];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
                       
        return dp[n][target];
}; </pre>        

