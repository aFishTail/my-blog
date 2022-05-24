---
title: 每日一题之两数相加
description: 两数相加的求解
date: 2021-05-31
slug: 算法-数组
# image: helena-hertz-wWZzXlDpMog-unsplash.jpg
categories:
    - 算法
---

**题目**
给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**例子**

```code
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

**题目大意**
2 个逆序的链表，要求从低位开始相加，得出结果也逆序输出，返回值是逆序结果链表的头结点。

**解题思路**
需要注意的是各种进位问题。可以声明`carry`变量标明进位。

注意：carry的计算方式应该是 `(sum+carry)/10`，即整除10得出的结果

为了处理方法统一，可以先建立一个虚拟头结点，这个虚拟头结点的 Next 指向真正的 head，这样 head 不需要单独处理，直接 while 循环即可。另外判断循环终止的条件不用是 p.Next ！= nil，这样最后一位还需要额外计算，循环终止条件应该是 p != nil。

极端情况，例如

**代码**
Go实现

```go
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
 dummy := &ListNode{Val: 0}
 sum, carry, current := 0, 0, dummy
 for l1 != nil || l2 != nil || carry != 0 {
  sum = 0
  if l1 != nil {
   sum += l1.Val
   l1 = l1.Next
  }
  if l2 == nil {
   sum += l2.Val
   l2 = l2.Next
  }
  current.Next = &ListNode{Val: (sum + carry) % 10}
  current = current.Next
  carry = (sum + carry) / 10
 }
 return dummy.Next
}
```

javascript实现

```js
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var addTwoNumbers = function (l1, l2) {
  let dummy = new ListNode(0)
  let sum = 0
  let carry = 0
  let current = dummy
  while (l1 || l2 || carry != 0) {
    sum = 0
    if (l1) {
      sum += l1.val
      l1 = l1.next
    }
    if (l2) {
      sum += l2.val
      l2 = l2.next
    }
    current.next = new ListNode((sum + carry) % 10)
    current = current.next
    carry = Math.floor((sum + carry) / 10)
  }
  return dummy.next
}
```
