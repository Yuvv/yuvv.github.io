---
layout: post
title: "LeetCode-2799-求完全子数组的数量"
date: 2023-09-03 22:09:01+0800
math: true
categories: [LeetCode]
tags:
  - Array
  - Hash Table
  - Sliding Window
---

# 题目信息

**原题链接**：[https://leetcode.com/problems/count-complete-subarrays-in-an-array/](https://leetcode.com/problems/count-complete-subarrays-in-an-array/)

## 原题描述

对于一个数组 `nums`，我们定义它的**完全子数组** `sub_nums`，`sub_nums` 有如下属性：

1. `sub_nums` 是 `nums` 的子数组；（即 `sub_nums == nums[i:j]` （其中，`0 <= i <= j < nums.length`）
2. `sub_nums` 中的所有元素去重后的结果与 `nums` 去重后的结果相同；（即 `DISTINCT(nums) == DISTINCT(sub_nums)` ）

求 `nums` 的**完全子数组**的数量。

## 输入输出示例

- *Example 1*
```
Input: nums = [1,3,1,2,2]
Output: 4
```

- *Example 2*
```
Input: nums = [5,5,5,5]
Output: 10
```

## 条件约束

- $ 1 \leqslant nums.length \leqslant 1000 $
- $ 1 \leqslant nums[i] \leqslant 2000 $


# 题解

## 分析

本题的目的是要找符合特定条件的子数组，那么首先我们需要找子数组。找子数组有一个简单有效的方法是：以每一个数为开始位置，然后往后迭代（包括自身），对于其后的每一个位置，都可以生成一个子数组。对于本题，再额外判断一下子数组是否满足 ***条件2*** 即可得到答案。该方式的为代码如下：
```python
for i in range(N):
    for j in range(i, N):
        check(nums[i:j+1])
```
但是如果用这种方式来生成子数组，然后逐个判断是否符合条件的话，时间复杂度为 $ O(n^2) $ （不考虑 `check` 方法内部复杂度，优化一下可以达到 $ O(1) $），这很容易就会超时。

让我们再回到题目的 ***条件2*** 上，要使得子数组的元素去重后的结果跟原始数组去重后的结果相同，那是不是意味着我们不需要找出所有子数组，只需要找出每一种场景下的最小的符合条件的子数组，就能得到该场景下的所有子数组，因为找到最小子数组后，后面再添加任何元素，它也一定是满足条件的子数组。那么，我们可以将上述伪代码简化为如下形式：
```python
for i in range(N):
    j = findMin(nums)  # 找到以 i 开始的子数组中满足 条件2 的最小的 j
    subNumsCount = N - j  # 再添加 j 后面的元素都一定满足 条件2，所以我们直接计算得到最终结果
```
这样的话，时间复杂度变为 $ O(n) $ （暂不考虑 `findMin` 方法内部复杂度）。

接下来，我们需要用最优化的方式，实现 `findMin` 方法。
因为 ***条件2*** 本质上是使得两个集合相等，所以我们考虑使用使用集合来记录迭代过程中的元素。进行集合比较时，我们也不需要真的逐个元素去判断，因为原始数组的集合 `SET(nums)` 一定是最大的集合，所以当子数组的集合 `SET(sub_nums)` 与 `SET(nums)` 大小相等时，他们的元素也一定相等。因此得到 `findMin` 的伪代码如下：
```python
def findMin(nums, N, N_SET_LEN):
    SET = set()
    for j in range(N):
        SET.add(nums[j])
        if len(SET) == N_SET_LEN:
          return j
```

现在只要我们将上述流程结合起来就可以解本题了。但是，由于 `findMin` 又进行了一次遍历（虽然不是完全遍历），最坏的情况下也还是会退化到 $ O(n^2) $ 的。还需要进一步优化。
我们再回到 `findMin` 函数，不难分析得到，对于 `i` 和 `i+1` 位置，执行 `findMin` 函数的时候，其实大部分流程都是重复的，`i+1` 相较于 `i` 只是少了元素 `nums[i]`，然后再加上 `i` 所对应的 `j` 位置后面的若干个元素（这里也有可能不需要加，比如 `nums[i] == nums[i+1]` 的场景）。于是乎，这个 `findMin` 函数就可以转变成一个 **滑动窗口**，我们保证这个 **滑动窗口** 始终是一个满足条件的最小子数组，即可得到最终解。


## 实现

实际实现时，我们可以先通过一个集合，获得原始数组 `nums` 去重后的集合大小。对于 **滑动窗口** 则可以用一个 `map` 来记录，因为滑动的过程中需要删除元素，我们需要对元素进行计数，看是剔除元素还是将计数减 1。

最终实现的 Java 代码如下：

```java
public int countCompleteSubarrays(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        set.add(num);
    }
    // sliding-window
    int count = 0;
    int j = 0;
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        map.put(nums[i], map.getOrDefault(nums[i], 0) + 1);
        while (map.size() == set.size()) {  // 需注意，这里用的是 while
            count += nums.length - i;
            Integer val = map.get(nums[j]);
            if (val == 1) {
                map.remove(nums[j]);
            } else {
                map.put(nums[j], val - 1);
            }
            j++;
        }
    }
    return count;
}
```


- **时间复杂度**：$ O(n) $
- **空间复杂度**：$ O(n) $