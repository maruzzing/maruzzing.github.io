---
layout: post
title: LeetCode_Lowest Common Ancestor of a Binary Tree
date: 2021-06-30
comments: true
categories: [Study, algorithm]
tags: [Javascript]
excerpt: Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.
hidden: true
---

Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).”

![Lowest_common_ancestor](/images/Lowest_common_ancestor.png "Lowest_common_ancestor")

### Constraints

- The number of nodes in the tree is in the range `[2, 105]`.
- `-109 <= Node.val <= 109`
- All `Node.val` are **unique**.
- `p != q`
- `p` and `q` will exist in the tree.

### 접근법

DFS 방식으로 트리를 순회하다가 p 또는 q를 만나면 node를 반환.

최소 공통 조상은 두 하위 트리의 재귀가 null이 아닌 node.

### Solution

```javascript
const lowestCommonAncestor = function (root, p, q) {
  if (!root || root === p || root === q) return root;
  const left = lowestCommonAncestor(root.left, p, q);
  const right = lowestCommonAncestor(root.right, p, q);
  return left ? (right ? root : left) : right;
};
```
