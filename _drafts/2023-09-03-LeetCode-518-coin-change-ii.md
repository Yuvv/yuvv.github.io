---
layout: post
title: "LeetCode-518-硬币找零2"
date: 2023-09-03 21:00:23+0800
katex: true
category: LeetCode
tags:
  - Array
  - Dynamic Programming
---

# 题目信息

**原题链接**：[https://leetcode.com/problems/coin-change-ii/](https://leetcode.com/problems/coin-change-ii/)

## 原题描述

TODO:

## 输入输出示例

- *Example 1*
```
Input: amount = 5, coins = [1,2,5]
Output: 4
```

- *Example 2*
```
Input: amount = 3, coins = [2]
Output: 0
```

## 条件约束

- &nbsp; $$ 1 \leqslant coins.length \leqslant 300 $$
- &nbsp; $$ 1 \leqslant coins[i] \leqslant 5000 $$
- `coins` 中元素不重复
- &nbsp; $$ 0 \leqslant amount \leqslant 5000 $$


# 题解

## 分析

TODO

## 实现

TODO

```java
class Solution {
    private Map<Integer, Map<Integer, Integer>> cacheMap;

    public int change(int amount, int[] coins) {
        if (amount == 0) {
            return 1;
        }
        this.cacheMap = new HashMap<>();  // init every time
        Arrays.sort(coins);
        return change(amount, coins, 0);
    }

    public int change(int amount, int[] coins, int fromIdx) {
        if (fromIdx >= coins.length || coins[fromIdx] > amount) {
            return 0;
        }
        Map<Integer, Integer> map = cacheMap.computeIfAbsent(fromIdx, k -> new HashMap<>());
        if (map.containsKey(amount)) {
            return map.get(amount);
        }
        int count = 0;
        int i = 0;
        int base = 0;
        while (base <= amount) {
            if (base == amount) {
                count += 1;
            } else {
                count += change(amount-base, coins, fromIdx+1);
            }
            base += coins[fromIdx];
        }

        map.put(amount, count);
        return count;
    }
}
```

- **时间复杂度**：$$ O(n) $$
- **空间复杂度**：$$ O(n) $$