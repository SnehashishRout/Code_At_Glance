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

**D.Unique Paths**
There is a robot on an m x n grid. The robot is initially located at the top-left corner (i.e., grid[0][0]). The robot tries to move to the bottom-right corner (i.e., grid[m - 1][n - 1]). The robot can only move either down or right at any point in time.
Return the number of possible unique paths that the robot can take to reach the bottom-right corner.

To approcah this problem we all have to traverse the grid and for each cell position we need to make count in how many possible ways can we reach the destination if start from that particular cell. So we just need to need to recursive call for dp[x+1[y] and dp[x][y+1] i.e one cell below and one cell to right and we add those values to get possible values for current cell.

Memoized Approach :

    int uniquePaths(int m, int n) {
        vector<vector<int>> dp(m, vector<int>(n, -1));
        return totalUniquePaths(0, 0, m, n, dp);
    }

    int totalUniquePaths(int x, int y, int &m, int &n, vector<vector<int>> &dp){
        if(x == m-1 && y == n-1)
            return 1;
        if(x >= m || y>= n)
            return 0;
        
        if(x+1 < m && dp[x+1][y] != -1)
            dp[x][y] = dp[x+1][y];
        else
            dp[x][y] = totalUniquePaths(x+1, y, m, n, dp);
        
        if(y+1 < n && dp[x][y+1] != -1)
            dp[x][y] += dp[x][y+1];
        else
            dp[x][y] += totalUniquePaths(x, y+1, m, n, dp);

        return dp[x][y];
    }

Top-Down Approcah :
Writing this approach because while Bottom-Up appraoch/ Table method can be aimplemented in the above case also, there one more way to solve the problem in reverse direction. Instead of calculating how many possible ways are to reach the bottom-right bu starting at a particualr cell, we can instead think of calculating as how many ways can I reach that particular cell if I start from top left corner. So in this way it becomes easy to implement using table method. We just need to add dp[i-1][j] and dp[i][j-1].


    int uniquePaths(int m, int n) {
        vector<vector<int>> dp(m, vector<int>(n, 0));
        for(int i=0; i<m; i++)
            dp[i][0] = 1;
        for(int j=0; j<n; j++)
            dp[0][j] = 1;
        
        for(int i=1; i<m; i++){
            for(int j=1; j<n; j++){
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }

        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                cout<<dp[i][j]<<" ";
            }
            cout<<endl;
        }

        return dp[m-1][n-1];
    }

**D.Best Time to Buy and Sell Stock with Cooldown**
You are given an array prices where prices[i] is the price of a given stock on the ith day.
Find the maximum profit you can achieve. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times) with the following restrictions:
After you sell your stock, you cannot buy stock on the next day (i.e., cooldown one day).
Note: You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

Input: prices = [1,2,3,0,2]
Output: 3
Explanation: transactions = [buy, sell, cooldown, buy, sell]

Input: prices = [1]
Output: 0

Solution : This problem can be thought of as for each price, we have two choices either we buy/sell or skip for that day. Buy or sell can be applied to all the prices but it is conditional. We can buy a stock only if we have sold the previous stock and we are not holding any stock, and similarly we can sell a stock only if we have bought a stock already and holding it. So it is situation dependent. So when we construct the solution we can see the solution tree branches out in binary tree and thus we have 2 options for every price, either buy/sell or skip. Thus we can memoize it since we have a lot of repeating subproblems and dont have to recurse for a situation that is already there.

    int maxProfit(vector<int>& prices) {
        vector<vector<int>> dp(prices.size(), vector<int>(2, -1));
        return maxProf(prices, 0, 1, dp);
    }

    int maxProf(vector<int>& prices, int ind, int isBuy, vector<vector<int>> &dp) {
        if(ind >= prices.size()) {
            return 0;
        }

        if(dp[ind][isBuy] != -1)
            return dp[ind][isBuy];

        int max_from_buy = 0, max_from_sell = 0, max_from_skip = 0;

        if(isBuy) {
            max_from_buy = maxProf(prices, ind+1, false, dp) - prices[ind];
            max_from_skip = maxProf(prices, ind+1, isBuy, dp);
            dp[ind][isBuy] = max(max_from_buy, max_from_skip);
        } else {
            max_from_sell = maxProf(prices, ind+2, true, dp) + prices[ind];
            max_from_skip = maxProf(prices, ind+1, isBuy, dp);
            dp[ind][isBuy] = max(max_from_sell, max_from_skip);
        }
        return dp[ind][isBuy];
    }

**D.Coin Change**
You are given an integer array coins representing coins of different denominations and an integer amount representing a total amount of money.
Return the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.
You may assume that you have an infinite number of each kind of coin.

Input: coins = [1,2,5], amount = 11
Output: 3
Explanation: 11 = 5 + 5 + 1

Solution : You can think of it as every coin has 2 choices either to included or not, and if it is to be included or not included. So at every stage your answer is sum of the least number of coins to be used to form the remaining amount + least no. of coins for the same amt. for the rest of the pile. 
<pre>
       int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount+1, amount+1);
        dp[0] = 0;
        for(int i=1; i<=amount; i++){
            for(int j=0; j<coins.size(); j++){
                if(i - coins[j] >= 0){
                    dp[i] = min(dp[i], 1 + dp[i - coins[j]]);
                }
            }
        }
        return dp[amount] != amount+1 ? dp[amount] : -1;
    }
</pre>
  
**D.Coin Change II**
You are given an integer array coins representing coins of different denominations and an integer amount representing a total amount of money.
Return the number of combinations that make up that amount. If that amount of money cannot be made up by any combination of the coins, return 0.
You may assume that you have an infinite number of each kind of coin.

Input: amount = 5, coins = [1,2,5]
Output: 4
Explanation: there are four ways to make up the amount:
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1

Solution : This problem can be thought of as for each coin we have two choices either to include that coin or not include. So the total number of ways of forming an amount if the array starts from that coin, is addition of no. of ways by including  and  no. of ways by not-including that coin. But we have to remember that if we are including that coin, we should not just move to the next coin but rather stay at the same one because it is mentioned we can have as many number of a particular coin as possible. So after memoizing the sub problems we get the below : 

    int change(int amount, vector<int>& coins) {
        vector<vector<int>> dp(coins.size(), vector<int>(amount+1, -1));
        return dfs(amount, coins, 0, dp);
    }

    int dfs(int amount, vector<int>& coins, int i, vector<vector<int>> &dp){
        if(amount == 0)
            return 1;
        if(amount < 0 || i == coins.size())
            return 0;
        
        if(dp[i][amount] != -1)
            return dp[i][amount];
        
        return dp[i][amount] = dfs(amount, coins, i+1, dp) + dfs(amount - coins[i], coins, i, dp);
    }

Similarly the Tabular approach can be as follows : 


       int change(int amount, vector<int>& coins) {
        vector<vector<int>> dp(coins.size()+1, vector<int>(amount+1, 1));
        int res = 0;
        for(int j=1; j<=amount; j++)
            dp[0][j] = 0;

        for(int i=1; i<=coins.size(); i++){
            for(int j=1; j<=amount; j++){
                if(j - coins[i-1] >= 0){
                    dp[i][j] = dp[i-1][j] + dp[i][j - coins[i-1]];
                } else
                    dp[i][j] = dp[i-1][j];
            }
        }
        return dp[coins.size()][amount];
    }

**Longest Increasing Subsequence**
Given an integer array nums, return the length of the longest strictly increasing subsequence.

Input: nums = [10,9,2,5,3,7,101,18]
Output: 4
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.

Solution : A simple approach to the solution can be thought of as we traverse every element in the array and for every element we try to compute what would be the Longest Increasing Subsequence if that were the starting element of the sequence. And for that we traverse for every element in the rest of the array in the right and that is in increasing sequence. And we keep on keeping a check on the longest we achieve so far.

class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> dp(nums.size(), 1);
        dp[nums.size()-1] = 1;
        int res = 1;
        for(int i=nums.size()-2; i>=0; i--){
            for(int j=i+1; j<nums.size(); j++){
                if(nums[i] < nums[j])
                    dp[i] = max(dp[i], 1 + dp[j]);
            }
            res = max(res,dp[i]);
        }
        return res;
    }
}
                                   
