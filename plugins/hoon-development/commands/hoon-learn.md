---
name: hoon-learn
description: Interactive learning workflow for Hoon programming using progressive exercises, hands-on examples, and guided tutorials
subagent_type: hoon-tutor
---

# Hoon Learning Command

Structured learning path for mastering Hoon through progressive exercises, interactive examples, and hands-on practice.

## Purpose

This command guides learners from Hoon basics through advanced concepts using a proven pedagogical approach with immediate feedback and practical examples.

## When to Use

- Learning Hoon for the first time
- Transitioning from other languages
- Teaching Hoon to others
- Refreshing Hoon knowledge
- Preparing for complex projects
- Understanding specific concepts

## Learning Principles

1. **Learn by Doing** - Code along with examples
2. **Progressive Difficulty** - Start simple, build complexity
3. **Immediate Feedback** - Test understanding frequently
4. **Practical Focus** - Real-world examples
5. **Spaced Repetition** - Review previous concepts
6. **Active Learning** - Solve problems, don't just read

---

## 6-Level Learning Path

### Level 1: Hoon Fundamentals (Day 1-3)

**Objective**: Understand basic Hoon concepts and syntax.

**Topics**:
1. Nouns (atoms and cells)
2. Subject-oriented programming
3. Basic runes
4. Type system basics
5. Dojo usage

**Exercise 1.1: Understanding Nouns**

```hoon
::  In dojo, try these:
42                     ::  Atom
[1 2]                  ::  Cell
[1 [2 3]]             ::  Nested cell
[[1 2] [3 4]]         ::  Cell of cells

::  Question: What is [1 2 3]?
::  Answer: [1 [2 3]] - right-associative
```

**Exercise 1.2: Basic Runes**

```hoon
::  =/ - Pin value to subject
=/  x  42
x                      ::  Returns 42

::  =+ - Also pins value
=+  y=10
y                      ::  Returns 10

::  :- - Construct cell
:-  1  2              ::  Returns [1 2]
:-(1 2)               ::  Same (wide form)
[1 2]                 ::  Same (irregular)
```

**Exercise 1.3: Type Casting**

```hoon
::  ^- - Enforce type
^-  @ud  42           ::  Returns 42 as @ud
^-  @t   'hello'      ::  Returns 'hello' as @t

::  Try this (will fail):
^-  @ud  'text'       ::  nest-fail!

::  Auras (type decorations)
`@ud`42               ::  Unsigned decimal
`@t`'text'            ::  Cord (text)
`@p`~zod              ::  Ship name
`@da`~2024.1.1        ::  Absolute date
```

**Practice Problems 1**:

```hoon
::  Problem 1: Create a cell [42 'hello']
::  Your solution:



::  Problem 2: Pin value 10 as 'x' and return x + 5
::  Your solution:



::  Problem 3: Cast 100 to @ud type
::  Your solution:
```

**Solutions 1**:

```hoon
::  Solution 1
[42 'hello']

::  Solution 2
=/  x  10
(add x 5)

::  Solution 3
^-  @ud  100
```

---

### Level 2: Control Flow and Functions (Day 4-7)

**Objective**: Write functions and control program flow.

**Topics**:
1. Conditionals (?:, ?~, ?@)
2. Pattern matching (?-, ?+)
3. Functions (|=, |*)
4. Recursion

**Exercise 2.1: Conditionals**

```hoon
::  ?: - If-then-else
?:  =(1 1)  'yes'  'no'      ::  Returns 'yes'
?:  =(1 2)  'yes'  'no'      ::  Returns 'no'

::  ?~ - Test for null
?~  ~  'null'  'not null'    ::  Returns 'null'
?~  [1 2]  'null'  'not null'  ::  Returns 'not null'

::  ?@ - Test for atom
?@  42  'atom'  'cell'       ::  Returns 'atom'
?@  [1 2]  'atom'  'cell'    ::  Returns 'cell'
```

**Exercise 2.2: Pattern Matching**

```hoon
::  ?- - Switch on value
=/  action  %create
?-  action
  %create  'creating'
  %delete  'deleting'
  %update  'updating'
==
::  Returns 'creating'

::  ?+ - Switch with default
=/  color  %blue
?+  color  'unknown'
  %red    'red'
  %green  'green'
==
::  Returns 'unknown'
```

**Exercise 2.3: Functions**

```hoon
::  |= - Create function (gate)
=/  double  |=(n=@ud (mul n 2))
(double 5)                    ::  Returns 10

::  Function with multiple arguments
=/  add-three
  |=  [a=@ud b=@ud c=@ud]
  (add (add a b) c)
(add-three 1 2 3)             ::  Returns 6
```

**Practice Problems 2**:

```hoon
::  Problem 1: Write function that returns 'even' if number is even, 'odd' otherwise
++  parity
  |=  n=@ud
  ::  Your code here



::  Problem 2: Write function that finds max of two numbers
++  max-of-two
  |=  [a=@ud b=@ud]
  ::  Your code here



::  Problem 3: Write recursive function to sum list
++  sum-list
  |=  items=(list @ud)
  ::  Your code here
```

**Solutions 2**:

```hoon
::  Solution 1
++  parity
  |=  n=@ud
  ?:  =(0 (mod n 2))  'even'  'odd'

::  Solution 2
++  max-of-two
  |=  [a=@ud b=@ud]
  ?:  (gth a b)  a  b

::  Solution 3
++  sum-list
  |=  items=(list @ud)
  ?~  items  0
  (add i.items $(items t.items))
```

---

### Level 3: Data Structures (Day 8-12)

**Objective**: Work with Hoon's core data structures.

**Topics**:
1. Lists
2. Maps
3. Sets
4. Molds (types)

**Exercise 3.1: Lists**

```hoon
::  Create list
~[1 2 3]                      ::  List of 1, 2, 3
~['a' 'b' 'c']               ::  List of cords

::  List operations
=/  items  ~[1 2 3 4 5]
(lent items)                  ::  Length: 5
(snag 0 items)               ::  First: 1
(snag 2 items)               ::  Third: 3

::  Transform list
(turn items |=(n=@ud (mul n 2)))  ::  ~[2 4 6 8 10]

::  Filter list
(skim items |=(n=@ud (gth n 3)))  ::  ~[4 5]
```

**Exercise 3.2: Maps**

```hoon
::  Create map
=/  ages  (my ~[['alice' 30] ['bob' 25]])

::  Lookup
(~(get by ages) 'alice')     ::  `30
(~(get by ages) 'charlie')   ::  ~

::  Insert
(~(put by ages) 'charlie' 35)

::  Check membership
(~(has by ages) 'alice')     ::  %.y
(~(has by ages) 'dave')      ::  %.n
```

**Exercise 3.3: Sets**

```hoon
::  Create set
=/  admins  (~(gas in *(set @p)) ~[~zod ~bus])

::  Check membership
(~(has in admins) ~zod)      ::  %.y
(~(has in admins) ~web)      ::  %.n

::  Add to set
(~(put in admins) ~web)

::  Set operations
=/  set-a  (~(gas in *(set @ud)) ~[1 2 3])
=/  set-b  (~(gas in *(set @ud)) ~[2 3 4])
(~(int in set-a) set-b)      ::  Intersection: {2 3}
(~(uni in set-a) set-b)      ::  Union: {1 2 3 4}
```

**Practice Problems 3**:

```hoon
::  Problem 1: Filter list to only even numbers
=/  numbers  ~[1 2 3 4 5 6]
::  Your solution:



::  Problem 2: Create map of user IDs to names, then lookup ID 2
::  Users: 1='Alice', 2='Bob', 3='Charlie'
::  Your solution:



::  Problem 3: Find common elements between two lists using sets
=/  list-a  ~[1 2 3 4]
=/  list-b  ~[3 4 5 6]
::  Your solution:
```

**Solutions 3**:

```hoon
::  Solution 1
(skim numbers |=(n=@ud =(0 (mod n 2))))

::  Solution 2
=/  users  (my ~[[1 'Alice'] [2 'Bob'] [3 'Charlie']])
(~(get by users) 2)  ::  `'Bob'

::  Solution 3
=/  set-a  (~(gas in *(set @ud)) list-a)
=/  set-b  (~(gas in *(set @ud)) list-b)
~(tap in (~(int in set-a) set-b))  ::  ~[3 4]
```

---

### Level 4: Type System (Day 13-17)

**Objective**: Master Hoon's type system and molds.

**Topics**:
1. Mold definition (+$)
2. Structures
3. Tagged unions
4. Type inference

**Exercise 4.1: Simple Molds**

```hoon
::  Define mold
+$  user  [name=@t age=@ud email=@t]

::  Create value
=/  alice  [name='Alice' age=30 email='a@ex.com']

::  Access fields
name.alice                    ::  'Alice'
age.alice                     ::  30
```

**Exercise 4.2: Tagged Unions**

```hoon
::  Define action type
+$  action
  $%  [%create name=@t]
      [%delete id=@ud]
      [%update id=@ud name=@t]
  ==

::  Create action
=/  my-action  [%create name='test']

::  Pattern match on action
?-  -.my-action
  %create  'creating'
  %delete  'deleting'
  %update  'updating'
==
```

**Exercise 4.3: Nested Structures**

```hoon
::  Define nested types
+$  address  [street=@t city=@t zip=@ud]
+$  user-full
  $:  name=@t
      age=@ud
      address=address
  ==

::  Create value
=/  bob
  :*  name='Bob'
      age=25
      address=[street='Main St' city='NYC' zip=10001]
  ==

::  Access nested
city.address.bob              ::  'NYC'
```

**Practice Problems 4**:

```hoon
::  Problem 1: Define mold for blog post (title, author, content, date)
::  Your solution:



::  Problem 2: Define tagged union for result type (success with value, error with message)
::  Your solution:



::  Problem 3: Pattern match on result and extract value/error
=/  result  [%success value=42]
::  Your solution:
```

**Solutions 4**:

```hoon
::  Solution 1
+$  blog-post
  $:  title=@t
      author=@t
      content=@t
      date=@da
  ==

::  Solution 2
+$  result
  $%  [%success value=@ud]
      [%error message=@t]
  ==

::  Solution 3
?-  -.result
  %success  value.result        ::  42
  %error    message.result
==
```

---

### Level 5: Gall Agents (Day 18-25)

**Objective**: Build stateful Urbit applications.

**Topics**:
1. Agent structure
2. State management
3. Agent arms (on-init, on-poke, etc.)
4. Cards and effects

**Exercise 5.1: Minimal Agent**

```hoon
::  app/counter.hoon
::
::  Minimal counter agent
::
/+  default-agent
::
|%
+$  versioned-state  [%0 count=@ud]
+$  card  card:agent:gall
--
::
=|  state=versioned-state
|_  =bowl:gall
+*  this  .
    def   ~(. (default-agent this %|) bowl)
::
++  on-init
  ^-  (quip card _this)
  `this(count.state 0)
::
++  on-poke
  |=  [=mark =vase]
  ^-  (quip card _this)
  ?>  =(mark %noun)
  =/  action  !<(?(%inc %dec) vase)
  ?-  action
    %inc  `this(count.state +(count.state))
    %dec  `this(count.state (dec count.state))
  ==
::
++  on-save   on-save:def
++  on-load   on-load:def
++  on-peek   on-peek:def
++  on-watch  on-watch:def
++  on-agent  on-agent:def
++  on-arvo   on-arvo:def
++  on-leave  on-leave:def
++  on-fail   on-fail:def
--
```

**Exercise 5.2: State Migration**

```hoon
::  Version 0 state
+$  state-0  [count=@ud]

::  Version 1 state (added name)
+$  state-1  [count=@ud name=@t]

::  Versioned state
+$  versioned-state
  $%  [%0 state-0]
      [%1 state-1]
  ==

::  Migration
++  on-load
  |=  old-vase=vase
  ^-  (quip card _this)
  =/  old  !<(versioned-state old-vase)
  ?-  -.old
    %0  `this(state [%1 count.old 'default'])
    %1  `this(state old)
  ==
```

**Practice Problems 5**:

```hoon
::  Problem 1: Add on-peek arm to return current count
::  Your solution:



::  Problem 2: Modify on-poke to handle %reset action (sets count to 0)
::  Your solution:



::  Problem 3: Add on-watch arm that sends initial count to subscribers
::  Your solution:
```

---

### Level 6: Advanced Patterns (Day 26-30)

**Objective**: Master advanced Hoon techniques.

**Topics**:
1. Doors for stateful APIs
2. Wet gates for polymorphism
3. Parser combinators
4. Performance optimization

**Exercise 6.1: Doors**

```hoon
::  Door with state
|_  state=[count=@ud total=@ud]
++  increment
  |=  n=@ud
  ^-  [value=@ud _state]
  =/  new-count  (add count.state n)
  =/  new-total  (add total.state n)
  [new-count state(count new-count, total new-total)]
::
++  get-average
  ^-  @ud
  ?:  =(count.state 0)  0
  (div total.state count.state)
--
```

**Exercise 6.2: Wet Gates**

```hoon
::  Polymorphic function
++  first
  |*  items=(list)
  ^-  ?
  ?~  items  !!
  i.items

::  Works with any list type
(first ~[1 2 3])             ::  1
(first ~['a' 'b'])           ::  'a'
```

---

## Learning Resources

### Practice Exercises
- [Hoon School](https://developers.urbit.org/guides/core/hoon-school) - Official tutorial
- [Hoon Workbook](https://github.com/urbit/hoon-workbook) - Practice problems
- Dojo experimentation - Try everything!

### Reference Materials
- [Hoon Reference](https://docs.urbit.org/language/hoon/reference) - Complete reference
- [Rune Index](https://docs.urbit.org/language/hoon/reference/rune) - All runes
- [Standard Library](https://docs.urbit.org/language/hoon/reference/stdlib) - Built-in functions

---

## Learning Checklist

### Fundamentals ✓
- [ ] Understand nouns (atoms and cells)
- [ ] Can use basic runes (=/  :-  ?:)
- [ ] Understand type casting (^-)
- [ ] Comfortable with dojo

### Functions ✓
- [ ] Can write simple functions (|=)
- [ ] Understand recursion
- [ ] Can pattern match (?-)
- [ ] Can use conditionals

### Data Structures ✓
- [ ] Can work with lists
- [ ] Can use maps for lookups
- [ ] Can use sets for membership
- [ ] Understand when to use each

### Type System ✓
- [ ] Can define molds (+$)
- [ ] Understand tagged unions
- [ ] Can pattern match on types
- [ ] Understand type inference

### Gall Agents ✓
- [ ] Built minimal agent
- [ ] Understand all 10 arms
- [ ] Can manage state
- [ ] Can handle migrations

### Advanced ✓
- [ ] Can write doors
- [ ] Understand wet gates
- [ ] Can parse input
- [ ] Can optimize code

---

## Success Metrics

- **Comfort with dojo** - Can experiment freely
- **Read existing code** - Understand Hoon codebases
- **Write functions** - Solve problems independently
- **Build agents** - Create full applications
- **Debug effectively** - Find and fix issues
- **Optimize when needed** - Improve performance
