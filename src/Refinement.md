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
  incrementalLists: true

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

I am going to use the following nomenclature to describe types:

```haskell
aVariableOrSomething :: TheTypeOfSaidThing
```

For instance.

```haskell
6 :: Integer

6.7556 :: Single

"Horses" :: String
```

# The Typescript Tax

* In most cases, if you can gain a significant benefit from TypeScript in your refactoring, that’s often a code smell indicating that your code is too tightly coupled. 

> Eric Elliott, "The Typescript Tax"


# Defensive programming

* Making sure that the string is definitely a string and not empty

```javascript
function printName(name) {
  if (name && name.length > 0 && typeof name === 'string') then {
    return name
  } else {
    return "No name!"
}
```

* avoiding divide by zero errors

```javascript
function divide(a, b) {
  if (b == 0) then {
    return 0; // would have caused error
  } else {
    return a / b
  }
}
```

# Why do we still even have these problems?

* ...there is little evidence that type safety makes a big difference... pair TDD with design review, spec review, and code review, and you’re looking at 90%+ reductions in bug density.

> Eric Elliott, "The Typescript Tax"


