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
