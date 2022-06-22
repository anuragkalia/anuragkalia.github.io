---
layout: post
title: "CSS Selectors: Notes from Specifications"
date: 2022-05-05 12:00:00 +0530
---

CSS Selectors:

Two main things

1. Latest selector list: https://drafts.csswg.org/selectors/#overview

Notes:

1. Selector Lists
2. Combinators: `space` (subsequent descendent), `>` (direct descendent), `+` (direct sibling), `~` (subsequent sibling)
3. Columns combinators:
   1. `A || B`: Cell element `B` that falls under `A` columns. e.g. `col.selected || td`.
   2. `:nth-col(An+B)`: Cell element falling under An+B column
   3. `:nth-last-col(An+B)`: Cell element falling under An+B column counting from the end
4. Elemental selectors: tag name (e.g. `p`), `*`, `ns|tagname`, `:defined`
   1. Defined elements are host language specific. Used to distinguish undefined custom elements in HTML.
5. Attribute Selectors:

   1. `.class` and `#id`: class and id selectors
   2. `[att]`: presence of attribute
   3. `[att=val]`: presence of attribute and exact value `val`
   4. `[att~=val]`: presence of attribute and space delimited exact value `val`. Useful for classes: `<div class="val1 val2 val3">`.
   5. `[att|=val]`: presence of attribute and starting from `-` delimited value `val`. Useful for languages and regions: `<div lang="en-US">`.
   6. `[att^=val]`, `[att$=val]`, `[att*=val]`: Regex substring matching: beginning, ending and anywhere matching respectively
   7. Note: case-sensitivity depends on host language. For example, `class` is case sensitive whereas `lang` is not because of HTML rules. If we want to force case sensitivity, we can say `[att=val i]` or `[att=val s]`.

6. Special Selectors:
   1. `:dir(rtl)`, `:lang(zh, "*-Hant")`: Matches elements with given directionality and languages. They need not be provided explicitly in the DOM tree and can be inferred based on HTML rules.
   2. Links:
      1. `:link`, `:visited`, `:any-link`: Visited, unvisited, any and current page links respectively.
         1. We use `:any-link` because `<a>`, `<area>` and `<link>` all of them can be hyperlinks if `href` is specified for them.
      2. `:local-link` and `:target`: Local links and target within the current document respectively.
      3. `:scope`: Root of the current scope.
         1. Equals `:root` for stylesheets.
         2. Equals the element when used in APIs like `element.querySelector`.
   3. `:hover`, `:active`, `:focus`, `:focus-visible`, `:focus-within`:
      1. Active: Activated to receive focus (e.g. tab to a button)
      2. Focus: Focused to received input. (e.g. press enter on the activated button before)
      3. Focus visible: Subset of focus instances, when UA decides to use an explicit focus ring on the focused element
      4. Focus within: Focused element + all the descendents of focused element
   4. `:current`, `:past`, `:future`: The past, present and future in some form: e.g. speech rendering of document OR subtitles shown in the video.
   5. `:playing`, `:paused`, `:seeking`, `:buffering`, `:stalled`, `:muted`, `:volume-locked`: Various audio/video states
   6. Input selectors:
      1. State: `:enabled`, `:disabled`, `read-only`, `:read-write`, `:placeholder-shown`, `:default`
      2. Value: `:checked`, `:indeterminate`
      3. Validity: `:blank`, `:valid`, `:invalid`, `:in-range`, `:out-of-range`, `:required`, `:optional`, `:user-valid`, `:user-invalid`
   7. Structural
      1. `:root`, `:empty`
      2. `:nth-child(An+B)`, `:nth-last-child(An+B)`, `:first-child`, `:last-child`, `:only-child`
         1. `:nth-child(2n+1 of li.important)` is another form where `of` limits the siblings considered in the calculation.
      3. `:nth-of-type(An+B)`, `:nth-last-of-type(An+B)`, `first-of-type`, `last-of-type`, `:only-of-type`: Similar to previous, but limited only to siblings of the same type.
7. Specificity
   1. Number of id selectors
   2. Number of class selectors, attribute selectors and pseudo classes
   3. NUmber of tag name selectors and pseudo elements

Implementation details:

1. The whole rule is discarded if anything in it is invalid.
2. Selector matching in DOM:
   1. RTL for selectors in combinators
   2. id > class > tag > universal in terms of efficiency
      1. RTL rule does not trump id matching: `#bomb a` still matches `a` first and then `#bomb`.
   3. Less the number of combinators, the better.
