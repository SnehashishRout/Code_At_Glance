**1. Dynamic Programming 1D**

**A.** Given an integer array nums, return true if you can partition the array into two subsets such that 
       the sum of the elements in both subsets is equal or false otherwise.

Input: nums = [1,5,11,5]
Output: true
Explanation: The array can be partitioned as [1, 5, 5] and [11].

Input: nums = [1,2,3,5]
Output: false
Explanation: The array cannot be partitioned into equal sum subsets.

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

