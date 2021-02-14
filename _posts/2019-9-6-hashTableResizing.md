---
layout: post
title: hashTableResizing
date: 2019-09-06
comments: true
categories: [Study, algorithm]
tags: [Javascript]
excerpt: Create a hash table with insert(), retrieve(), and remove() methods. Be sure to handle hashing collisions correctly.
hidden: true
---

### 문제

Create a hash table with `insert()`, `retrieve()`, and `remove()` methods.
Be sure to handle hashing collisions correctly.
Set your hash table up to double the storage limit as soon as the total number of items stored is greater than 3/4th of the number of slots in the storage array.
Resize by half whenever utilization drops below 1/4.

### 풀이

```javascript
var makeHashTable = function() {
  var result = {};
  var storage = [];
  var storageLimit = 4;
  var size = 0;
  result.insert = function(key, value) {
    let index = getIndexBelowMaxForKey(key, storageLimit);
    if (storage[index]) {
      storage[index][key] = value;
    } else {
      storage[index] = {};
      storage[index][key] = value;
      size++;
    }
    if (size >= (storageLimit * 3) / 4) {
      storageLimit = storageLimit * 2;
    }
  };

  result.retrieve = function(key) {
    let index = getIndexBelowMaxForKey(key, storageLimit);
    if (storage[index]) {
      let value = storage[index][key];
      return value;
    }
    return undefined;
  };

  result.remove = function(key) {
    let index = getIndexBelowMaxForKey(key, storageLimit);
    delete storage[index][key];
    if (storage[index] === {}) {
      size--;
      storage[index] = undefined;
    }
    if (size < storageLimit / 4) {
      storageLimit = storageLimit / 2;
    }
  };
  return result;
};

// This is a "hashing function". You don't need to worry about it, just use it
// to turn any string into an integer that is well-distributed between
// 0 and max - 1
var getIndexBelowMaxForKey = function(str, max) {
  var hash = 0;
  for (var i = 0; i < str.length; i++) {
    hash = (hash << 5) + hash + str.charCodeAt(i);
    hash = hash & hash; // Convert to 32bit integer
    hash = Math.abs(hash);
  }
  return hash % max;
};
```
