---
layout: post
title: LeetCode_Gray Code
date: 2021-07-02
comments: true
categories: [Study, algorithm]
tags: [Javascript]
excerpt: Given an integer n, return any valid n-bit gray code sequence.
hidden: true
---

An n-bit gray code sequence is a sequence of `2ⁿ` integers where:

Every integer is in the **inclusive** range `[0, 2n - 1]`,
The first integer is `0`,
An integer appears no **more than once** in the sequence,
The binary representation of every pair of **adjacent** integers differs by **exactly one bit**, and
The binary representation of the **first** and **last** integers differs by **exactly one bit**.
Given an integer `n`, return any valid **n-bit gray code sequence**.

### Example 1:

<div class='innerBox'>
<strong>Input:</strong> n = 2<br>
<strong>Output:</strong> [0,1,3,2]<br>
<strong>Explanation:</strong><br>
The binary representation of [0,1,3,2] is [00,01,11,10].<br>
- 0<u>0</u> and 0<u>1</u> differ by one bit<br>
- <u>0</u>1 and <u>1</u>1 differ by one bit<br>
- 1<u>1</u> and 1<u>0</u> differ by one bit<br>
- <u>1</u>0 and <u>0</u>0 differ by one bit<br>
[0,2,3,1] is also a valid gray code sequence, whose binary representation is [00,10,11,01].<br>
- <u>0</u>0 and <u>1</u>0 differ by one bit<br>
- 1<u>0</u> and 1<u>1</u> differ by one bit<br>
- <u>1</u>1 and <u>0</u>1 differ by one bit<br>
- 0<u>1</u> and 0<u>0</u> differ by one bit
</div>

### Example 2:

<div class='innerBox'>
<strong>Input</strong>: n = 1<br>
<strong>Output</strong>: [0,1]
</div>

### Constraints

- `1 <= n <= 16`

### Solution

```javascript
const grayCode = function (n) {
  let answer = [0];
  for (let i = 1; i <= n; i++) {
    let len = answer.length;
    let shifted = 1 << (i - 1);
    for (let j = len - 1; j >= 0; j--) {
      answer.push(shifted + answer[j]);
    }
    console.log(i, shifted, answer);
  }
  return answer;
};
```
