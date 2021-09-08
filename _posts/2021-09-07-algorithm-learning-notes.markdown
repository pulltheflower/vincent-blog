---
layout: post
title:  "算法学习笔记"
date:   2021-09-07 17:02:31 +0800
categories: jekyll update
---

#### 两数之和

1. 给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 和为目标值 `target`  的那 两个 整数，并返回它们的数组下标。

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

#### 两数相加

2. 给你两个 `非空` 的链表，表示两个非负的整数。它们每位数字都是按照 `逆序` 的方式存储的，并且每个节点只能存储 `一位` 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

 

示例 1：

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

示例 2：

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

示例 3：

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]
```

提示：

* 每个链表中的节点数在范围 [1, 100] 内
* 0 <= Node.val <= 9
* 题目数据保证列表表示的数字不含前导零


答案

```go
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
    carry := 0
    var tail *ListNode
    var head *ListNode
    for l1 != nil || l2 != nil {
        n1, n2 := 0, 0
        if l1 != nil {
            n1 = l1.Val
            l1 = l1.Next
        }
        if l2 != nil {
            n2 = l2.Val
            l2 = l2.Next
        }

        sum := n1 + n2 + carry
        sum, carry = sum%10, sum/10

        if head == nil {
            head = &ListNode{Val: sum}
            tail = head 
        } else {
            tail.Next = &ListNode{Val: sum}
            tail = tail.Next
        }
    }
    if (carry > 0){
        tail.Next = &ListNode{Val: carry}
    }
    return head
    
}
```

#### 无重复字符的最长子串

3. 给定一个字符串 `s` ，请你找出其中不含有重复字符的 `最长子串` 的长度。

 

示例 1:

```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```
示例 2:

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```
示例 3:

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```
示例 4:
```
输入: s = ""
输出: 0
```

提示：

* `0 <= s.length <= 5 * 104`
* `s 由英文字母、数字、符号和空格组成`

我的答案

```go
func lengthOfLongestSubstring(s string) int {
    // 指定窗口结束标志为-1，表示还没有开始移动，在字符串数组最左侧
    // 指定最长字串长度为0
    end, wordsLength := -1, 0
    // 使用words map记录滑窗过程中每个字符的出现次数
    words := map[byte]int{}

    for i := 0; i < len(s); i ++ {
        // 如果左边界超过0，则删除对左边字符数量的统计
        if i != 0 {
            delete(words, s[i - 1])
        }

        for end + 1 < len(s) && words[s[end + 1]] == 0{
            words[s[end + 1]] ++
            end ++
        }

        wordsLength = max(wordsLength, end + 1 - i)
    }
    return wordsLength
}


func max(x, y int) int {
    if x < y {
        return y
    }
    return x
}
```

#### 最长回文子串

4. 给你一个字符串 `s`，找到 `s` 中最长的回文子串。

 

示例 1：

```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```
示例 2：

```
输入：s = "cbbd"
输出："bb"
```
示例 3：

```
输入：s = "a"
输出："a"
```
示例 4：

```
输入：s = "ac"
输出："a"
```

提示：

* `1 <= s.length <= 1000`
* `s` 仅由数字和英文字母（大写和/或小写）组成

答案

```go
func longestPalindrome(s string) string {
    if s == "" {
        return ""
    }
    start, end := 0, 0
    for i := 0; i < len(s); i ++ {
        left1, right1 := findPlalindrome(s, i, i)
        left2, right2 := findPlalindrome(s, i, i + 1)

        if right1 - left1 > end - start {
            start, end = left1, right1
        }
        if right2 - left2 > end - start {
            start, end = left2, right2
        }
    }
    return s[start:end+1]


}

func findPlalindrome(s string, left, right int) (int, int) {
    for ; left >= 0 && right < len(s) && s[left] == s[right]; left, right = left - 1, right + 1 {}
    return left + 1, right - 1
}
```