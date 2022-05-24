---
title: 每日一题之两数之和
description: 两数之和的求解
date: 2021-05-30
slug: 算法-数组
# image: helena-hertz-wWZzXlDpMog-unsplash.jpg
categories:
    - 算法
---

**题目**
给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

**例子**

```code
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1]
```

**题目大意**
在数组中找到2个数之和等于给定值的数字，结果返回两个数字在数组中的下标。

**解题思路**
这道题最优的做法时间复杂度是O(n)
顺序扫描数组，对于每一个元素，在map中能找到组合给定值的另一半数字，就直接返回2个数字下标即可。如果找不到就把这个数字存入map中，等到扫到"另一半"数字的时候，再取出返回结果。

**代码**
Go实现

```go
func twoSum(nums []int, target int) []int {
 m := make(map[int]int)
 for i := 0; i < len(nums); i++ {
  anthor := target - nums[i]
  if _, ok := m[anthor]; ok {
   return []int{m[anthor], i}
  }
  m[nums[i]] = i
 }
 return nil
}
```

javascript实现

```js
var twoSum = function (nums, target) {
  let map = {}
  for(let i=0; i < nums.length; i++){
    let anthor = target - nums[i]
    if(map[anthor] != null) {
      return [map[anthor], i]
    }
    map[nums[i]] = i
  }
}
```
