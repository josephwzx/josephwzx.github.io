---
title: leetcode notes - greedy
date: 2024-03-09 23:15:14
tags: leetcode
cover: https://picbed.josephweng.com/img/cover/code_thinking.svg
---
# leetcode notes - greedy

Greedy algorithms are generally divided into the following four steps:

Decompose the problem into several subproblems.
Find a suitable greedy strategy.
Solve for the optimal solution of each subproblem.
Stack the local optimal solutions into a global optimal solution.
These four steps are actually too theoretical. When we work on greedy-type problems, it's hard to think according to these four steps; it's somewhat "useless" in practice.

When solving problems, as long as you understand what the local optimum is and how to derive the global optimum from it, that's actually enough.

## LeetCode 455 - Assign Cookies

### Problem Description

Assume you are an awesome parent and want to give your children some cookies. However, you should give each child at most one cookie.

Each child `i` has a greed factor `g[i]`, which is the minimum size of a cookie that the child will be content with; and each cookie `j` has a size `s[j]`. If `s[j] >= g[i]`, we can assign the cookie `j` to the child `i`, and the child `i` will be content. Your goal is to maximize the number of your content children and output the maximum number.

### Examples

#### Example 1:

- **Input:** `g = [1,2,3], s = [1,1]`
- **Output:** `1`
- **Explanation:** You have 3 children and 2 cookies. The greed factors of 3 children are 1, 2, 3. And even though you have 2 cookies, since their size is both 1, you could only make the child whose greed factor is 1 content. You need to output 1.

#### Example 2:

- **Input:** `g = [1,2], s = [1,2,3]`
- **Output:** `2`
- **Explanation:** You have 2 children and 3 cookies. The greed factors of 2 children are 1, 2. You have 3 cookies and their sizes are big enough to gratify all of the children, You need to output 2.

### Constraints

- `1 <= g.length <= 3 * 10^4`
- `0 <= s.length <= 3 * 10^4`
- `1 <= g[i], s[j] <= 2^31 - 1`

### Solution

To satisfy more children, we should avoid wasting cookie sizes.

A larger-sized cookie can satisfy both a child with a larger appetite and one with a smaller appetite; therefore, we should prioritize satisfying those with larger appetites.

The local optimum here is to feed the larger cookies to children with larger appetites, making full use of the cookie size to satisfy one, while the global optimum is to satisfy as many children as possible.

We can try using a greedy strategy, first sorting both the cookie array and the children array.

Then, iterate from the back of the children array, using larger cookies to preferably satisfy those with bigger appetites, and count the number of satisfied children.

##### Data Structures Used

- **Priority Queues:** Two priority queues are used, `pqg` for the greed factors of children (`g[]`) and `pqs` for the sizes of the cookies (`s[]`). Priority queues are used to easily sort and access the smallest elements, aligning with the greedy approach of matching the smallest or least greedy child with the smallest size cookie that can satisfy them.

##### Time Complexity

- **O(n log n + m log m + n + m):** The time complexity can be broken down into several parts:
  1. **O(n log n):** Sorting the greed factors of `n` children using a priority queue, where `n` is the length of array `g[]`.
  2. **O(m log m):** Sorting the sizes of `m` cookies using a priority queue, where `m` is the length of array `s[]`.
  3. **O(n + m):** In the worst case, iterating through all the children and cookies once. Each poll operation takes O(log n) or O(log m) time, but since each child and cookie is only polled once, the total time for polling is linear to the size of the input arrays.

The time complexity is dominated by the sorting (heapifying) of the children and cookies in the priority queues.

##### Space Complexity

- **O(n + m):** The space complexity is due to storing all `n` children's greed factors and all `m` cookies' sizes in two separate priority queues. Therefore, the space complexity is linear with respect to the size of the input arrays.


```java

class Solution {
    public int findContentChildren(int[] g, int[] s) {
        PriorityQueue<Integer> pqg = new PriorityQueue<>();
        PriorityQueue<Integer> pqs = new PriorityQueue<>();
        for (int i = 0; i < g.length; i++) {
            pqg.offer(g[i]);
        }
        for (int i = 0; i < s.length; i++) {
            pqs.offer(s[i]);
        }
        int cnt = 0;
        while (!pqg.isEmpty() && !pqs.isEmpty()) {
            if (pqg.peek() <= pqs.peek()) {
                pqg.poll();
                pqs.poll();
                cnt++;
            }else {
                pqs.poll();
            }
        }
        return cnt;
    }
}
```

## LeetCode 376 - Wiggle Subsequence

### Problem Description

A wiggle sequence is a sequence where the differences between successive numbers strictly alternate between positive and negative. The first difference (if one exists) may be either positive or negative. A sequence with one element and a sequence with two non-equal elements are trivially wiggle sequences.

For example, `[1, 7, 4, 9, 2, 5]` is a wiggle sequence because the differences `(6, -3, 5, -7, 3)` alternate between positive and negative.
In contrast, `[1, 4, 7, 2, 5]` and `[1, 7, 4, 5, 5]` are not wiggle sequences. The first is not because its first two differences are positive, and the second is not because its last difference is zero.
A subsequence is obtained by deleting some elements (possibly zero) from the original sequence, leaving the remaining elements in their original order.

Given an integer array `nums`, return the length of the longest wiggle subsequence of `nums`.

#### Examples

- **Example 1:**
  - **Input:** `nums = [1,7,4,9,2,5]`
  - **Output:** `6`
  - **Explanation:** The entire sequence is a wiggle sequence with differences `(6, -3, 5, -7, 3)`.

- **Example 2:**
  - **Input:** `nums = [1,17,5,10,13,15,10,5,16,8]`
  - **Output:** `7`
  - **Explanation:** There are several subsequences that achieve this length. One is `[1, 17, 10, 13, 10, 16, 8]` with differences `(16, -7, 3, -3, 6, -8)`.

- **Example 3:**
  - **Input:** `nums = [1,2,3,4,5,6,7,8,9]`
  - **Output:** `2`

#### Constraints

- `1 <= nums.length <= 1000`
- `0 <= nums[i] <= 1000`

### Solution

The local optimum involves deleting nodes on a monotone slope (excluding the endpoints of the slope), so this slope can have two local peaks.

The global optimum is that the entire sequence has the most local peaks, thereby achieving the longest wiggle sequence.

Local optimum leads to global optimum, and no counterexample can be presented, so let's try greed!

For simplicity, the peaks mentioned below refer to local peaks.

In practice, even the operation of deleting is not necessary because the problem requires the length of the longest wiggle subsequence, so it's enough to just count the number of peaks in the array (equivalent to deleting the nodes on a monotone slope and then counting the length).

This is where greed comes into play, keeping peaks as much as possible and deleting nodes on a monotone slope.

When calculating whether there is a peak, by knowing the traversal index `i`, calculate `prediff` (`nums[i] - nums[i-1]`) and `curdiff` (`nums[i+1] - nums[i]`), if `prediff < 0 && curdiff > 0` or `prediff > 0 && curdiff < 0`, there is a fluctuation that needs to be counted.

We consider three cases for this problem:

1. The slope includes flat sections.
2. The beginning and end of the array.
3. The monotone slope includes flat sections.

##### Data Structure Used

- **Arrays:** The primary data structure used is the input array itself. No additional data structures are needed for the implementation of the solution.

##### Time Complexity

- **O(n):** The solution iterates through the array exactly once, where `n` is the length of the input array. Each iteration involves constant-time operations, leading to a linear time complexity.

##### Space Complexity

- **O(1):** The solution uses a fixed amount of extra space (a few integer variables) regardless of the input size, resulting in constant space complexity.


#### Code

```java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        if (nums.length <= 1) {
            return nums.length;
        }

        int preCuff = 0;
        int curCuff = 0;
        int cnt = 1;
        for (int i = 0; i < nums.length - 1; i++) {
            curCuff = nums[i+1] - nums[i];
            if ((preCuff <= 0 && curCuff > 0) || (preCuff >= 0 && curCuff < 0)) {
                cnt++;
                preCuff = curCuff;
            }
        }

        return cnt;
    }
}
```
## LeetCode 1005 - Maximize Sum Of Array After K Negations

### Problem Description

Given an integer array `nums` and an integer `k`, modify the array in the following way:

- choose an index `i` and replace `nums[i]` with `-nums[i]`.
- You should apply this process exactly `k` times. You may choose the same index `i` multiple times.

Return the largest possible sum of the array after modifying it in this way.

### Examples

- **Example 1:**
  - **Input:** `nums = [4,2,3], k = 1`
  - **Output:** `5`
  - **Explanation:** Choose index 1 and nums becomes `[4,-2,3]`.

- **Example 2:**
  - **Input:** `nums = [3,-1,0,2], k = 3`
  - **Output:** `6`
  - **Explanation:** Choose indices `(1, 2, 2)` and nums becomes `[3,1,0,2]`.

- **Example 3:**
  - **Input:** `nums = [2,-3,-1,5,-4], k = 2`
  - **Output:** `13`
  - **Explanation:** Choose indices `(1, 4)` and nums becomes `[2,3,-1,5,4]`.

### Constraints

- `1 <= nums.length <= 104`
- `-100 <= nums[i] <= 100`
- `1 <= k <= 104`

### Solution

The greedy approach involves locally optimizing to convert the largest negative numbers to positive, thereby maximizing the current value, and globally optimizing to maximize the sum of the entire array.

After converting all negatives to positives, if `k` is still greater than 0, we're left with a sorted sequence of positive integers. The greedy choice then is to flip the smallest positive integer, maximizing the array sum.

#### Approach

1. **Sort** the array by absolute values in descending order.
2. **Flip negatives to positives** as long as `k > 0`.
3. If `k` is still greater than 0, **flip the smallest element** repeatedly until `k` is used up.
4. **Sum** the array.

##### Data Structure Used

- **Arrays:** The solution primarily involves manipulating the input array, sorting it, and altering elements based on conditions.

##### Time Complexity

- **O(nlogn):** The dominant operation in the algorithm is sorting the array, which has a time complexity of O(n log n).

##### Space Complexity

- **O(1):** The space complexity is constant since the solution manipulates the array in place and uses a fixed number of variables.


#### Code

```java
class Solution {
    public int largestSumAfterKNegations(int[] nums, int k) {
        Arrays.sort(nums);
        int sum = 0;
        if (nums[0] >= 0 && k % 2 == 1) {
            nums[0] = -nums[0];
        } else if (nums[0] < 0) {
            int i = 0;
            while (k > 0 && i < nums.length && nums[i] < 0) {
                nums[i] = -nums[i++];
                k--;
            }
            if (k % 2 == 1) {
                int tmp = i < nums.length && nums[i] < nums[i - 1] ? i : i -1;
                nums[tmp] = -nums[tmp];
            }
        }
        for (int val : nums) {
            sum += val;
        }
        return sum;
    }
}
```

