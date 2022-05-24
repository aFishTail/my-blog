---
title: "每日一题之无重复字符的最长子串"
date: 2021-06-01T09:35:41+08:00
image: helena-hertz-wWZzXlDpMog-unsplash.jpg
draft: true
categories:
    - 算法
---
**题目**
给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

**例子**

示例1
```code
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```
示例2
```code
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```
示例3
```code
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```
示例4
```code
输入: s = ""
输出: 0
```

**题目大意**
在一个字符串重寻找没有重复字母的最长子串。

**解题思路**

"滑动窗口"思想

- 我们使用两个指针表示字符串中的某个子串（或窗口）的左右边界，其中左指针代表着上文中「枚举子串的起始位置」，而右指针即为上文中的 *r_k*；

- 在每一步的操作中，我们会将左指针向右移动一格，表示 **我们开始枚举下一个字符作为起始位置**，然后我们可以不断地向右移动右指针，但需要保证这两个指针对应的子串中没有重复的字符。在移动结束后，这个子串就对应着 **以左指针开始的，不包含重复字符的最长子串**。我们记录下这个子串的长度；

- 在枚举结束后，我们找到的最长的子串的长度即为答案。

**判断重复字符**

在上面的流程中，我们还需要使用一种数据结构来判断 **是否有重复的字符**，常用的数据结构为哈希集合（即 `Go`中的`map`， `JavaScript` 中的 `Set`，`Java` 中的 `HashSet`，`Python` 中的 `set`）。在左指针向右移动的时候，我们从哈希集合中移除一个字符，在右指针向右移动的时候，我们往哈希集合中添加一个字符。

至此，我们就完美解决了本题。

**代码**

Go实现

```go

func lengthOfLongestSubstring(s string) int {
    // 哈希集合，记录每个字符是否出现过
	m := map[byte]int{}
	n := len(s)
	ans := 0
	rk := 0
	for i := 0; i < n; i++ {
		if i > 0 {
			// 左指针向右移动一格，删除一个字符
			delete(m, s[i-1])
		}
		// 不断移动右指针直到出现重复字符
		for rk < n && m[s[rk]] == 0 {
			m[s[rk]]++
			rk++
		}
		// 第 i 到 rk 个字符是一个极长的无重复字符子串
		ans = max(ans, rk-i)
	}
	return ans
}

func max(x, y int) int {
	if x < y {
		return y
	}
	return x
}
```

Javascript实现

```js
// 哈希集合，记录每个字符是否出现过
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function (s) {
  // 哈希集合，记录每个字符是否出现过
  let set = new Set()
  let rk = 0, // 右指针
    result = 0
  const len = s.length
  for (let i = 0; i < len; i++) {
    if (i > 0) {
      // 左指针向右移动一格，删除一个字符
      set.delete(s.charAt(i - 1))
    }
    // 不断移动右指针直到出现重复字符
    while (rk < len && !set.has(s.charAt(rk))) {
      set.add(s.charAt(rk))
      rk++
    }
    // 第 i 到 rk 个字符是一个极长的无重复字符子串
    result = Math.max(result, rk - i)
  }
  return result
}
```

