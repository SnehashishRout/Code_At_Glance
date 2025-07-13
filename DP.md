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

class Solution {
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
};         

**B.House Robber** Given an integer array nums representing the amount of money of each house, return the maximum amount of money you can rob by not robbing from adjacent houses. 

Solution : The Logic should be pretty simple by keeping a DP array and at each index representing the maximum that can be robbed till that position of the house (either including or excluding the house). So at each position we check the (i-2)th postition dp value, and add the money value of the current house and compare it with the (i-1)th house dp value and store which one is greater.
In that way the end of the dp cache should give us the maximum we can rob with the above constraints.


       int rob(vector<int>& nums) {

        for(int i=1; i<nums.size(); i++){
            if(i-2>=0)
                nums[i] = max(nums[i] + nums[i-2], nums[i-1]);
            else
                nums[i] = max(nums[i], nums[i-1]);
        }
        return nums[nums.size()-1];
    } 


**B.House Robber II** This is one is same as the above one with a slight variation and that is the houses are kept in circular position which makes the last and first house as the adjacent neighbours.

Solution : So we notice that since 1st and Last houses are adjacent, we can't have an answer where the 1st and last houses have been robbed. So we break into two separate House Robber I problems, i.e. consider two arrays from [0:n-2] and [1:n-1] , where n is the number of Houses. So in one of the arrays Last house is removed and on another 1st house has been removed, and we just find the solutions for both and return the maximum of both.

**C.Longest Palindromic Substring** 
Given a string s, return the longest palindromic substring in s.

Solution: This problem can be easily solved if we observe that the condition for an substring [i:j] is palindrome if s[i] == s[j]
and the substring [i+1:j-1] is a palindrome. 
So there can be easy recursive solution where we recursively check for [i+1:j-1] is palindrome or not if s[i] == s[j]. The steps can be memoized using dp[i][j] to have optimal solution. 
Similarly a Bottom Up appraoch can also be conducted.

**D.Longest Common Subsequence**
Given two strings text1 and text2, return the length of their longest common subsequence. If there is no common subsequence, return 0.

Input: text1 = "abcde", text2 = "ace" 
Output: 3  
Explanation: The longest common subsequence is "ace" and its length is 3.

Solution : This problem can be analyed in a way that every time a character matches in both strings, we ask give me the LCS from the remaining substring of s1 and s2 and then add 1 to it and if the characters dont match we consider the maximum of LCS of the reminaing substring of (s1 after that character and s2 ) and (s2 after that character and s1). 

       for(int i=1; i<text1.length()+1; i++){
            for(int j=1; j<text2.length()+1; j++){
                if(text1[i-1] == text2[j-1]){
                    dp[i][j] = max(1 + dp[i-1][j-1], max(dp[i-1][j], dp[i][j-1]));
                } else
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
        return dp[text1.length()][text2.length()];


