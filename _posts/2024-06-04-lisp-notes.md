---
layout: post
title:  "Lisp Notes"
date:   2024-06-04 14:35:05 -0400
author: "Thomas."
---

> notes on A Gentle Introduction To Common Lisp

### Useful links:
- [Steve L's How to learn Lisp blog](https://stevelosh.com/blog/2018/08/a-road-to-common-lisp/#s10-get-a-lisp)
 
- [Interpreter v Compiler wiki](https://www.webopedia.com/definitions/interpreter/)
	 - interpreters (like Lisp) immediately run high-level programs
	 - compilers are translating things to machine code. 
 
## Benefits of Common Lisp
1. avoid backwards incompatibility problems for persistent code
2. rely on only a few libraries (instead of many!)
3. Extensibility
   - Macros allow users to write libraries instead of changing core language features (like in in other languages).
   - you can write stable libraries in the core language and then depend on a small number of those libraries in your applications to add exactly the features you need for any particular problem.
4. Power
	- Common Lisp macros are different because they're part of the language.
	- In Common Lisp, you write macros in Common Lisp itself. You can then use those macros to write functions, and use those functions to write more macros.

---

## Parts
1. *symbols*
	- AARDVARK
2. *integers*
	- 1
3. *other numbers* (floats etc.)
	- 1.2 or 1.0
4. *(lists)* 
	- (AARDVARK)
5. functions & macro-functions

all these parts are moving `cons cells` around with a pointer.
- CAR | CDR 
	- CAR is the pointer part, and CDR is the (list) part

---


## Predicates
- question answering function
- returns T for true, otherwise returns NIL for false
- only works on numbers

`NUMBERP` as in Nmber predicate (checks if the input is a number)

`SYMBOLP` as in Symbol predicate
- checks if the input is a symbol

`ZEROP`
`EVENP`
`ODDP`
`<` (returns T if the first input is less than the second)
`>` (returns T if the first input is greater than the second)
`EQUAL` (returns T if the two inputs are equal)

---

## Primitive Functions
`LENGTH` (computes the length of a list) [no numers or symbols allowed]

`NIL` can also be written as an empty list `()`
- because `NIL` is the only thing, that is both a *list* and a *symbol*

`FIRST` extracts the first elements from a list `SECOND` extracts the second, `THIRD`...

`REST` returns everything except the first element of a list

- so, to return everything but the second, do `REST` --> `FIRST`

these primitive functions also return the CAR and CDR parts of the cons cells respectively. Wierdly because they are those things... 

`FIRST` returns the CAR (the pointer part)
`REST` returns the CDR (the list part)

---

### How to unfuck the dumb words like CAADAR, or CAAAAAR, or CDDADADADAR
CAR and CDR are ways to navigate cons cells
1. draw the cons cell
2. A's move downward, D's move sideways
3. each move adds a letter in between C _ _ _ R (from right to left for some fucking reason)

So the CAAR of a list `(((annoying)) (system))` is the list `(annoying)` 
- and the CAAAR would be the symbol `annoying`
- the CAR would be `((annoying))`

both the `CDR` and `CAR` of the `NIL` value / symbol / empty list

## How can I add some parentheses to make something a list?

`CONS` appends a new cons cell to the front of a list
like this: 
input 1: A
input 2: (B C D) 
result: (A B C D)

what if i dont have a list to put it in? --> it makes one!

the primitive function `CONS` also turns a symbol into a list
input 1: irregardlessly
input 2: NIL
result: (irregardlessly)

the easy way to do this is with:
`LIST` - creates a list from a bunch of elements

## List Predicates
`LISTP` as in list-predicate, returns True if its input is a (List) - remember that `NIL` is an empty list.

`CONSP` as in cons-predicate returns True if its input is a cons cell (so `NIL` doesn't count this time).

`ATOM` is the opposite - it returns True if its input is anything other than a cons cell (such as a number, or a symbol)

`NULL` returns T if its input is `NIL`
- the convention is to use the `NOT` predicate for logical operations and use `NULL` for testing if a list is empty.


## Lists Summary
- lists have a printed (in parense) and an internal representation (cons cells)
- they have elements, which can be;  numbers, symbols, or nested lists
- take lists apart with CAR, and CDR (aka `FIRST` and `REST`)
- put lists together with `CONS` or `LIST`
- `LENGTH` counts elements in a list (represented as the number of top level cons-cells)

---

## EVAL Notation
- this is the thing that evaluates Lisp expressions and returns a result
- this double arrow &rArr; is another way to write it
- expressions are typically a function, followed by a set of inputs
	-  (+ 1 2) &rArr; 3


#### EVAL has a few rules
1. Numbers and certain other objects EVALuate to themselves 
- 25 &rArr; 25
2. the first element in a list specifies a function to be called, then the remaining elements specify arguments to the function. The function is called on the evaluated elements.

## definine functions 
`DEFUN` as in define function, defines functions -*it is a macro function*
- defun first input is the name of the function,
- its next input is the argument list,
- and the remaining inputs are the body

`(name (argument argument) (body))`

like this: (no quotes required because DEFUN is special)
`(defun average (x y) (/ (+ x y) 2.0))`

then you can call the function with `EVAL`  like `eval (average 6 8)` &rArr; 7.0


## the quote mark
- use the quote before (lists) to indicate that they are lists not variables
- unassigned variable or undefined function typically indicate the absence of a quote mark
- symbols used as data must be quoted

# IF - special function
IF has three arguments `test` `true-part` `false-part`
if the test is true `IF` &rArr; the value of the `true-part`
if the test is false it returns the other value

`(IF (some test) (then do this) (else do this))`



# the COND(itional) macro
COND is made up of it's own name, then any number of test-consequent clauses
like: `(COND (test-1 consequent-1)
(test-2 consequent-2)
(test-3 consequent-3))`

if test 1 passes &rArr;  its value
if test 1 does NOT pass &rarr; try test 2
if no tests pass &rArr; NIL

### Common trick with COND -- T as a test
use T as a consequent to to do something whenever the pointer reaches this conditional.
- ex: use `(t 'unknown)`  to have cond print "unknown" if all the previous test-consequent clauses fail. like:  `(COND (test-1 consequent-1)
(test-2 consequent-2)
(test-3 consequent-3)(t 'unknown))`

### parens trickery
most COND clauses begin with exactly two parentheses

#### cond example
```
(defun where-is (x)
  (cond ((equal x `paris) `france)
        ((equal x `london) `england)
        ((equal x `boise) `idaho)
        (t `unkown)
  )
)
```

(where-is 'boise) &rArr; idaho

---