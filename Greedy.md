**Greedy Algo**

**1. Valid Parenthesis** Given a string s containing only three types of characters: '(', ')' and '*', return true if s is valid.

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

**4. Job Sequencing**  
You are given a set of N jobs where each job comes with a deadline and profit. The profit can only be earned upon completing the job within its deadline. Find the number of jobs done and the maximum profit that can be obtained. Each job takes a single unit of time and only one job can be performed at a time.  

Example 1:  
Input: N = 4, Jobs = {(1,4,20),(2,1,10),(3,1,40),(4,1,30)}  
Output: 2 60  
Explanation: The 3rd job with a deadline 1 is performed during the first unit of time .The 1st job is performed during the second unit of time as its deadline is 4. Profit = 40 + 20 = 60  
Example 2:  
Input: N = 5, Jobs = {(1,2,100),(2,1,19),(3,2,27),(4,1,25),(5,1,15)}  
Output: 2 127  
Explanation: The  first and third job both having a deadline 2 give the highest profit. Profit = 100 + 27 = 127

Approach : The strategy to maximize profit should be to pick up jobs that offer higher profits. Hence we should sort the jobs in descending order of profit. Now say if a job has a deadline of 4 we can perform it anytime between day 1-4, but it is preferable to perform the job on its last day. This leaves enough empty slots on the previous days to perform other jobs.

* Sort the jobs in descending order of profit. 
* If the maximum deadline is x, make an array of size x .Each array index is set to -1 initially as no jobs have been performed yet.
* For every job check if it can be performed on its last day.
* If possible mark that index with the job id and add the profit to our answer. 
* If not possible, loop through the previous indexes until an empty slot is found.

```cpp
struct Job {
   int id; // Job Id 
   int dead; // Deadline of job 
   int profit; // Profit if job is over before or on deadline 
};
class Solution {
   public:
      bool static comparison(Job a, Job b) {
         return (a.profit > b.profit);
      }
   //Function to find the maximum profit and the number of jobs done
   pair < int, int > JobScheduling(Job arr[], int n) {

      sort(arr, arr + n, comparison);
      int maxi = arr[0].dead;
      for (int i = 1; i < n; i++) {
         maxi = max(maxi, arr[i].dead);
      }

      int slot[maxi + 1];

      for (int i = 0; i <= maxi; i++)
         slot[i] = -1;

      int countJobs = 0, jobProfit = 0;

      for (int i = 0; i < n; i++) {
         for (int j = arr[i].dead; j > 0; j--) {
            if (slot[j] == -1) {
               slot[j] = i;
               countJobs++;
               jobProfit += arr[i].profit;
               break;
            }
         }
      }

      return make_pair(countJobs, jobProfit);
   }
};
int main() {
   int n = 4;
   Job arr[n] = {{1,4,20},{2,1,10},{3,2,40},{4,2,30}};

   Solution ob;
   //function call
   pair < int, int > ans = ob.JobScheduling(arr, n);
   cout << ans.first << " " << ans.second << endl;

   return 0;
} 
```

**5. Minimum Number of Platform**  
Problem Statement: We are given two arrays that represent the arrival and departure times of trains that stop at the platform. We need to find the minimum number of platforms needed at the railway station so that no train has to wait.  

Examples 1:  
Input: N=6,   
arr[] = {9:00, 9:45, 9:55, 11:00, 15:00, 18:00}   
dep[] = {9:20, 12:00, 11:30, 11:50, 19:00, 20:00}  
Output:3  
Explanation: There are at-most three trains at a time. The train at 11:00 arrived but the trains which had arrived at 9:45 and 9:55 have still not departed. So, we need at least three platforms here.  

Approach : [Striver's Explaination](https://www.youtube.com/watch?v=AsGzwR_FWok&list=PLgUwDviBIf0rF1w2Koyh78zafB0cz7tea&index=10)

```cpp
int countPlatforms(int n,int arr[],int dep[])
 {
    sort(arr,arr+n);
    sort(dep,dep+n);
 
    int ans=1;
    int count=1;
    int i=1,j=0;
    while(i<n && j<n)
    {
        if(arr[i]<=dep[j]) //one more platform needed
        {
            count++;
            i++;
        }
        else //one platform can be reduced
        {
            count--;
            j++;
        }
        ans=max(ans,count); //updating the value with the current maximum
    }
    return ans;
 }
 
 int main()
 {
    int arr[]={900,945,955,1100,1500,1800};
    int dep[]={920,1200,1130,1150,1900,2000};
    int n=sizeof(dep)/sizeof(dep[0]);
    cout<<"Minimum number of Platforms required "<<countPlatforms(n,arr,dep)<<endl;
 }
```

**6. Candy**  
There are n children standing in a line. Each child is assigned a rating value given in the integer array ratings.  
You are giving candies to these children subjected to the following requirements:

 * Each child must have at least one candy.
 * Children with a higher rating get more candies than their neighbors.  
 
Return the minimum number of candies you need to have to distribute the candies to the children.  

Input: ratings = [1,0,2]  
Output: 5  
Explanation: You can allocate to the first, second and third child with 2, 1, 2 candies respectively.

Input: ratings = [1,2,2]  
Output: 4  
Explanation: You can allocate to the first, second and third child with 1, 2, 1 candies respectively.
The third child gets 1 candy because it satisfies the above two conditions.

Approach : 
So the idea is to have to check the neighbour and if current rating is more than the neighbour we increment 1 from the previous neighbour rating and if it is less or equal we make it 1 because we want minimum number of Candies. So since we need to check both left and right neigbours we maintain two arrays left and right to track minimum number of candies for each child if we consider left neighbour and right neigbour respectively.
Once we have that we can just take maximum of the two left and right arrays at each position and then thats it. That will be the minimum number of candies required for each child. After that we just need to sum up all the candies and we are done.

```cpp
class Solution {
public:
    int candy(vector<int>& ratings) {
        vector<int> left(ratings.size(), 1);
        vector<int> right(ratings.size(), 1);

        for(int i=1; i<ratings.size(); i++) {
            if(ratings[i-1] < ratings[i])
                left[i] = left[i-1] + 1;
            else
                left[i] = 1;
        }

        for(int i=ratings.size() - 2; i>=0; i--) {
            if(ratings[i+1] < ratings[i])
                right[i] = right[i+1] + 1;
            else
                right[i] = 1;
        }
        
        int sum = 0;
        for(int i=0; i<ratings.size(); i++)
            sum += max(left[i], right[i]);
        return sum;
    }
};
```

