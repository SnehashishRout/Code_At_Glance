**1. Binary Search**

A. You are given a sorted array consisting of only integers where every element appears exactly twice, except for one element which appears exactly once.
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
<pre>class Solution {
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
}; </pre>        


B. Given two sorted arrays arr1 and arr2 of size m and n respectively, return the median of the two sorted arrays. The median is defined as the middle value of a sorted list of numbers. In case the length of the list is even, the median is the average of the two middle elements.
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
           All the elements in left part are such that the First element is at even index and its corresponding other one is at odd. And vice versa in Right half.
           So every time we can apply binary search with the following appraoch :

1. Assign left and right pointer to the smaller length array of the two (lets say arr1) and apply binbary search on it.
2. Find the mid and assume that mid is the index of the arr1 till which we have to consider the elements of the arr1 and rest of the elements (half - number of elements in     left partition of arr1) are under left partitions of arr2. The elements from both the left partitions of two arrays are assumed to be valid left partitions of the full      merged sorted array.
3. To check if the left portion we have assumed is actually the correct left-portion, we compare the maximum of rightmost elements of left-portion both arrays and it should    be lesser than the minimum of the left-most elements of the right portions of both the arrays. 
   If the above condition is not true, then we have to decrement right-pointer of arr1 to mid-1, if rightmost element of left-portion of arr1 is > right-most of left           portion of arr2, so that we can decrease the number of elements count of arr1 elements in our imaginary should-be left-portion of final sorted array.
   Increment left pointer to mid+1, if rightmost element of left-portion of arr2 is > right-most of left portion of arr1, so that we can decrease the number of elements        count of arr2 elements in our imaginary should-be left-portion of final sorted array by increasing the number of arr1 elements to be included.
4. On the other hand if the conditions are correct, then we just have to check if the total number of elements (combining arr1 and arr2) is odd or even. If its odd then        consider MIN of Left-most element of right portion of both the arrays. Else, if it is even numbered, then it is (max(right-most part of left portions of both arrays) +      min(left-most of right portion of both arrays).
5. Link Solution : [Neetcode Solution](https://www.youtube.com/watch?v=q6IEA26hvXc)
<pre>class Solution {
public:
}; </pre>        

