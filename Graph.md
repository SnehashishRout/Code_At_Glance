**1. Number of Islands**
Given an m x n 2D binary grid grid which represents a map of '1's (land) and '0's (water), return the number of islands.

An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.  

```cpp
Example 1:

Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
Example 2:

Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

Approach : This is one of the standard problems and this just needs the concept of Connected Components in Graph. We do DFS or BFS to find the number of Connected components in the graph.

```cpp
class Solution {
public:
    int x_dir[2] = {-1, 1};
    int y_dir[2] = {-1, 1};
    void dfs(int i, int j, vector<vector<bool>> &vis, vector<vector<char>>& grid) {
        vis[i][j] = true;
        for(int x : x_dir) {
            if(i + x < grid.size() && i + x >= 0 && !vis[i + x][j] && grid[i + x][j] == '1')
                dfs(i + x, j, vis, grid);
        }

        for(int y : y_dir) {
            if(j + y < grid[0].size() && j + y >= 0 && !vis[i][j + y] && grid[i][j + y] == '1')
                dfs(i, j + y, vis, grid);
        }
    }
    int numIslands(vector<vector<char>>& grid) {
        int cnt = 0;
        vector<vector<bool>> vis(grid.size(), vector<bool>(grid[0].size(), false));
        for(int i=0; i<grid.size(); i++) {
            for(int j=0; j<grid[0].size(); j++) {
                if(grid[i][j] == '1' && !vis[i][j]){
                    dfs(i, j, vis, grid);
                    cnt++;
                }
            }
        }
        return cnt;
    }
};
```

**2. Clone Graph**  
Given a reference of a node in a connected undirected graph.
Return a deep copy (clone) of the graph.
Each node in the graph contains a value (int) and a list (List[Node]) of its neighbors.

class Node {
    public int val;
    public List<Node> neighbors;
}
 

Test case format:
For simplicity, each node's value is the same as the node's index (1-indexed). For example, the first node with val == 1, the second node with val == 2, and so on. The graph is represented in the test case using an adjacency list.
An adjacency list is a collection of unordered lists used to represent a finite graph. Each list describes the set of neighbors of a node in the graph.
The given node will always be the first node with val = 1. You must return the copy of the given node as a reference to the cloned graph.  

Input: adjList = [[2,4],[1,3],[2,4],[1,3]]  
Output: [[2,4],[1,3],[2,4],[1,3]]  
Explanation: There are 4 nodes in the graph.  
1st node (val = 1)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).  
2nd node (val = 2)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).  
3rd node (val = 3)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).  
4th node (val = 4)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).

Input: adjList = [[]]  
Output: [[]]  
Explanation: Note that the input contains one empty list. The graph consists of only one node with val = 1 and it does not have any neighbors.

Approach : So the appraoch is simple. We start from the given node and do DFS for all its neighbours. Every time we do we create a new Node of the neigbour and do DFS on it before returning it as the node. Once we have created a new Node, we mark it visited so that node is already created and we just need to add it to the neigbours list if that is one of the neighbour while doing DFS on any node.  
The Visited Map will store the created node with node value as the key. 

```cpp
class Solution {
public:
    Node* dfs(Node* node, unordered_map<int, Node*> &vis) {
        if(!node)
            return node;
        Node* newNode = new Node(node->val);
        vis[node->val] = newNode;
        for(Node* nbr : node->neighbors) {
                if(vis.find(nbr->val) == vis.end()) {
                    Node* nbrNode = dfs(nbr, vis);
                    newNode->neighbors.push_back(nbrNode);
                }
                else
                    newNode->neighbors.push_back(vis[nbr->val]);
        }
        return newNode;
    }

    Node* cloneGraph(Node* node) {
        unordered_map<int, Node*> vis;
        Node* nd = dfs(node, vis);
        for(auto it=vis.begin(); it!=vis.end(); it++) {
            cout<<it->first<<" ";
        }
        return nd;
    }
};
```

**2. Islands and Treasures (Leetcode : Walls and Gates)**  
You are given a m×n 2D grid initialized with these three possible values:  

-1 - A water cell that can not be traversed.  
0 - A treasure chest.  
INF - A land cell that can be traversed. We use the integer 2^31 - 1 = 2147483647 to represent INF.  
Fill each land cell with the distance to its nearest treasure chest. If a land cell cannot reach a treasure chest then the value should remain INF.  

Assume the grid can only be traversed up, down, left, or right.  
Modify the grid in-place.  

Example :  
```cpp
Input: [
  [2147483647,-1,0,2147483647],
  [2147483647,2147483647,2147483647,-1],
  [2147483647,-1,2147483647,-1],
  [0,-1,2147483647,2147483647]
]

Output: [
  [3,-1,0,1],
  [2,2,1,-1],
  [1,-1,2,-1],
  [0,-1,3,4]
]

```

Expample 2 :   

```cpp
Input: [
  [0,-1],
  [2147483647,2147483647]
]

Output: [
  [0,-1],
  [1,2]
]

```
Approach: So in this problem when we ae asked to find nearest distance to the Treasure, we should do BFS from each cell and when we encounter a gate that should be the nearest gate to that land cell. We do this for all the land cells. So the overall TC : O((m*n)^2)   
But we can do better. So we can use something called Multi-source BFS. Here instead of starting from each land cell we start from each Treasure and do BFS. We make sure every cell is visited just once. So the nearest land cell will be visited first by its nearest Treasure BFS than any other Treasure's BFS. So in this way we can have all the land cells traversed only once and have the distance populated to their nearest Treasure. In this algo we use a queue and we push all the Treause coordinates into the queue and mark the coordinates as visted. We then start our BFS and we keep on popping each coordinate and then push its neighbours and mark the popped one as visited. 

```cpp
class Solution {
public:
    vector<vector<int>> direc = {{-1, 0}, {1, 0}, {0, 1}, {0, -1}};
    
    void islandsAndTreasure(vector<vector<int>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        queue<pair<int, int>> q;
        for(int i=0; i<grid.size(); i++) {
            for(int j=0; j<grid[0].size(); j++) {
                if(grid[i][j] == 0)
                    q.push({i, j}); 
            }
        }

        while(!q.empty()) {
            int x = q.front().first;
            int y = q.front().second;
            q.pop();
            for(vector<int> dir : direc) {
                int r = x + dir[0], c = y + dir[1];
                if(r >= 0 && r < n && c >= 0 && c < m && grid[r][c] == INT_MAX) {
                    grid[r][c] = grid[x][y] + 1;
                    q.push({r, c});
                }
            }
        }
    }
};

``` 
**3. Rotten Oranges**  
You are given an m x n grid where each cell can have one of three values:

0 representing an empty cell,
1 representing a fresh orange, or
2 representing a rotten orange.
Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten.

Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return -1.

```cpp
Example 1:

Input: grid = [[2,1,1],[1,1,0],[0,1,1]]
Output: 4

Example 2:

Input: grid = [[2,1,1],[0,1,1],[1,0,1]]
Output: -1
Explanation: The orange in the bottom left corner (row 2, column 0) is never rotten, because rotting only happens 4-directionally.

Example 3:

Input: grid = [[0,2]]
Output: 0
Explanation: Since there are already no fresh oranges at minute 0, the answer is just 0.
```

Appraoch : In this problem we want to know the minimum time in which all Oranges will be rotten. So this is also similar to Multi source BFS and we start from Rotten Oranges and do BFS. We make sure if a node is visited once it is not visited again. So we mark the Orange as visited before pushing into the queue. We keep a variable to keep track of the maximum time encountered so far. After we are done with BFS we check if all the Oranges are Rotten or not. If they are then we return the result else it is not possible to rot all oranges.  

```cpp
class Orange {
    public :
        int x, y, t;
        Orange(int x, int y, int time) {
            this->x = x;
            this->y = y;
            this->t = time;
        }
};

class Solution {
public:
    vector<vector<int>> direc = {{-1,0}, {1,0}, {0,1}, {0,-1}};
    int orangesRotting(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        vector<vector<int>> vis(grid.size(), vector<int>(grid[0].size(), false));
        queue<Orange> q;
        for(int i=0; i<n; i++) {
            for(int j=0; j<m; j++){
                if(grid[i][j] == 2){
                    q.push(Orange(i, j, 0));
                    vis[i][j] = true;
                }
            }
        }

        int res = 0;
        while(!q.empty()) {
            Orange orange = q.front();
            q.pop();
            res = max(res, orange.t);
            for(vector<int> dir: direc) {
                int r = orange.x + dir[0];
                int c = orange.y + dir[1];
                if(r >= 0 && c >= 0 && r < n && c < m && !vis[r][c] && grid[r][c] == 1) {
                    q.push(Orange(r, c, orange.t + 1));
                    vis[r][c] = true;
                }
            }
        }

        for(int i=0; i<n; i++) {
            for(int j=0; j<m; j++){
                if(grid[i][j] == 1 && !vis[i][j])
                    return -1;
            }
        }

        return res;
    }
};
```
**4. Pacific Atlantic Water Flow**  
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
