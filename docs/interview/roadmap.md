# DSA

## 1. Arrays & Strings (Foundations)
Goal: comfort with indices, in-place operations, basic string manipulation.

- Easy: Two Sum
- Easy: Best Time to Buy and Sell Stock
- Easy: Contains Duplicate
- Easy: Valid Anagram
- Easy: Reverse String
- Easy: Majority Element
- Medium: Product of Array Except Self
- Medium: Rotate Array
- Medium: Set Matrix Zeroes
- Medium: Group Anagrams
- Medium: Longest Common Prefix

## 2. Two Pointers
Goal: recognize when a linear scan from both ends beats brute force O(n²).

- Easy: Valid Palindrome
- Easy: Merge Sorted Array
- Medium: Two Sum II (sorted array)
- Medium: 3Sum
- Medium: Container With Most Water
- Medium: Sort Colors (Dutch National Flag)
- Hard: Trapping Rain Water

## 3. Sliding Window
Goal: subarray/substring problems with a "window" that grows/shrinks.

- Easy: Maximum Subarray (Kadane's)
- Medium: Longest Substring Without Repeating Characters
- Medium: Longest Repeating Character Replacement
- Medium: Minimum Size Subarray Sum
- Medium: Permutation in String
- Hard: Sliding Window Maximum
- Hard: Minimum Window Substring

## 4. Hashing (Hash Maps / Sets)
Goal: trade space for time; frequency counting, lookups.

- Easy: Two Sum (revisit — solve with hashmap)
- Easy: Ransom Note
- Medium: Group Anagrams (revisit with hashmap approach)
- Medium: Top K Frequent Elements
- Medium: Subarray Sum Equals K
- Medium: Longest Consecutive Sequence

## 5. Stacks & Queues
Goal: LIFO/FIFO patterns, monotonic stacks.

- Easy: Valid Parentheses
- Easy: Implement Queue using Stacks
- Medium: Min Stack
- Medium: Evaluate Reverse Polish Notation
- Medium: Daily Temperatures (monotonic stack)
- Medium: Next Greater Element I & II
- Hard: Largest Rectangle in Histogram

## 6. Linked Lists
Goal: pointer manipulation, fast/slow pointer technique.

- Easy: Reverse Linked List
- Easy: Merge Two Sorted Lists
- Easy: Linked List Cycle
- Medium: Remove Nth Node From End of List
- Medium: Reorder List
- Medium: Add Two Numbers
- Medium: Copy List with Random Pointer
- Hard: Merge k Sorted Lists
- Hard: Reverse Nodes in k-Group

## 7. Recursion & Backtracking
Goal: think in terms of "choice, explore, un-choose."

- Easy: Fibonacci Number (recursive + memoized)
- Medium: Subsets
- Medium: Subsets II (with duplicates)
- Medium: Permutations
- Medium: Combination Sum
- Medium: Word Search
- Medium: Palindrome Partitioning
- Hard: N-Queens
- Hard: Sudoku Solver

## 8. Binary Search
Goal: recognize monotonic search spaces, not just sorted arrays.

- Easy: Binary Search
- Easy: Search Insert Position
- Medium: Search in Rotated Sorted Array
- Medium: Find Minimum in Rotated Sorted Array
- Medium: Find First and Last Position of Element in Sorted Array
- Medium: Koko Eating Bananas (binary search on answer)
- Hard: Median of Two Sorted Arrays
- Hard: Split Array Largest Sum (binary search on answer)

## 9. Trees (Binary Trees)
Goal: DFS/BFS traversals, recursion on tree structure.

- Easy: Maximum Depth of Binary Tree
- Easy: Invert Binary Tree
- Easy: Same Tree
- Easy: Diameter of Binary Tree
- Medium: Binary Tree Level Order Traversal
- Medium: Validate Binary Search Tree
- Medium: Lowest Common Ancestor of a Binary Tree
- Medium: Kth Smallest Element in a BST
- Medium: Construct Binary Tree from Preorder and Inorder Traversal
- Hard: Binary Tree Maximum Path Sum
- Hard: Serialize and Deserialize Binary Tree

## 10. Heaps / Priority Queues
Goal: "top-K" and streaming problems.

- Easy: Kth Largest Element in a Stream
- Medium: Kth Largest Element in an Array
- Medium: Top K Frequent Elements (revisit with heap)
- Medium: Task Scheduler
- Hard: Merge k Sorted Lists (revisit with heap)
- Hard: Find Median from Data Stream

## 11. Tries
Goal: prefix-based string storage/search.

- Medium: Implement Trie (Prefix Tree)
- Medium: Design Add and Search Words Data Structure
- Hard: Word Search II

## 12. Graphs (BFS/DFS)
Goal: represent graphs (adjacency list), traverse, detect cycles.

- Medium: Number of Islands
- Medium: Clone Graph
- Medium: Course Schedule (cycle detection / topological sort)
- Medium: Course Schedule II
- Medium: Pacific Atlantic Water Flow
- Medium: Rotting Oranges (multi-source BFS)
- Medium: Graph Valid Tree
- Hard: Word Ladder
- Hard: Alien Dictionary (topological sort)

## 13. Advanced Graphs
Goal: weighted graphs, shortest paths, union-find.

- Medium: Number of Connected Components (Union-Find)
- Medium: Redundant Connection (Union-Find)
- Medium: Network Delay Time (Dijkstra)
- Hard: Cheapest Flights Within K Stops (Bellman-Ford / modified Dijkstra)
- Hard: Swim in Rising Water (Dijkstra/binary search + BFS)
- Hard: Minimum Spanning Tree — Prim's/Kruskal's (conceptual, e.g. Min Cost to Connect All Points)

## 14. Intervals
Goal: sorting + greedy merging.

- Easy: Merge Intervals (basic version)
- Medium: Insert Interval
- Medium: Non-overlapping Intervals
- Medium: Meeting Rooms II
- Hard: Merge k Sorted Intervals-type problems (e.g. Employee Free Time)

## 15. Greedy
Goal: local optimal choice → global optimal, know when it's provably correct.

- Easy: Assign Cookies
- Medium: Jump Game
- Medium: Jump Game II
- Medium: Gas Station
- Medium: Partition Labels
- Hard: Candy

## 16. 1D Dynamic Programming
Goal: recognize overlapping subproblems, build bottom-up from recursion.

- Easy: Climbing Stairs
- Easy: House Robber
- Medium: House Robber II
- Medium: Longest Increasing Subsequence
- Medium: Coin Change
- Medium: Maximum Product Subarray
- Medium: Word Break
- Medium: Decode Ways
- Hard: Palindrome Partitioning II

## 17. 2D Dynamic Programming
Goal: DP over two dimensions — strings, grids.

- Medium: Unique Paths
- Medium: Longest Common Subsequence
- Medium: Longest Palindromic Substring
- Medium: Edit Distance
- Medium: Interleaving String
- Hard: Regular Expression Matching
- Hard: Distinct Subsequences
- Hard: Burst Balloons

## 18. Bit Manipulation
Goal: XOR tricks, bit masks — usually quick wins if practiced.

- Easy: Single Number
- Easy: Number of 1 Bits
- Easy: Counting Bits
- Medium: Sum of Two Integers (bit-level addition)
- Medium: Reverse Bits

## 19. Design / Low-Level Coding
Goal: combine data structures into a working system (very common at Amazon/Google).

- Medium: LRU Cache
- Medium: LFU Cache
- Medium: Design Twitter
- Medium: Insert Delete GetRandom O(1)
- Hard: Design Search Autocomplete System

---

## How to use this list
1. Go topic by topic, top to bottom — don't skip around.
2. Within a topic, do all Easy first, then Medium, then Hard. If Easy feels shaky, that
   topic isn't ready for Medium yet.
3. After solving, spend 2 minutes stating the pattern out loud (e.g. "sliding window with
   a hashmap to track char counts") — this is what actually transfers to new problems.
4. Re-attempt (don't just re-read) any problem you couldn't solve in ~25 minutes, a few
   days later, from scratch.
5. Once you finish topics 1–13, you already cover ~80% of what's asked in standard big
   tech loops. Topics 14–19 round out the rest, especially for Amazon/Google-style rounds.
