**3. Pacific Atlantic Water Flow**  
There is an m x n rectangular island that borders both the Pacific Ocean and Atlantic Ocean. The Pacific Ocean touches the island's left and top edges, and the Atlantic Ocean touches the island's right and bottom edges. The island is partitioned into a grid of square cells. You are given an m x n integer matrix heights where heights[r][c] represents the height above sea level of the cell at coordinate (r, c).  
The island receives a lot of rain, and the rain water can flow to neighboring cells directly north, south, east, and west if the neighboring cell's height is less than or equal to the current cell's height. Water can flow from any cell adjacent to an ocean into the ocean.
Return a 2D list of grid coordinates result where result[i] = [ri, ci] denotes that rain water can flow from cell (ri, ci) to both the Pacific and Atlantic oceans.  

For Visual TCs : [Leetcode Link](https://leetcode.com/problems/pacific-atlantic-water-flow/description/)  

Approach : The most intuitive way to solve it is for each cell run dfs and explore all directions to see which all Oceans you are able to reach. If its both Pacific and Atlantic then its one of your answer. The Time complexity for this solution is 
O((m*n)^2) but you can see there are a lot of repeating subproblems as we explore same path multiple times for different cell and all we want to know if we can reach atlantic or pacific from that cell though any possible path.
So immediate intuition is to use DP to store the results and not repeat them. But for Leetcode TCs are so desgined that even for them it throws TLE. So a more optimized way is to traverse from destination rather than from source.
So we traverse either dfs or bfs from the both Atlantic and Pacific and keep a note of all the cells through which water can flow in reverse direction from Pacific or Atlantic. Now once we have each cell marked all we need to do is 
find out all the cells that are common or the ones which can be reached from Pacific and Atlantic both. This way we only traverse each cell only once : one for Pacific and one for Atlantic. 

Normal Cell waise DFS with DP optimized : 
```cpp
class Solution {
public:
    vector<vector<int>> direc = {{-1, 0}, {1,0}, {0,-1}, {0,1}};
    vector<vector<int>> pacificAtlantic(vector<vector<int>>& heights) {
        vector<vector<bool>> vis(heights.size(), vector<bool>(heights[0].size(), false));
        vector<bool> water = {false, false};
        vector<vector<int>> path;
        vector<vector<int>> res;
        map<pair<int,int>, int> dpp;
        map<pair<int,int>, int> dpa;
        for(int i=0; i<heights.size(); i++) {
            for(int j=0; j<heights[0].size(); j++){
                water[0] = false; water[1] = false;
                dfs(heights, vis, water, path, res, i, j, dpp, dpa);
                if(water[0] && water[1]) {
                    res.push_back({i, j});
                }
                if(water[0])
                    dpp[{i,j}]++;
                if(water[1])
                    dpa[{i,j}]++;
            }
        }
        return res;
    }

    void dfs(vector<vector<int>>& heights, vector<vector<bool>> vis, vector<bool> &water, vector<vector<int>> path, vector<vector<int>>& res, int i, int j,
            map<pair<int,int>, int>& dpp, map<pair<int,int>, int>& dpa) {
        vis[i][j] = true;
        if(dpa.find({i,j}) != dpa.end() || dpp.find({i,j}) != dpp.end()) {
            if(dpa.find({i,j}) != dpa.end())
                water[1] = true;
            if(dpp.find({i,j}) != dpp.end())
                water[0] = true;
            return;
        }

        for(vector<int> dir : direc) {
            int r = i + dir[0];
            int c = j + dir[1];
            if(r < 0 || c < 0) {
                water[0] = true;
            } else if (r == heights.size() || c == heights[0].size()) {
                water[1] = true;
            } else if(!vis[r][c] && heights[r][c] <= heights[i][j]) {
                dfs(heights, vis, water, path, res, r, c, dpp, dpa);
            }
        }
    }
};
```

From Destination to Source Logic :
```cpp
class Solution {
public:
    vector<vector<int>> direc = {{-1, 0}, {1,0}, {0,-1}, {0,1}};
    vector<vector<int>> pacificAtlantic(vector<vector<int>>& heights) {
        vector<vector<int>> res;
        map<pair<int,int>, int> hpp;
        map<pair<int,int>, int> hpa;
        for(int i=0; i<heights.size(); i++) {
            dfs(heights, i, 0, hpp);
            dfs(heights, i, heights[0].size()-1, hpa);
        }
        for(int j=0; j<heights[0].size(); j++) {
            dfs(heights, 0, j, hpp);
            dfs(heights, heights.size()-1, j, hpa);
        }

        for(int i=0; i<heights.size(); i++) {
            for(int j=0; j<heights[0].size(); j++) {
                if(hpp.find({i,j}) != hpp.end() && hpa.find({i,j}) != hpa.end()) {
                    res.push_back({i,j});
                }
            }
        }
        return res;
    }

    void dfs(vector<vector<int>>& heights, int i, int j, map<pair<int,int>, int>& hm) {
        if(hm.find({i,j}) != hm.end())
            return;
        hm[{i,j}]++;
        for(vector<int> dir : direc) {
            int r = i + dir[0];
            int c = j + dir[1];
            if(r >= 0 && c >= 0 && r < heights.size() && c < heights[0].size() &&
                heights[r][c] >= heights[i][j]) {
                dfs(heights, r, c, hm);
            }
        }
    }
};
```
