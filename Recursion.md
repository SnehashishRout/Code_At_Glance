**Recursion**

**Subset II** : Given an integer array nums that may contain duplicates, return all possible subsets (the power set).
The solution set must not contain duplicate subsets. Return the solution in any order.  
Input: nums = [1,2,2]  
Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]  
Input: nums = [0]  
Output: [[],[0]]  

**Solution**  
We just have to understand that when do we face a repeatation of subsets. It is when there are duplicate numbers in the original set. So this can be avoided by skipping the recursive call for the next number if the next number is same as the current one. So this way we can avoid same subsets being repeated. For our sake of simplicity and to be able to figure out the same numbers and skips them, we sort the array so that all the same numbers are grouped adjacently. 

```cpp
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        vector<int> subset;
        dfs(nums, res, subset, 0);
        return res;
    }

    void dfs(vector<int>& nums, vector<vector<int>> &res, vector<int> subset, int i){
            if(i==nums.size())
            {
                res.push_back(subset);
                return;
            }

            subset.push_back(nums[i]);
            dfs(nums, res, subset, i+1);
            subset.pop_back();

            while(i+1 < nums.size() && nums[i] == nums[i+1])
                i++;
            dfs(nums, res, subset, i+1);
    }
};
```
