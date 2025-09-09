# HARD DIFFICULTY PROBLEMS

## Problem 1: Median of Two Sorted Arrays

Given two sorted arrays nums1 and nums2 of size m and n respectively, return the median of the two sorted arrays.

The overall run time complexity should be O(log (m+n)).

Example 1:
Input: nums1 = [1,3], nums2 = [2]
Output: 2.00000
Explanation: merged array = [1,2,3] and median is 2.

Example 2:
Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.50000
Explanation: merged array = [1,2,3,4] and median is (2 + 3) / 2 = 2.5.

Constraints:
- nums1.length == m
- nums2.length == n
- 0 <= m <= 1000
- 0 <= n <= 1000
- 1 <= m + n <= 2000
- -10^6 <= nums1[i], nums2[i] <= 10^6

Solution:
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        # Ensure nums1 is the smaller array
        if len(nums1) > len(nums2):
            nums1, nums2 = nums2, nums1
        
        m, n = len(nums1), len(nums2)
        left, right = 0, m
        
        while left <= right:
            partition1 = (left + right) // 2
            partition2 = (m + n + 1) // 2 - partition1
            
            # Handle edge cases
            max_left1 = float('-inf') if partition1 == 0 else nums1[partition1 - 1]
            min_right1 = float('inf') if partition1 == m else nums1[partition1]
            
            max_left2 = float('-inf') if partition2 == 0 else nums2[partition2 - 1]
            min_right2 = float('inf') if partition2 == n else nums2[partition2]
            
            if max_left1 <= min_right2 and max_left2 <= min_right1:
                # Found correct partition
                if (m + n) % 2 == 0:
                    return (max(max_left1, max_left2) + min(min_right1, min_right2)) / 2
                else:
                    return max(max_left1, max_left2)
            elif max_left1 > min_right2:
                # Too far on right side for partition1
                right = partition1 - 1
            else:
                # Too far on left side for partition1
                left = partition1 + 1
        
        raise ValueError("Input arrays are not sorted")

Time Complexity: O(log(min(m,n)))
Space Complexity: O(1)


## Problem 2: Merge k Sorted Lists

You are given an array of k linked-lists lists, each linked-list is sorted in ascending order.

Merge all the linked-lists into one sorted linked-list and return it.

Example 1:
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6

Example 2:
Input: lists = []
Output: []

Example 3:
Input: lists = [[]]
Output: []

Constraints:
- k == lists.length
- 0 <= k <= 10^4
- 0 <= lists[i].length <= 500
- -10^4 <= lists[i][j] <= 10^4
- lists[i] is sorted in ascending order.
- The sum of lists[i].length will not exceed 10^4.

Solution:
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

import heapq

class Solution:
    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        if not lists:
            return None
        
        # Use a min heap to track the smallest elements
        heap = []
        
        # Add the first node of each list to the heap
        for i, node in enumerate(lists):
            if node:
                heapq.heappush(heap, (node.val, i, node))
        
        dummy = ListNode(0)
        current = dummy
        
        while heap:
            val, i, node = heapq.heappop(heap)
            current.next = node
            current = current.next
            
            # Add the next node from the same list
            if node.next:
                heapq.heappush(heap, (node.next.val, i, node.next))
        
        return dummy.next

Alternative Solution (Divide and Conquer):
class Solution:
    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        if not lists:
            return None
        
        def mergeTwoLists(l1, l2):
            dummy = ListNode(0)
            current = dummy
            
            while l1 and l2:
                if l1.val <= l2.val:
                    current.next = l1
                    l1 = l1.next
                else:
                    current.next = l2
                    l2 = l2.next
                current = current.next
            
            current.next = l1 or l2
            return dummy.next
        
        while len(lists) > 1:
            merged_lists = []
            
            for i in range(0, len(lists), 2):
                l1 = lists[i]
                l2 = lists[i + 1] if i + 1 < len(lists) else None
                merged_lists.append(mergeTwoLists(l1, l2))
            
            lists = merged_lists
        
        return lists[0]

Time Complexity: O(n log k) where n is total number of nodes and k is number of lists
Space Complexity: O(k) for heap or O(log k) for recursion


## Problem 3: Trapping Rain Water

Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.

Example 1:
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped.

Example 2:
Input: height = [4,2,0,3,2,5]
Output: 9

Constraints:
- n == height.length
- 1 <= n <= 2 * 10^4
- 0 <= height[i] <= 3 * 10^4

Solution:
class Solution:
    def trap(self, height: List[int]) -> int:
        if not height:
            return 0
        
        left, right = 0, len(height) - 1
        left_max, right_max = 0, 0
        water_trapped = 0
        
        while left < right:
            if height[left] < height[right]:
                if height[left] >= left_max:
                    left_max = height[left]
                else:
                    water_trapped += left_max - height[left]
                left += 1
            else:
                if height[right] >= right_max:
                    right_max = height[right]
                else:
                    water_trapped += right_max - height[right]
                right -= 1
        
        return water_trapped

Alternative Solution (Dynamic Programming):
class Solution:
    def trap(self, height: List[int]) -> int:
        if not height:
            return 0
        
        n = len(height)
        left_max = [0] * n
        right_max = [0] * n
        
        # Fill left_max array
        left_max[0] = height[0]
        for i in range(1, n):
            left_max[i] = max(left_max[i - 1], height[i])
        
        # Fill right_max array
        right_max[n - 1] = height[n - 1]
        for i in range(n - 2, -1, -1):
            right_max[i] = max(right_max[i + 1], height[i])
        
        # Calculate trapped water
        water_trapped = 0
        for i in range(n):
            water_level = min(left_max[i], right_max[i])
            if water_level > height[i]:
                water_trapped += water_level - height[i]
        
        return water_trapped

Time Complexity: O(n)
Space Complexity: O(1) for two-pointer, O(n) for DP


## Problem 4: Sliding Window Maximum

You are given an array of integers nums, there is a sliding window of size k which is moving from the very left of the array to the very right. You can only see the k numbers in the window. Each time the sliding window moves right by one position.

Return the max sliding window.

Example 1:
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation: 
Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7

Example 2:
Input: nums = [1], k = 1
Output: [1]

Constraints:
- 1 <= nums.length <= 10^5
- -10^4 <= nums[i] <= 10^4
- 1 <= k <= nums.length

Solution:
from collections import deque

class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        if not nums or k == 0:
            return []
        
        # Deque to store indices
        dq = deque()
        result = []
        
        for i in range(len(nums)):
            # Remove indices that are out of current window
            while dq and dq[0] <= i - k:
                dq.popleft()
            
            # Remove indices whose corresponding values are smaller than current
            while dq and nums[dq[-1]] <= nums[i]:
                dq.pop()
            
            dq.append(i)
            
            # Add to result when we have processed at least k elements
            if i >= k - 1:
                result.append(nums[dq[0]])
        
        return result

Time Complexity: O(n)
Space Complexity: O(k)


## Problem 5: Longest Valid Parentheses

Given a string containing just the characters '(' and ')', find the length of the longest valid (well-formed) parentheses substring.

Example 1:
Input: s = "(()"
Output: 2
Explanation: The longest valid parentheses substring is "()".

Example 2:
Input: s = ")()())"
Output: 4
Explanation: The longest valid parentheses substring is "()()".

Example 3:
Input: s = ""
Output: 0

Constraints:
- 0 <= s.length <= 3 * 10^4
- s[i] is '(', or ')'.

Solution:
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        if not s:
            return 0
        
        stack = [-1]  # Initialize with -1 to handle edge cases
        max_length = 0
        
        for i in range(len(s)):
            if s[i] == '(':
                stack.append(i)
            else:  # s[i] == ')'
                stack.pop()
                
                if not stack:
                    # No matching '(' found
                    stack.append(i)
                else:
                    # Calculate length of current valid substring
                    current_length = i - stack[-1]
                    max_length = max(max_length, current_length)
        
        return max_length

Alternative Solution (Dynamic Programming):
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        if not s:
            return 0
        
        n = len(s)
        dp = [0] * n
        max_length = 0
        
        for i in range(1, n):
            if s[i] == ')':
                if s[i - 1] == '(':
                    # Case: ...()
                    dp[i] = (dp[i - 2] if i >= 2 else 0) + 2
                elif dp[i - 1] > 0:
                    # Case: ...))
                    match_index = i - dp[i - 1] - 1
                    if match_index >= 0 and s[match_index] == '(':
                        dp[i] = dp[i - 1] + 2 + (dp[match_index - 1] if match_index > 0 else 0)
                
                max_length = max(max_length, dp[i])
        
        return max_length

Time Complexity: O(n)
Space Complexity: O(n)


## Problem 6: Regular Expression Matching

Given an input string s and a pattern p, implement regular expression matching with support for '.' and '*' where:

'.' Matches any single character.
'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

Example 1:
Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".

Example 2:
Input: s = "aa", p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".

Example 3:
Input: s = "ab", p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".

Constraints:
- 1 <= s.length <= 20
- 1 <= p.length <= 30
- s contains only lowercase English letters.
- p contains only lowercase English letters, '.', and '*'.

Solution:
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        memo = {}
        
        def dp(i, j):
            if (i, j) in memo:
                return memo[(i, j)]
            
            # Base case: pattern is exhausted
            if j == len(p):
                result = i == len(s)
            else:
                # Check if current characters match
                first_match = i < len(s) and (p[j] == s[i] or p[j] == '.')
                
                # Handle '*' wildcard
                if j + 1 < len(p) and p[j + 1] == '*':
                    # Two choices: skip current pattern or use it
                    result = (dp(i, j + 2) or  # Skip pattern
                             (first_match and dp(i + 1, j)))  # Use pattern
                else:
                    # Regular character matching
                    result = first_match and dp(i + 1, j + 1)
            
            memo[(i, j)] = result
            return result
        
        return dp(0, 0)

Alternative Solution (Bottom-up DP):
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m, n = len(s), len(p)
        dp = [[False] * (n + 1) for _ in range(m + 1)]
        
        # Base case: empty string matches empty pattern
        dp[m][n] = True
        
        # Fill the DP table bottom-up
        for i in range(m, -1, -1):
            for j in range(n - 1, -1, -1):
                first_match = i < m and (p[j] == s[i] or p[j] == '.')
                
                if j + 1 < n and p[j + 1] == '*':
                    dp[i][j] = dp[i][j + 2] or (first_match and dp[i + 1][j])
                else:
                    dp[i][j] = first_match and dp[i + 1][j + 1]
        
        return dp[0][0]

Time Complexity: O(m * n)
Space Complexity: O(m * n)