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
```cpp
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
```cpp  


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
```cpp    

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
If students > m: On satisfying this condition, we can conclude that the number ‘mid’ is smaller than our answer. So, we will eliminate the left half and consider the right half(i.e. low = mid+1).
Otherwise, the value mid is one of the possible answers. But we want the minimum value. So, we will eliminate the right half and consider the left half(i.e. high = mid-1).
Finally, outside the loop, we will return the value of low as the pointer will be pointing to the answer.
The steps from 3-4 will be inside a loop and the loop will continue until low crosses high.

<pre>
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
</pre>
