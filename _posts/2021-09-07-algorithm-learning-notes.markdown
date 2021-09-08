---
layout: post
title:  "算法学习笔记"
date:   2021-09-07 17:02:31 +0800
categories: jekyll update
---

#### 两数之和

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 和为目标值 `target`  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

示例 1：

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

示例 2：

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

示例 3：

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

提示：

* 2 <= nums.length <= 104
* -109 <= nums[i] <= 109
* -109 <= target <= 109
* 只会存在一个有效答案

进阶：你可以想出一个时间复杂度小于 O(n2) 的算法吗？


##### 我的答案


```go
func twoSum(nums []int, target int) []int {
    for i := 0; i < len(nums); i ++ {
        for j := i + 1; j < len(nums); j ++ {
            if nums[i] + nums[j] == target {
                return []int{i, j}
                break;
            }
        }
    }
    return nil
}
```

##### 使用hashTable

使用哈希表，可以将寻找 `target - x` 的时间复杂度降低到从 `O(N)` 降低到 `O(1)`。

这样我们创建一个哈希表，对于每一个 `x`，我们首先查询哈希表中是否存在 `target - x`，然后将 `x` 插入到哈希表中，即可保证不会让 `x` 和自己匹配。

```go
func twoSum(nums []int, target int) []int {
    hashTable := map[int]int{}
    for i, x := range nums {
        if p, ok := hashTable[target-x]; ok {
            return []int{p, i}
        }
        hashTable[x] = i
    }
    return nil
}
```