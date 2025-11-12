---
name: hoon-fundamentals
description: Master subject-oriented programming, the noun data model, and Hoon's compilation to Nock. Use when understanding core Hoon architecture, debugging low-level issues, explaining fundamental concepts, or working with subject transformations and noun-level operations.
---

# Hoon Fundamentals Skill

Master subject-oriented programming, the noun data model, and Hoon's compilation to Nock. Use when understanding core Hoon architecture, debugging low-level issues, or explaining fundamental concepts.

## Overview

This skill provides deep knowledge of Hoon's foundational concepts: subject-oriented programming, the noun data model, compilation to Nock, and the unique paradigm that distinguishes Hoon from other functional languages.

## Learning Objectives

After mastering this skill, you will understand:

1. **Subject-Oriented Programming**: How Hoon's subject model differs from traditional scoping
2. **Noun Data Model**: Atoms, cells, and tree-structured data
3. **Compilation Model**: How Hoon compiles to Nock
4. **Type System Foundation**: How molds and auras build on nouns
5. **Rune Semantics**: How runes manipulate the subject
6. **Functional Purity**: Immutability and referential transparency in Hoon

## 1. Subject-Oriented Programming

### What is the Subject?

The **subject** is the fundamental context in which all Hoon code executes. Think of it as:
- **Scope** in traditional languages (but explicit)
- **"this"** in JavaScript (but immutable)
- **Environment** in Lisp (but type-safe)

Every Hoon expression evaluates in the context of a subject, which is a noun containing all accessible data and code.

### Subject Transformation

Hoon programming is primarily about transforming the subject:

```hoon
::  Start with empty subject
::  Add 'x' to subject
=/  x  5
::  Subject now contains x=5
::  Add 'y' to subject
=/  y  10
::  Subject now contains x=5, y=10
::  Compute using subject
(add x y)  ::  15
```

### Subject Resolution

When you reference a name like `x`, Hoon searches the subject:
1. Check if `x` is a face (named value) in the current subject
2. Search outward through subject layers
3. If not found: compile error (`-find-limb.x`)

### Key Insight

**In traditional languages**: Variables exist in nested scopes (invisible context)
**In Hoon**: The subject IS the context (explicit, immutable, transformable)

## 2. Noun Data Model

### Everything is a Noun

A **noun** is the only data structure in Nock/Hoon:
- Either an **atom** (unsigned integer of any size)
- Or a **cell** (ordered pair of two nouns)

```
noun = atom | [noun noun]
```

### Examples

```hoon
42                  ::  Atom
[1 2]               ::  Cell of two atoms
[1 [2 3]]           ::  Cell: atom and cell
[[1 2] [3 4]]       ::  Cell of two cells
```

### Tree Structure

Cells form binary trees:
```
    [1 [2 3]]
       / \
      1   [2 3]
          / \
         2   3
```

### Addressing with Axis

Every position in a noun tree has an **axis** (tree address):
```
        1
       / \
      2   3
     / \ / \
    4 5 6 7
```

- Axis 1: Root (entire tree)
- Axis 2: Left child
- Axis 3: Right child
- Axis 4: Left-left child
- etc.

```hoon
=/  tree  [[1 2] [3 4]]
+1:tree  ::  [[1 2] [3 4]] (entire tree)
+2:tree  ::  [1 2] (left branch)
+3:tree  ::  [3 4] (right branch)
+4:tree  ::  1 (left-left)
+5:tree  ::  2 (left-right)
```

## 3. From Nouns to Types

### Atoms with Meaning: Auras

An atom is just a number, but we give it **meaning** with an **aura**:

```hoon
42        ::  Just a number
`@ud`42   ::  Unsigned decimal: 42
`@ux`42   ::  Hexadecimal: 0x2a
`@t`42    ::  Text character (ASCII '*')
```

Common auras:
- `@` - Atom (any)
- `@ud` - Unsigned decimal (42)
- `@ux` - Hexadecimal (0x2a)
- `@t` - UTF-8 text ('hello')
- `@p` - Ship name (~sampel-palnet)
- `@da` - Absolute date/time

### Cells with Structure: Molds

A **mold** is a type that validates and normalizes nouns:

```hoon
::  Define a mold
+$  point  [x=@ud y=@ud]

::  Validate a noun
=/  p  [x=5 y=10]
^-  point  p  ::  Type-checks!
```

### The Power of Nouns

1. **Homoiconicity**: Code and data have the same structure
2. **Serialization**: Any noun can be serialized to/from atoms
3. **Network Transfer**: Nouns are the unit of Ames networking
4. **State Persistence**: Ship state is a noun

## 4. Hoon to Nock Compilation

### Compilation Pipeline

```
Hoon Source Code
     ↓ (parsed)
Abstract Syntax Tree (AST)
     ↓ (compiled)
Nock Code
     ↓ (executed)
Result Noun
```

### Example

```hoon
::  Hoon
(add 2 3)

::  Compiles to Nock (simplified)
[0 4]  ::  Nock opcode: Add atoms at address 4
```

### Why This Matters

- **Determinism**: Same Hoon always compiles to same Nock
- **Portability**: Any Nock interpreter produces identical results
- **Debugging**: Understanding Nock helps debug deep issues
- **Performance**: Jet acceleration works on Nock patterns

## 5. Runes: Subject Manipulation

Runes are Hoon's syntax for subject transformation:

### Adding to Subject

```hoon
=/  name  value  ::  Add face 'name' with 'value' to subject
```

### Composing Subject

```hoon
=>  context     ::  Evaluate right, add to subject, then evaluate left
    expression

=<  expression  ::  Same but reversed (result-first)
    context
```

### Conditionals (Subject-Aware)

```hoon
?:  test
  true-branch   ::  Evaluated with current subject
false-branch    ::  Evaluated with current subject
```

## 6. Functional Purity

### Immutability

All Hoon data is immutable:
```hoon
=/  x  5
::  Cannot mutate x!
::  Must create new value
=/  x  (add x 1)  ::  x is now 6 (new binding)
```

### Referential Transparency

Same inputs always produce same outputs:
```hoon
++  double
  |=  n=@ud
  (mul 2 n)

(double 5)  ::  Always 10, every time
```

### No Side Effects

Pure computation only - effects happen via Gall cards (not in pure Hoon):
```hoon
::  Pure function
++  compute
  |=  x=@ud
  (mul x x)

::  Effect (in Gall agent)
++  on-poke
  :_  this
  ~[[%give %fact ~[/updates] %json !>([%result (compute 5)])]]
```

## 7. Practical Implications

### Debugging Strategy

1. **Print subject**: Use `!>` to inspect what's in scope
2. **Trace subject transformations**: Follow `=/`, `=>`, `=<` runes
3. **Understand resolution**: Know how names resolve in subject

### Code Organization

Structure code around subject manipulation:
```hoon
=>  |%                ::  Build library core
    ++  helper-1  ...
    ++  helper-2  ...
    --
=<  (main-logic)      ::  Use library
|%
++  main-logic
  ::  Can access helper-1, helper-2 from subject
  ...
--
```

### Type-Driven Development

1. Design noun structure (data model)
2. Create molds (types)
3. Write functions operating on typed nouns
4. Compiler enforces correctness

## 8. Common Patterns

### Building Context

```hoon
::  Pattern: Layer context progressively
=>  [constant-1=100 constant-2=200]
=>  |%
    ++  helper  |=(x=@ (add x constant-1))
    --
=<  (main-program)
|%
++  main-program
  (helper constant-2)  ::  300
--
```

### Core Construction

```hoon
::  Pattern: Reusable library
|%
++  add-ten  |=(x=@ (add x 10))
++  mul-two  |=(x=@ (mul x 2))
--
```

## 9. Advanced Concepts

### Subject as First-Class Value

The subject itself is a noun that can be captured:
```hoon
.  ::  The entire subject (axis 1)
```

### Computation = Subject Transformation

Every Hoon program:
1. Starts with a subject (context)
2. Transforms the subject (computation)
3. Produces a new subject or extract value from it

### Wings, Limbs, and Faces

- **Face**: A name bound to a value (`x=5`)
- **Limb**: An axis or face in the subject
- **Wing**: A path through the subject (`a.b.c`)

## 10. Key Takeaways

1. **Subject-oriented** programming is Hoon's core paradigm
2. **Nouns** are the only data structure (atoms or cells)
3. **Auras** give meaning to atoms
4. **Molds** give structure to nouns
5. **Runes** manipulate the subject
6. **Compilation** to Nock ensures determinism
7. **Immutability** and **purity** are enforced
8. **Understanding the subject** is key to mastering Hoon

## Resources

- [Hoon School](https://developers.urbit.org/guides/core/hoon-school)
- [Subject-Oriented Programming](https://docs.urbit.org/language/hoon/reference/concepts#subject-oriented-programming)
- [Noun Documentation](https://docs.urbit.org/language/hoon/reference/nouns)
- [Nock Specification](https://docs.urbit.org/language/nock/reference/specification)

## Summary

Hoon's subject-oriented programming model, built on the simple noun data structure, provides a unique and powerful approach to functional programming. Mastering these fundamentals unlocks understanding of all higher-level Hoon concepts and enables effective Hoon development.
