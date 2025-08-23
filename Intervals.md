**Intervals**

**1. Non-Overlapping Invetervals**  
Given an array of intervals intervals where intervals[i] = [starti, endi], return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.  
Note that intervals which only touch at a point are non-overlapping. For example, [1, 2] and [2, 3] are non-overlapping.

Example 1:   
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]  
Output: 1  
Explanation: [1,3] can be removed and the rest of the intervals are non-overlapping.  

Example 2:  
Input: intervals = [[1,2],[1,2],[1,2]]  
Output: 2  
Explanation: You need to remove two [1,2] to make the rest of the intervals non-overlapping.  

Example 3:  
Input: intervals = [[1,2],[2,3]]  
Output: 0  
Explanation: You don't need to remove any of the intervals since they're already non-overlapping.

Approach :
So to understand the appraoch it is recommended to checkout : [N-meetings in one room](https://takeuforward.org/data-structure/n-meetings-in-one-room/). The simple approach is to try to sort the meetings in order of end time and then try to accomodate as many meetings as possible. The reason sorting by start time won't work here is because you can have an interval whose start time is the least but its end time is the maximum of all. So in this case even though it starts early but it ends very late due to which we are missing on other intervals which could have been accomodated within that interval. We want to include meetings that end early i.e have earlier end-time so that I can greedily accomodate more and more meetings if I have more time left.
But that is N-Meetings, and in this problem we have to just find how many of those slots we need to remove that is basically seeing how many slot you can accomodate and then deduct from total slots to get the slots we need to reject.

```cpp
bool compar(const vector<int>& v1, const vector<int>& v2) {
    if(v1[1] != v2[1])
        return v1[1] < v2[1];
    else
        return v1[0] < v2[0];
}

class Solution {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end(), compar);
        int i=1, cnt=1, limit = intervals[0][1];
        while(i<intervals.size()) {
            if(intervals[i][0] >= limit){
                cnt++;
                limit = intervals[i][1];
            }
            i++;
        }
        return intervals.size() - cnt;
    }
};
```

**2. Insert Intervals**  
You are given an array of non-overlapping intervals intervals where intervals[i] = [starti, endi] represent the start and the end of the ith interval and intervals is sorted in ascending order by starti. You are also given an interval newInterval = [start, end] that represents the start and end of another interval.
Insert newInterval into intervals such that intervals is still sorted in ascending order by starti and intervals still does not have any overlapping intervals (merge overlapping intervals if necessary).
Return intervals after the insertion.

Note that you don't need to modify intervals in-place. You can make a new array and return it.

Example 1:
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
Example 2:

Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].

Approach : In this question we need to observe that the vector is sorted according to the start time. So new interval merges when its end time is lesser than the current interval at any index.
So we observe one thing the whole array can be divided into two parts 1st part where the end time is lesser than the new Interval start time. The 2nd part is where intervals merge and last part is again where the start times are more than the ending times of the merged intervals.
So we run three loops and keep on tasking care of each of the three scenarios.
Time Complexity : O(NLogN)

```cpp
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
        vector<vector<int>> res;
        int i=0;
        while(i<intervals.size() && intervals[i][1] < newInterval[0]){
            res.push_back(intervals[i]);
            i++;
        }

        int start = newInterval[0];
        int end = newInterval[1];
        while(i<intervals.size() && end >= intervals[i][0]){
            start = min(intervals[i][0], start);
            end = max(intervals[i][1], end);
            i++;
        }
        res.push_back({start, end});

        while(i<intervals.size()){
            res.push_back(intervals[i]);
            i++;
        }
        return res;
    }
};
```
**2. Merge Intervals**
Given an array of intervals where intervals[i] = [starti, endi], merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input.

Example 1:

Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlap, merge them into [1,6].
Example 2:

Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.

Approach : So the appraoch here is to first sort the array according to the start times and then iterate over the array and see if the last interval in the array 
is overlapping with the current index and if they are then just consider the minimum of the starting and maximum of ending to get the final merged interval and then pop the previous one 
and push the next merged intervals. And we jst keep on doinf this till the end. 
Time Complexity : O(N)

```cpp
bool compare(vector<int> &v1, vector<int> &v2) {
    if(v1[0] < v2[0])
        return true;
    return false;
}
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end(), compare);
        vector<vector<int>> res;
        res.push_back(intervals[0]);
        int j=1, ind = 0;
        while(j<intervals.size()) {
            ind = res.size()-1;
            if(intervals[j][0] <= res[ind][1]) {
                int start = min(res[ind][0], intervals[j][0]);
                int end = max(res[ind][1], intervals[j][1]);
                res.pop_back();
                res.push_back({start, end});
            } else 
                res.push_back(intervals[j]);
            j++;
        }
        return res;
    }
};
```
