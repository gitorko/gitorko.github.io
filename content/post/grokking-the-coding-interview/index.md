---
title: 'Grokking the Coding Interview'
description: 'Grokking the Coding Interview'
summary: 'Interview preparation guide for coding'
date: '2023-08-07'
aliases: [/grokking-the-coding-interview/]
author: 'Arjun Surendra'
categories: [Algorithms, DS, leetcode]
tags: [coding, interview, algorithms, data-structure]
toc: true
---

A comprehensive guide for java coding interviews covering areas like algorithms, datastructures, sorting, leetcode problems, concurrency, java fundamentals.

Github: [https://github.com/gitorko/project01](https://github.com/gitorko/project01)

## Preparation

- We will first understand the fundamentals of data structure, like how heap, queue, stacks work and how to implement them from scratch. 
- Then we will look at the various sorting algorithms.
- Then we will move to leetcode problems covering easy, medium & hard problems.
- Then we will move to concurrency problems, how atomic variable work, how locks work etc.
- Then we will cover some SQL and database queries.
- The problems solved here are concise & small making it easy to understand and revise.
- All problems solved here are developed with test driven approach, with various test that can be run locally.
- Each of the solutions follow certain pattern. Eg: if you learn back-tracking solution in one problem the pattern is similar when you solve it in other problem. This is very important when it comes to the learning aspect.
- Most problems have a parent problem, Once you solve this parent problem you can solve various subset or variation of this problem. Such problems are grouped as similar in the solutions.

## Approach To Solve

Given a problem here are some questions that should help you figure out the general direction of how to solve it

1. Which data structure can I use? Arrays, LinkedList, HashMap, Heap, Tree, or Trie
2. Do I need to use 2 data structures? eg: LRU
3. How do I break the problem into smaller units, is there a problem within a problem, can i write a decision tree?
4. Does this problem look similar to other problems you have solved?
5. Will sorting make the problem easier to solve?
6. Can I use any algorithmic techniques, like bfs, dfs, two pointer etc.
7. Do any design patterns apply that could make it easier to maintain, like observer pattern?
8. What is the time & space complexity? Best case, worst case time complexity? Average case is usually difficult to derive.

<b>Tips</b>

1. Linked list problems often use a dummy node.
2. To delete a node in the link list you need previous node or be position one node behind.
3. Time complexity is O(number of branches ^ n)
4. In-order traversal of BST results in ascending order array.
5. BFS requires a queue, DFS requires recursion.
6. If the char set is bound to 26 chars, use char[26] array instead of Map or Set.

![](coding.png)

## Algo Techniques

* Sorting
* Map & Set
* Recursion
* Fast pointer & Slow pointer
* Min-Heap vs Max-Heap (Priority Queue)
* Binary search
* BFS vs DFS
* Two Pointers
* Sliding Window
* Fast pointer vs Slow pointer
* Backtracking
* Matrix
* Prefix sum
* Divide & Conquer
* Memoization / Dynamic programming
* Greedy
* Topological Sort
* Intervals
* Cyclic Sort
* Bitwise XOR / Bit manipulation
* Trie
* Stacks & Queue
 
![](gitorko-algorithms.jpg)

## Big-O

![](big-o.png)


```
log(n) < √(n) < n < nlog(n) < n^2 < n^3 < 2^n < n!
```

[Algorithms and Data Structures Cheatsheet](https://algs4.cs.princeton.edu/cheatsheet/)

## Coding

### Sorting

|Id   | Leetcode     | Solution        | Type        |
| --- | ---          | ---             | ---         |
|1|Bubble Sort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_01_BubbleSort.java)|EASY|
|2|Selection sort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_02_SelectionSort.java)|EASY|
|3|Insertion sort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_03_InsertionSort.java)|EASY|
|4|MergeSort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_04_MergeSort.java)|MEDIUM|
|5|[912. Sort an Array](https://leetcode.com/problems/sort-an-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_04_MergeSort.java)|MEDIUM|
|6|QuickSort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_05_QuickSort.java)|MEDIUM|
|7|[912. Sort an Array](https://leetcode.com/problems/sort-an-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_05_QuickSort.java)|MEDIUM|
|8|Shell sort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_06_ShellSort.java)|HARD|
|9|Counting sort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_07_CountingSort.java)|EASY|
|10|Radix sort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_08_RadixSort.java)|MEDIUM|
|11|Bucket sort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_09_BucketSort.java)|EASY|
|12|Heap sort|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_10_HeapSort.java)|MEDIUM|
|13|[704. Binary Search](https://leetcode.com/problems/binary-search/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_11_BinarySearch.java)|EASY|
|14|Employee Search|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/sorting/_12_EmployeeSort.java)|EASY|

### Data Structure

|Id   | Leetcode     | Solution        | Type        |
| --- | ---          | ---             | ---         |
|1|Implement Circular Array|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CircularArray.java)|EASY|
|2|[622. Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CircularQueue.java)|MEDIUM|
|3|Implement ArrayList with array|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomArrayList.java)|EASY|
|4|Insert to BST, Delete from BST, Find from BST|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomBST.java)|EASY|
|5|Implement Doubly Linked List|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomDoublyLinkedList.java)|EASY|
|6|[707. Design Linked List](https://leetcode.com/problems/design-linked-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomLinkedList.java)|MEDIUM|
|7|[706. Design HashMap](https://leetcode.com/problems/design-hashmap/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomMap.java)|EASY|
|8|Implement Map|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomMapGeneric.java)|MEDIUM|
|9|Implement Max Heap|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomMaxHeap.java)|EASY|
|10|Implement Min Heap|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomMinHeap.java)|EASY|
|11|Implement Queue|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomQueue.java)|EASY|
|12|Implement Stack|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomStack.java)|EASY|
|13|[208. Implement Trie, Prefix Tree](https://leetcode.com/problems/implement-trie-prefix-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/CustomTrie.java)|EASY|
|14|[225. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/datastructure/StackUsingQueue.java)|EASY|

### LeetCode - Easy

|Id   | Leetcode     | Solution        | Type        |
| --- | ---          | ---             | ---         |
|1|[67. Add Binary](https://leetcode.com/problems/add-binary/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_addbinary_67/AddBinary.java)|EASY - Number|
|2|[989. Add to Array-Form of Integer](https://leetcode.com/problems/add-to-array-form-of-integer)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_arrayformofint_989/ArrayFormInteger.java)|EASY - Number|
|3|[455. Assign Cookies](https://leetcode.com/problems/assign-cookies)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_assigncookie_455/AssignCookie.java)|EASY - Number|
|4|[682. Baseball Game](https://leetcode.com/problems/baseball-game/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_baseballgame_682/BaseballGame.java)|EASY - Number|
|5|[121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_besttimetobuysell_121/BestTimeBuySell.java)|EASY - Number|
|6|[605. Can Place Flowers](https://leetcode.com/problems/can-place-flowers/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_canplaceflower_605/CanPlaceFlower.java)|EASY - Number|
|7|[724. Find Pivot Index](https://leetcode.com/problems/find-pivot-index/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_findpivotindex_724/FindPivotIndex.java)|EASY - Number|
|8|[9. Palindrome Number](https://leetcode.com/problems/palindrome-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_ispalindromenumber_9/isPalindromeNumber.java)|EASY - Number|
|9|[860. Lemonade Change](https://leetcode.com/problems/lemonade-change/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_lemonadechange_860/LemonadeChange.java)|EASY - Number|
|10|[1572. Matrix Diagonal Sum](https://leetcode.com/problems/matrix-diagonal-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_matrixdiagonalsum_1572/MatrixDiagSum.java)|EASY - Number|
|11|[485. Max Consecutive Ones](https://leetcode.com/problems/max-consecutive-ones/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_maxone_485/MaxOne.java)|EASY - Number|
|12|[53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_maxsubarraysum_53/MaxSubarraySum.java)|EASY - Number|
|13|[88. Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_mergesortedarray_88/MergeSortedArray.java)|EASY - Number|
|14|[1984. Minimum Difference Between Highest and Lowest of K Scores](https://leetcode.com/problems/minimum-difference-between-highest-and-lowest-of-k-scores/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_mindiffhighlowscore_1984/MinDiffHighLowScore.java)|EASY - Number|
|15|[268. Missing Number](https://leetcode.com/problems/missing-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_missingnumber_268/MissingNumber.java)|EASY - Number|
|16|[283. Move Zeroes](https://leetcode.com/problems/move-zeroes/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_movezero_283/MoveZero.java)|EASY - Number|
|17|[1523. Count Odd Numbers in an Interval Range](https://leetcode.com/problems/count-odd-numbers-in-an-interval-range/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_oddnuminrange_1523/OddNumInRange.java)|EASY - Number|
|18|[119. Pascal's Triangle II](https://leetcode.com/problems/pascals-triangle-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_pascaltriange_119/PascalTriangle2.java)|EASY - Number|
|19|[118. Pascal's Triangle](https://leetcode.com/problems/pascals-triangle/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_pascaltriangle_118/PascalTriangle.java)|EASY - Number|
|20|[66. Plus One](https://leetcode.com/problems/plus-one/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_plusone/PlusOne.java)|EASY - Number|
|21|[1299. Replace Elements with Greatest Element on Right Side](https://leetcode.com/problems/replace-elements-with-greatest-element-on-right-side/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_replaceelementgreatest_1299/ReplaceElementGreatestElement.java)|EASY - Number|
|22|[7. Reverse Integer](https://leetcode.com/problems/reverse-integer/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_reverseinteger_7/ReverseInteger.java)|EASY - Number|
|23|[1470. Shuffle the Array](https://leetcode.com/problems/shuffle-the-array)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_shufflearray_1470/ShuffleArray.java)|EASY - Number|
|24|[136. Single Number](https://leetcode.com/problems/single-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_singlenumber_136/SingleNum.java)|EASY - Number|
|25|[228. Summary Ranges](https://leetcode.com/problems/summary-ranges/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_summaryrange_228/SummaryRange.java)|EASY - Number|
|26|[263. Ugly Number](https://leetcode.com/problems/ugly-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_01_uglynumber_263/UglyNumber.java)|EASY - Number|
|27|[242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_anagram_242/CheckAnagram.java)|EASY - String|
|28|[389. Find the Difference](https://leetcode.com/problems/find-the-difference/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_finddifferenceinstring_389/FindDifferenceInString.java)|EASY - String|
|29|[412. Fizz Buzz](https://leetcode.com/problems/fizz-buzz/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_fizzbuzzjazz_412/FizzBuzzJazz.java)|EASY - String|
|30|[1071. Greatest Common Divisor of Strings](https://leetcode.com/problems/greatest-common-divisor-of-strings/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_greatestcommondivisior_1071/GreatestCommonDivisorString.java)|EASY - String|
|31|[205. Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_isomorphicstring_205/IsomorphicString.java)|EASY - String|
|32|[392. Is Subsequence](https://leetcode.com/problems/is-subsequence/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_issubsequence_392/IsSubsequence.java)|EASY - String|
|33|[58. Length of Last Word](https://leetcode.com/problems/length-of-last-word/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_lengthoflastword_58/LengthOfLastWord.java)|EASY - String|
|34|[14. Longest Common Prefix](https://leetcode.com/problems/longest-common-prefix/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_longcommonprefix_14/LongestCommonPrefixString.java)|EASY - String|
|35|[1189. Maximum Number of Balloons](https://leetcode.com/problems/maximum-number-of-balloons/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_maxballoons_1189/MaxBalloons.java)|EASY - String|
|36|[Minimum append to make string palindrome](https://www.geeksforgeeks.org/minimum-number-appends-needed-make-string-palindrome/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_palindromeadjacentswap/PalindromeMin.java)|EASY - String|
|37|[1047. Remove All Adjacent Duplicates In String](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_removeadjacentdup_1047/RemoveAdjacentDuplicate.java)|EASY - String|
|38|[344. Reverse String](https://leetcode.com/problems/reverse-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_reversestring_344/ReverseString.java)|EASY - String|
|39|[13. Roman to Integer](https://leetcode.com/problems/roman-to-integer/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_romantointeger_13/RomanToInteger.java)|EASY - String|
|40|[28. Implement strStr](https://leetcode.com/problems/implement-strstr/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_stringinstring_28/isSubarray.java)|MEDIUM - String|
|41|[929. Unique Email Addresses](https://leetcode.com/problems/unique-email-addresses/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_uniqueemailaddress_929/UniqueEmailAddress.java)|EASY - String|
|42|[953. Verifying an Alien Dictionary](https://leetcode.com/problems/verifying-an-alien-dictionary/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_02_verifyaliendict_953/VerifyAlienDict.java)|EASY - String|
|43|[219. Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_03_containsduplicate2_219/ContainsDuplicate2.java)|EASY - Map & Set|
|44|[217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_03_containsduplicate_217/ContainsDuplicate.java)|EASY - Map & Set|
|45|[1603. Design Parking System](https://leetcode.com/problems/design-parking-system/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_03_designparkingsystem_1603/DesignParkingSystem.java)|EASY - Map & Set|
|46|[202. Happy Number](https://leetcode.com/problems/happy-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_03_happynumber_202/HappyNumber.java)|EASY - Map & Set|
|47|[705. Design HashSet](https://leetcode.com/problems/design-hashset/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_03_hashset_705/DesignHashSet.java)|EASY - Map & Set|
|48|[169. Majority Element](https://leetcode.com/problems/majority-element/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_03_majorityelement_169/MajorityElement.java)|EASY - Map & Set|
|49|[1. Two Sum](https://leetcode.com/problems/two-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_03_twosum1_1/TwoSum.java)|EASY - Map & Set|
|50|[387. First Unique Character in a String](https://leetcode.com/problems/first-unique-character-in-a-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_03_uniquechar_387/UniqueChar.java)|EASY - Map & Set|
|51|[290. Word Pattern](https://leetcode.com/problems/word-pattern/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_03_wordpattern_290/WordPattern.java)|EASY - Map & Set|
|52|[1636. Sort Array by Increasing Frequency](https://leetcode.com/problems/sort-array-by-increasing-frequency/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_04_frequencysort_1636/FrequencySortNum.java)|EASY - Heap|
|53|[703. Kth Largest Element in a Stream](https://leetcode.com/problems/kth-largest-element-in-a-stream/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_04_kthlargestinstream_703/KthLargestInStream.java)|EASY - Heap|
|54|[1046. Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_04_laststoneweight_1046/LastStoneWeight.java)|EASY - Heap|
|55|[1005. Maximize Sum Of Array After K Negations](https://leetcode.com/problems/maximize-sum-of-array-after-k-negations/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_04_maxsumknegation_1005/MaxSumKNegation.java)|EASY - Heap|
|56|[349. Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_arrayintersection_349/ArrayIntersection.java)|EASY - Sliding window / Two pointer|
|57|[696. Count Binary Substrings](https://leetcode.com/problems/count-binary-substrings/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_countbinarysubstring_696/BinarySubstring.java)|EASY - Sliding window / Two pointer|
|58|[Factorial](#)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_factorial/Factorial.java)|EASY - Sliding window / Two pointer|
|59|[509. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_fibonacci_509/Fibonacci.java)|EASY - Sliding window / Two pointer|
|60|[125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_ispalindromestring_125/IsPalindromeString.java)|EASY - Sliding window / Two pointer|
|61|[674. Longest Continuous Increasing Subsequence](https://leetcode.com/problems/longest-continuous-increasing-subsequence/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_longestcontinuousincreasingsubseq_674/LongContinuousIncreasingSubSeq.java)|EASY - Sliding window / Two pointer|
|62|[26. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_removeduplicatesortedarray_26/RemoveDuplicateSortedArray.java)|EASY - Sliding window / Two pointer|
|63|[27. Remove Element](https://leetcode.com/problems/remove-element/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_removeelement_27/RemoveElement.java)|EASY - Sliding window / Two pointer|
|64|[977. Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_squaresofsortedarray_977/SquaresOfSortedArray.java)|EASY - Sliding window / Two pointer|
|65|[680. Valid Palindrome II](https://leetcode.com/problems/valid-palindrome-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_05_validpalindrome2_680/ValidPalindrome.java)|EASY - Sliding window / Two pointer|
|66|[463. Island Perimeter](https://leetcode.com/problems/island-perimeter/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_06_islandpremeter_463/IslandPerimeter.java)|EASY - Matrix|
|67|Ones in Range|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_08_onesinrange/OnesInRange.java)|EASY - Pre-Sum|
|68|[303. Range Sum Query - Immutable](https://leetcode.com/problems/range-sum-query-immutable/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_08_rangesumimmutable_303/RangeSum.java)|EASY - Pre-Sum|
|69|[70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_09_climbstairs_70/ClimbStairs.java)|EASY - DP|
|70|[338. Counting Bits](https://leetcode.com/problems/counting-bits/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_09_countingbits_338/CountingBits.java)|EASY - DP|
|71|[746. Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_09_mincostclimbstairs_746/MinCostClimbStairs.java)|EASY - DP|
|72|[1137. N-th Tribonacci Number](https://leetcode.com/problems/n-th-tribonacci-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_09_tribonacci_1137/Tribonacci.java)|EASY - DP|
|73|[203. Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_10_deletenodelinklist_203/DeleteNodeLinkedList.java)|EASY - Link List|
|74|[141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_10_detectlooplinklist_141/DetectLoopInLinkList.java)|EASY - Link List|
|75|[142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_10_detectloopstartlinklist_142/RemoveLoopInLinkList.java)|EASY - Link List|
|76|[160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_10_intersectionlinklist_160/IntersectionLinkList.java)|EASY - Link List|
|77|[21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_10_mergesortedlinklist_21/Merge2SortedList.java)|EASY - Link List|
|78|[876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_10_midoflinklist_876/MidOfLinkedList.java)|EASY - Link List|
|79|[234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_10_palindromelinklist_234/PalindromeLinkList.java)|EASY - Link List|
|80|[83. Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_10_removeduplicateslinklist_83/RemoveDuplicatesSortLinkList.java)|EASY - Link List|
|81|[206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_10_reverselinklist_206/ReverseLinkList.java)|EASY - Link List|
|82|[235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bstlca_235/FindLCAinBST.java)|EASY - Binary Tree|
|83|[938. Range Sum of BST](https://leetcode.com/problems/range-sum-of-bst/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bstrangesum_938/BstRangeSum.java)|EASY - Binary Tree|
|84|Binary Tree two sum different level|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bt2sum/BinaryTreeTwoSum.java)|EASY - Binary Tree|
|85|Create Binary Tree from Level order|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btcreatelevelorder/BinaryTreeCreateLevelOrder.java)|EASY - Binary Tree|
|86|Get height of binary tree|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btdepth/HeightBT.java)|EASY - Binary Tree|
|87|[104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btdepth/MaxDepthBT.java)|EASY - Binary Tree|
|88|[111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btdepth/MinDepthBT.java)|EASY - Binary Tree|
|89|Get size of binary tree|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btdepth/SizeBT.java)|EASY - Binary Tree|
|90|[543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btdiameter_543/DiameterBT.java)|EASY - Binary Tree|
|91|[226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btinvert_226/InvertBinaryTree.java)|EASY - Binary Tree|
|92|[617. Merge Two Binary Trees](https://leetcode.com/problems/merge-two-binary-trees/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btmerge/MergeBinaryTree.java)|EASY - Binary Tree|
|93|[257. Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btpath_257/BinaryTreePath.java)|EASY - Binary Tree|
|94|[112. Path Sum](https://leetcode.com/problems/path-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btpathsum1_112/PathSum1BT.java)|EASY - Binary Tree|
|95|[100. Same Tree](https://leetcode.com/problems/same-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btsametree_100/SameTree.java)|EASY - Binary Tree|
|96|[671. Second Minimum Node In a Binary Tree](https://leetcode.com/problems/second-minimum-node-in-a-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btsecondmin_671/SecondMinBT.java)|EASY - Binary Tree|
|97|[572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btsubtree_572/CheckSubtree.java)|EASY - Binary Tree|
|98|[404. Sum of Left Leaves](https://leetcode.com/problems/sum-of-left-leaves/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_btsumofleftleaf/SumOfLeftLeafBT.java)|EASY - Binary Tree|
|99|[144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bttraversal/BTTraversal.java)|EASY - Binary Tree|
|100|[145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bttraversal/BTTraversal.java)|EASY - Binary Tree|
|101|[94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bttraversal/BTTraversal.java)|EASY - Binary Tree|
|102|[102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bttraversal/BTTraversal.java)|EASY - Binary Tree|
|103|[144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bttraversal/BTTraversalIterative.java)|EASY - Binary Tree|
|104|[145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bttraversal/BTTraversalIterative.java)|EASY - Binary Tree|
|105|[94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bttraversal/BTTraversalIterative.java)|EASY - Binary Tree|
|106|[107. Binary Tree Level Order Traversal II](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_bttraversalreverse_107/ReverseLevelOrder.java)|EASY - Binary Tree|
|107|[783. Minimum Distance Between BST Nodes](https://leetcode.com/problems/minimum-distance-between-bst-nodes/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_mindistbst_783/MinDistBST.java)|EASY - Binary Tree|
|108|[606. Construct String from Binary Tree](https://leetcode.com/problems/construct-string-from-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_stringfrombt_606/StringFromBT.java)|EASY - Binary Tree|
|109|[101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_11_symmetrictree_101/SymmetricTree.java)|EASY - Binary Tree|
|110|[441. Arranging Coins](https://leetcode.com/problems/arranging-coins/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_13_arrangecoins_441/ArrangeCoins.java)|EASY - Binary Search|
|111|[108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_13_bstfromarray_108/BinarySearchTreeFromArray.java)|EASY - Binary Search|
|112|[374. Guess Number Higher or Lower](https://leetcode.com/problems/guess-number-higher-or-lower/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_13_guessnumber_374/GuessNumber.java)|EASY - Binary Search|
|113|[1539. Kth Missing Positive Number](https://leetcode.com/problems/kth-missing-positive-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_13_kthmissingpositivenumber_1539/KthMissingPositiveNumber.java)|EASY - Binary Search|
|114|[35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_13_searchinsertposition_35/SearchInsertPosition.java)|EASY - Binary Search|
|115|[69. Sqrtx](https://leetcode.com/problems/sqrtx/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_13_sqrt_69/SqrtNum.java)|EASY - Binary Search|
|116|[367. Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_13_validperfectsquare_367/ValidPerfectSquare.java)|EASY - Binary Search|
|117|[844. Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_15_backspacestring_844/CheckBackSpaceString.java)|EASY - Stack & Monotonic Stack|
|118|[155. Min Stack](https://leetcode.com/problems/min-stack/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_15_minstack_155/MinStack.java)|EASY - Stack & Monotonic Stack|
|119|Nearest Greater to Right|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_15_nextgreatertoright/NextGreaterRight.java)|EASY - Stack & Monotonic Stack|
|120|[496. Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_15_nextgreatestelement_496/NextGreatestElement.java)|EASY - Stack & Monotonic Stack|
|121|Nearest Greater to Right|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_15_nextgreatestelement_496/NextGreatestRight.java)|EASY - Stack & Monotonic Stack|
|122|[20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_15_parenthesisvalid_20/ValidParenthesis.java)|EASY - Stack & Monotonic Stack|
|123|[71. Simplify Path](https://leetcode.com/problems/simplify-path/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_15_relativepath_71/RelativePath.java)|EASY - Stack & Monotonic Stack|
|124|Sort a stack|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_15_stacksort/StackSort.java)|EASY - Stack & Monotonic Stack|
|125|[733. Flood Fill](https://leetcode.com/problems/flood-fill/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_16_floodfill_733/FloodFill.java)|EASY - Graph|
|126|[1260. Shift 2D Grid](https://leetcode.com/problems/shift-2d-grid/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_16_shift2dgrid_1260/Shift2DGrid.java)|EASY - Graph|
|127|[1114. Print in Order](https://leetcode.com/problems/print-in-order/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_17_printorder_1114/PrintInOrder.java)|EASY - Thread|
|128|[448. Find All Numbers Disappeared in an Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_21_findalldisappearednum_448/FindAllDisappearNumber.java)|EASY - Cyclic sort|
|129|[191. Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_22_numberof1bits_191/NumberOfOneBits.java)|EASY - Bit Manipulation|
|130|[190. Reverse Bits](https://leetcode.com/problems/reverse-bits/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_22_reversebits_190/ReverseBits.java)|EASY - Bit Manipulation|
|131|[339. Nested List Weight Sum](https://leetcode.com/problems/nested-list-weight-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/easy/_25_nestedlistweightsum_551/NestedListWeightSum.java)|EASY - Generic|

### LeetCode - Medium

|Id   | Leetcode     | Solution        | Type        |
| --- | ---          | ---             | ---         |
|1|[665. Non-decreasing Array](https://leetcode.com/problems/non-decreasing-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_arraynondecreasing_665/NonDecreasingArray.java)|MEDIUM - Number|
|2|[1968. Array With Elements Not Equal to Average of Neighbors](https://leetcode.com/problems/array-with-elements-not-equal-to-average-of-neighbors/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_arraynotavgneighbour_1968/ArrayNotAvgNeighbour.java)|MEDIUM - Number|
|3|[853. Car Fleet](https://leetcode.com/problems/car-fleet/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_carfleet_853/CarFleet.java)|MEDIUM - Number|
|4|[1958. Check if Move is Legal](https://leetcode.com/problems/check-if-move-is-legal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_checkmove_1958/CheckMove.java)|MEDIUM - Number|
|5|[38. Count and Say](https://leetcode.com/problems/count-and-say/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_countandsay_38/CountAndSay.java)|MEDIUM - Number|
|6|[2466. Count Ways To Build Good Strings](https://leetcode.com/problems/count-ways-to-build-good-strings/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_countwaysgoodstring_2466/CountWaysGoodString.java)|MEDIUM - Number|
|7|[1921. Eliminate Maximum Number of Monsters](https://leetcode.com/problems/eliminate-maximum-number-of-monsters/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_eliminatemonster_1921/EliminateMonster.java)|MEDIUM - Number|
|8|Pair with diff|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_findpair_532/FindPair.java)|MEDIUM - Number|
|9|[532. K-diff Pairs in an Array](https://leetcode.com/problems/k-diff-pairs-in-an-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_findpair_532/KDiffPair.java)|MEDIUM - Number|
|10|[18. 4Sum](https://leetcode.com/problems/4sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_foursum_18/FourSum.java)|MEDIUM - Number|
|11|[12. Integer to Roman](https://leetcode.com/problems/integer-to-roman/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_integertoroman_12/IntegerToRoman.java)|MEDIUM - Number|
|12|[841. Keys and Rooms](https://leetcode.com/problems/keys-and-rooms/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_keyandroom_841/KeysAndRoom.java)|MEDIUM - Number|
|13|[670. Maximum Swap](https://leetcode.com/problems/maximum-swap/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_maximumswap_670/MaximumSwap.java)|MEDIUM - Number|
|14|[152. Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_maxsubarrayproduct_152/MaxSubarrayProduct.java)|MEDIUM - Number|
|15|[918. Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_maxsumcirclesubarray_918/MaxSumCircularSubarray.java)|MEDIUM - Number|
|16|[1899. Merge Triplets to Form Target Triplet](https://leetcode.com/problems/merge-triplets-to-form-target-triplet/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_mergetriplets_1899/MergeTriplets.java)|MEDIUM - Number|
|17|[1007. Minimum Domino Rotations For Equal Row](https://leetcode.com/problems/minimum-domino-rotations-for-equal-row/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_mindominorotation_1007/MinDominoRotation.java)|MEDIUM - Number|
|18|[2439. Minimize Maximum of Array](https://leetcode.com/problems/minimize-maximum-of-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_minmaxarray_2439/MinMaxArray.java)|MEDIUM - Number|
|19|Smallest Positive Integer|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_minpositivesubset/MinPositiveSubset.java)|MEDIUM - Number|
|20|[2028. Find Missing Observations](https://leetcode.com/problems/find-missing-observations/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_missingobservation_2028/MissingObservation.java)|MEDIUM - Number|
|21|[31. Next Permutation](https://leetcode.com/problems/next-permutation/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_nextpermutation_31/NextPermutation.java)|MEDIUM - Number|
|22|[2001. Number of Pairs of Interchangeable Rectangles](https://leetcode.com/problems/number-of-pairs-of-interchangeable-rectangles/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_pairinterchangerectangle_2001/PairInterchangeRectangle.java)|MEDIUM - Number|
|23|[50. Pow x, n](https://leetcode.com/problems/powx-n/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_power_50/Power.java)|MEDIUM - Number|
|24|[238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_productofarrayexceptself_238/ProductOfArray.java)|MEDIUM - Number|
|25|[80. Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_removeduplicatessortedarray_80/RemoveDuplicates.java)|MEDIUM - Number|
|26|[402. Remove K Digits](https://leetcode.com/problems/remove-k-digits/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_removekdigits_402/RemoveKDigits.java)|MEDIUM - Number|
|27|[1041. Robot Bounded In Circle](https://leetcode.com/problems/robot-bounded-in-circle/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_robotincircle_1041/RobotInCircle.java)|MEDIUM - Number|
|28|Shuffle Array|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_shufflearray/ShuffleArray.java)|MEDIUM - Number|
|29|[75. Sort Colors](https://leetcode.com/problems/sort-colors/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_sortcolors_75/SortColors.java)|MEDIUM - Number|
|30|[280. Wiggle Sort](https://leetcode.com/problems/wiggle-sort/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_wigglesort_280/WiggleSort.java)|MEDIUM - Number|
|31|[2348. Number of Zero-Filled Subarrays](https://leetcode.com/problems/number-of-zero-filled-subarrays/description/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_01_zerofillsubarray_2348/ZeroFillSubarray.java)|MEDIUM - Number|
|32|Caesar Cipher|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_caesarcipher/CaesarCipher.java)|MEDIUM - String|
|33|[165. Compare Version Numbers](https://leetcode.com/problems/compare-version-numbers/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_compareversionnumber_165/VersionNumber.java)|MEDIUM - String|
|34|[271. Encode and Decode Strings](https://leetcode.com/problems/encode-and-decode-strings/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_encodedecodestring/EncodeDecodeString.java)|MEDIUM - String|
|35|[395. Longest Substring with At Least K Repeating Characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_longestsubstringkrepeating_395/LargestSubstringKRepeat.java)|MEDIUM - String|
|36|[1963. Minimum Number of Swaps to Make the String Balanced](https://leetcode.com/problems/minimum-number-of-swaps-to-make-the-string-balanced/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_minswaptobalance_1963/MinSwapToBalance.java)|MEDIUM - String|
|37|[43. Multiply Strings](https://leetcode.com/problems/multiply-strings/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_multiplystring_43/MultiplyString.java)|MEDIUM - String|
|38|[752. Open the Lock](https://leetcode.com/problems/open-the-lock/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_openlock_752/OpenLock.java)|MEDIUM - String|
|39|[647. Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_palindromicsubstrings_647/PalindromeSubstr.java)|MEDIUM - String|
|40|[838. Push Dominoes](https://leetcode.com/problems/push-dominoes/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_pushdomino_838/PushDomino.java)|MEDIUM - String|
|41|[1209. Remove All Adjacent Duplicates in String II](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_removeadjacentdup_1209/RemoveAdjacentDuplicate.java)|MEDIUM - String|
|42|[6. Zigzag Conversion](https://leetcode.com/problems/zigzag-conversion/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_02_zigzag_6/ZigZag.java)|MEDIUM - String|
|43|[1461. Check If a String Contains All Binary Codes of Size K](https://leetcode.com/problems/check-if-a-string-contains-all-binary-codes-of-size-k/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_binarycodesizek_1461/BinaryCodeSizeK.java)|MEDIUM - Map & Set|
|44|[554. Brick Wall](https://leetcode.com/problems/brick-wall/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_brickwall_554/BrickWall.java)|MEDIUM - Map & Set|
|45|[2013. Detect Squares](https://leetcode.com/problems/detect-squares/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_detectsquare_2013/DetectSquare.java)|MEDIUM - Map & Set|
|46|[1296. Divide Array in Sets of K Consecutive Numbers](https://leetcode.com/problems/divide-array-in-sets-of-k-consecutive-numbers/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_dividearraykset_1296/DivideArrayKSet.java)|MEDIUM - Map & Set|
|47|[535. Encode and Decode TinyURL](https://leetcode.com/problems/encode-and-decode-tinyurl/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_encodedecodetinyurl_535/EncodeDecodeTinyUrl.java)|MEDIUM - Map & Set|
|48|[973. K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_findkclosestpointorigin_973/FindKClosestPointToOrigin.java)|MEDIUM - Map & Set|
|49|[49. Group Anagrams](https://leetcode.com/problems/group-anagrams/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_groupanagrams_49/GroupAnagrams.java)|MEDIUM - Map & Set|
|50|[846. Hand of Straights](https://leetcode.com/problems/hand-of-straights/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_handofstraights_846/HandOfStraights.java)|MEDIUM - Map & Set|
|51|[380. Insert Delete GetRandom](https://leetcode.com/problems/insert-delete-getrandom-o1/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_insertdeletegetrandom_380/RandomSet.java)|MEDIUM - Map & Set|
|52|[1930. Unique Length 3 Palindromic Subsequences](https://leetcode.com/problems/unique-length-3-palindromic-subsequences/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_length3palindrome_1930/Length3PalindromeSubseq.java)|MEDIUM - Map & Set|
|53|[128. Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_longestconsecutivesequence_128/LongConsecutiveSequence.java)|MEDIUM - Map & Set|
|54|[146. LRU Cache](https://leetcode.com/problems/lru-cache/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_lrucache_146/LRUCacheConcept.java)|MEDIUM - Map & Set|
|55|[146. LRU Cache](https://leetcode.com/problems/lru-cache/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_lrucache_146/LRUCacheImpl.java)|MEDIUM - Map & Set|
|56|[187. Repeated DNA Sequences](https://leetcode.com/problems/repeated-dna-sequences/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_repeateddnaseq_187/RepeatedDnaSeq.java)|MEDIUM - Map & Set|
|57|[621. Task Scheduler](https://leetcode.com/problems/task-scheduler/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_taskscheduler_621/TaskScheduler.java)|MEDIUM - Map & Set|
|58|[16. 3Sum Closest](https://leetcode.com/problems/3sum-closest/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_threesumclosest_16/ThreeSumClosest.java)|MEDIUM - Map & Set|
|59|[1396. Design Underground System](https://leetcode.com/problems/design-underground-system/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_undergroundsystem_1396/DesingUndergroudSystem.java)|MEDIUM - Map & Set|
|60|[36. Valid Sudoku](https://leetcode.com/problems/valid-sudoku/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_03_validsukoku_36/ValidSudoku.java)|MEDIUM - Map & Set|
|61|[215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_arraykthlargest_215_1985/KthLargestArray.java)|MEDIUM - Heap|
|62|[1985. Find the Kth Largest Integer in the Array](https://leetcode.com/problems/find-the-kth-largest-integer-in-the-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_arraykthlargest_215_1985/KthLargestArray.java)|MEDIUM - Heap|
|63|[1094. Car Pooling](https://leetcode.com/problems/car-pooling/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_carpooling_1094/CarPooling.java)|MEDIUM - Heap|
|64|[1167. Minimum Cost to Connect Sticks](https://leetcode.com/problems/minimum-cost-to-connect-sticks/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_connectsticks_1167/ConnectSticksMinCost.java)|MEDIUM - Heap|
|65|[1834. Single-Threaded CPU](https://leetcode.com/problems/single-threaded-cpu/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_cpupriority_1834/CpuPriority.java)|MEDIUM - Heap|
|66|[355. Design Twitter](https://leetcode.com/problems/design-twitter/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_designtwitter_355/DesignTwitter.java)|MEDIUM - Heap|
|67|[451. Sort Characters By Frequency](https://leetcode.com/problems/sort-characters-by-frequency/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_frequencysortchar_451/FrequencySortChar.java)|MEDIUM - Heap|
|68|[1405. Longest Happy String](https://leetcode.com/problems/longest-happy-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_longesthappystring/HappyString.java)|MEDIUM - Heap|
|69|[983. Minimum Cost For Tickets](https://leetcode.com/problems/minimum-cost-for-tickets/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_mincostticket_983/MinCostTicket.java)|MEDIUM - Heap|
|70|[1882. Process Tasks Using Servers](https://leetcode.com/problems/process-tasks-using-servers/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_processtaskserver_1882/ProcessTaskServer.java)|MEDIUM - Heap|
|71|[767. Reorganize String](https://leetcode.com/problems/reorganize-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_reorganizestring_767/ReorganizeString.java)|MEDIUM - Heap|
|72|[358. Rearrange String k Distance Apart](https://leetcode.com/problems/rearrange-string-k-distance-apart/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_reorganizestring_767/ReorganizeString.java)|MEDIUM - Heap|
|73|[1845. Seat Reservation Manager](https://leetcode.com/problems/seat-reservation-manager/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_seatreservation_1845/SeatReservation.java)|MEDIUM - Heap|
|74|[Sort K sorted array](https://www.geeksforgeeks.org/nearly-sorted-algorithm/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_sortksortedarray/SortKSortedArray.java)|MEDIUM - Heap|
|75|[347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_topkfrequentelement_347/TopKFrequentElementInArray.java)|MEDIUM - Heap|
|76|[692. Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_04_topkfrequentwords_692/TopKfrequentWords.java)|MEDIUM - Heap|
|77|[438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_allanagram_438/FindAllAnagram.java)|MEDIUM - Sliding window / Two pointer|
|78|[122. Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_besttimetobuysell2_122/BestTimeBuySell2.java)|MEDIUM - Sliding window / Two pointer|
|79|[881. Boats to Save People](https://leetcode.com/problems/boats-to-save-people/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_boatstosavepeople_881/BoatsToSave.java)|MEDIUM - Sliding window / Two pointer|
|80|Boats to Save People without count per boat|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_boatstosavepeople_881/BoatsToSaveWithoutLimit.java)|MEDIUM - Sliding window / Two pointer|
|81|[11. Container With Most Water](https://leetcode.com/problems/container-with-most-water/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_containerwithmostwater_11/ContainerWithMostWater.java)|MEDIUM - Sliding window / Two pointer|
|82|[1838. Frequency of the Most Frequent Element](https://leetcode.com/problems/frequency-of-the-most-frequent-element/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_frequenceyofmostfrequent/FrequencyOfMostFrequent.java)|MEDIUM - Sliding window / Two pointer|
|83|[904. Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_fruitsinbasket_904/FruitsInBasket.java)|MEDIUM - Sliding window / Two pointer|
|84|[5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_longestpalindromesubstr_5/LongestPalindromeSubstr.java)|MEDIUM - Sliding window / Two pointer|
|85|[424. Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_longestrepeatingcharreplace_424/LongestRepeatingCharReplace.java)|MEDIUM - Sliding window / Two pointer|
|86|[159. Longest Substring with At Most Two Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_longestsubstrwithkdistinct_340/LongestSubstringKDistinct.java)|MEDIUM - Sliding window / Two pointer|
|87|[340. Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_longestsubstrwithkdistinct_340/LongestSubstringKDistinct.java)|MEDIUM - Sliding window / Two pointer|
|88|[3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_longestsubstrwithoutrepeating_3/LongSubStrWithoutRepeating.java)|MEDIUM - Sliding window / Two pointer|
|89|[1004. Max Consecutive Ones III](https://leetcode.com/problems/max-consecutive-ones-iii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_maxconsecutiveone3_1004/MaxOneFlipK.java)|MEDIUM - Sliding window / Two pointer|
|90|[1423. Maximum Points You Can Obtain from Cards](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_maxpointscard_1423/MaxPointsCards.java)|MEDIUM - Sliding window / Two pointer|
|91|[1888. Minimum Number of Flips to Make the Binary String Alternating](https://leetcode.com/problems/minimum-number-of-flips-to-make-the-binary-string-alternating/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_minflipbinaryalternate_1888/MinFlipBinaryAlternating.java)|MEDIUM - Sliding window / Two pointer|
|92|[64. Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_minimumpathsumgrid_64/MinPathSum.java)|MEDIUM - Sliding window / Two pointer|
|93|[209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_minsubarraysumsize_209/MinSizeSubArraySum.java)|MEDIUM - Sliding window / Two pointer|
|94|[763. Partition Labels](https://leetcode.com/problems/partition-labels/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_partitionlabel_763/PartitionLabel.java)|MEDIUM - Sliding window / Two pointer|
|95|[567. Permutation in String](https://leetcode.com/problems/permutation-in-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_permutation2string_567/PermutationIn2String.java)|MEDIUM - Sliding window / Two pointer|
|96|[151. Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_reversewordsinstring/ReverseWordsInString.java)|MEDIUM - Sliding window / Two pointer|
|97|[581. Shortest Unsorted Continuous Subarray](https://leetcode.com/problems/shortest-unsorted-continuous-subarray/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_shortestunsortedcontsubarray_581/ShortestUnsortedContSubarray.java)|MEDIUM - Sliding window / Two pointer|
|98|[1343. Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold](https://leetcode.com/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_subarraykavgthreshold_1343/SubarrayKThreshold.java)|MEDIUM - Sliding window / Two pointer|
|99|[1498. Number of Subsequences That Satisfy the Given Sum Condition](https://leetcode.com/problems/number-of-subsequences-that-satisfy-the-given-sum-condition/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_subseqgivensum_1498/NumberOfSubSeqGivenSum.java)|MEDIUM - Sliding window / Two pointer|
|100|[15. 3Sum](https://leetcode.com/problems/3sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_threesum_15/ThreeSum.java)|MEDIUM - Sliding window / Two pointer|
|101|[167. Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_05_twosum2_167/TwoSumSorted.java)|MEDIUM - Sliding window / Two pointer|
|102|[1254. Number of Closed Islands](https://leetcode.com/problems/number-of-closed-islands/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_closedisland_1254/ClosedIsland.java)|MEDIUM - Matrix|
|103|[1905. Count Sub Islands](https://leetcode.com/problems/count-sub-islands/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_countsubislands/CountSubIslands.java)|MEDIUM - Matrix|
|104|[289. Game of Life](https://leetcode.com/problems/game-of-life/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_gameoflife_289/GameOfLife.java)|MEDIUM - Matrix|
|105|[1428. Leftmost Column with at Least a One](https://leetcode.com/problems/leftmost-column-with-at-least-a-one/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_leftmostwithatleast1/LeftMostWithAtleast1.java)|MEDIUM - Matrix|
|106|[695. Max Area of Island](https://leetcode.com/problems/max-area-of-island/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_maxareaisland_695/MaxAreaIsland.java)|MEDIUM - Matrix|
|107|[221. Maximal Square](https://leetcode.com/problems/maximal-square/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_maximalsquare_221/MaximalSquare.java)|MEDIUM - Matrix|
|108|[200. Number of Islands](https://leetcode.com/problems/number-of-islands/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_numberofislands_200/NumberOfIslands.java)|MEDIUM - Matrix|
|109|[417. Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_pacificatlanticwater_417/PacificAtlanticWater.java)|MEDIUM - Matrix|
|110|[48. Rotate Image](https://leetcode.com/problems/rotate-image/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_rotateimage_48/RotateImage.java)|MEDIUM - Matrix|
|111|[73. Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_setmatrixzero_73/SetMatrixZero.java)|MEDIUM - Matrix|
|112|[1091. Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_shortestpathbinarymatrix_1091/ShortestPathBinaryMatrix.java)|MEDIUM - Matrix|
|113|[54. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_spiralmatrix_54/SpiralMatrix.java)|MEDIUM - Matrix|
|114|[59. Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_spiralmatrix_59/SpiralMatrix.java)|MEDIUM - Matrix|
|115|[130. Surrounded Regions](https://leetcode.com/problems/surrounded-regions/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_surroundedregion_130/SurroundedRegion.java)|MEDIUM - Matrix|
|116|[286. Walls and Gates](https://leetcode.com/problems/walls-and-gates)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_wallgate_286/WallGate.java)|MEDIUM - Matrix|
|117|[79. Word Search](https://leetcode.com/problems/word-search/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_06_wordsearch_79/WordSearch.java)|MEDIUM - Matrix|
|118|[113. Path Sum II](https://leetcode.com/problems/path-sum-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_btpathsum2_113/PathSum2BT.java)|MEDIUM - Backtracking|
|119|[77. Combinations](https://leetcode.com/problems/combinations/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_combinations_77/Combinations.java)|MEDIUM - Backtracking|
|120|[39. Combination Sum](https://leetcode.com/problems/combination-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_combinationsum1_39/CombinationSum.java)|MEDIUM - Backtracking|
|121|[40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_combinationsum2_40/CombinationSum2.java)|MEDIUM - Backtracking|
|122|[2101. Detonate the Maximum Bombs](https://leetcode.com/problems/detonate-the-maximum-bombs/description/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_detonatebombs_2101/DetonateBombs.java)|MEDIUM - Backtracking|
|123|[1239. Maximum Length of a Concatenated String with Unique Characters](https://leetcode.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_maxlenconcatunq_1239/MaxLengthConcatUnique.java)|MEDIUM - Backtracking|
|124|[131. Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_palindromepartition_131/PalindromePartition.java)|MEDIUM - Backtracking|
|125|[22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_parenthesisgenerate_22/GenerateParenthesis.java)|MEDIUM - Backtracking|
|126|[46. Permutations](https://leetcode.com/problems/permutations/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_permutationnumber1_46/PermutationsNumber.java)|MEDIUM - Backtracking|
|127|[47. Permutations II](https://leetcode.com/problems/permutations-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_permutationnumber2_47/PermutationsNumberHasDuplicates.java)|MEDIUM - Backtracking|
|128|String permutation|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_permutationstring/PermutationString.java)|MEDIUM - Backtracking|
|129|[17. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_phonenumbercombination_17/PhoneNumberCombination.java)|MEDIUM - Backtracking|
|130|[93. Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_restoreipaddress_93/RestoreIpAddress.java)|MEDIUM - Backtracking|
|131|[1849. Splitting a String Into Descending Consecutive Values](https://leetcode.com/problems/splitting-a-string-into-descending-consecutive-values/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_splitstringdescending_1849/SplitStringDescending.java)|MEDIUM - Backtracking|
|132|[90. Subsets II](https://leetcode.com/problems/subsets-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_subset2_90/SubsetHasDuplicates.java)|MEDIUM - Backtracking|
|133|[78. Subsets](https://leetcode.com/problems/subsets/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_subset_78/Subset.java)|MEDIUM - Backtracking|
|134|[1376. Time Needed to Inform All Employees](https://leetcode.com/problems/time-needed-to-inform-all-employees/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_timetoinform_1376/TimeToInform.java)|MEDIUM - Backtracking|
|135|[1980. Find Unique Binary String](https://leetcode.com/problems/find-unique-binary-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_07_uniquebinarystring_1980/UniqueBinaryString.java)|MEDIUM - Backtracking|
|136|[523. Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_08_continuoussubarraysum_523/ContinuousSubarraySum.java)|MEDIUM - Pre-Sum|
|137|[926. Flip String to Monotone Increasing](https://leetcode.com/problems/flip-string-to-monotone-increasing/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_08_flipstringmonotone_926/FlipStringMonotone.java)|MEDIUM - Pre-Sum|
|138|[2017. Grid Game](https://leetcode.com/problems/grid-game/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_08_gridgame_2017/GridGame.java)|MEDIUM - Pre-Sum|
|139|[528. Random Pick with Weight](https://leetcode.com/problems/random-pick-with-weight/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_08_randompickweight/RandomPickWeight.java)|MEDIUM - Pre-Sum|
|140|[304. Range Sum Query 2D - Immutable](https://leetcode.com/problems/range-sum-query-2d-immutable/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_08_rangesum2dimmutable_304/RangeSum2D.java)|MEDIUM - Pre-Sum|
|141|[560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_08_subarraysumk_560/SubArraySumK.java)|MEDIUM - Pre-Sum|
|142|[325. Maximum Size Subarray Sum Equals k](https://leetcode.com/problems/maximum-size-subarray-sum-equals-k/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_08_subarraysumkmaxsize_325/MaxSizeSubarraySumK.java)|MEDIUM - Pre-Sum|
|143|0/1 knapsack|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_01knapsack/KnapSack.java)|MEDIUM - DP|
|144|[894. All Possible Full Binary Trees](https://leetcode.com/problems/all-possible-full-binary-trees/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_allbinaryfulltree_894/AllBinaryFullTree.java)|MEDIUM - DP|
|145|[1626. Best Team With No Conflicts](https://leetcode.com/problems/best-team-with-no-conflicts/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_bestteamnoconflict_1626/BestTeamNoConflict.java)|MEDIUM - DP|
|146|[309. Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_besttimetobuysell3_309/BestTimeBuySellCoolDown.java)|MEDIUM - DP|
|147|[518. Coin Change 2](https://leetcode.com/problems/coin-change-2/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_coinchange2_518/CoinChange2.java)|MEDIUM - DP|
|148|[322. Coin Change](https://leetcode.com/problems/coin-change/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_coinchange_322/CoinChange.java)|MEDIUM - DP|
|149|[377. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_combinationsum4_377/CombinationSum4.java)|MEDIUM - DP|
|150|[91. Decode Ways](https://leetcode.com/problems/decode-ways/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_decodeways_91/DecodeWays.java)|MEDIUM - DP|
|151|[740. Delete and Earn](https://leetcode.com/problems/delete-and-earn/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_deleteearn/DeleteEarn.java)|MEDIUM - DP|
|152|Egg Drop|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_eggdrop_1884/EggDrop.java)|MEDIUM - DP|
|153|[1884. Egg Drop With 2 Eggs and N Floors](https://leetcode.com/problems/egg-drop-with-2-eggs-and-n-floors/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_eggdrop_1884/SuperEggDrop.java)|MEDIUM - DP|
|154|[198. House Robber](https://leetcode.com/problems/house-robber/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_houserobber1_198/HouseRob.java)|MEDIUM - DP|
|155|[213. House Robber II](https://leetcode.com/problems/house-robber-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_houserobber2_213/HouseRob2.java)|MEDIUM - DP|
|156|[337. House Robber III](https://leetcode.com/problems/house-robber-iii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_houserobber3_337/HouseRob3.java)|MEDIUM - DP|
|157|[343. Integer Break](https://leetcode.com/problems/integer-break/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_integerbreak_343/IntegerBreak.java)|MEDIUM - DP|
|158|[97. Interleaving String](https://leetcode.com/problems/interleaving-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_interleavingstring_97/InterleavingString.java)|MEDIUM - DP|
|159|[1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_longestcommonsubsequence_1143/LongestCommonSubSequence.java)|MEDIUM - DP|
|160|[300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_longestincreasingsubseq_300/LongestIncreasingSubSeq.java)|MEDIUM - DP|
|161|[516. Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_longestpalindromicsubseq/LongestPalindromeSubSeq.java)|MEDIUM - DP|
|162|[473. Matchsticks to Square](https://leetcode.com/problems/matchsticks-to-square/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_matchsticksquare_473/MatchStickSquare.java)|MEDIUM - DP|
|163|[1911. Maximum Alternating Subsequence Sum](https://leetcode.com/problems/maximum-alternating-subsequence-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_maxalterningsubseq_1911/MaxAlternatingSubSeq.java)|MEDIUM - DP|
|164|[2002. Maximum Product of the Length of Two Palindromic Subsequences](https://leetcode.com/problems/maximum-product-of-the-length-of-two-palindromic-subsequences/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_maxprodpalindromesubseq_2002/MaxProductionPalindromeSubSeq.java)|MEDIUM - DP|
|165|[673. Number of Longest Increasing Subsequence](https://leetcode.com/problems/number-of-longest-increasing-subsequence/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_numberoflongestincreasingsubseq_673/NumberOfLongestIncreasingSubseq.java)|MEDIUM - DP|
|166|[256. Paint House](https://leetcode.com/problems/paint-house/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_painthouse_256/PaintHouse.java)|MEDIUM - DP|
|167|[416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_partitionequalsubsetsum/PartitionEqualSubsetSum.java)|MEDIUM - DP|
|168|[698. Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_partitionkequalsubset_698/PartitionKEqualSubset.java)|MEDIUM - DP|
|169|[279. Perfect Squares](https://leetcode.com/problems/perfect-squares/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_perfectsquare_279/PerfectSquare.java)|MEDIUM - DP|
|170|Rod Cutting|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_rodcutting/RodCutting.java)|MEDIUM - DP|
|171|[2140. Solving Questions With Brainpower](https://leetcode.com/problems/solving-questions-with-brainpower/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_solvingquestion_2140/SolveQuestion.java)|MEDIUM - DP|
|172|[1140. Stone Game II](https://leetcode.com/problems/stone-game-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_stonegame2_1140/StoneGame2.java)|MEDIUM - DP|
|173|[877. Stone Game](https://leetcode.com/problems/stone-game/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_stonegame_877/StoneGame.java)|MEDIUM - DP|
|174|SubSet Sum|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_subsetsum/SubSetSum.java)|MEDIUM - DP|
|175|SubSet Sum Count|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_subsetsumcount/SubSetSumCount.java)|MEDIUM - DP|
|176|[494. Target Sum](https://leetcode.com/problems/target-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_targetsum_494/TargetSum.java)|MEDIUM - DP|
|177|[120. Triangle](https://leetcode.com/problems/triangle/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_triangle_120/Triangle.java)|MEDIUM - DP|
|178|Unbounded knapsack|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_unboundedknapsack/UnboundedKnapSack.java)|MEDIUM - DP|
|179|[1035. Uncrossed Lines](https://leetcode.com/problems/uncrossed-lines/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_uncrossedline_1035/UncrossedLine.java)|MEDIUM - DP|
|180|[95. Unique Binary Search Trees II](https://leetcode.com/problems/unique-binary-search-trees-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_uniquebinarysearchtree2_95/UniqueBST2.java)|MEDIUM - DP|
|181|[96. Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_uniquebinarysearchtree_96/UniqueBST.java)|MEDIUM - DP|
|182|[63. Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_uniquepath2_63/UniquePath2.java)|MEDIUM - DP|
|183|[62. Unique Paths](https://leetcode.com/problems/unique-paths/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_uniquepath_62/UniquePath.java)|MEDIUM - DP|
|184|[139. Word Break](https://leetcode.com/problems/word-break/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_09_wordbreak_139/WordBreak.java)|MEDIUM - DP|
|185|[2. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_add2numbers_2/AddTwoNumbers.java)|MEDIUM - Link List|
|186|[138. Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_copylistrandompointer_138/CopyListRandomPointer.java)|MEDIUM - Link List|
|187|[287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_duplicatenumber_287/DuplicateNumber.java)|MEDIUM - Link List|
|188|[147. Insertion Sort List](https://leetcode.com/problems/insertion-sort-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_insertionsortlinklist_147/InsertionSortLinkList.java)|MEDIUM - Link List|
|189|[109. Convert Sorted List to Binary Search Tree](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_linklisttobst_109/LinkListToBST.java)|MEDIUM - Link List|
|190|[86. Partition List](https://leetcode.com/problems/partition-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_partitionlinklist_86/PartitionLinkList.java)|MEDIUM - Link List|
|191|[82. Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_removeduplicates2linklist_82/RemoveDuplicatesSortList2.java)|MEDIUM - Link List|
|192|[19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_removennodelinklist_19/RemoveNNodeLinkList.java)|MEDIUM - Link List|
|193|[143. Reorder List](https://leetcode.com/problems/reorder-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_reorderlinklist_143/ReorderLinkList.java)|MEDIUM - Link List|
|194|[92. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_reverselinklist2_92/ReverseLinkList2.java)|MEDIUM - Link List|
|195|Reverse link list even odd|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_reverselinklisteven/ReverseLinkListEven.java)|MEDIUM - Link List|
|196|[189. Rotate Array](https://leetcode.com/problems/rotate-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_rotatearray_189/RotateArray.java)|MEDIUM - Link List|
|197|[61. Rotate List](https://leetcode.com/problems/rotate-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_rotatelinklist_61/RotateLinkList.java)|MEDIUM - Link List|
|198|[148. Sort List](https://leetcode.com/problems/sort-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_sortlinklist_148/SortLinkList.java)|MEDIUM - Link List|
|199|[24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_10_swapnodepairlinklist_24/SwapNodeLinkList.java)|MEDIUM - Link List|
|200|[513. Find Bottom Left Tree Value](https://leetcode.com/problems/find-bottom-left-tree-value/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_bottomlefttreevalue_513/BottomLeftTreeValue.java)|MEDIUM - Binary Tree|
|201|[173. Binary Search Tree Iterator](https://leetcode.com/problems/binary-search-tree-iterator/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_bstiterator_173/BTIterator.java)|MEDIUM - Binary Tree|
|202|[230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_bstkthsmall_230/KthSmallestBST.java)|MEDIUM - Binary Tree|
|203|[99. Recover Binary Search Tree](https://leetcode.com/problems/recover-binary-search-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_bstrecover_99/BSTRecover.java)|MEDIUM - Binary Tree|
|204|[98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_bstvalid_98/CheckIfValidBST.java)|MEDIUM - Binary Tree|
|205|Check Level Order Traversal of BST|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_bstvalidfromarray/BSTValidArray.java)|MEDIUM - Binary Tree|
|206|[110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btbalanced_110/BalancedBT.java)|MEDIUM - Binary Tree|
|207|[1110. Delete Nodes And Return Forest](https://leetcode.com/problems/delete-nodes-and-return-forest/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btdeleteforest_1110/DeleteNodeReturnForest.java)|MEDIUM - Binary Tree|
|208|[979. Distribute Coins in Binary Tree](https://leetcode.com/problems/distribute-coins-in-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btdistributecoins_979/DistributeCoins.java)|MEDIUM - Binary Tree|
|209|[114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btflatten_114/FlattenBinaryTree.java)|MEDIUM - Binary Tree|
|210|[106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btfrompostinorder_106/BTFromPostInOrder.java)|MEDIUM - Binary Tree|
|211|[105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btfrompreinorder_105/BTFromPreInOrder.java)|MEDIUM - Binary Tree|
|212|[1448. Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btgoodnode_1448/GoodNodeBT.java)|MEDIUM - Binary Tree|
|213|[236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btlca_236/FindLCANodeExist.java)|MEDIUM - Binary Tree|
|214|[236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btlca_236/FindLCANodeMayExist.java)|MEDIUM - Binary Tree|
|215|[437. Path Sum III](https://leetcode.com/problems/path-sum-iii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btpathsum3_437/PathSum3BT.java)|MEDIUM - Binary Tree|
|216|[814. Binary Tree Pruning](https://leetcode.com/problems/binary-tree-pruning/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btpruning_814/BinaryTreePruning.java)|MEDIUM - Binary Tree|
|217|[199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btrightview_199/RightViewBT.java)|MEDIUM - Binary Tree|
|218|[129. Sum Root to Leaf Numbers](https://leetcode.com/problems/sum-root-to-leaf-numbers/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btroottoleafsum_129/RootToLeafSumBT.java)|MEDIUM - Binary Tree|
|219|[107. Binary Tree Level Order Traversal II](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_bttraversalleaftoroot_107/BTTraversalLeafToRoot.java)|MEDIUM - Binary Tree|
|220|[662. Maximum Width of Binary Tree](https://leetcode.com/problems/maximum-width-of-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btwidth_662/BTWidth.java)|MEDIUM - Binary Tree|
|221|[103. Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_btzigzag_103/ZigZagLevelOrderBT.java)|MEDIUM - Binary Tree|
|222|[538. Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_convertbstgreatertree_538/ConvertBSTGreaterTree.java)|MEDIUM - Binary Tree|
|223|[450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_deletenodebst_450/DeleteNodeBST.java)|MEDIUM - Binary Tree|
|224|[951. Flip Equivalent Binary Trees](https://leetcode.com/problems/flip-equivalent-binary-trees/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_flipequivalentbt_951/FlipEquivalentBT.java)|MEDIUM - Binary Tree|
|225|[701. Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_insertnodebst_701/InsertNodeBST.java)|MEDIUM - Binary Tree|
|226|[1993. Operations on Tree](https://leetcode.com/problems/operations-on-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_operationontree_1993/OperationOnTree.java)|MEDIUM - Binary Tree|
|227|[116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_populaterightpointer_116/PopulateRightPointer.java)|MEDIUM - Binary Tree|
|228|[669. Trim a Binary Search Tree](https://leetcode.com/problems/trim-a-binary-search-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_11_trimbst_669/TrimBST.java)|MEDIUM - Binary Tree|
|229|[252. Meeting Rooms](https://leetcode.com/problems/meeting-rooms/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_12_checkintervaloverlap_252/CanAttendMeeting.java)|MEDIUM - Interval|
|230|[1288. Remove Covered Intervals](https://leetcode.com/problems/remove-covered-intervals/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_12_coveredinterval_1288/CoveredInterval.java)|MEDIUM - Interval|
|231|[57. Insert Interval](https://leetcode.com/problems/insert-interval/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_12_insertinterval_57/InsertInterval.java)|MEDIUM - Interval|
|232|[986. Interval List Intersections](https://leetcode.com/problems/interval-list-intersections/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_12_intervalintersection_986/IntervalIntersection.java)|MEDIUM - Interval|
|233|[56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_12_mergeinterval_56/MergeInterval.java)|MEDIUM - Interval|
|234|[253. Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_12_minmeetingroom_253/MinMeetingRoom2.java)|MEDIUM - Interval|
|235|[435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_12_nonoverlappinginterval_435/NonOverlappingInterval.java)|MEDIUM - Interval|
|236|[1011. Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_capcitytoshippackages_1011/CapacityToShipPackages.java)|MEDIUM - Binary Search|
|237|[658. Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_findkclosestelement_658/FindKClosestElement.java)|MEDIUM - Binary Search|
|238|[34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_firstlastelement_34/FirstLastElement.java)|MEDIUM - Binary Search|
|239|[875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_kokobannana_875/KokoBannana.java)|MEDIUM - Binary Search|
|240|[1898. Maximum Number of Removable Characters](https://leetcode.com/problems/maximum-number-of-removable-characters/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_maxremovablechar_1898/MaxRemoveableChars.java)|MEDIUM - Binary Search|
|241|[2616. Minimize the Maximum Difference of Pairs](https://leetcode.com/problems/minimize-the-maximum-difference-of-pairs/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_minmaxdiffpair_2616/MinMaxDiffPair.java)|MEDIUM - Binary Search|
|242|[153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_rotatedsortarrayminfind_153/FindMinRotatedSortArray.java)|MEDIUM - Binary Search|
|243|[81. Search in Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_rotatedsortedarraysearch2_81/SearchRotateSortArray2.java)|MEDIUM - Binary Search|
|244|[33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_rotatedsortedarraysearch_33/SearchRotatedSortArray.java)|MEDIUM - Binary Search|
|245|[74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_search2dmatrix_74/Search2DMatrix.java)|MEDIUM - Binary Search|
|246|[981. Time Based Key-Value Store](https://leetcode.com/problems/time-based-key-value-store/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_13_timebasedkeyvaluestore_981/TimeBasedKeyValueStore.java)|MEDIUM - Binary Search|
|247|[210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_14_courseschedule2_210/CourseSchedule2.java)|MEDIUM - Topological Sort|
|248|[456. 132 Pattern](https://leetcode.com/problems/132-pattern/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_15_132pattern_456/Pattern132.java)|MEDIUM - Stack & Monotonic Stack|
|249|[735. Asteroid Collision](https://leetcode.com/problems/asteroid-collision/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_15_asteroidcollision_735/AsteroidCollision.java)|MEDIUM - Stack & Monotonic Stack|
|250|[739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_15_dailytemperature_739/DailyTemperature.java)|MEDIUM - Stack & Monotonic Stack|
|251|[394. Decode String](https://leetcode.com/problems/decode-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_15_decodestring_394/DecodeString.java)|MEDIUM - Stack & Monotonic Stack|
|252|[1856. Maximum Subarray Min-Product](https://leetcode.com/problems/maximum-subarray-min-product/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_15_maxsubarrayminproduct_1856/MaxSubarrayMinProduct.java)|MEDIUM - Stack & Monotonic Stack|
|253|[1249. Minimum Remove to Make Valid Parentheses](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_15_minremovevalidparenthesis_1249/MinRemoveValidParenthesis.java)|MEDIUM - Stack & Monotonic Stack|
|254|[150. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_15_reversepolishnotation_150/ReversePolishNotation.java)|MEDIUM - Stack & Monotonic Stack|
|255|[901. Online Stock Span](https://leetcode.com/problems/online-stock-span/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_15_stockspan_901/StockSpan.java)|MEDIUM - Stack & Monotonic Stack|
|256|[721. Accounts Merge](https://leetcode.com/problems/accounts-merge/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_accountmerge_721/AccountMerge.java)|MEDIUM - Graph|
|257|[787. Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_cheapflightkstop_787/CheapFlightKStop.java)|MEDIUM - Graph|
|258|[133. Clone Graph](https://leetcode.com/problems/clone-graph/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_clonegraph_133/CloneGraph.java)|MEDIUM - Graph|
|259|[2359. Find Closest Node to Given Two Nodes](https://leetcode.com/problems/find-closest-node-to-given-two-nodes/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_closestnodetotwo_2359/ClosestNode.java)|MEDIUM - Graph|
|260|[323. Number of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_connectedcomponents_323/ConnectedComponents.java)|MEDIUM - Graph|
|261|[207. Course Schedule](https://leetcode.com/problems/course-schedule/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_courseschedule1_207/CourseSchedule.java)|MEDIUM - Graph|
|262|[802. Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_eventualsafestate_802/EventualSafeState.java)|MEDIUM - Graph|
|263|[261. Graph Valid Tree](https://leetcode.com/problems/graph-valid-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_graphvalidtree_261/GraphValidTree.java)|MEDIUM - Graph|
|264|[2477. Minimum Fuel Cost to Report to the Capital](https://leetcode.com/problems/minimum-fuel-cost-to-report-to-the-capital/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_minfueltocapital_2477/MinFuelToCapital.java)|MEDIUM - Graph|
|265|[2492. Minimum Score of a Path Between Two Cities](https://leetcode.com/problems/minimum-score-of-a-path-between-two-cities/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_minscorepath_2492/MinScorePath.java)|MEDIUM - Graph|
|266|[1584. Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_minspanningtree_1584/MinSpanningTree.java)|MEDIUM - Graph|
|267|[1443. Minimum Time to Collect All Apples in a Tree](https://leetcode.com/problems/minimum-time-to-collect-all-apples-in-a-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_mintimecollectapple_1443/MinTimeCollectApple.java)|MEDIUM - Graph|
|268|[743. Network Delay Time](https://leetcode.com/problems/network-delay-time/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_networkdelaytime_743/NetworkDelayTime.java)|MEDIUM - Graph|
|269|[1514. Path with Maximum Probability](https://leetcode.com/problems/path-with-maximum-probability/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_pathmaxprobability_1514/PathMaxProbability.java)|MEDIUM - Graph|
|270|[684. Redundant Connection](https://leetcode.com/problems/redundant-connection/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_redundant_connection_684/RedundantConnection.java)|MEDIUM - Graph|
|271|[1466. Reorder Routes to Make All Paths Lead to the City Zero](https://leetcode.com/problems/reorder-routes-to-make-all-paths-lead-to-the-city-zero/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_reorderroutes_1466/ReorderRoutes.java)|MEDIUM - Graph|
|272|[994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_rottenorange_994/RottenOrange.java)|MEDIUM - Graph|
|273|[934. Shortest Bridge](https://leetcode.com/problems/shortest-bridge/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_shortbridge_934/ShortBridge.java)|MEDIUM - Graph|
|274|[1129. Shortest Path with Alternating Colors](https://leetcode.com/problems/shortest-path-with-alternating-colors/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_shortestpathaltcolor_1129/ShortestPathAlternateColor.java)|MEDIUM - Graph|
|275|[909. Snakes and Ladders](https://leetcode.com/problems/snakes-and-ladders/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_16_snakeladder_909/SnakeLadder.java)|MEDIUM - Graph|
|276|Bathroom Problem|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_17_bathroomproblem/BathRoomProblem.java)|MEDIUM - Thread|
|277|[1115. Print FooBar Alternately](https://leetcode.com/problems/print-foobar-alternately/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_17_foobar_1115/FooBarLockPrint.java)|MEDIUM - Thread|
|278|[1115. Print FooBar Alternately](https://leetcode.com/problems/print-foobar-alternately/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_17_foobar_1115/FooBarLockWaitNotify.java)|MEDIUM - Thread|
|279|[1115. Print FooBar Alternately](https://leetcode.com/problems/print-foobar-alternately/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_17_foobar_1115/FooBarPrintSemaphore.java)|MEDIUM - Thread|
|280|[1116. Print Zero Even Odd](https://leetcode.com/problems/print-zero-even-odd/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_17_zeroevenodd_1116/ZeroEvenOddThread.java)|MEDIUM - Thread|
|281|[134. Gas Station](https://leetcode.com/problems/gas-station/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_18_gasstation_134/GasStation.java)|MEDIUM - Greedy|
|282|[45. Jump Game II](https://leetcode.com/problems/jump-game-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_18_jumpgame2_45/JumpGame2.java)|MEDIUM - Greedy|
|283|[1871. Jump Game VII](https://leetcode.com/problems/jump-game-vii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_18_jumpgame7_1871/JumpGame7.java)|MEDIUM - Greedy|
|284|[55. Jump Game](https://leetcode.com/problems/jump-game/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_18_jumpgame_55/JumpGame.java)|MEDIUM - Greedy|
|285|[179. Largest Number](https://leetcode.com/problems/largest-number/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_18_largestnumber_179/LargestNumber.java)|MEDIUM - Greedy|
|286|[678. Valid Parenthesis String](https://leetcode.com/problems/valid-parenthesis-string/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_18_parenthesisvalid_wild_678/ValidParenthesisWildCard.java)|MEDIUM - Greedy|
|287|[1029. Two City Scheduling](https://leetcode.com/problems/two-city-scheduling/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_18_twocityscheduling_1029/TwoCityScheduling.java)|MEDIUM - Greedy|
|288|[307. Range Sum Query - Mutable](https://leetcode.com/problems/range-sum-query-mutable/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_19_rangesummutable_307/RangeSumMutable.java)|MEDIUM - Segment Tree|
|289|[307. Range Sum Query - Mutable](https://leetcode.com/problems/range-sum-query-mutable/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_19_rangesummutable_307/RangeSumMutableSegTree.java)|MEDIUM - Segment Tree|
|290|[211. Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_20_addsearchwords_211/AddSearchWords.java)|MEDIUM - Prefix Tree / Trie|
|291|[442. Find All Duplicates in an Array](https://leetcode.com/problems/find-all-duplicates-in-an-array/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_21_findallduplicates_442/FindAllDuplicatesArray.java)|MEDIUM - Cyclic sort|
|292|[371. Sum of Two Integers](https://leetcode.com/problems/sum-of-two-integers/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_22_sumtwointeger_371/SumTwoInteger.java)|MEDIUM - Bit Manipulation|
|293|[1472. Design Browser History](https://leetcode.com/problems/design-browser-history/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_25_browserbackforward/BrowserHistoryLeet1.java)|MEDIUM - Generic|
|294|[1472. Design Browser History](https://leetcode.com/problems/design-browser-history/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_25_browserbackforward/BrowserHistoryLeet2.java)|MEDIUM - Generic|
|295|[1472. Design Browser History](https://leetcode.com/problems/design-browser-history/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_25_browserbackforward/BrowserHistoryLeet3.java)|MEDIUM - Generic|
|296|[1268. Search Suggestions System](https://leetcode.com/problems/search-suggestions-system/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/medium/_25_searchsuggestion_1268/SearchSuggestion.java)|MEDIUM - Generic|

### LeetCode - Hard

|Id   | Leetcode     | Solution        | Type        |
| --- | ---          | ---             | ---         |
|1|[273. Integer to English Words](https://leetcode.com/problems/integer-to-english-words/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_01_integertowords_273/IntegerToWords.java)|HARD - Number|
|2|[42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_01_trappingrainwater_42/TrappingRainWater.java)|HARD - Number|
|3|[472. Concatenated Words](https://leetcode.com/problems/concatenated-words/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_02_concatwords_472/ConcatenatedWords.java)|HARD - String|
|4|[2306. Naming a Company](https://leetcode.com/problems/naming-a-company)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_02_namingcompany_2306/NamingCompany.java)|HARD - String|
|5|[154. Find Minimum in Rotated Sorted Array II](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_02_rotatedarrayminfind2_154/FindMinRotatedSortedArray2.java)|HARD - String|
|6|[295. Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_03_findmedianstream_295/MedianFromDataStream.java)|HARD - Map & Set|
|7|[460. LFU Cache](https://leetcode.com/problems/lfu-cache/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_03_lfucache_460/LFUCacheImpl.java)|HARD - Map & Set|
|8|[149. Max Points on a Line](https://leetcode.com/problems/max-points-on-a-line/description/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_03_maxpointsline_149/MaxPointsLine.java)|HARD - Map & Set|
|9|[1383. Maximum Performance of a Team](https://leetcode.com/problems/maximum-performance-of-a-team/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_04_maxperformanceteam_1383/MaxPerformanceTeam.java)|HARD - Heap|
|10|[1851. Minimum Interval to Include Each Query](https://leetcode.com/problems/minimum-interval-to-include-each-query/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_04_minintervalquery_1851/MinIntervalQuery.java)|HARD - Heap|
|11|[76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_05_minwindowsubstring/MinWindowSubstring.java)|HARD - Sliding window / Two pointer|
|12|[239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_05_slidingwindowmax_239/SlidingWindowMax.java)|HARD - Sliding window / Two pointer|
|13|[312. Burst Balloons](https://leetcode.com/problems/burst-balloons/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_burstballoons_312/BurstBalloon.java)|HARD - DP|
|14|[1220. Count Vowels Permutation](https://leetcode.com/problems/count-vowels-permutation/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_countvowelpermutation_1220/CountVowelPermutation.java)|HARD - DP|
|15|[72. Edit Distance](https://leetcode.com/problems/edit-distance/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_editdistance_72/EditDistance.java)|HARD - DP|
|16|[32. Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_longestvalidparentheses/LongestValidParentheses.java)|HARD - DP|
|17|[1547. Minimum Cost to Cut a Stick](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_mincosttocut_1547/MinCostToCutStick.java)|HARD - DP|
|18|[1553. Minimum Number of Days to Eat N Oranges](https://leetcode.com/problems/minimum-number-of-days-to-eat-n-oranges/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_mindayseatorange_1553/MinDaysEatNOranges.java)|HARD - DP|
|19|[52. N-Queens II](https://leetcode.com/problems/n-queens-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_nqueens2_52/NQueen2.java)|HARD - DP|
|20|[51. N-Queens](https://leetcode.com/problems/n-queens/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_nqueens_51/NQueen.java)|HARD - DP|
|21|[920. Number of Music Playlists](https://leetcode.com/problems/number-of-music-playlists)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_numberofmusicplaylist_920/NumberMusicPlaylist.java)|HARD - DP|
|22|[132. Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_palindromepartition2_132/PalindromePartition2.java)|HARD - DP|
|23|[1866. Number of Ways to Rearrange Sticks With K Sticks Visible](https://leetcode.com/problems/number-of-ways-to-rearrange-sticks-with-k-sticks-visible/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_rearrangestickswithk_1866/RearrangeSticks.java)|HARD - DP|
|24|[10. Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_regularexpression_10/RegularExpression.java)|HARD - DP|
|25|[691. Stickers to Spell Word](https://leetcode.com/problems/stickers-to-spell-word/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_stickerspellword_691/StickerSpellWord.java)|HARD - DP|
|26|[1406. Stone Game III](https://leetcode.com/problems/stone-game-iii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_stonegame3_1406/StoneGame3.java)|HARD - DP|
|27|[140. Word Break II](https://leetcode.com/problems/word-break-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_09_wordbreak2_140/WordBreak2.java)|HARD - DP|
|28|[23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_10_mergeksortedlinklist_23/MergeKSortedList.java)|HARD - Link List|
|29|[25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_10_reversenodeskgroup_25/ReverseNodeKGroups.java)|HARD - Link List|
|30|[297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_11_binarytreeserialize_297/SerializeBinaryTree.java)|HARD - Binary Tree|
|31|[124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_11_btmaxpathsum_124/BinaryTreeMaxPathSum.java)|HARD - Binary Tree|
|32|[352. Data Stream as Disjoint Intervals](https://leetcode.com/problems/data-stream-as-disjoint-intervals/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_12_disjointinterval_352/DisjoinInterval.java)|HARD - Interval|
|33|[4. Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_13_findmediansortedarray_4/MedianSortedArray.java)|HARD - Binary Search|
|34|[410. Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_13_splitarraylargestsum_410/SplitArrayLargestSum.java)|HARD - Binary Search|
|35|[269. Alien Dictionary](https://leetcode.com/problems/alien-dictionary/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_14_aliendictionary_269/AlienDictionary.java)|HARD - Topological Sort|
|36|[115. Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_15_distinctsubseq_115/DistinctSubSeq.java)|HARD - Stack & Monotonic Stack|
|37|[84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_15_largestrectanglehistogram_84/LargestRectangleHistogram.java)|HARD - Stack & Monotonic Stack|
|38|[329. Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_15_longestIncreasingpathmatrix_329/LongestPathMatrix.java)|HARD - Stack & Monotonic Stack|
|39|[1964. Find the Longest Valid Obstacle Course at Each Position](https://leetcode.com/problems/find-the-longest-valid-obstacle-course-at-each-position/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_15_longestobstacle_1964/LongestObstacle.java)|HARD - Stack & Monotonic Stack|
|40|[895. Maximum Frequency Stack](https://leetcode.com/problems/maximum-frequency-stack/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_15_maxfrequencystack_895/MaxFrequencyStack.java)|HARD - Stack & Monotonic Stack|
|41|[85. Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_15_maximalrectangle_85/MaximalRectangle.java)|HARD - Stack & Monotonic Stack|
|42|[1857. Largest Color Value in a Directed Graph](https://leetcode.com/problems/largest-color-value-in-a-directed-graph/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_16_largestcolordg_1857/LargestColorGraph.java)|HARD - Graph|
|43|[2421. Number of Good Paths](https://leetcode.com/problems/number-of-good-paths/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_16_numgoodpaths_2421/GoodPaths.java)|HARD - Graph|
|44|[332. Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_16_reconstructitinerary_332/ReconstructItinerary.java)|HARD - Graph|
|45|[778. Swim in Rising Water](https://leetcode.com/problems/swim-in-rising-water/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_16_swiminrisingwater_778/SwimInRisingWater.java)|HARD - Graph|
|46|[127. Word Ladder](https://leetcode.com/problems/word-ladder/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_16_wordladder_127/WordLadder.java)|HARD - Graph|
|47|[212. Word Search II](https://leetcode.com/problems/word-search-ii/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_20_wordsearch2_212/WordSearch2.java)|HARD - Prefix Tree / Trie|
|48|[41. First Missing Positive](https://leetcode.com/problems/first-missing-positive/)|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/leetcode/hard/_21_findmissingpositive_41/MissingPositive.java)|HARD - Cyclic sort|

### Concurrency

|Id   | Leetcode     | Solution        | Type        |
| --- | ---          | ---             | ---         |
|1|Long Adder & Long Accumulator|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_01_addaccumulate/LongAccumulatorTest.java)|MEDIUM|
|2|Callable|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_02_callable/CallableTest.java)|EASY|
|3|Create Dead Lock|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_03_deadlock/DeadLockTest.java)|MEDIUM|
|4|Produce Consumer|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_04_producerconsumer/PCLock.java)|EASY|
|5|Produce Consumer|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_04_producerconsumer/PCWaitNotify.java)|EASY|
|6|Produce Consumer|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_04_producerconsumer/ProduceConsumer.java)|EASY|
|7|Semaphore|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_05_semaphore/SemaphoreTest.java)|MEDIUM|
|8|ScheduledExecutorService|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_06_scheduler/ScheduledExecutorTest.java)|MEDIUM|
|9|Stop Thread|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_07_threadstop/StopThreadInterrupt.java)|EASY|
|10|Stop Thread|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_07_threadstop/StopThreadVolatile.java)|EASY|
|11|Print Even Odd|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_08_evenodd/EvenOdd.java)|EASY|
|12|Thread Starvation|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_09_starvation/ThreadStarvation.java)|EASY|
|13|Thread Abort Policy|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_10_threadabort/ThreadAbortPolicy.java)|EASY|
|14|Read Write lock|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_11_locks/ReadWriteLockTest.java)|MEDIUM|
|15|ReentrantLock|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_11_locks/ReentrantLockTest.java)|MEDIUM|
|16|Stamped Lock|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_11_locks/StampedLockTest.java)|MEDIUM|
|17|Fork Join|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_12_forkjoinpool/ForkJoinTest.java)|MEDIUM|
|18|Common Words|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_13_commonwords/MostCommonWord.java)|EASY|
|19|Common Words multi thread|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_13_commonwords/MostCommonWordThread.java)|MEDIUM|
|20|Concurrent Modification|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_14_concurrentmodification/ListModifyTest.java)|EASY|
|21|Cyclic Barrier|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_15_cyclicbarrier/CyclicBarrierTest.java)|MEDIUM|
|22|Count Down Latch|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_16_countdownlatch/CountDownLatchTest.java)|EASY|
|23|Increment Array|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_17_incrementarray/IncrementArray.java)|MEDIUM|
|24|Phaser|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_18_phaser/PhaserTest.java)|MEDIUM|
|25|Completable Future|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_19_completablefuture/CompletableFutureTest.java)|EASY|
|26|AtomicStampedReference|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_20_atomic/AtomicRefStampTest.java)|MEDIUM|
|27|AtomicReference|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_20_atomic/AtomicRefTest.java)|MEDIUM|
|28|Implement Custom Thread Pool|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_21_threadpool/CustomThreadPool.java)|MEDIUM|
|29|Implement Custom Semaphore|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_22_semaphore/CustomSemaphore.java)|MEDIUM|
|30|Parallel Stream|[Solution](https://github.com/gitorko/project01/tree/main/src/test/java/com/demo/basics/concurrency/_23_parallelstream/ParallelStreamPool.java)|MEDIUM|

## Java Concurrency

### LinkedBlockingQueue (unbounded)

1. SynchronousQueue - space for only 1
2. ArrayBlockingQueue (bounded)
3. DelayQueue - unbounded blocking queue of delayed elements, element can only be taken when its delay has expired.

### Thread Pool Size

1. cpu intensive = num of cores
2. io intensive = time it takes for IO to complete.
3. ideal thread pool size = cores * (1 + (wait time/cpu time))

[https://youtu.be/ErNre5varF8](https://youtu.be/ErNre5varF8)

### Types of ExecutorService

1. ExecutorService uses BlockingQueue by default
2. newFixedThreadPool - LinkedBlockingQueue
3. newSingleThreadExecutor
4. newCachedThreadPool - SynchronousQueue, dynamically scale the threads to handle the amount of tasks, threads are idle for 60 second, they will be scaled down

### Rejection policy

1. AbortPolicy - This is the default policy. It causes the executor to throw a RejectedExecutionException.
2. CallerRunsPolicy - the producer thread will be employed to run the task it just submitted. This is effective back pressure.
3. DiscardOldestPolicy - accept the task and throw away the oldest task in the BlockingQueue
4. DiscardPolicy - accept the task but silently throw it away
5. Custom Policy - We can implement the RejectedExecutionHandler interface and provide our own logic to handle

### Mutex vs Semaphore

1. Mutex (or Mutual Exclusion Semaphores) is a locking mechanism used to synchronize access to a resource. Only one task can acquire the mutex. It means there will be ownership associated with mutex, and only the owner can release the lock (mutex).
2. Semaphore (or Binary Semaphore) is signaling mechanism (“I am done, you can carry on” kind of signal). A binary semaphore is NOT protecting a resource from access. Semaphores are more suitable for some synchronization problems like producer-consumer.

Short version:

1. Mutex can be released only by the thread that had acquired it.
2. Binary Semaphore can be signaled by any thread (or process).

### Read vs Write lock

Read Lock – if no thread acquired the write lock or requested for it then multiple threads can acquire the read lock
Write Lock – if no threads are reading or writing then only one thread can acquire the write lock

###  Concurrency

1. CyclicBarrier - CyclicBarriers are used in programs in which we have a fixed number of threads that must wait for each other to reach a common point before continuing execution.
2. Phaser
3. CountDownLatch
4. Exchanger - share objects between two threads of type T
5. Semaphore
6. SynchronousQueue

### More Questions

1. ShutdownNow vs Shutdown
2. Dynamic Striping - Striped64 class
3. Deadlock vs Livelock
4. Lock vs Synchronized Block
5. ReentrantReadWriteLock.ReadLock vs ReentrantReadWriteLock.WriteLock
6. Stamped lock - optimistic locking
7. DelayQueue

## SQL

Start a Postgres DB

```bash
docker run -p 5432:5432 --name pg-container -e POSTGRES_PASSWORD=password -d postgres:9.6.10
docker ps
docker exec -it pg-container psql -U postgres -W postgres
CREATE USER test WITH PASSWORD 'test@123';
CREATE DATABASE "test-db" WITH OWNER "test" ENCODING UTF8 TEMPLATE template0;
grant all PRIVILEGES ON DATABASE "test-db" to test;
\c test-db

docker stop pg-container
docker start pg-container
```

Create the tables & Seed the test data

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main/src/test/java/com/demo/sql/setup/setup.sql" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main/src/test/java/com/demo/sql/queries/employee-queries.sql" >}}

## Youtube Channels

[NeetCode](https://www.youtube.com/c/NeetCode)

[Tushar Roy - Coding Made Simple](https://www.youtube.com/channel/UCZLJf_R2sWyUtXSKiKlyvAw)

## References

[https://www.youtube.com/c/NeetCode](https://www.youtube.com/c/NeetCode)

[https://medium.com/interviewnoodle/grokking-leetcode-a-smarter-way-to-prepare-for-coding-interviews-e86d5c9fe4e1](https://medium.com/interviewnoodle/grokking-leetcode-a-smarter-way-to-prepare-for-coding-interviews-e86d5c9fe4e1)

[https://designgurus.org/course/grokking-the-coding-interview](https://designgurus.org/course/grokking-the-coding-interview)

[https://algs4.cs.princeton.edu/cheatsheet/](https://algs4.cs.princeton.edu/cheatsheet/)

[https://www.bigocheatsheet.com/](https://www.bigocheatsheet.com/)

[https://seanprashad.com/leetcode-patterns/](https://www.bigocheatsheet.com/)

[https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU](https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU)

[https://walkccc.me/LeetCode/preface/](https://walkccc.me/LeetCode/preface/)

[https://neetcode.io/](https://neetcode.io/)
