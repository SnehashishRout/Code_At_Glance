**1. Find median from Data Stream**  
The median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value, and the median is the mean of the two middle values.

For example, for arr = [2,3,4], the median is 3.
For example, for arr = [2,3], the median is (2 + 3) / 2 = 2.5.
Implement the MedianFinder class:

MedianFinder() initializes the MedianFinder object.
void addNum(int num) adds the integer num from the data stream to the data structure.
double findMedian() returns the median of all elements so far. Answers within 10-5 of the actual answer will be accepted.  

Input  
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]  
[[], [1], [2], [], [3], []]  
Output  
[null, null, null, 1.5, null, 2.0]
  
Explanation  
MedianFinder medianFinder = new MedianFinder();  
medianFinder.addNum(1);    // arr = [1]  
medianFinder.addNum(2);    // arr = [1, 2]  
medianFinder.findMedian(); // return 1.5 (i.e., (1 + 2) / 2)  
medianFinder.addNum(3);    // arr[1, 2, 3]  
medianFinder.findMedian(); // return 2.0

**2. K-Closet Points to Origin**   
Given an array of points where points[i] = [xi, yi] represents a point on the X-Y plane and an integer k, return the k closest points to the origin (0, 0).
The distance between two points on the X-Y plane is the Euclidean distance (i.e., √(x1 - x2)2 + (y1 - y2)2).
You may return the answer in any order. The answer is guaranteed to be unique (except for the order that it is in).  

Input: points = [[1,3],[-2,2]], k = 1  
Output: [[-2,2]]  
Explanation:  
The distance between (1, 3) and the origin is sqrt(10).  
The distance between (-2, 2) and the origin is sqrt(8). Since sqrt(8) < sqrt(10), (-2, 2) is closer to the origin.
We only want the closest k = 1 points from the origin, so the answer is just [[-2,2]].    

Input: points = [[3,3],[5,-1],[-2,4]], k = 2  
Output: [[3,3],[-2,4]]  
Explanation: The answer [[-2,4],[3,3]] would also be accepted.

Approach : So here we just need to use a Min-Heap but the rule based on which the elements are sorted need to be customeized. So this problem is a good example to understand how rules can be customized (in c++) to suit our purpose. But the algo remains simple and that is to either have MinHeap formed of all the points and then pop the first k elements or you can have a Max-Heap that always maintians the size K. While inserting a new elkement if size exceeds K then we just push the max element and then pop the Max element. This way the the end the elements we have in Heap are the K smallest elements.   

```cpp
class Compare {
    public:
        bool operator()(vector<int> v1, vector<int> v2) {
            return sqrt(v1[0]*v1[0] + v1[1]*v1[1]) > sqrt(v2[1]*v2[1] + v2[0]*v2[0]);
        }
};

class Solution {
public:
    vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
        priority_queue<vector<int>, vector<vector<int>>, Compare> pq;
        vector<vector<int>> res;
        for(auto x : points)
            pq.push(x);

        for(int i=0; i < k; i++) {
            res.push_back(pq.top());
            pq.pop();
        }
        return res;
    }
};
```

**3. Task Scheduling**  
You are given an array of CPU tasks, each labeled with a letter from A to Z, and a number n. Each CPU interval can be idle or allow the completion of one task. Tasks can be completed in any order, but there's a constraint: there has to be a gap of at least n intervals between two tasks with the same label.
Return the minimum number of CPU intervals required to complete all tasks.  

Example 1:  
Input: tasks = ["A","A","A","B","B","B"], n = 2  
Output: 8  
Explanation: A possible sequence is: A -> B -> idle -> A -> B -> idle -> A -> B.  
After completing task A, you must wait two intervals before doing A again. The same applies to task B. In the 3rd interval, neither A nor B can be done, so you idle. By the 4th interval, you can do A again as 2 intervals have passed.  

Example 2:  
Input: tasks = ["A","C","A","B","D","B"], n = 1  
Output: 6  
Explanation: A possible sequence is: A -> B -> C -> D -> A -> B.  
With a cooling interval of 1, you can repeat a task after just one other task.  

Example 3:  
Input: tasks = ["A","A","A", "B","B","B"], n = 3  
Output: 10  
Explanation: A possible sequence is: A -> B -> idle -> idle -> A -> B -> idle -> idle -> A -> B.  
There are only two types of tasks, A and B, which need to be separated by 3 intervals. This leads to idling twice between repetitions of these tasks.

Approach : The approach to this problem can be thought of after we find out that to get the tasks done in minimum time, we need to do the most frequent task first because if we dont do that and keep the most frequent task to the end we will have to waste a lot of time in idle time which can be utilized doing some other task. So the intution is we use max heap to slove the problem to fetch the work with highest freq first. So we maintain a queue also so that we can keep track of elements after they are done with their cool off period and can be picked up again.   
So the Algo is simple, just add the elements with their frequencies in the max heap initially and then start picking up the most frequent one, and then pop it out and decrement its frequency by 1 as it is performed once and then push it in the queue along with the time it will next be available to be picked up. A time counter keeps on going and we check if we have any pending task in the queue and if they can be picked up at that time. If yes then they are popped from queue and pushed to Heap. And again we fetch the max freq element from heap and schedule it and so on. If a process freq becomes 0 then it means we have already compelted all the task of that element and no need to push in the queue. We return the final time after there is no jobs in the heap and the queue is empty of any scheduled tasks.

```cpp
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        priority_queue<int> pq;
        vector<int> freq(26, 0);
        queue<vector<int>> que;
        for(auto x : tasks) {
            freq[x - 'A']++;
        }
        for(int i=0; i<freq.size(); i++) {
            if(freq[i] > 0)
                pq.push(freq[i]);
        }
        int time = 0;
        while(!pq.empty() || !que.empty()) {
            time++;
            int charFreq = 0;
            while(!que.empty() && que.front()[1] == time) {  // We pop out all elements in the queue that can be picked at that time and push to heap 
                pq.push(que.front()[0]);
                que.pop();
            }
            if(!pq.empty()) {    // Seeing if any jobs are there to be worked upon
                charFreq = pq.top();
                pq.pop();
                if(charFreq - 1 > 0)
                    que.push({charFreq-1, time + n + 1});
            }
        }
        return time;
    }
};
```

