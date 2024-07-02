---
title: 前端面试题
---

## 写一个函数把字符串转为驼峰

```
const foo = 'get-element-by-id'

function toCamel(str) {
  const arr = str.split('-')
  for (let i = 1; i < arr.length; ++i) {
    arr[i] = arr[i][0].toUpperCase() + arr[i].slice(1)
  }
  return arr.join('')
}
toCamel(foo)
```

## 给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。

```
function findFirstUniqueChar(str) {
  const map = {}
  for (let char of s) {
    map[char] = (map[v] || 0) + 1
  }
  for (let i = 0; i < str.length; ++i) {
    if (map[str[i]] === 1) {
      return i
    }
  }
  return -1
}
```


## 有效的字母异位词

给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的字母异位词。

注意：若 s 和 t 中每个字符出现的次数都相同，则称 s 和 t 互为字母异位词。

```

function isMatch(s, t) {
  const sLen = s.length
  const tLen = t.length
  if (sLen !== tLen) {
    return false
  }
  const map = {}
  for (let i = 0; i < sLen; ++i) {
    const currentS = s[i]
    const currentT = t[i]
    map[currentS] ? map[currentS]++ : map[currentS] = 1
    map[currentT] ? map[currentT]-- : map[currentT] = -1
  }
  return Object.values(map).every(v => v === 0)
}

```


## 多数元素

给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

```

function matchElement(nums) {
  const map = {}
  const n = nums.length / 2
  for (let i = 0; i < nums.length; ++i) {
    map[nums[i]] = map[nums[i]] ? map[nums[i]] + 1 : 1
    if (map[nums[i]] > n) {
      return nums[i]
    }
  }
}

```

## 只出现一次的数字

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

说明：

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

```
function matchElement(arr) {
  const map = {}
  arr.forEach(d => {
    map[d] = map[d] ? map[d] + 1 : 1
  })
  for (let key in map) {
    if (map[key] == 1) {
      return key
    }
  }
}
```

## 给定两个数组，编写一个函数来计算它们的交集。

```

function interSection(nums1, nums2) {
  let map = {}
  let result = []
  nums1.forEach(d => {
    map[d] = true
  })
  nums2.forEach(d => {
    if (map[d]) {
      map[d] = false
      result.push(d)
    }
  })
  return result
}

```


## 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

思路：求出前两个公共前缀，跟后续求最长公共前缀

```
function longestCommonPrefix(strs) {
  if (strs.length === 0) return ''
  if (strs.length === 1) return strs[0]
  return strs.reduce(getSameStr)
}


function getSameStr(a, b) {
  let result = ''
  for (let j = 0; j < a.length; ++j) {
    if (a[j] == b[j]) {
      res += a[j]
    } else {
      return result
    }
  }
  return result
}

```


## 将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。


```
function ListNode(value, next) {
  this.value = value === undefined ? 0 : value
  this.next = next === undefined ? null : next
}

function mergeList(l1, l2) {
  let dummy = node = new ListNode()
  while (l1 && l2) {
    if (l1.value >= l2.value) {
      node.next = l2
      node = node.next
      l2 = l2.next
    } else {
      node.next = l1
      node = node.next
      l1 = l1.next
    }
    node.next = l1 || l2
    return dummy.next
  }
}
```


## strStr

给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回  -1 。

```
function strStr(haystack, needle) {
  if (needle === '') return 0
  for (let i = 0; i < haystack.length - needle.length; ++i) {
    if (haystack[i] === needle[0]) {
      if (haystack.substring(i, i + needle.length) === needle) {
        return i
      }
    }
  }
  return -1
}
```


##  股票买卖

给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。
你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。
返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

```
function maxProfit(prices) {
  var result = 0
  var min = prices[0]
  for (let i = 1; i < prices.length; ++i) {
    if (price[i] < min) {
      min = price[i]
    } else {
      result = Math.max(result, price[i] - min)
    }
  }
  return result
}
```

# 链表反转

```
function reverseList(head) {
  var pre = null
  var current = head

  while(current) {
    const tmp = current.next
    current.next = pre
    pre = current
    current = tmp
  }
  return pre
}
```

# 删除数组中的重复项

给你一个有序数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。

不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

```
function removeDuplicates(nums) {
  var i = 0
  for (var j = 1; j < nums.length; ++j) {
    if (nums[i] != nums[j]) {
      nums[i + 1] = nums[j]
      i ++
    }
  }
  return i + 1
}
```

# 合并两个有序数组

给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。
初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。你可以假设 nums1 的空间大小等于 m + n，这样它就有足够的空间保存来自 nums2 的元素。

```
function mergeArray(nums1, nums2) {
  var m = nums1.length
  var n = nums2.length
  var len = m + n
  m--
  n--
  while (m >= 0 && n >= 0) {
    if (nums1[m] > nums2[n]) {
      nums1[len] = nums1[m--]
    } else {
      nums1[len] = nums2[n--]
    }
    len --
  }
  if (m === -1) {
    return nums1.splice(0, len + 1, ...nums1.slice(0, n + 1))
  }
  return nums1
}
```

# 回文

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

说明：本题中，我们将空字符串定义为有效的回文串。

```
function isPalindrome(s) {
  s = s.replace(/[^\w]/, '').toLowerCase()
  let leftPointer = 0
  let rightPointer = s.length - 1
  while (rightPointer > leftPointer) {
    if (s[leftPointer++] === s[rightPointer--]) {
      continue
    } else {
      return false
    }
  }
  return true
}
```

# 回文链表

```

function isPalindrome(head) {
  if (head == null || head.hext == null) {
    return true
  }
  let fast = slow = head
  let prev = null
  while (fast && fast.next) {
    prev = slow
    slow = slow.next
    fast = fast.next.next
  }
  prev.next = null

  var head2 = null
  while (slow) {
    const tmp = slow.next
    slow.next = head2
    head2 = slow
    slow = tmp
  }

  while (head && head2) {
    if (head.val !== head2.val) {
      return false
    }
    head = head.next
    head2 = head2.next
  }
  return true
}

```

# 删除链表中的节点

```
function deleteNode(node) {
  node.val = node.next.val
  node.next = node.next.next
}
```

# 二叉树遍历

思路，使用stack保存节点

```
function traversal(root) {
  const stack = []
  while (root || stack.length) {
    while (root) {
      stack.push(root)
      root = root.left
    }
    root = stack.pop()
    root = root.right
  }
}


function preorderTraversal(root) {
  const res = []
  const stack = []
  while (root || stack.length) {
    while (root) {
      res.push(root.val)
      stack.push(root)
      root = root.left
    }
    root = stack.pop()
    root = root.right
  }
  return res
}


function inorderTraversal(root) {
  const res = []
  const stack = []
  while (root || stack.length) {
    while (root) {
      stack.push(root)
      root = root.left
    }

    root = stack.pop()
    res.push(root.val)
    root = root.right
  }
  return res
}

function postorderTraversal(root) {
  const res = []
  const stack = []
  while (root || stack.length) {
    while (root) {
      stack.push(root)
      res.unshift(root.val)
      root = root.right
    }
    root = stack.pop()
    root = root.left
  }
  return res
}

```


# 对称二叉树

判断A的右子树与B的左子树是否对称
判断A的左子树与B的右子树是否对称

```
function isMatch(leftNode, rightNode) {
  if (leftNode == null && rightNode === null) return true
  if (leftNode === null || rightNode === null) return false
  return leftNode.val === rightNode.val
    && isSame(leftNode.left, rightNode.right)
    && isSame(leftNode.right, rightNode.left)
}

function compare(root) {
  if (!root) {
    return false
  }
  return isMatch(root.left, root.right)
}
```
