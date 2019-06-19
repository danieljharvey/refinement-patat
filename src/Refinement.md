---
title: What have types ever done for us?
author: Daniel J. Harvey
patat:
  wrap: true
  margins:
    left: 10
    right: 10
    top: 10
    bottom: 10
  images:
    backend: auto
---

# Types

<!--
In which our hero talks
-->


What the fuck even are they? Do we care?

```javascript
function makeBig(thing) {
  return thing.toUpperCase()
}

makeBig('horse') // 'HORSE'
makeBig(12) // Error: toUpperCase is not defined
```

What?

![](javascript.jpg)

# Intro to some types

```javascript
"dog" :: String
6 :: Integer
6.7556 :: Single
```
