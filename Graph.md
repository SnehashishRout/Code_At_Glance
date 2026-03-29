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

**5. Surrounded Regions**
You are given an m x n matrix board containing letters 'X' and 'O', capture regions that are surrounded:

Connect: A cell is connected to adjacent cells horizontally or vertically.
Region: To form a region connect every 'O' cell.
Surround: The region is surrounded with 'X' cells if you can connect the region with 'X' cells and none of the region cells are on the edge of the board.
To capture a surrounded region, replace all 'O's with 'X's in-place within the original board. You do not need to return anything.  

Examples : [Leetcode 130](https://leetcode.com/problems/surrounded-regions/description/)  

Approach : This one should be quite easily comprehensible, as the problem can be restated as finding out the connected componenets in which atleast one node is an edge node and then mark the rest of the nodes as "X". So all we do is try DFS from all the edge nodes first and then mark each of the connected nodes of those edge nodes in visited Matrix.  
Once we have done that we can just see which all nodes are "O" and not visited and those are marked as "X".  

```cpp
class Solution {
public:
    vector<vector<int>> direc = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    void dfs(vector<vector<char>>& board, vector<vector<bool>>& vis, int i,
             int j) {
        vis[i][j] = true;
        for (vector<int> dir : direc) {
            int r = i + dir[0], c = j + dir[1];
            if (r < 0 || c < 0 || r >= board.size() || c >= board[0].size()) {
                
            } else if (!vis[r][c] && board[r][c] == 'O') {
                dfs(board, vis, r, c);
            }
        }
    }

    void solve(vector<vector<char>>& board) {
        vector<vector<bool>> vis(board.size(),
                                 vector<bool>(board[0].size(), false));
        for (int i = 0; i < board.size(); i++) {
            for (int j = 0; j < board[0].size(); j++) {
                if (i == 0 || j == 0 || i == board.size() - 1 ||
                    j == board[0].size() - 1) {
                    if (board[i][j] == 'O' && !vis[i][j]) {
                        if (i == 0 || j == 0 || i == board.size() - 1 ||
                            j == board[0].size() - 1) {
                            dfs(board, vis, i, j);
                        }
                    }
                }
            }
        }

        for (int i = 0; i < board.size(); i++) {
            for (int j = 0; j < board[0].size(); j++) {
                if (board[i][j] == 'O' && !vis[i][j]) {
                    board[i][j] = 'X';
                }
            }
        }
    }
};
```

**6. Course Schedule**  
There are a total of numCourses courses you have to take, labeled from 0 to numCourses - 1. You are given an array prerequisites where prerequisites[i] = [ai, bi] indicates that you must take course bi first if you want to take course ai.  
For example, the pair [0, 1], indicates that to take course 0 you have to first take course 1.
Return true if you can finish all courses. Otherwise, return false.  

```cpp
Example 1:

Input: numCourses = 2, prerequisites = [[1,0]]
Output: true
Explanation: There are a total of 2 courses to take. 
To take course 1 you should have finished course 0. So it is possible.

Example 2:

Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
Explanation: There are a total of 2 courses to take. 
To take course 1 you should have finished course 0, and to take course 0 you should also have finished course 1. So it is impossible.
```

Approach : So here whatever prequisite array is being given just form an adjacency List from it, with the prior task directing an edge to the dependent task. So the problem just boils down to find whether we have a cycle in Directed Graph. Now we will see two ways to find cycle in directed graph. One is through simple DFS and the other one through BFS (Kahn's ALgo) which maeks code more simpler.  

*With Simple DFS*, we just maintain as recursive Stack and if we encounter a node that is already there in the recursive stack, that means there is a cycle. If there is a possibility of disconnected components then we have to have a visited array as well and traverse each node and check if there are any unvisited node left to make sure all components are covered.  

 ```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        unordered_map<int, vector<int>> adj;
        vector<bool> vis(numCourses, false);
        for(int i=0; i<prerequisites.size(); i++) {
            adj[prerequisites[i][1]].push_back(prerequisites[i][0]);
        }

        unordered_map<int, int> recStk;
        bool isCycle = false;
        for(int i=0; i<numCourses; i++) {
            if(!vis[i]) {
                if(dfs(adj, vis, i, recStk, isCycle))
                    return false;
            }
        }
        
        return true;
    }

    bool dfs(unordered_map<int, vector<int>>& adj, vector<bool> &vis, int node, unordered_map<int, int> &recStk, bool &isCycle) {
        vis[node] = true;
        recStk[node]++;

        for(int nbr : adj[node]) {
            if(recStk.find(nbr) != recStk.end()) {
                return true;
            }
            if(dfs(adj, vis, nbr, recStk, isCycle))
                return true;
        }

        recStk[node]--;
        if(recStk[node] == 0)
            recStk.erase(node);

        return false;
    }
};
```  

Now there is another way that is to find a valid topological ordering of the graph, either by DFS or BFS. If after finding the Topological ordering, the size of the ordering is equal to the total number of nodes, that means we were able to sort all the nodes and there is no cycle else we wont have been able to find ordering of cyclic components.  

*Topological Sort using BFS* : 
Now to fidn cycle in Directed Graph, in DFS we used a recStack which used pop out elements as we backtrack but in BFS this isnt possible. So we come up with a technique using Topoloigcal sort using BFS. So the idea is that since we know we can Topo sort graph only if it is DAG Graph. So what we do is, we try to Topo sort the Graph and if we are able to TopoSort it then the graph is Acyclic for sure and if we are not then there must be cycle present. So we use Kahn's Algorithm to do TopoSort using BFS and then at the end we check if we are able to topo Sort all the nodes and even if we are not able to sort (even though its just one node) we can say for sure there is a cycle.  

This is called Kahn's ALgorithm. We use the concept called indegree of all nodes. Indegree means number of incoming edges to a particular node. So we use a queue here and we push all the nodes that have indegree 0 at the beginning, because these are the nodes that are not dependent upon any previous node to compelete. Then we keep on popping and fron the front of the queue and whatever node we get we decrement the indegree of the its neighbouring nodes by 1. And if for any neigbour the indegree becomes 0 we push that neigbour in queue which signifies we explored all the prerequisite node of this and this node can now be explored. We do the process until queue is empty. Every time we pop a node from queue we keep on adding it to a topological sorting order.  

```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        unordered_map<int, vector<int>> adj;
        vector<int> indeg(numCourses, 0);
        for(int i=0; i<prerequisites.size(); i++) {
            adj[prerequisites[i][1]].push_back(prerequisites[i][0]);
            indeg[prerequisites[i][0]]++;
        }

        queue<int> q;
        for(int i=0; i<numCourses; i++) {
            if(indeg[i] == 0)
                q.push(i);
        }

        vector<int> topo;
        while(!q.empty()) {
            int node = q.front();
            q.pop();
            topo.push_back(node);

            for(int nbr : adj[node]) {
                indeg[nbr]--;
                if(indeg[nbr] == 0)
                    q.push(nbr);
            }
        }

        return topo.size() == numCourses;
    }
};
```

**Topological Sort using DFS** :  Just do normal DFS and keep a stack and when we enocunter a node, first visit all its neigbouring nodes and once they are in stack, we then push the current node into the stack. We do that because when we pop out from stack the nodes one by one, we get the nodes in order of parent to neighbouring nodes.

*P.S. : This Topo Sort using DFS cannot be used for finding Cycle in Directed Graph. This method is just mentioned in the passing to do Topological Sorting and the Graph needs to be Acyclic Directed graph for this to work*

```cpp
class Solution {
private:
	void dfs(int node, int vis[], stack<int> &st,
	         vector<int> adj[]) {
		vis[node] = 1;
		for (auto it : adj[node]) {
			if (!vis[it]) dfs(it, vis, st, adj);
		}
		st.push(node);
	}
public:
	//Function to return list containing vertices in Topological order.
	vector<int> topoSort(int V, vector<int> adj[])
	{
		int vis[V] = {0};
		stack<int> st;
		for (int i = 0; i < V; i++) {
			if (!vis[i]) {
				dfs(i, vis, st, adj);
			}
		}

		vector<int> ans;
		while (!st.empty()) {
			ans.push_back(st.top());
			st.pop();
		}
		return ans;
	}
};
```

**Detect Cycle in Un-Directed Graph**  

Approach : So this problem is a good example to understand how to find Cycle in an Undirected Graph. So we we need to understand in undirecetd graph the only issue/ diff from a direcetd graph is if there is an edge b/w n1 and n2 then that means n1 is a neighbour of n2 and n2 is also a neighbour of n1 which means while traversing n2 we will get n1 but that n1 would already been visited so the algo would return a cycle is present which actually is not a cycle in an unidrected graph. So to avoid this scenario we in each DFS call pass in the parent, which is the the immediate previous node to the current node and if we encounter any node that is visited and is also a neighbour of current node, and is not the parent then that means a cycle is found.  

```cpp
class Solution {
  private: 
    bool dfs(int node, int parent, int vis[], vector<int> adj[]) {
        vis[node] = 1; 
        // visit adjacent nodes
        for(auto adjacentNode: adj[node]) {
            // unvisited adjacent node
            if(!vis[adjacentNode]) {
                if(dfs(adjacentNode, node, vis, adj) == true) 
                    return true; 
            }
            // visited node but not a parent node
            else if(adjacentNode != parent) return true; 
        }
        return false; 
    }
  public:
    // Function to detect cycle in an undirected graph.
    bool isCycle(int n, vector<vector<int>>& edges) {
       unordered_map<int, vector<int>> adj;
        vector<bool> vis(n, false);
        for(int i=0; i<edges.size(); i++) {
            adj[edges[i][0]].push_back(edges[i][1]);
            adj[edges[i][1]].push_back(edges[i][0]);
        }
       // for graph with connected components 
       for(int i = 0;i<V;i++) {
           if(!vis[i]) {
               if(dfs(i, -1, vis, adj) == true) return true; 
           }
       }
       return false; 
    }
};
```

**Shortest Path in Un-Directed Graph with Unit Weight**   

```cpp
Input: adj[][] = [[1, 3], [0, 2], [1, 6], [0, 4], [3, 5], [4, 6], [2, 5, 7, 8], [6, 8], [7, 6]],
src=0
Output: [0, 1, 2, 1, 2, 3, 3, 4, 4]  
```
Approach : Whenever Shortest path for Un-Directed Graph for Unit length is asked we can use BFS to solve this. We know the first distance calculated for any node while doing the BFS traversal will be the shortest path as thgats the speciality of BFS as it does Level Wise traversal. 


```cpp
class Solution {
  public:
    // Function to find the shortest path from source to all other nodes
    vector<int> shortestPath(vector<vector<int>>& adj, int src) {
        // code here
        vector<int> dist(adj.size(), -1);
        queue<pair<int,int>> q;
        q.push({src,0});
        dist[src] = 0;
        while(!q.empty()) {
            int node = q.front().first;
            int d = q.front().second;
            q.pop();
            for(int nbr : adj[node]) {
                if(dist[nbr] == -1) {
                    dist[nbr] = d + 1;
                    q.push({nbr, d+1});
                }
            }
        }
        return dist;
    }
};
```

**Shortest path in Weighted Directed Acyclic Graph**  

Given a Directed Acyclic Graph of V vertices from 0 to n-1 and a 2D Integer array(or vector) edges[ ][ ] of length E, where there is a directed edge from edge[i][0] to edge[i][1] with a distance of edge[i][2] for all i.

Find the shortest path from src(0) vertex to all the vertices and if it is impossible to reach any vertex, then return -1 for that vertex.  

```cpp
Input: V = 6, E = 7, edges = [[0,1,2], [0,4,1], [4,5,4], [4,2,2], [1,2,3], [2,3,6], [5,3,1]]
Output: [0, 2, 3, 6, 1, 5]
Explanation: Shortest path from 0 to 1 is 0->1 with edge weight 2. Shortest path from 0 to 2 is 0->4->2 with edge weight 1+2=3. Shortest path from 0 to 3 is 0->4->5->3 with edge weight 1+4+1=6. Shortest path from 0 to 4 is 0->4 with edge weight 1.Shortest path from 0 to 5 is 0->4->5 with edge weight 1+4=5.
```

Approach : So to solve this there is a very easy and less complex method. You have to just follow the following steps :
 + Do a Topological Sorting of the Graph nodes and store them in a List
 + Now have a distance array to store shortest distance value computed from source. The initial values will be INT_MAX or INF+
 + Mark the distance of source node as 0 in the list. Traverse one by one serially each node from topo sorted list of nodes and relax the edges of that node i.e check if the current distance of curretn node (from src) + edge weight < current distance of the neigbour node (source) and if it is update the distance value for that neighbour node.
 + The Final value you have is the shortest distance

The distances value for nodes which still have +INF are the onces that cannot be reached form that source.      

The intuition behind this algorithm is since it is topologically sorted nodes, so before we come to any node for realxing its edges, all the preceding nodes must have been traversed before it and thus whatever the shortest distance that could be there to reach current node from source would have been computed.  

*P.S : This algorithm works for any source and not just 0 and you just have to mark the distance of source node as 0 initially*  

```cpp
class Solution {
  public:
    void topoSort(vector<vector<int>>& adj, int node, stack<int> &stk, vector<bool>& vis) {
        vis[node] = true;
        for(auto nbr : adj[node]) {
            if(!vis[nbr]) {
                topoSort(adj, nbr, stk, vis);
            }
        }
        stk.push(node);
    }
    
    vector<int> shortestPath(int V, int E, vector<vector<int>>& edges) {
        // code here
        vector<vector<int>> adj(V);
        vector<vector<int>> mat(V, vector<int> (V, INT_MAX));
        vector<bool> vis(V, false);
        for(int i=0; i<E; i++) {
            adj[edges[i][0]].push_back(edges[i][1]);
            mat[edges[i][0]][edges[i][1]] = edges[i][2];
        }
        
        //TOPO Sort
        stack<int> stk;
        topoSort(adj, 0, stk, vis);
        vector<int> topo;
        while(!stk.empty()) {
            topo.push_back(stk.top());
            stk.pop();
        }

		//Picking each node and relaxing the edges
        vector<int> dist(V, INT_MAX);
        dist[0] = 0;
        for(int i=0; i<topo.size(); i++) {
            int node = topo[i];
            for(auto nbr : adj[node]) {
                if(dist[node] + mat[node][nbr] < dist[nbr]) {
                    dist[nbr] = dist[node] + mat[node][nbr];
                }
            }
        }
        
        for(int i=0; i<dist.size(); i++) {
            if(dist[i] == INT_MAX)
                dist[i] = -1;
        }
        
        return dist;
    }
};

```

**Shortest Path in Weighted Undirected Graph / Dijkstra Algrorithm**  

Given an undirected, weighted graph with V vertices numbered from 0 to V-1 and E edges, represented by 2d array edges[][], where edges[i]=[u, v, w] represents the edge between the nodes u and v having w edge weight.
You have to find the shortest distance of all the vertices from the source vertex src, and return an array of integers where the ith element denotes the shortest distance between ith node and source vertex src.

Note: The Graph is connected and doesn't contain any negative weight edge.

GFG Link : [https://www.geeksforgeeks.org/problems/implementing-dijkstra-set-1-adjacency-matrix/1](https://www.geeksforgeeks.org/problems/implementing-dijkstra-set-1-adjacency-matrix/1)  

```cpp
Input: V = 3, edges[][] = [[0, 1, 1], [1, 2, 3], [0, 2, 6]], src = 2
Output: [4, 3, 0]
Shortest Paths:
For 2 to 0 minimum distance will be 4. By following path 2 -> 1 -> 0
For 2 to 1 minimum distance will be 3. By following path 2 -> 1
For 2 to 2 minimum distance will be 0. By following path 2 -> 2

Input: V = 5, edges[][] = [[0, 1, 4], [0, 2, 8], [1, 4, 6], [2, 3, 2], [3, 4, 10]], src = 0
Output: [0, 4, 8, 10, 10]
Shortest Paths: 
For 0 to 1 minimum distance will be 4. By following path 0 -> 1
For 0 to 2 minimum distance will be 8. By following path 0 -> 2
For 0 to 3 minimum distance will be 10. By following path 0 -> 2 -> 3 
For 0 to 4 minimum distance will be 10. By following path 0 -> 1 -> 4
```

Approach : So we solve the above issue for Wighted Undirectced Graph, using Dijkstra's Algorithm. In Dijkstra's algortihm we follow the following steps simply : 
+ Use a Min-Heap and a distance array which is marked with INF for all nodes. Distance array stores all the distances from the source to that Node. 
+ Make the distance of source node as 0 and push it into Min-Heap. The Min-heap is meant to return the minimum distance. We push {distance, node} into the min-heap.
	+ So we push {0, srcNode} into min-heap and then iterate until its empty. In each iteration, we fetch the the minium distance node and then relax its edges that is traverse all adjacent nodes and see if that neighbour nodes distance is more than the currnet nodes distance + edge weight then that means, we have a better distance/shorter distance and then update the neighbour node's distance and push that distance and node into Min-Heap. if the condition is not met we just skip it. 
+ We continue doing this till the min-heap is empty and what we have finally is the distance array having shortest distance from source to that location.  

*P.S. : Dijkstra's Algrorithm is always E x LogV Time complexity where E = Total no. of edges and V = Total Vertices*
```cpp
// User Function Template
class Solution {
  public:
    vector<int> dijkstra(int V, vector<vector<int>> &edges, int src) {
        // Code here
        vector<pair<int,int>> adj[V];
        for(auto edge : edges) {
            adj[edge[0]].push_back({edge[1], edge[2]});
            adj[edge[1]].push_back({edge[0], edge[2]});
        }
        
        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
        vector<int> dist(V, INT_MAX);
        dist[src] = 0;
        pq.push({0, src});
        
        while(!pq.empty()) {
            int weight = pq.top().first;
            int node = pq.top().second;
            pq.pop();
            
            for(auto edge : adj[node]) {
                int nbr = edge.first;
                int edgeWt = edge.second;
                if(weight + edgeWt < dist[nbr]) {
                    dist[nbr] = weight + edgeWt;
                    pq.push({dist[nbr], nbr});
                }
            }
        }
        
        return dist;
    }
};
```

Now there can be an extension to the problem and that is what if instead of asking the shortest Distance value, they ask you to print the shortest path. So for that we need to have a separate Parent array to store the parent of the node from which the shortest distance got updated.

+ We start by initializing an adjacency list which will store all the adjacent nodes for a particular node along with the weights associated with them.
+ Then, as a part of the initial configuration, we define a dist array to store the updated shortest distances for each node, a priority queue for storing the distance-node pairs, and a source node.
+ In addition to this, we also declare a ‘parent’ array which would store the parent node for each node and will update itself to a different parent if a shorter path from a node is found at some point in time.
+ At the start, all nodes’ parents have been set to the nodes themselves to indicate that the traversal has not yet been started.
+ For every node at the top of the queue, we pop the element out and look out for its adjacent nodes. If the current reachable distance is better than the previous distance (dis + edW < dist[adjNode]), indicated by the distance array, we update the distance and push it into the queue.
+ A node with a lower distance would be at the top of the priority queue as opposed to a node with a higher distance because we are using a min-heap.
+ In addition to the previous step, we will also update the parent array to the node from where the current node came while traversing.
+ By following step 5 repeatedly until our queue becomes empty, we would get the minimum distance from the source node to all other nodes and also our parent array would be updated according to the shortest path.
+ Now, we run a loop starting from the destination node storing the node’s parent and then moving to the parent again (backtrack) till the parent[node] becomes equal to the node itself.
+ At last, we reverse the array in which the path is being stored as the path is in reverse order. Finally, we return the ‘path’ array.

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution
{
public:
    vector<int> shortestPath(int n, int m, vector<vector<int>> &edges)
    {
        // Create an adjacency list of pairs of the form node1 -> {node2, edge weight}
        // where the edge weight is the weight of the edge from node1 to node2.
        vector<pair<int, int>> adj[n + 1];
        for (auto it : edges)
        {
            adj[it[0]].push_back({it[1], it[2]});
            adj[it[1]].push_back({it[0], it[2]});
        }
        // Create a priority queue for storing the nodes along with distances 
        // in the form of a pair { dist, node }.
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int,int>>> pq;

        // Create a dist array for storing the updated distances and a parent array
        //for storing the nodes from where the current nodes represented by indices of
        // the parent array came from.
        vector<int> dist(n + 1, 1e9), parent(n + 1);
        for (int i = 1; i <= n; i++)
            parent[i] = i;

        dist[1] = 0;

        // Push the source node to the queue.
        pq.push({0, 1});
        while (!pq.empty())
        {
            // Topmost element of the priority queue is with minimum distance value.
            auto it = pq.top();
            pq.pop();
            int node = it.second;
            int dis = it.first;

            // Iterate through the adjacent nodes of the current popped node.
            for (auto it : adj[node])
            {
                int adjNode = it.first;
                int edW = it.second;

                // Check if the previously stored distance value is 
                // greater than the current computed value or not, 
                // if yes then update the distance value.
                if (dis + edW < dist[adjNode])
                {
                    dist[adjNode] = dis + edW;
                    pq.push({dis + edW, adjNode});

                    // Update the parent of the adjNode to the recent 
                    // node where it came from.
                    parent[adjNode] = node;
                }
            }
        }

        // If distance to a node could not be found, return an array containing -1.
        if (dist[n] == 1e9)
            return {-1};

        // Store the final path in the ‘path’ array.
        vector<int> path;
        int node = n;

        // Iterate backwards from destination to source through the parent array.
        while (parent[node] != node)
        {
            path.push_back(node);
            node = parent[node];
        }
        path.push_back(1);

        // Since the path stored is in a reverse order, we reverse the array
        // to get the final answer and then return the array.
        reverse(path.begin(), path.end());
        return path;
    }
};
```
***Disjoint Set Union***  
***Note : A key thing to note rember whenever there is Dynamic Graphs or Graphs are changing, in most cases it can be solved using DSU and DSU gives the most optimal answer in such cases***  

**7. Number of Operations to Make Network Connected**  
You are given an array points representing integer coordinates of some points on a 2D-plane, where points[i] = [xi, yi].
The cost of connecting two points [xi, yi] and [xj, yj] is the manhattan distance between them: |xi - xj| + |yi - yj|, where |val| denotes the absolute value of val.
Return the minimum cost to make all points connected. All points are connected if there is exactly one simple path between any two points.  

```cpp
Input: n = 4, connections = [[0,1],[0,2],[1,2]]
Output: 1
Explanation: Remove cable between computer 1 and 2 and place between computers 1 and 3.

Input: n = 6, connections = [[0,1],[0,2],[0,3],[1,2],[1,3]]
Output: 2

Input: n = 6, connections = [[0,1],[0,2],[0,3],[1,2]]
Output: -1
Explanation: There are not enough cables.
```
*Approach* : So here if we carefully observed the minimum number of connections to connect all the computers is n-1 if n is the number of computers. So first we should check if the given number of connections are atleast n-1. If not then no possible to connect all. If there are enough connections, all we have to do is find the number of components in the current configuretion. If there are total M components, the minimum connections that are required among the M components is M-1 connections. So all we have to do is find the number of components. We can do this easily by using DSU. 

```cpp
class DSU {
public :
    vector<int> rank;
    vector<int> parent;
    DSU(int n) {
        for(int i=0; i<n; i++) {
            rank.push_back(0);
            parent.push_back(i); 
        }
    }

    int getPar(int node) {
        if(parent[node] == node)
            return node;
        
        return parent[node] = getPar(parent[node]);
    }

    void unionByRank(int u, int v) {
        int up = getPar(u);
        int vp = getPar(v);

        if(up == vp)
            return;
        if(rank[up] < rank[vp]) {
            parent[up] = vp;
        } else if(rank[up] > rank[vp]) {
            parent[vp] = up;
        } else {
            parent[vp] = up;
            rank[up]++;
        }
    } 
};

class Solution {
public:
    int makeConnected(int n, vector<vector<int>>& connections) {
        if(n-1 > connections.size())
            return -1;
        int compo = getComponents(n, connections);
        
        return compo-1;
    }

    int getComponents(int n, vector<vector<int>>& connections) {
        DSU ds(n);
        for(auto connec : connections) {
            ds.unionByRank(connec[0], connec[1]);
        }
        unordered_map<int,int> hm;

        // Dont use Par array to traverse as it might still have some nodes whose ultimate par is not yet calc
        for(int i=0; i<n; i++) {
            hm[ds.getPar(i)]++;
        }
        return hm.size();
    }
};
```

**8. Most Stones Removed with Same Row or Column**  
On a 2D plane, we place n stones at some integer coordinate points. Each coordinate point may have at most one stone.
A stone can be removed if it shares either the same row or the same column as another stone that has not been removed.
Given an array stones of length n where stones[i] = [xi, yi] represents the location of the ith stone, return the largest possible number of stones that can be removed.  
```cpp
Input: stones = [[0,0],[0,1],[1,0],[1,2],[2,1],[2,2]]
Output: 5
Explanation: One way to remove 5 stones is as follows:
1. Remove stone [2,2] because it shares the same row as [2,1].
2. Remove stone [2,1] because it shares the same column as [0,1].
3. Remove stone [1,2] because it shares the same row as [1,0].
4. Remove stone [1,0] because it shares the same column as [0,0].
5. Remove stone [0,1] because it shares the same row as [0,0].
Stone [0,0] cannot be removed since it does not share a row/column with another stone still on the plane.

Input: stones = [[0,0],[0,2],[1,1],[2,0],[2,2]]
Output: 3
Explanation: One way to make 3 moves is as follows:
1. Remove stone [2,2] because it shares the same row as [2,0].
2. Remove stone [2,0] because it shares the same column as [0,0].
3. Remove stone [0,2] because it shares the same row as [0,0].
Stones [0,0] and [1,1] cannot be removed since they do not share a row/column with another stone still on the plane.

Input: stones = [[0,0]]
Output: 0
Explanation: [0,0] is the only stone on the plane, so you cannot remove it.
```
Approach : So we see coordinates sharing same row and column are someway linked and only those which share a row or col can be removed. So we see these coordinates are removed only when there exist a member in a group. The group is formed by all coordinates which share either row or column. So we can think of them as connected components. If we observe cautiously, We can remove all memebers of a group except one. There will always be one coordinate left at the end which cant be removed. So we arrive at conclusion that if we can some way find the size of all the connected components and decrease their sizes by 1 and add, we can get our answer. So we use DSU to make Union and then calc of size of each component easier. 
Note that we use UnionBySize here bcz we need the size of the components at the end.  

```cpp
class DSU {
public :
    vector<int> size;
    vector<int> parent;
    DSU(int n) {
        for(int i=0; i<n; i++) {
            size.push_back(1);
            parent.push_back(i); 
        }
    }

    int getPar(int node) {
        if(parent[node] == node)
            return node;
        
        return parent[node] = getPar(parent[node]);
    }

    void unionBySize(int u, int v) {
        int up = getPar(u);
        int vp = getPar(v);

        if(up == vp)
            return;
        if(size[up] < size[vp]) {
            parent[up] = vp;
            size[vp] += size[up];
        } else if(size[up] > size[vp]) {
            parent[vp] = up;
            size[up] += size[vp];
        } else {
            parent[vp] = up;
            size[up] += size[vp];
        }
    } 
};

class Solution {
public:
    int removeStones(vector<vector<int>>& stones) {
        DSU ds(stones.size());
        for(int i=0; i<stones.size(); i++) {
            for(int j=i+1; j<stones.size(); j++) {
                if(stones[i][0] == stones[j][0] || stones[i][1] == stones[j][1]) {
                    ds.unionBySize(i, j);
                }
            }
        }

        unordered_map<int,int> hm;
        for(int n : ds.parent) {
            hm[ds.getPar(n)]++;
        }

        int res = 0;
        for(auto it : hm) {
            res += ds.size[it.first] - 1;
        }

        return res;
    }
};
```
Time Comlplexity : O(N^2) where N is size of stones array. 

**9. Accounts Merge**    
Given a list of accounts where each element accounts[i] is a list of strings, where the first element accounts[i][0] is a name, and the rest of the elements are emails representing emails of the account.

Now, we would like to merge these accounts. Two accounts definitely belong to the same person if there is some common email to both accounts. Note that even if two accounts have the same name, they may belong to different people as people could have the same name. A person can have any number of accounts initially, but all of their accounts definitely have the same name.

After merging the accounts, return the accounts in the following format: the first element of each account is the name, and the rest of the elements are emails in sorted order. The accounts themselves can be returned in any order.

```cpp
Input: accounts = [["John","johnsmith@mail.com","john_newyork@mail.com"],["John","johnsmith@mail.com","john00@mail.com"],["Mary","mary@mail.com"],["John","johnnybravo@mail.com"]]
Output: [["John","john00@mail.com","john_newyork@mail.com","johnsmith@mail.com"],["Mary","mary@mail.com"],["John","johnnybravo@mail.com"]]
Explanation:
The first and second John's are the same person as they have the common email "johnsmith@mail.com".
The third John and Mary are different people as none of their email addresses are used by other accounts.
We could return these lists in any order, for example the answer [['Mary', 'mary@mail.com'], ['John', 'johnnybravo@mail.com'], 
['John', 'john00@mail.com', 'john_newyork@mail.com', 'johnsmith@mail.com']] would still be accepted.
```

```cpp
class DSU {
public :
    vector<int> size;
    vector<int> parent;
    DSU(int n) {
        for(int i=0; i<n; i++) {
            size.push_back(1);
            parent.push_back(i); 
        }
    }

    int findPar(int node) {
        if(parent[node] == node)
            return node;
        
        return parent[node] = findPar(parent[node]);
    }

    void unionBySize(int u, int v) {
        int up = findPar(u);
        int vp = findPar(v);

        if(up == vp)
            return;
        if(size[up] < size[vp]) {
            parent[up] = vp;
            size[vp] += size[up];
        } else if(size[up] > size[vp]) {
            parent[vp] = up;
            size[up] += size[vp];
        } else {
            parent[vp] = up;
            size[up] += size[vp];
        }
    } 
};

class Solution {
public:
    vector<vector<string>> accountsMerge(vector<vector<string>>& accounts) {
        unordered_map<string, int> hm;

        DSU ds(accounts.size());
        int node = 0;
        for(int i=0; i<accounts.size(); i++) {
            for(int j=1; j<accounts[i].size(); j++) {
                if(hm.find(accounts[i][j]) != hm.end()) {
                    ds.unionBySize(ds.findPar(hm[accounts[i][j]]), i);
                    continue;
                } else {
                    hm[accounts[i][j]] = i;
                }
            }
        }

        unordered_map<int, vector<string>> nodeToEmail;
        for(auto it : hm) {
            int node = ds.findPar(it.second);
            nodeToEmail[node].push_back(it.first);
        }

        vector<vector<string>> res;
        for(auto it : nodeToEmail) {
            vector<string> temp;
            temp.push_back(accounts[it.first][0]);
            sort(nodeToEmail[it.first].begin(), nodeToEmail[it.first].end());
            for(auto email : nodeToEmail[it.first])
                temp.push_back(email);
            res.push_back(temp);
        }

        return res;
    }
};
```

**10. Making a Large Island**  

You are given an n x n binary matrix grid. You are allowed to change at most one 0 to be 1.
Return the size of the largest island in grid after applying this operation.
An island is a 4-directionally connected group of 1s.  

```cpp
Input: grid = [[1,0],[0,1]]
Output: 3
Explanation: Change one 0 to 1 and connect two 1s, then we get an island with area = 3.

Input: grid = [[1,1],[1,0]]
Output: 4
Explanation: Change the 0 to 1 and make the island bigger, only one island with area = 4.

Input: grid = [[1,1],[1,1]]
Output: 4
Explanation: Can't change any 0 to 1, only one island with area = 4.
```

*Approach :* So the very first appraoch that comes into our mind is to traverse each zero and then convert it to 1 and then find the size of the largest island that exists in the matrix. But it would be really inefficient as this might result in O((m*n)^2) Time complexity. If we obeserve carefully instead of converting each zero to 1 we can just look in all 4 directions of current 0 and see if the neighbouring cell is 1 and what is the size of the component that it is part of. So adding up the sizes of components of all such neighbouring 1 are part of and then adding 1 (to account for the current 0 that would be converted to 1) should give us the total size of the component that would be formed by converting the current 0 to 1. But we need to be careful to not double count some components as two neighbouring 1s can be part of same component. 
  
So our problem boild down to for each zero, finding how many distinct components are its neighbouring 1s (if any) are part of and then their sizes. So the mast efficient way to know a node is part of same component and their size is to use DSU. So we use Disjoint Set Union but by Size as we would require sizes of each component. 

+ We first traverse the whole matrix and perform union of two nodes which are 1 and neighbours so that we have all the components formed by the end of our traversal.
+ Since its a matrix, we would need some conversion to extract the node number out of the x and y coordinates. One of the ways is : if m is the number of columns, x and y are coordinates, then node number = x*m + y. So we use this to calc node number coordinate corresponds to.
+ Now we traverse each 0 and then lookk in 4 directions and then store their ultimate parents in a map so that we know how many distinct parents/components are the neighbouring 1s part of. We then add up sizes of all the distinct components and then add 1 at the end. We keep track of the maximum size found so far.
+ Then after this we again traverse the array to just compare the individual component sizes with the maximum result so far. This is there to make sure, we are accounting for the case where there is no such 0 whose conversion contributes to joining  of components and in turn giving us a larger componenent. 

```cpp
class DSU {
public:
    vector<int> rank;
    vector<int> parent;
    vector<int> size;
    DSU(int n) {
        for (int i = 0; i < n; i++) {
            rank.push_back(0);
            parent.push_back(i);
            size.push_back(1);
        }
    }

    int getPar(int node) {
        if (parent[node] == node)
            return node;

        return parent[node] = getPar(parent[node]);
    }

    void unionByRank(int u, int v) {
        int up = getPar(u);
        int vp = getPar(v);

        if (up == vp)
            return;
        if (rank[up] < rank[vp]) {
            parent[up] = vp;
        } else if (rank[up] > rank[vp]) {
            parent[vp] = up;
        } else {
            parent[vp] = up;
            rank[up]++;
        }
    }

    void unionBySize(int u, int v) {
        int up = getPar(u);
        int vp = getPar(v);

        if (up == vp)
            return;
        if (size[up] < size[vp]) {
            parent[up] = vp;
            size[vp] += size[up];
        } else {
            parent[vp] = up;
            size[up] += size[vp];
        }
    }
};

class Solution {
public:
    bool isValid(int row, int col, vector<vector<int>>& grid) {
        return (row >= 0 && row < grid.size() && col >= 0 &&
                col < grid[0].size() && grid[row][col] == 1);
    }
    int largestIsland(vector<vector<int>>& grid) {
        vector<vector<int>> dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        int n = grid.size(), m = grid[0].size();
        DSU ds(n * m);
        for (int i = 0; i < grid.size(); i++) {
            for (int j = 0; j < grid[0].size(); j++) {
                for (auto dir : dirs) {
                    int x = dir[0] + i;
                    int y = dir[1] + j;
                    if (grid[i][j] == 1 && isValid(x, y, grid)) {
                        int currNode = i * m + j;
                        int nbr = x * m + y;
                        ds.unionBySize(currNode, nbr);
                    }
                }
            }
        }

        unordered_map<int,int> hm;
        int res = 0;
        for (int i = 0; i < grid.size(); i++) {
            for (int j = 0; j < grid[0].size(); j++) {
                if (grid[i][j] == 0) {
                    for (auto dir : dirs) {
                        int x = dir[0] + i;
                        int y = dir[1] + j;
                        if (isValid(x, y, grid)) {
                            int nbr = x * m + y;
                            hm[ds.getPar(nbr)]++;
                        }
                    }
                    int compo_sizes = 0;
                    for(auto it : hm) {
                        compo_sizes += ds.size[it.first];
                    }
                    res = max(res, compo_sizes+1);
                    hm.clear();
                }
            }
        }

    // This is step is there to tackle as case where there are no zeros or there is no such 0 which being converted to 1 contributes to a
    // larger island. So in this case we choose the largest among the existing islands.
        for (int i = 0; i < grid.size(); i++) {
            for (int j = 0; j < grid[0].size(); j++) {
                if (grid[i][j] == 1) {
                    int node = i*m + j;
                    hm[ds.getPar(node)]++;
                }
            }
        }

        for(auto it : hm) {
            res = max(res, ds.size[it.first]);
        }

        return res;
    }
};
```

*Time Complexity : O(M * N)*

**11. Number of Islands - II - Online Queries**  
You are given an n, m which means the row and column of the 2D matrix, and an array of size k denoting the number of operations. Matrix elements are 0 if there is water or 1 if there is land. Originally, the 2D matrix is all 0 which means there is no land in the matrix. The array has k operator(s) and each operator has two integers A[i][0], A[i][1] means that you can change the cell matrix[A[i][0]][A[i][1]] from sea to island. Return how many islands are there in the matrix after each operation. You need to return an array of size k.

Note: An island means a group of 1s such that they share a common side.  
```cpp
Example 1:
Input Format: n = 4 m = 5 k = 4 A = {{1,1},{0,1},{3,3},{3,4}} 
Output: 1 1 2 2 
Explanation: The following illustration is the representation of the operation:

Example 2:
Input Format: n = 4 m = 5 k = 12 A = {{0,0},{0,0},{1,1},{1,0},{0,1},{0,3},{1,3},{0,4}, {3,2}, {2,2},{1,2}, {0,2}} 
Output: 1 1 2 1 1 2 2 2 3 3 1 1 
Explanation: If we follow the process like in example 1, we will get the above result.
```
Approach : [Striver's Explaination](https://takeuforward.org/graph/number-of-islands-ii-online-queries-dsu-g-51)  

```cpp
#include <bits/stdc++.h>
using namespace std;

// Disjoint Set Union (DSU) class
class DisjointSet {
    vector<int> rank, parent, size;
public:
    DisjointSet(int n) {
        rank.resize(n + 1, 0);
        parent.resize(n + 1);
        size.resize(n + 1, 1);
        for (int i = 0; i <= n; i++) {
            parent[i] = i;
        }
    }

    int findUPar(int node) {
        if (node == parent[node])
            return node;
        return parent[node] = findUPar(parent[node]); // Path compression
    }

    void unionByRank(int u, int v) {
        int ulp_u = findUPar(u);
        int ulp_v = findUPar(v);
        if (ulp_u == ulp_v) return;
        if (rank[ulp_u] < rank[ulp_v]) {
            parent[ulp_u] = ulp_v;
        } else if (rank[ulp_v] < rank[ulp_u]) {
            parent[ulp_v] = ulp_u;
        } else {
            parent[ulp_v] = ulp_u;
            rank[ulp_u]++;
        }
    }

    void unionBySize(int u, int v) {
        int ulp_u = findUPar(u);
        int ulp_v = findUPar(v);
        if (ulp_u == ulp_v) return;
        if (size[ulp_u] < size[ulp_v]) {
            parent[ulp_u] = ulp_v;
            size[ulp_v] += size[ulp_u];
        } else {
            parent[ulp_v] = ulp_u;
            size[ulp_u] += size[ulp_v];
        }
    }
};

// Solution to find the number of islands after each operation
class Solution {
private:
    // Helper function to check if a cell is within bounds
    bool isValid(int adjr, int adjc, int n, int m) {
        return adjr >= 0 && adjr < n && adjc >= 0 && adjc < m;
    }

public:
    // Main function to process all operators and count number of islands
    vector<int> numOfIslands(int n, int m, vector<vector<int>>& operators) {
        DisjointSet ds(n * m); // Disjoint set to manage connected land cells
        int vis[n][m];
        memset(vis, 0, sizeof vis);
        int cnt = 0;
        vector<int> ans;

        for (auto it : operators) {
            int row = it[0], col = it[1];

            // Skip if the cell is already land
            if (vis[row][col] == 1) {
                ans.push_back(cnt);
                continue;
            }

            // Mark cell as land
            vis[row][col] = 1;
            cnt++;

            // Directions to check for adjacent cells (up, right, down, left)
            int dr[] = {-1, 0, 1, 0};
            int dc[] = {0, 1, 0, -1};
            for (int ind = 0; ind < 4; ind++) {
                int adjr = row + dr[ind], adjc = col + dc[ind];
                if (isValid(adjr, adjc, n, m)) {
                    if (vis[adjr][adjc] == 1) {
                        int nodeNo = row * m + col;
                        int adjNodeNo = adjr * m + adjc;
                        if (ds.findUPar(nodeNo) != ds.findUPar(adjNodeNo)) {
                            cnt--;
                            ds.unionBySize(nodeNo, adjNodeNo);
                        }
                    }
                }
            }

            ans.push_back(cnt);
        }
        return ans;
    }
};

int main() {
    int n = 4, m = 5;
    vector<vector<int>> operators = {{0, 0}, {0, 0}, {1, 1}, {1, 0}, {0, 1},
        {0, 3}, {1, 3}, {0, 4}, {3, 2}, {2, 2}, {1, 2}, {0, 2}
    };

    Solution obj;
    vector<int> ans = obj.numOfIslands(n, m, operators);
    for (auto res : ans) {
        cout << res << " ";
    }
    cout << endl;
    return 0;
}

```


***MST***  
*A Minimum Spanning Tree (MST) is a subset of edges from a connected, undirected, and weighted graph that connects all the vertices together without any cycles and with the minimum possible total edge weight.*  

**12. Min Cost to Connect All Points / Prims's Algorithm**    

**By Prim's Algo**

You are given an array points representing integer coordinates of some points on a 2D-plane, where points[i] = [xi, yi].
The cost of connecting two points [xi, yi] and [xj, yj] is the manhattan distance between them: |xi - xj| + |yi - yj|, where |val| denotes the absolute value of val.
Return the minimum cost to make all points connected. All points are connected if there is exactly one simple path between any two points.

```cpp
Input: points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
Output: 20
Input: points = [[3,12],[-2,5],[-4,1]]
Output: 18
```
Approach : This problem straight aways is asking what will be the weight of Minimum SPanning tree of the Graph formed by the coordinates. The graph here is not directly given but here each coordinate is connected with the other and the weight of the path is the Manhattan Distance. 		
So once you understand that all that is left is to form the graph and Use one of the algorithm (Prim's or Kruskals) to form MST or just get the total weight in the MST as in this case as the whole MST is not required. So we perform following steps (Prim's Algroithm) :
+ We first form the graph which has an edge b/w every coordinate.
+ We in our mind mark each coordinate with a node number which is equal to the index at which that coordinate is there in the input array.
+ We push the pair of weight and node into Min-Heap. First we push {0, 0} signifying first 0 node with 0 weight is pushed.
+ Now we run a loop till the heap is non-empty and in each iteration we pull out the entry with minimum weight. We check if the node is not visited and if not We mark the node as visited, use that distance in our final minimum weight calculation and explore its unvisited neighbours and push the pair {Manhattan distance, neighbour} into the heap. If the node was already visited we do nothing and just continue with next iteration.
+ After we are done, we should have the minimum weight of the MST with us.

*P.S : Here we only interested with the total weight of the MST but if we can also form the whole MST using Prim's Algo. There would be an additional step in the above process. So For that just checkout : [TUF Min Weight](https://takeuforward.org/data-structure/prims-algorithm-minimum-spanning-tree-c-and-java-g-45)*
```cpp
class Solution {
public:
    bool compar(Node n1, Node n2) {
        return n2.dist < n1.dist;
    }
    int minCostConnectPoints(vector<vector<int>>& points) {
        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
        vector<vector<int>> adj(points.size());
        for(int i=0; i<points.size(); i++) {
            for(int j=i+1; j<points.size(); j++) {
                adj[i].push_back(j);
                adj[j].push_back(i);
            }
        }

        pq.push({0, 0});
        vector<bool> vis(points.size(), false);
        int res = 0;
        while(!pq.empty()) { 
            int dist = pq.top().first;
            int node = pq.top().second;
            pq.pop();

            if(vis[node])
                continue;
            
            vis[node] = true;
            res += dist;
            for(auto nbr : adj[node]) {
                if(!vis[nbr])
                    pq.push({abs(points[nbr][0] - points[node][0]) + abs(points[nbr][1] - points[node][1]), nbr});
            }
        }
        return res;
    }
};
```
Time Complexity : O(E*logE) -> E is no of Edges || Space Complexity : O(E) + O(V), where E = no. of edges and V = no. of vertices. O(E) occurs due to the size of the priority queue and O(V) due to the visited array.

**By Kruskal's Algo (DisjointSet)**  

Intuition with Kruskal's Algo is quite simple. 
+ Store all the edges in a List and sort them by weight.
+ Now iterate over the list and check the nodes of the edge , if they are in same component or have same Ultimate parent. If they are then that means we can ignore this edge as those two nodes are already connected through some route (directly or indirectly). If they are not part of same component then considert hat edge, include that weight as part of MST Weight calc and perform Union of those two nodes i.e make them connected. 


```cpp
class DSU{
public :
    vector<int> parent, rank;
    DSU(int n) {
        for(int i=0; i<n; i++) {
            parent.push_back(i);
            rank.push_back(0);
        }
    }

    int getPar(int node) {
        if(parent[node] == node)
            return node;
        return parent[node] = getPar(parent[node]);
    }

    void unionByRank(int u, int v) {
        int up = getPar(u);
        int vp = getPar(v);
        if(up == vp)
            return;
        if(rank[up] < rank[vp]){
            parent[up] = vp;
        } else if(rank[vp] < rank[up]){
            parent[vp] = up;
        } else {
            parent[vp] = up;
            rank[up]++;
        }
    }
};

class Solution {
public:
    int minCostConnectPoints(vector<vector<int>>& points) {
        vector<vector<int>> adj(points.size());
        for(int i=0; i<points.size(); i++) {
            for(int j=i+1; j<points.size(); j++) {
                adj[i].push_back(j);
                adj[j].push_back(i);
            }
        }

        // {wt, {u, v}} ---> edge
        vector<pair<int, pair<int,int>>> edges;
        for(int i=0; i<adj.size(); i++) {
            for(int j=0; j<adj[i].size(); j++) {
                int nbr = adj[i][j];
                int wt = abs(points[i][0] - points[nbr][0]) + abs(points[i][1] - points[nbr][1]);
                edges.push_back({wt, {i, nbr}});
            }
        }

        sort(edges.begin(), edges.end());

        DSU ds(points.size());
        int res = 0;

        for(auto edge : edges) {
            int u = edge.second.first;
            int v = edge.second.second;
            int wt = edge.first;

            if(ds.getPar(u) == ds.getPar(v))
                continue;
    
            res += wt;
            ds.unionByRank(u, v);
        }

        return res;
    }
};
```
Time Complexity : *O(E LogE) or O(E LogV)*  
Space Compelxity : *O(E + V)*   

**13. Swim in Rising Water**  

You are given an n x n integer matrix grid where each value grid[i][j] represents the elevation at that point (i, j).
It starts raining, and water gradually rises over time. At time t, the water level is t, meaning any cell with elevation less than equal to t is submerged or reachable.
You can swim from a square to another 4-directionally adjacent square if and only if the elevation of both squares individually are at most t. You can swim infinite distances in zero time. Of course, you must stay within the boundaries of the grid during your swim.

Return the minimum time until you can reach the bottom right square (n - 1, n - 1) if you start at the top left square (0, 0).  

[Leetcode 778](https://leetcode.com/problems/swim-in-rising-water/description/)  

Approach : Basically You're minimising the maximum elevation along your path from (0,0) to (n-1,n-1) — the classic minimax path problem. The min-heap always surfaces the path whose worst cell so far is smallest. So we use Dijkstra's Algorithm (or Prim's) with some modification. We pop from heap and explore all the nodes in its neighbours. Only diff is what we do with weight. So isntead of adding up the edge weight and then pushing (as in Dijkstra), we calculate the max of current node elevation and its neighbour cell and push it in the heap. We do this because here to traverse from current node to neighbouring node, have to wait till the max of the two elevations and that is what the edge weight is. So instead of just pusing the edge weight as in Prim's Algo, our calculation of edge weight is a bit different.

## How it differs from standard Dijkstra

|                   | Standard Dijkstra          | LC 778 (minimax)               |
|-------------------|----------------------------|--------------------------------|
| Heap stores       | `(dist from source, node)` | `(max elevation so far, r, c)` |
| Relaxation        | `dist[src] + edge_weight`  | `max(cost, grid[nr][nc])`      |
| Terminates        | All nodes settled          | Destination first popped       |
| vis array needed  | Optional                   | Required                       |  


```cpp
vector<vector<int>> dirs = {{0,1}, {0,-1}, {-1,0}, {1, 0}};
class Solution {
public:
    int swimInWater(vector<vector<int>>& grid) {
        priority_queue<pair<int, pair<int,int>>, vector<pair<int, pair<int,int>>>, greater<pair<int, pair<int,int>>>> pq;
        vector<vector<bool>> vis(grid.size(), vector<bool>(grid[0].size(), false));
        
        pq.push({grid[0][0], {0, 0}});

        while(!pq.empty()) {
            int wt = pq.top().first;
            int x = pq.top().second.first;
            int y = pq.top().second.second;
            pq.pop();
            if(x==grid.size()-1 && y==grid[0].size()-1)
                return wt;

            if(vis[x][y])
                continue;
            vis[x][y] = true;

            for(auto dir : dirs) {
                int nx = x + dir[0];
                int ny = y + dir[1];
                if(nx >=0 && ny>=0 && nx<grid.size() && ny <grid[0].size() && !vis[nx][ny]) {
                    pq.push({max(wt, grid[nx][ny]), {nx, ny}});
                }
            }
        }
        return -1;
    }
};
```
