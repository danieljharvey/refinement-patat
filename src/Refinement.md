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


What the fuck even are they? Do we care?

```javascript
function makeBig(thing) {
  return thing.toUpperCase()
}

makeBig('horse') // 'HORSE'
makeBig(12) // Error: toUpperCase is not defined
```
-->

# What's in it for us?

# Firstly

# What are we trying to avoid?

# To s

![](./src/javascript.png)

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

# Why do we still even have these problems?

* ...there is little evidence that type safety makes a big difference... pair TDD with design review, spec review, and code review, and you’re looking at 90%+ reductions in bug density.

> Eric Elliott, "The Typescript Tax"

# There are other solutions to these problems

* Test Driven Design
* Code review
* Manual testing
* Spec review

# However...

One thing in common with many of these.

* They are solutions that already assume a high level of developer proficiancy

# How far do types even get us?

# Let's fix these YOLO errors

```javascript
function makeBig(thing) {
  return thing.toUpperCase()
}
```

- Crack open a type signature on that function.

```typescript
function makeBig(thing: string) {
  return thing.toUpperCase()
}
```

- Let's give it a smash

```typescript
makeBig('horse') // 'HORSE'
```

- Now if we try and do some wild type stupidity, our code doesn't even compile.

```typescript
makeBig(12) 
// Argument of type '6' is not assignable to parameter of type 'string'.
```

# Can we fix this malformed user input?

```javascript
function printName(name) {
  if (name && name.length > 0 && typeof name === 'string') then {
    return name
  } else {
    return "No name!"
}
```

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

```javascript
function divide(a, b) {
  if (b == 0 || isNaN(a) || isNaN(b)) then {
    return 0; // would have caused error
  } else {
    return a / b
  }
}
```

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


# A warning

From now on I may occasionally use the following syntax to describe types:

```haskell
aVariableOrSomething :: TheTypeOfSaidThing
```

- `6` is an `Integer`

```haskell
6 :: Integer
```

- `6.7556` is a `Number`

```haskell
6.7556 :: Number
```

- "Horses" is a `String`

```haskell
"Horses" :: String
```

# Refined types

A Refined type looks like this:

```haskell
Refined predicate value
```

`value` is the type we are refining, for example `Int` or `Number`.

`predicate` is a type that lets us better describe the `value`.

# The most basic Refinement

The `id` predicate, which doesn't really do anything.

```haskell
Refined Id Int
```

- Any value that is a value `Int` can be made into a valid `Refined Id Int`.

```haskell
refine (11233) -- Sure
```

- Great stuff.

```haskell
refine (-213123) -- why not?
```

# A bit more clever

The `Positive` predicate, which only allows numbers over 0.

```haskell
Refined Positive Int
```

- The `refine` function works here:
 
```haskell
refine(10) -- Great
```

- But this isn't going to fly

```haskell
refine(-10) -- NO
```

# We can be more specific

The `From` predicate takes an integer and only allows values equal to or above
it.

```haskell
Refined (From 10) Int
```

- So this will not `refine`.

```haskell
refine(9) -- NO WAY
```

- But this is totally great.

```haskell
refine(100) -- really good stuff
```

# And in balance

The `To` predicate gives us a maximum figure.

```haskell
Refined (To 100) Int
```

- So this is terrible.

```haskell
refine (101) -- NO THANKS
```

- But this is great stuff.

```haskell
refine(80) -- SURE THING
```

# It doesn't just have to be numbers

We can check strings too with `SizeEqualTo`, `SizeGreaterThan` and
`SizeLessThan`.

```haskell
Refined (SizeGreaterThan 3) String
```

- This works...

```haskell
refine "oh" -- nope
```

- But this does

```haskell
refine "well" -- great!
```

# Let's combine them

We've also got `And` and `Or` for combining them.

- This will only allow whole numbers from `1` to `100`.

```haskell
Refined ((From 1) And (To 100)) Int
```

- This will allow all whole numbers EXCEPT `1` to `100`.

```haskell
Refined ((To 0) Or (From 101)) Int
```

- This describes the roll of a dice.

```haskell
type Dice = Refined ((From 1) And (To 6)) Int
```

- Or something rather stupid.

```haskell
type Prime 
  = Refined 
      (Or (Equal 2) 
        (Or (Equal 3) 
          (Or (Equal 5) 
            (Or (Equal 7) 
              (Or (Equal 11) 
                (Or (Equal 13) (Equal 17))
              )
            )
          )
        )
      ) Int
```

# So what can we do with them?

# JSON validators

We could change this data type...

```haskell
type AlcoholUser
  = { name :: String
    , age  :: Int
    }
```

- To use `Refined`...

```haskell
type AlcoholUser
  = { name :: Refined (SizeFrom 1) String
    , age  :: Refined (From 18) Int
    }
```

- ...we can then automatically create validators that include `Refined` types, so our APIs can be fussy and reject bad data before it even makes it into our program.

# Contract tests

......dfs.dsf.sd.f...


# Not defensively program

- This printName function is now unnecessary

```typescript
type Name = Refined (SizeFrom 1) String

function printName(name: Name) {
  return name
}
```

- And this division can now be safe from fear

```typescript
type Divide = Refined (Not (Equal 0)) Number

function divide(a: Number, b: Divide) {
  return a / b
}
```


<!--
# The Typescript Tax

* In most cases, if you can gain a significant benefit from TypeScript in your refactoring, that’s often a code smell indicating that your code is too tightly coupled. 

> Eric Elliott, "The Typescript Tax"

-->

# Problem: keeping test data up to date

Let's say we have a data type that we get from a server.

```haskell
type User 
  = { name      :: String
    , age       :: Number
    , likesDogs :: Boolean
    }
```

* Oh shit! It's changed!

 ```haskell
type User 
  = { name     :: String
    , age      :: Number
    , petNames :: Array String
    }
```

* Now where do we need to change it?

# Where does the data live?

- On the server
- Initial / default data in the app
- Every piece of test data
- (hopefully) in the type signature itself

* Changing all those every time our data changes is going to be a 1x pain in
  the arse.

# What if....?

We could just auto generate from the types instead?

# That's right

![](./src/oh-shit.png)

# Arbitrary type generation

.............TODO............


