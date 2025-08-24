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
**2. Meeting Rooms II**  
Given an array of meeting time interval objects consisting of start and end times [[start_1,end_1],[start_2,end_2],...] (start_i < end_i), find the minimum number of days required to schedule all meetings without any conflicts.

Note: (0,8),(8,10) is not considered a conflict at 8.  

Example 1:
Input: intervals = [(0,40),(5,10),(15,20)]
Output: 2
Explanation:
day1: (0,40)
day2: (5,10),(15,20)

Example 2:
Input: intervals = [(4,9)]
Output: 1

Approach : The Approach can be thought of in like the Minimum number of platforms required for Train. Here the departure times are are the interval endtimes and arrival times are the interval start times. We can just have two sorted arrays containing start times and end times separately and then we can just apply the same logic as we do for Minimum number of platforms.

```cpp
class Solution {
public:
    int minMeetingRooms(vector<Interval>& intervals) {
        vector<int> starts;
        vector<int> ends;
        for(auto x : intervals) {
            starts.push_back(x.start);
            ends.push_back(x.end);
        }
        sort(starts.begin(), starts.end());
        sort(ends.begin(), ends.end());
        int i=0, cnt=0, res = 0, j=0;;
        while(i<starts.size()) {
            if(starts[i] < ends[j]){
                cnt++;
                i++;
                res = max(res, cnt);
            } else {
                cnt--;
                j++;
            }
        }
        return res;
    }
};
```
**3. Merge Triplets to Form Target Triplet**  
A triplet is an array of three integers. You are given a 2D integer array triplets, where triplets[i] = [ai, bi, ci] describes the ith triplet. You are also given an integer array target = [x, y, z] that describes the triplet you want to obtain.

To obtain target, you may apply the following operation on triplets any number of times (possibly zero):

Choose two indices (0-indexed) i and j (i != j) and update triplets[j] to become [max(ai, aj), max(bi, bj), max(ci, cj)].
For example, if triplets[i] = [2, 5, 3] and triplets[j] = [1, 7, 5], triplets[j] will be updated to [max(2, 1), max(5, 7), max(3, 5)] = [2, 7, 5].
Return true if it is possible to obtain the target triplet [x, y, z] as an element of triplets, or false otherwise.

Example 1:

Input: triplets = [[2,5,3],[1,8,4],[1,7,5]], target = [2,7,5]
Output: true
Explanation: Perform the following operations:
- Choose the first and last triplets [[2,5,3],[1,8,4],[1,7,5]]. Update the last triplet to be [max(2,1), max(5,7), max(3,5)] = [2,7,5]. triplets = [[2,5,3],[1,8,4],[2,7,5]]
The target triplet [2,7,5] is now an element of triplets.
Example 2:

Input: triplets = [[3,4,5],[4,5,6]], target = [3,2,5]
Output: false
Explanation: It is impossible to have [3,2,5] as an element because there is no 2 in any of the triplets.
Example 3:

Input: triplets = [[2,5,3],[2,3,4],[1,2,5],[5,2,3]], target = [5,5,5]
Output: true
Explanation: Perform the following operations:
- Choose the first and third triplets [[2,5,3],[2,3,4],[1,2,5],[5,2,3]]. Update the third triplet to be [max(2,1), max(5,2), max(3,5)] = [2,5,5]. triplets = [[2,5,3],[2,3,4],[2,5,5],[5,2,3]].
- Choose the third and fourth triplets [[2,5,3],[2,3,4],[2,5,5],[5,2,3]]. Update the fourth triplet to be [max(2,5), max(5,2), max(5,3)] = [5,5,5]. triplets = [[2,5,3],[2,3,4],[2,5,5],[5,5,5]].
The target triplet [5,5,5] is now an element of triplets.

Approach : In this question we know that certain intervals are of no use to us. These are the ones that have any one of the element greater than corresponding element in the target interval. So I want to ignore/remove those intervals and the rest of the intervals I am left with are all the intervals that when merged do not produce any element that is greater than the target interval element. So since we are allowed to repeat the activity of merging any number of times, lets say we are greedy and merge all the intervals. After merging all we should be able to get our target interval because in none of the intervals we have any element that is greater than the target interval element.

```cpp
class Solution {
public:
    bool mergeTriplets(vector<vector<int>>& triplets, vector<int>& target) {
        unordered_map<int,int> hm;
        int val1 = 0, val2 = 0, val3 = 0;
        for(int i=0; i<triplets.size(); i++){
            if(triplets[i][0] > target[0] || triplets[i][1] > target[1] || triplets[i][2] > target[2]) {
                continue;
            }
            val1 = max(triplets[i][0], val1);
            val2 = max(triplets[i][1], val2);
            val3 = max(triplets[i][2], val3);
        }

        return (val1 == target[0] && val2 == target[1] && val3 == target[2]);
        
    }
};
```
