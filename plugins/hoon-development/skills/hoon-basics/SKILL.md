---
name: hoon-basics
description: Quick reference for Hoon syntax fundamentals including rune forms, data types, gates, and common idioms. Use when needing fast syntax lookups, verifying rune usage, or resolving common gotchas for developers with working Hoon knowledge.
---

# Hoon Basics Quick Reference

Concise syntax reference for core Hoon patterns and common operations.

## Rune Forms

### Tall vs Wide vs Irregular

```hoon
::  Tall form (multi-line, explicit)
%-  add
:-  2
3

::  Wide form (single-line, compact)
%-(add [2 3])

::  Irregular form (syntactic sugar)
(add 2 3)
```

**Gotcha**: Irregular forms are preferred for readability but compile identically.

## Core Data Types

### Atoms (unsigned integers with auras)

```hoon
42              ::  @ud (unsigned decimal)
0x2a            ::  @ux (hexadecimal)
~zod            ::  @p (ship name)
'Hello'         ::  @t (text/cord)
0b101010        ::  @ub (binary)
```

### Cells (ordered pairs)

```hoon
[1 2]           ::  Two-element cell
[1 2 3]         ::  Right-branching: [1 [2 3]]
[[1 2] [3 4]]   ::  Nested cells
```

## Gates (Functions)

### Basic Gate Syntax

```hoon
|=  a=@ud           ::  Gate with typed argument
(add a 10)

|=  [a=@ud b=@ud]   ::  Multiple arguments
(add a b)

|%                  ::  Core with multiple arms
++  increment
  |=  a=@ud
  (add a 1)
++  decrement
  |=  a=@ud
  (sub a 1)
--
```

## Conditional Logic

```hoon
?:  condition       ::  If-then-else (wutcol)
  true-branch
false-branch

?~  list            ::  If null/not-null (wutsig)
  null-case
not-null-case

?@  value           ::  If atom/cell (wutpat)
  atom-case
cell-case
```

## Lists

```hoon
~[1 2 3 4]         ::  List literal
[1 2 3 4 ~]        ::  Manual construction (equivalent)
~                  ::  Empty list (null)

::  List operations
(lent list)        ::  Length
(snag 2 list)      ::  Index access (0-based)
(weld list1 list2) ::  Concatenate
(turn list gate)   ::  Map
(roll list gate)   ::  Reduce/fold
```

## Common Idioms

### Type Casting

```hoon
`@ud`0x10          ::  Cast hex to decimal → 16
`@t`'string'       ::  Cast to cord
^-  @ud  value     ::  Type assertion (kethep)
```

### Pinning Values (=/  tisfas)

```hoon
=/  x  42          ::  Pin value to face
=/  y  (add x 10)  ::  Use pinned value
(mul x y)          ::  Both available in subject
```

### Pattern Matching with Faces

```hoon
=/  cell  [1 2]
=/  [a b]  cell    ::  Destructure into faces
(add a b)          ::  → 3
```

## Gotchas

1. **Null is `~`** not `0` or `false` or `nil`
2. **Lists are right-branching**: `~[1 2 3]` = `[1 [2 [3 ~]]]`
3. **Runes are two characters**: `=+` not `=`, `|-` not `|`
4. **Hoon has no strings**: Use `@t` (cord/tape) or `tape` (list of @t)
5. **Subject-oriented**: Everything operates on implicit context (the subject)
6. **Whitespace matters in tall form**: Two spaces for indentation
7. **No mutation**: All data structures are immutable

## Fast Lookups

### Arithmetic

```hoon
(add a b)    ::  Addition
(sub a b)    ::  Subtraction
(mul a b)    ::  Multiplication
(div a b)    ::  Division
(mod a b)    ::  Modulo
(pow a b)    ::  Exponentiation
```

### Boolean Logic

```hoon
&(a b)       ::  AND (pam)
|(a b)       ::  OR (bar)
!(a)         ::  NOT (zap)
=(a b)       ::  Equality test
```

### Common Runes

```hoon
|=  ::  Gate (function definition)
|-  ::  Trap (recursion)
?:  ::  If-then-else
=/  ::  Pin value to face
=<  ::  Compose with subject on right
=>  ::  Compose with subject on left
^-  ::  Type assertion
%+  ::  Call gate with two arguments
%-  ::  Call gate with one argument
```

## Resources

- [Hoon Rune Reference](https://developers.urbit.org/reference/hoon/rune)
- [Hoon Standard Library](https://developers.urbit.org/reference/hoon/stdlib)
- [Type System Documentation](https://developers.urbit.org/reference/hoon/hoon-school/types)
