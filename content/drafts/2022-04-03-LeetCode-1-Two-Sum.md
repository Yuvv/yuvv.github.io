# LeetCode-01.Two Sum - 两数之和

Created Time: April 3, 2022 11:35 PM
Tags: Array, Hash Table
Updated Time: April 4, 2022 12:10 AM

# Description

**原题链接**：[https://leetcode.com/problems/two-sum/](https://leetcode.com/problems/two-sum/)

## **题干**

给定一个整形数组 `nums` 和一个目标数 `target`，在数组中的找出两个数 `a` 和 `b`，使得这两个数的和为目标数，即 `a + b = target` ，返回 `a` 和 `b` 的下标。

数组中有且只有一个解，同一个元素可以多次使用，返回的下标顺序不限。

## **示例**

### *Example 1*

```
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
```

### *Example 2*

```
Input: nums = [3,2,4], target = 6
Output: [1,2]
```

### *Example 3*

```
Input: nums = [3,3], target = 6
Output: [0,1]
```

## **条件约束**

- $2 \leqslant nums.length \leqslant 10^4$
- $-10^9 \leqslant nums[i] \leqslant 10^9$
- $-10^9 \leqslant target \leqslant 10^9$
- **Only one valid answer exists.**

# Solution

## 1. Brute Force

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        if (nums.length < 2) {
            return new int[] {-1, -1};
        }
        for (int i=0; i<nums.length; i++) {
            for (int j=i+1; j<nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[] {i, j};
                }
            }
        }
        return new int[] {-1, -1};
    }
}
```

**时间复杂度**：$O(n^2)$

**空间复杂度**：$O(1)$ 

## 2. Hash Table

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        if (nums.length < 2) {
            return new int[] {-1, -1};
        }
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int a = target - nums[i];
            if (map.containsKey(a)) {
                return new int[] {map.get(a), i};
            }
            map.put(nums[i], i);
        }
        return new int[] {-1, -1};
    }
}
```

**时间复杂度**：$O(n)$

**空间复杂度**：$O(n)$

