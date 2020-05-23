# [Leetcode Questions]

## Summary

Here's some of my favorite Leetcode questions to prepare for interviews / refresh (or gain) knowledge.

## Arrays / Sliding Window / Two-Pointer

* [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)

> Given n non-negative integers a1, a2, ..., an , where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

> You may not slant the container and n is at least 2.

* [Find All Numbers Disappeared In An Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/)

> Given an array of integers where 1 ≤ a[i] ≤ n (n = size of array), some elements appear twice and others appear once.

> Find all the elements of [1, n] inclusive that do not appear in this array.

> Could you do it without extra space and in O(n) runtime? You may assume the returned list does not count as extra space.

* [Find First And Last Position Of Element In Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

> Given an array of integers nums sorted in ascending order, find the starting and ending position of a given target value.

> Your algorithm's runtime complexity must be in the order of O(log n).

> If the target is not found in the array, return [-1, -1].

* [Find The Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

> Given an array nums containing n + 1 integers where each integer is between 1 and n (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.

* [Find Minimum In Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

> Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand (i.e.,  [0,1,2,4,5,6,7] might become  [4,5,6,7,0,1,2]).

> Find the minimum element.

> You may assume no duplicate exists in the array.

* [Majority Element](https://leetcode.com/problems/majority-element/)

> Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.

> You may assume that the array is non-empty and the majority element always exist in the array.

* [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

> Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

* [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)

> Given an integer array nums, find the contiguous subarray within an array (containing at least one number) which has the largest product.

* [Product Of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

> Given an array nums of n integers where n > 1,  return an array output such that output[i] is equal to the product of all the elements of nums except nums[i]

> Please solve it without division and in O(n).

* [Search In Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

> Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand (i.e., [0,1,2,4,5,6,7] might become [4,5,6,7,0,1,2]).

> You are given a target value to search. If found in the array return its index, otherwise return -1.

> You may assume no duplicate exists in the array.

> Your algorithm's runtime complexity must be in the order of O(log n).

* [Shortest Unsorted Continuous Subarray](https://leetcode.com/problems/shortest-unsorted-continuous-subarray/)

> Given an integer array, you need to find one continuous subarray that if you only sort this subarray in ascending order, then the whole array will be sorted in ascending order, too.

> You need to find the shortest such subarray and output its length.

* [Sort Colors](https://leetcode.com/problems/sort-colors/)

> Given an array with n objects colored red, white or blue, sort them in-place so that objects of the same color are adjacent, with the colors in the order red, white and blue.

> Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

> You are not suppose to use the library's sort function for this problem.

* [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

> Given an array of integers and an integer k, you need to find the total number of continuous subarrays whose sum equals to k.

* [Two Sum](https://leetcode.com/problems/two-sum/)

> Given an array of integers, return indices of the two numbers such that they add up to a specific target.

> You may assume that each input would have exactly one solution, and you may not use the same element twice.

* [Three Sum](https://leetcode.com/problems/3sum/)

> Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.

> The solution set must not contain duplicate triplets

## Dynamic Programming

* [Best Time To Buy And Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

> Say you have an array for which the ith element is the price of a given stock on day i.

> If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of the stock), design an algorithm to find the maximum profit.

> Note that you cannot sell a stock before you buy one.

* [Best Time To Buy And Sell Stock With Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

> Say you have an array for which the ith element is the price of a given stock on day i.

> Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times) with the following restrictions:
> * You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
> * After you sell your stock, you cannot buy stock on next day. (ie, cooldown 1 day)


* [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)

> You are climbing a stair case. It takes n steps to reach to the top.

> Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

> Given n will be a positive integer.

* [Coin Change](https://leetcode.com/problems/coin-change/)

> You are given coins of different denominations and a total amount of money amount. Write a function to compute the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.

* [Combination Sum 4](https://leetcode.com/problems/combination-sum-iv/)

> Given an integer array with all positive numbers and no duplicates, find the number of possible combinations that add up to a positive integer target.

* [House Robber](https://leetcode.com/problems/house-robber/)

> You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

> Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

* [House Robber 2](https://leetcode.com/problems/house-robber-ii/)

> You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are arranged in a circle. That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

> Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

* [Jump Game](https://leetcode.com/problems/jump-game/)

> Given an array of non-negative integers, you are initially positioned at the first index of the array.

> Each element in the array represents your maximum jump length at that position.

> Determine if you are able to reach the last index.

* [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

> Given an unsorted array of integers, find the length of longest increasing subsequence.

* [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)

> Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.

* [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)

> Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.
 
> You can only move either down or right at any point in time.

* [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)

> Given a string, your task is to count how many palindromic substrings in this string.

> The substrings with different start indexes or end indexes are counted as different substrings even they consist of same characters.

* [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)

> Given a non-empty array containing only positive integers, find if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal.

* [Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/)

> Given n, how many structurally unique BST's (binary search trees) that store values 1 ... n?

* [Unique Paths](https://leetcode.com/problems/unique-paths/)

> A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).

> The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

> How many possible unique paths are there?

* [Word Break](https://leetcode.com/problems/word-break/)

> Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, determine if s can be segmented into a space-separated sequence of one or more dictionary words.

> The same word in the dictionary may be reused multiple times in the segmentation.

> You may assume the dictionary does not contain duplicate words.

## Graphs

* [Course Schedule](https://leetcode.com/problems/course-schedule/)

> There are a total of numCourses courses you have to take, labeled from 0 to numCourses-1.

> Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]

> Given the total number of courses and a list of prerequisite pairs, is it possible for you to finish all courses?

* [Course Schedule 2](https://leetcode.com/problems/course-schedule-ii/)

> There are a total of n courses you have to take, labeled from 0 to n-1.

> Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]

> Given the total number of courses and a list of prerequisite pairs, return the ordering of courses you should take to finish all courses.

> There may be multiple correct orders, you just need to return one of them. If it is impossible to finish all courses, return an empty array.

* [Number Of Islands](https://leetcode.com/problems/number-of-islands/)

> Given a 2d grid map of '1's (land) and '0's (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

* [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)

> Given an m x n matrix of non-negative integers representing the height of each unit cell in a continent, the "Pacific ocean" touches the left and top edges of the matrix and the "Atlantic ocean" touches the right and bottom edges.

> Water can only flow in four directions (up, down, left, or right) from a cell to another one with height equal or lower.

> Find the list of grid coordinates where water can flow to both the Pacific and Atlantic ocean.

## Intervals

* [Insert Interval](https://leetcode.com/problems/insert-interval/)

> Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).

> You may assume that the intervals were initially sorted according to their start times.

* [Merge Intervals](https://leetcode.com/problems/merge-intervals/)

> Given a collection of intervals, merge all overlapping intervals.

* [Non-Overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

> Given a collection of intervals, find the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

## Linked List

* [Detect Cycle In A Linked List](https://leetcode.com/problems/linked-list-cycle/)

> Given a linked list, determine if it has a cycle in it.

> To represent a cycle in the given linked list, we use an integer pos which represents the position (0-indexed) in the linked list where tail connects to. If pos is -1, then there is no cycle in the linked list.

* [Intersection Of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

> Write a program to find the node at which the intersection of two singly linked lists begins.

* [Linked List Cycle 2](https://leetcode.com/problems/linked-list-cycle-ii/)

> Given a linked list, return the node where the cycle begins. If there is no cycle, return null.

> To represent a cycle in the given linked list, we use an integer pos which represents the position (0-indexed) in the linked list where tail connects to. If pos is -1, then there is no cycle in the linked list.

> Do not modify the linked list.

* [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

> Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

* [Merge K Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

> Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

* [Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)

> Given a singly linked list, determine if it is a palindrome.

> Could you do it in O(n) time and O(1) space?

* [Remove Nth Node From End Of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

> Given a linked list, remove the n-th node from the end of list and return its head.

* [Reorder List](https://leetcode.com/problems/reorder-list/)

> Given a linked list, remove the n-th node from the end of list and return its head.

* [Reverse A Linked List](https://leetcode.com/problems/reverse-linked-list/)

> Reverse a singly linked list.

* [Sort List](https://leetcode.com/problems/sort-list/)

> Sort a linked list in O(n log n) time using constant space complexity.

## Trees

* [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)

> Given a binary tree, return the inorder traversal of its nodes' values.

* [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)

> Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

* [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)

> Given a non-empty binary tree, find the maximum path sum.

> For this problem, a path is defined as any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The path must contain at least one node and does not need to go through the root.

* [Construct Binary Tree From Preorder And Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

> Given preorder and inorder traversal of a tree, construct the binary tree.

* [Diameter Of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)

> Given a binary tree, you need to compute the length of the diameter of the tree. The diameter of a binary tree is the length of the longest path between any two nodes in a tree. This path may or may not pass through the root.

* [Flatten Binary Tree To Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

> Given a binary tree, flatten it to a linked list in-place.

* [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)

> Invert a binary tree.

* [Lowest Common Ancestor Of A Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

> According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow a node to be a descendant of itself).”

* [Maximum Depth Of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

> Given a binary tree, find its maximum depth.

> The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

* [Merge Two Binary Trees](https://leetcode.com/problems/merge-two-binary-trees/)

> Given two binary trees and imagine that when you put one of them to cover the other, some nodes of the two trees are overlapped while the others are not.

> You need to merge them into a new binary tree. The merge rule is that if two nodes overlap, then sum node values up as the new value of the merged node. Otherwise, the NOT null node will be used as the node of new tree.

* [Path Sum 3](https://leetcode.com/problems/path-sum-iii/)

> You are given a binary tree in which each node contains an integer value.

> Find the number of paths that sum to a given value.

> The path does not need to start or end at the root or a leaf, but it must go downwards (traveling only from parent nodes to child nodes).

* [Same Tree](https://leetcode.com/problems/same-tree/)

> Given two binary trees, write a function to check if they are the same or not.

> Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

* [Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)

> Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).

* [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)

> Given a binary tree, determine if it is a valid binary search tree (BST).