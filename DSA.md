**1. Binary Search**

**A.** You are given a sorted array consisting of only integers where every element appears exactly twice, except for one element which appears exactly once.
   Return the single element that appears only once.
   Your solution must run in O(log n) time and O(1) space.

Example 1:
Input: nums = [1,1,2,3,3,4,4,8,8]
Output: 2

Example 2:
Input: nums = [3,3,7,7,10,11,11]
Output: 10

Solution : The observation to be seen here is whichever is the single element, that partitions the array in to two parts : left and right. 
           All the elements in left part are such that the First element is at even index and its corresponding other one is at odd. And vice versa in Right half.
           So every time we can apply binary search with the following appraoch :

1. Initialize two pointers, left and right, to the first and last indices of the input array, respectively.
2. While the left pointer is less than the right pointer:
    a. Compute the index of the middle element by adding left and right and dividing by 2.
    b. If the index of the middle element is odd, subtract 1 to make it even.
    c. Compare the middle element with its adjacent element on the right:
       i. If the middle element is not equal to its right neighbor, the single element must be on the left side of the array, so update the right pointer to be the current middle index.
       ii. Otherwise, the single element must be on the right side of the array, so update the left pointer to be the middle index plus 2.
3. When the left and right pointers converge to a single element, return that element.
   
<pre>
class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        int left = 0, right = nums.size() - 1;
        while (left < right) {
            int mid = (left + right) / 2;
            if (mid % 2 == 1) {
                mid--;
            }
            if (nums[mid] != nums[mid + 1]) {
                right = mid;
            } else {
                left = mid + 2;
            }
        }
        return nums[left];
    }
};
</pre>

**B.** Given two sorted arrays arr1 and arr2 of size m and n respectively, return the median of the two sorted arrays. The median is defined as the middle value of a sorted list of numbers. In case the length of the list is even, the median is the average of the two middle elements.
   Your solution must run in O(log (n+m)) time

Example 1:
Input: arr1 = [2, 4, 6], arr2 = [1, 3, 5]
Output: 3.5
Explanation: The array after merging arr1 and arr2 will be [ 1, 2, 3, 4, 5, 6 ]. As the length of the merged list is even, the median is the average of the two middle elements. Here two medians are 3 and 4. So the median will be the average of 3 and 4, which is 3.5.

Example 2:
Input: arr1 = [2, 4, 6], arr2 = [1, 3]
Output: 3.0
Explanation: The array after merging arr1 and arr2 will be [ 1, 2, 3, 4, 6 ]. The median is simply 3.
Solution : The observation to be seen here is whichever is the single element, that partitions the array in to two parts : left and right. 
           So every time we can apply binary search with the following appraoch :

1. Assign left and right pointer to the smaller length array of the two (lets say arr1) and apply binbary search on it.
2. Find the mid and assume that mid is the index of the arr1 till which we have to consider the elements of the arr1 and rest of the elements (half - number of elements in     left partition of arr1) are under left partitions of arr2. The elements from both the left partitions of two arrays are assumed to be valid left partitions of the full      merged sorted array.
3. To check if the left portion we have assumed is actually the correct left-portion, we compare the maximum of rightmost elements of left-portion both arrays and it should    be lesser than the minimum of the left-most elements of the right portions of both the arrays. 
   If the above condition is not true, then we have to decrement right-pointer of arr1 to mid-1, if rightmost element of left-portion of arr1 is > right-most of left           portion of arr2, so that we can decrease the number of elements count of arr1 elements in our imaginary should-be left-portion of final sorted array.
   Increment left pointer to mid+1, if rightmost element of left-portion of arr2 is > right-most of left portion of arr1, so that we can decrease the number of elements        count of arr2 elements in our imaginary should-be left-portion of final sorted array by increasing the number of arr1 elements to be included.
4. On the other hand if the conditions are correct, then we just have to check if the total number of elements (combining arr1 and arr2) is odd or even. If its odd then        consider MIN of Left-most element of right portion of both the arrays. Else, if it is even numbered, then it is [(max(right-most part of left portions of both arrays) +      min(left-most of right portion of both arrays)] / 2.
5. Link Solution : [Neetcode Solution](https://www.youtube.com/watch?v=q6IEA26hvXc)

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        vector<int> v;
        if(nums1.size()>nums2.size()){
            v = nums2;
            nums2 = nums1;
            nums1 = v;
        }
        
        int l=0, h=nums1.size()-1;
        int half = (nums1.size() + nums2.size())/2;
        int len2 = 0;
        while(true){
            int mid = floor((double)(l+h)/2);
            // Postion of the element in v2 till which its left part extends
            len2 = half - mid - 2;
            int l1 = mid >= 0? nums1[mid]:INT_MIN;
            int l2 = len2 >= 0? nums2[len2]:INT_MIN;
            int r1 = mid+1 < nums1.size()? nums1[mid+1]:INT_MAX;
            int r2 = len2+1 < nums2.size()? nums2[len2+1]: INT_MAX;
            if(max(l1,l2) <= min(r1,r2)){
                if((nums1.size() + nums2.size())%2 == 0){
                    return (double)(max(l1,l2) + min(r1,r2)) / 2;
                } else
                    return min(r1,r2);
            } else {
                if(l1 > r2){
                    //Need to reduce elements from left-half of 1st array
                    h=mid-1;
                } else {
                    //Need to decrease number of elements from left-half of 2nd array &
                    // by increasing more from 1st array
                    l=mid+1;
                }
            }
        }
    }
};
```

**C.** Given an array ‘arr of integer numbers, ‘ar[i]’ represents the number of pages in the ‘i-th’ book. There are a ‘m’ number of students, and the task is to allocate all the books to the students.
Allocate books in such a way that:

Each student gets at least one book.
Each book should be allocated to only one student.
Book allocation should be in a contiguous manner.
You have to allocate the book to ‘m’ students such that the maximum number of pages assigned to a student is minimum. If the allocation of books is not possible. return -1

Examples

Example 1:
Input Format: n = 4, m = 2, arr[] = {12, 34, 67, 90}
Result: 113
Explanation: The allocation of books will be 12, 34, 67 | 90. One student will get the first 3 books and the other will get the last one.

Example 2:
Input Format: n = 5, m = 4, arr[] = {25, 46, 28, 49, 24}
Result: 71
Explanation: The allocation of books will be 25, 46 | 28 | 49 | 24.

We can allocate books in several ways but it is clearly said in the question that we have to allocate the books in such a way that the maximum number of pages received by a student should be minimum.


Assume the given array is {25 46 28 49 24} and number of students, M = 4. Now, we can allocate these books in different ways. Some of them are the following:


25 | 46 | 28 | 49, 24  → Maximum no. of pages a student receive = 73
25 | 46 | 28, 49 | 24  → Maximum no. of pages a student receive = 77
25 | 46, 28 | 49 | 24  → Maximum no. of pages a student receive = 74
25, 46 | 28 | 49 | 24  → Maximum no. of pages a student receive = 71

From the above allocations, we can clearly observe that the minimum possible maximum number of pages is 71.

**Observations**:

Minimum possible answer: We will get the minimum answer when we give n books of the array to n students(i.e. Each student will receive 1 book). Now, in this case, the maximum number of pages will be the maximum element in the array. So, the minimum possible answer is max(arr[]).
Maximum possible answer: We will get the maximum answer when we give all n books to a single student. The maximum no. of pages he/she will receive is the summation of array elements i.e. sum(arr[]). So, the maximum possible answer is sum(arr[]).

From the observations, it is clear that our answer lies in the range [max(arr[]), sum(arr[])].

How to calculate the number of students to whom we can allocate the books if one can receive at most ‘pages’ number of pages:

In order to calculate the number of students we will write a function, countStudents(). This function will take the array and ‘pages’ as parameters and return the number of students to whom we can allocate the books.
countStudents(arr[], pages):

We will first declare two variables i.e. ‘students’(stores the no. of students), and pagesStudent(stores the number of pages of a student). As we are starting with the first student, ‘students’ should be initialized with 1.
We will start traversing the given array.
If pagesStudent + arr[i] <= pages: If upon adding the pages with the existing number of pages does not exceed the limit, we can allocate this i-th book to the current student.
Otherwise, we will move to the next student(i.e. students += 1 ) and allocate the book.

Finally, we will return the value of ‘students’.

***This is typical maximise the minimum of Minimize the Maximum type of Problem. DP and recursion can also be used but the most optimized way of doing such type of Questions are Binary Search on the search space. Challenge only lies in deteriming what our search space is and how to determine isAllocation possible in O(n) Linear Time.***

Upon closer observation, we can recognize that our answer space, represented as [max(arr[]), sum(arr[])], is actually sorted. Additionally, we can identify a pattern that allows us to divide this space into two halves: one consisting of potential answers and the other of non-viable options. So, we will apply binary search on the answer space.

Algorithm:
If m > n: In this case, book allocation is not possible and so, we will return -1.
Place the 2 pointers i.e. low and high: Initially, we will place the pointers. The pointer low will point to max(arr[]) and the high will point to sum(arr[]).
Calculate the ‘mid’: Now, inside the loop, we will calculate the value of ‘mid’ using the following formula:
mid = (low+high) // 2 ( ‘//’ refers to integer division)
Eliminate the halves based on the number of students returned by countStudents():
We will pass the potential number of pages, represented by the variable 'mid', to the ‘countStudents()' function. This function will return the number of students to whom we can allocate the books.
If students > m: It means that even after taking in maximum number pages for each student, without corssing the max limit of pages, the least number of students we could reach is greater than m and thus we cant go lesser with the current limit on Pages. Thus, we can conclude that the number ‘mid’ is smaller than our answer. So, we will eliminate the left half and consider the right half(i.e. low = mid+1).
Otherwise, the value mid is one of the possible answers. But we want the minimum value. So, we will eliminate the right half and consider the left half(i.e. high = mid-1).
Finally, outside the loop, we will return the value of low as the pointer will be pointing to the answer.
The steps from 3-4 will be inside a loop and the loop will continue until low crosses high.

```cpp
   int countStudents(vector<int> &arr, int pages) {
    int n = arr.size(); //size of array.
    int students = 1;
    long long pagesStudent = 0;
    for (int i = 0; i < n; i++) {
        if (pagesStudent + arr[i] <= pages) {
            //add pages to current student
            pagesStudent += arr[i];
        }
        else {
            //add pages to next student
            students++;
            pagesStudent = arr[i];
        }
    }
    return students;
}

int findPages(vector<int>& arr, int n, int m) {
    //book allocation impossible:
    if (m > n) return -1;

    int low = *max_element(arr.begin(), arr.end());
    int high = accumulate(arr.begin(), arr.end(), 0);
    while (low <= high) {
        int mid = (low + high) / 2;
        int students = countStudents(arr, mid);
        if (students > m) {
            low = mid + 1;
        }
        else {
            high = mid - 1;
        }
    }
    return low;
}
```
To check if you grasped the question or not you may checkout this Leetcode : [split-array-largest-sum](https://leetcode.com/problems/split-array-largest-sum/description/)

**D.** You are given a sorted array containing N integers and a number X, you have to find the occurrences of X in the given array.
```cpp
Example 1:
Input: N = 7,  X = 3 , array[] = {2, 2 , 3 , 3 , 3 , 3 , 4}
Output: 4
Explanation: 3 is occurring 4 times in 
the given array so it is our answer.

Example 2:
Input: N = 8,  X = 2 , array[] = {1, 1, 2, 2, 2, 2, 2, 3}
Output: 5
Explanation: 2 is occurring 5 times in the given array so it is our answer.
```
*Approach* : This can be done using Binary Search in O(Log N). You just need to compute the Last occurrence and the First Occurrence of that element using Binary Search and then the number of diplicates is (Last occurrence - First Occurrence) + 1

**E .** Search In Rotated Array : Given an integer array arr of size N, sorted in ascending order (with distinct values) and a target value k. Now the array is rotated at some pivot point unknown to you. Find the index at which k is present and if k is not present return -1.  

```cpp
Example 1:

Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
Example 2:

Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
Example 3:

Input: nums = [1], target = 0
Output: -1
```

*Approach* : The approach should be very simple if we understand one fact and that is if you are able to find which protion of the array is sorted. So everytime we find a middle element, we just have to figure out which portion of array (left or right) is sorted and the one that is soerted, you can just check if the target element can lie in that portion. If the target can lie in that sorted portion we eliminate the unsorted portion and if not then we eliminate the sorted protion and likewise we can reach our target solution.

```cpp
int search(vector<int>& arr, int n, int k) {
    int low = 0, high = n - 1;
    while (low <= high) {
        int mid = (low + high) / 2;

        //if mid points the target
        if (arr[mid] == k) return mid;

        //if left part is sorted:
        if (arr[low] <= arr[mid]) {
            if (arr[low] <= k && k <= arr[mid]) {
                //element exists:
                high = mid - 1;
            }
            else {
                //element does not exist:
                low = mid + 1;
            }
        }
        else { //if right part is sorted:
            if (arr[mid] <= k && k <= arr[high]) {
                //element exists:
                low = mid + 1;
            }
            else {
                //element does not exist:
                high = mid - 1;
            }
        }
    }
    return -1;
}
```

**F.** Search In Rotated Array II : Given an integer array arr of size N, sorted in ascending order (with **duplicates**) and a target value k. Now the array is rotated at some pivot point unknown to you. Find the index at which k is present and if k is not present return -1.

Approach : The approach will be same as the previous one but there is just one case which we need to handle and i.e. where the low mid and high are the same number which then makes it difficult for us to choose which side is sorted and which one is not. So we explicitly check for this condition where arr[low] == arr[mid] == arr[high] and if that is true, we just shrink our search space from bith sides.

```cpp
bool searchInARotatedSortedArrayII(vector<int>&arr, int k) {
    int n = arr.size(); // size of the array.
    int low = 0, high = n - 1;
    while (low <= high) {
        int mid = (low + high) / 2;

        //if mid points the target
        if (arr[mid] == k) return true;

        //Edge case:
        if (arr[low] == arr[mid] && arr[mid] == arr[high]) {
            low = low + 1;
            high = high - 1;
            continue;
        }

        //if left part is sorted:
        if (arr[low] <= arr[mid]) {
            if (arr[low] <= k && k <= arr[mid]) {
                //element exists:
                high = mid - 1;
            }
            else {
                //element does not exist:
                low = mid + 1;
            }
        }
        else { //if right part is sorted:
            if (arr[mid] <= k && k <= arr[high]) {
                //element exists:
                low = mid + 1;
            }
            else {
                //element does not exist:
                high = mid - 1;
            }
        }
    }
    return false;
}
```

**G.** Find Min in Rotated Array : Given the sorted rotated array nums of unique elements, return the minimum element of this array.
You must write an algorithm that runs in O(log n) time.

Approach : So here we can approach the problem in a way that I know the minimum element will just be next to the maximum element in the array. So if I can get the maximum element then I can increment the index to get my smallest element. Now to get the largest element, we keep a track of indices. If we get any element greater than the current one, we update the index. While we fetch the mid, we check which one is the sorted portion and then eliminate that because in the sorted portion we are sure it wont get larger element, but we might get larger element in the unsorted one. Incase we dont we will still have the current maximum element stored in one of our variable.

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int res = 0;
        int l=0, h=nums.size()-1;
        while(l<=h) {
            int mid = (l+h)/2;
            if(nums[mid] > nums[res])
                res = mid;
            if(nums[mid] >= nums[l])
                l = mid+1;
            else
                h=mid-1;
        }
        return nums[res==nums.size()-1? 0 : res+1];
    }
};
```
**H.** Find Peak Element : <br>
A peak element is an element that is strictly greater than its neighbors.
Given a 0-indexed integer array nums, find a peak element, and return its index. If the array contains multiple peaks, return the index to any of the peaks.
You may imagine that nums[-1] = nums[n] = -∞. In other words, an element is always considered to be strictly greater than a neighbor that is outside the array.

You must write an algorithm that runs in O(log n) time.

```cpp
Example 1:

Input: nums = [1,2,3,1]
Output: 2
Explanation: 3 is a peak element and your function should return the index number 2.
Example 2:

Input: nums = [1,2,1,3,5,6,4]
Output: 5
Explanation: Your function can return either index number 1 where the peak element is 2, or index number 5 where the peak element is 6.
```
Approach : We conduct binary search and for every mid we compute we check if that element at that mid satisfies the condition of peak i.e if its greater than its neighbours.
If it is we return our answer else we check if that element is greater than its right neighbour, and if it is that means peak lies in the left and we make high pointer to mid-1 and otherwise if mid element is greater than its left element then that means peak lies in the right so we make l = mid+1 . In this way we will be searching for the peak. One thing to note is we have to handle the two edges, the first and last element separately and we just check if any of them is a peak element. If not then we start our search excluding the two edges.

Code :
```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        if(nums.size() == 1) return 0;
        if(nums[0] > nums[1]) return 0;
        if(nums[nums.size()-1] > nums[nums.size()-2]) return nums.size()-1;

        int l=1, h=nums.size()-2;
        while(l<=h) {
            int mid = (l+h)/2;
            if(nums[mid-1] < nums[mid] && nums[mid] > nums[mid+1])
                return mid;
            else if(nums[mid-1] < nums[mid])
                l = mid+1;
            else
                h = mid-1;
        }
        return 0;
    }
};
```

**B.S. On 2D Matrix**

**I.** Search in 2D Matrix : You are given an m x n integer matrix matrix with the following two properties:

Each row is sorted in non-decreasing order.
The first integer of each row is greater than the last integer of the previous row.
Given an integer target, return true if target is in matrix or false otherwise.

You must write a solution in O(log(m * n)) time complexity.

```cpp
Input: matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
Output: true

Input: matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 13
Output: false
```
Approach : 


**2. Sliding Window**

**A. Max Consecutive 1's :**  Given a binary array nums and an integer k, return the maximum number of consecutive 1's in the array if you can flip at most k 0's.

```cpp
Example 1:
Input: nums = [1,1,1,0,0,0,1,1,1,1,0], k = 2
Output: 6
Explanation: [1,1,1,0,0,1,1,1,1,1,1]
Bolded numbers were flipped from 0 to 1. The longest subarray is underlined.

Example 2:
Input: nums = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], k = 3
Output: 10
Explanation: [0,0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,1]
Bolded numbers were flipped from 0 to 1. The longest subarray is underlined.
```
Approach : So this problem can be thought of as finding the subarray that has the maximum number of ones with atmost K 0s. So if we cant find that our job is done. So we use the Sliding Window technique. In this we maintain a window size which has atmost K 0 i.e if at any moment the number of 0 in the window exceeds the maximum K 0s then we should shrink the widnow size and proceed. So everytime we keep a count of number of 1 in the window and store the maximum no. of 1 window size.

```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int left = 0;
        int zeros = 0;
        int maxLen = 0;

        for (int right = 0; right < nums.size(); right++) {

            if (nums[right] == 0) {
                zeros++;
            }

            while (zeros > k) {
                if (nums[left] == 0) {
                    zeros--;
                }
                // Move the left pointer forward
                left++; 
            }
            maxLen = max(maxLen, right - left + 1);
        }

        return maxLen;
    }
};
```

**B. Min Swaps to Group 1s :** 
Given a binary array consisting of only 0s and 1s, your task is to find the minimum number of swaps required to group all 1s. If there are no 1s in the array, return -1. The goal is to move all 1s into a contiguous subarray with the least number of swaps.

```cpp
Input: arr[] = [1, 0, 1, 0, 1]
Output: 1
Explanation: Only 1 swap is required to group all 1's together. Swapping index 1 and 4 will give arr[] = [1, 1, 1, 0, 0]

Input: arr[] = [1, 0, 1, 0, 1, 1] 
Output: 1

Input: arr[] = [0, 0, 0]
Output: -1
Explanation: No 1s are present in the array, so return -1.
```

Approach : So here we can just count the total number of 1s in the array and the final subarray/grp of all 1s will be of size = total number of 1. So we take a window of size of total no. of 1s and then keep on slidinf it. The window which has maximum number of 1s in that will be the one where all 1 grp can be achieved through minimum swaps. So we just subvtract the maximum no. of 1s in that window from the window size (i.e. the total 1s in whole array) to get the number of 0.

```cpp
public:
    int minSwaps(vector<int>& arr) {
        int cnt_one = 0, tot_one = 0, max_one = 0;
        for(int x : arr) {
            if(x == 1)
                tot_one++;
        }
        
        if(tot_one == 0)
            return -1;
            
        int l=0, r=0;
        while(r<arr.size()) {
            if(arr[r] == 1)
                cnt_one++;
            max_one = max(max_one, cnt_one);
            if(r-l+1 == tot_one) {
                if(arr[l] == 1)
                    cnt_one--;
                l++;
            }
             r++;
        }
        return tot_one - max_one;
    }
```

**C. Sliding Window in 2D Matrix :**       
Question Link : [Leetcode Magic Sqaures in Grid](https://leetcode.com/problems/magic-squares-in-grid/description/?envType=daily-question&envId=2025-12-30)

This question is mentioned as there is one important concept to be noted here i.e how to slide a 2d Matrix and do operation in each window. Questions involving such type of questions are common and thus appraoch should be kept in mind.

```cpp
class Solution {
public:
    bool checkValid(int val, int i, int j, unordered_map<int, vector<int>> &hm) {
        if(!(val >=1 && val <= 9))
            return false;

        if(hm.find(val) != hm.end()) {
            if(hm[val][0] != i || hm[val][1] != j)
                return false;
        }

        return true;
    }

    int numMagicSquaresInside(vector<vector<int>>& grid) {

        if(grid.size() < 3 || grid[0].size() < 3)
            return 0;

        unordered_map<int, vector<int>> hm;
        int row = 2, cnt = 0;
        while(row < grid.size()) {
            int col = 2;
            while(col < grid[0].size()) {
                bool isValid = true;
                int sum = 0;
                for(int i=row-2; i<=row; i++) {
                    int curr_sum = 0;
                    for(int j=col-2; j<=col; j++) {
                        if(!checkValid(grid[i][j], i, j, hm)) {
                            isValid = false;
                            break;
                        } else {
                            hm[grid[i][j]] = {i,j};
                        }
                        curr_sum += grid[i][j];
                    }
                    if(i==row-2)
                        sum = curr_sum;
                    else if(sum != curr_sum){
                        isValid = false;
                        break;
                    }
                }
                hm.clear();

                for(int j=col-2; j<=col; j++) {
                    int curr_sum = 0;
                    for(int i=row-2; i<=row; i++) {
                        curr_sum += grid[i][j];
                    }
                    if(sum != curr_sum){
                        isValid = false;
                        break;
                    }
                }

                int diag1 = 0, diag2 = 0;
                for(int i=row-2, j=col-2; i<=row && j<=col; i++, j++) {
                    diag1 += grid[i][j];
                }

                for(int i=row, j=col-2; i>=row-2 && j<=col; i--, j++) {
                    diag2 += grid[i][j];
                }

                if(!(diag1 == diag2 && diag1 == sum))
                    isValid = false;
                
                if(isValid) {
                    cnt++;
                    // cout<<"("<<row<<","<<col<<")"<<"="<<sum<<","<<diag1<<endl;
                }
                
                col++;
            }
            row++;
        } 

        return cnt;
    }
};
```
