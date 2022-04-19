---
layout: post
title: "Different equalities in JS: How does `==` work?"
date: 2022-04-19 12:23:15 +0530
---

There are four equality comparison algorithms in ES2015:

1.  Abstract Equality Comparison
2.  Strict Equality Comparison: used by `Array.prototype.indexOf`, `Array.prototype.lastIndexOf`, and case-matching
3.  SameValueZero: used by `%TypedArray%` and `ArrayBuffer` constructors, as well as `Map` and `Set` operations, and also `String.prototype.includes and` `Array.prototype.includes` since ES2016
4.  SameValue: used in all other places including `Object.defineProperty`

But there are only three equality operators exposed to the user:

1. `==` uses Abstract Equality Comparison
2. `===` uses Strict Equality Comparison
3. `Object.is` uses SameValue Comparison

## Abstract Equality Comparison

1. Is the same as "Strict Equality Comparison" when types are equal.
2. `undefined` is equal to `null`
3. Otherwise, these are the rules for type coercion:
   1. boolean → number
      1. true → 1
      2. false → 0
   2. string → number / bigint
      1. "" / " " → 0 / 0n
      2. " <finite> " → <finite> / <finite>n
      3. "Infinity" → Infinity / error
      4. "-Infinity" → -Infinity / error
      5. Else → NaN / error
   3. object → toPrimitive(object, "string")
      1. If `object[Symbol.toPrimitive]` exists, use it for type coercion.
      2. Else, use `toString()` and `valueOf()` in that order [whichever gives a primitive type first]
   4. number → bigint
      1. Exceptions: NaN, Infinity and - Infinity don't have representation in bigint.
   5. Following exceptions for numeric comparison:
      1. NaN always compares as false
      2. +0 and -0 compare as true

Link: https://262.ecma-international.org/12.0/#sec-abstract-equality-comparison

## Strict Equality Operation

1. Types have to match.
2. Equality on the basis of respective types.

Notes:

1. NaN !== NaN
2. symbols and objects only match by reference

Link: https://262.ecma-international.org/12.0/#sec-strict-equality-comparison

## SameValue Comparison Operation

It is similar to Strict Equality Operation except for following:

1. +0 is not equal to 0
2. NaN is equal to NaN

## SameValue Comparison Operation

It is similar to Strict Equality Operation except for following:

1. NaN is equal to NaN

Link: https://262.ecma-international.org/12.0/#sec-samevalue

## References

1. Visual chart to see `==` vs `===`: https://dorey.github.io/JavaScript-Equality-Table/unified/
2. Standard: https://262.ecma-international.org/12.0
3. MDN article on equality: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness#same-value_equality
