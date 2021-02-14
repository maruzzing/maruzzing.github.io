---
layout: post
title: allAnagrams
date: 2019-09-08
comments: true
categories: [Study, algorithm]
tags: [Javascript]
excerpt: Create a hash table with insert(), retrieve(), and remove() methods. Be sure to handle hashing collisions correctly.
hidden: true
---

### 문제

Given a single input string, write a function that produces all possible anagrams of a string and outputs them as an array. At first, don't worry about repeated strings. What time complexity is your solution?

Extra credit: Deduplicate your return array without using uniq().

example usage:

```javascript
var anagrams = allAnagrams("abc");
console.log(anagrams); // [ 'abc', 'acb', 'bac', 'bca', 'cab', 'cba' ]
```

### 풀이

```javascript
var allAnagrams = function(string) {
  let stringObj = {};
  for (let i = 0; i < string.length; i++) {
    stringObj[i] = false;
  }
  let anagramsObj = {};

  const makeAnagrams = (word, obj) => {
    if (word.length === string.length) {
      if (!anagramsObj[word]) {
        anagramsObj[word] = true;
      }
      return;
    }
    for (let i = 0; i < string.length; i++) {
      let newWord = word;
      if (!obj[i]) {
        newWord += string[i];
        obj[i] = !obj[i];
        makeAnagrams(newWord, obj);
        obj[i] = !obj[i];
      }
    }
  };
  makeAnagrams("", stringObj);
  return Object.keys(anagramsObj);
};
```
