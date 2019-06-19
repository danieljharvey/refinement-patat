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
  images:
    backend: iterm2

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

# To stop this I guess

![](./src/javascript.png)

# What are we trying to avoid then?

# Runtime errors caused by Javascript YOLO

```javascript
function makeBig(thing) {
  return thing.toUpperCase()
}
```

- This seems fine

```javascript
makeBig('horse') // 'HORSE'
```

- But this isn't so great

```javascript
makeBig(12) // Error: toUpperCase is not defined
```

# What else?

- Overly defensive code around user input

```javascript
function printName(name) {
  if (name && name.length > 0 && typeof name === 'string') then {
    return name
  } else {
    return "No name!"
}
```

- Defense against the dark arts

```javascript
function divide(a, b) {
  if (b == 0 || isNaN(a) || isNaN(b)) then {
    return 0; // would have caused error
  } else {
    return a / b
  }
}
```

# And hopefully on the way we get

- To write a few less low level tests
- Some documentation
- More intelligent tooling

# Let's fix these YOLO errors

- Crack open a type signature on that function.

```typescript
function makeBig(thing: string) {
  return thing.toUpperCase()
}

makeBig('horse') // 'HORSE'
```

- Now if we try and do some wild type stupidity, our code doesn't even compile.

```typescript
makeBig(12) 
// Argument of type '6' is not assignable to parameter of type 'string'.
```

# Can we fix this malformed user input?

- A string type on here means we don't have to check it exists

```typescript
function printName(name: string) {
  if (name.length > 0) then {
    return name
  } else {
    return "No name!"
}
```
- ...but we still need to check whether the string is long enough

# What about that classic divide by zero problem?

- We can get rid of the number checks...

```typescript
function divide(a: number, b: number) {
  if (b == 0) then {
    return 0; // would have caused error
  } else {
    return a / b
  }
}
```

- ...but we've still got to check for that zero value

# Why do we still even have these problems?

* ...there is little evidence that type safety makes a big difference... pair TDD with design review, spec review, and code review, and you’re looking at 90%+ reductions in bug density.

> Eric Elliott, "The Typescript Tax"

# There are other solutions to these problems

* Test Driven Design
* Code review
* Manual testing
* Spec review

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

# Problem: keeping test data up to date

Let's say we have a data type that we get from a server.

```typescript
type User 
  = { name: String
    , age:  Number
    , likesDogs: Boolean
    }
```

* Oh shit! It's changed!

 ```typescript
type User 
  = { name: String
    , age:  Number
    , petNames: String[]
    }
```

* Now where do we need to change it?

# Where does the data live?

- On the server
- Initial / default data in the app
- Every piece of test data
- (hopefully) in the type signature itself

# What if....?

We could just auto generate from the types instead?

# That's right

![](./src/oh-shit.png)



