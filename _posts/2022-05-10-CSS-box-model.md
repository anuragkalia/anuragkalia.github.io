---
layout: post
title: "CSS BOx Model: Notes from Specifications"
date: 2022-05-10 12:00:00 +0530
---

# Without padding, border and margins

## Normal Flow (flow positioning)

display: none | inline | block | list-item | inline-block | table | inline-table | table-\*

block container box: block, list-item, inline-block
block level box: block, list-item, table

### Anonymous block boxes

Siblings can be all inline leve or block level boxes. Text node is counted as inline box.

Mixed inline and block siblings are handled by adding anonymous block boxes to the inline siblings. Its properties are computed from the parent in the box tree, not the element tree.

## Generate Box Tree from Element Tree

1. Add principal boxes and other boxes based on the value of `display`.
   1. `none`: delete everything.
   2. `contents`: visually behave as if the box is replaced by its children boxes in the box tree.
   3.
2. Add anonymous block and inline boxes to make sure siblings are all inline or all block.
3. Give values to padding, border and margin.

## Find formatting contexts

1. Figure out new formatting contexts of the subtrees. Synonymous with layout in that subtree. Can be one of the following. Computed based on `display-inside` value:
   1. block formatting context
   2. inline formatting context
   3. flex formatting context
   4. grid formatting context
2. Blockification and inlinefication.

# Relevant Properties

1. `padding`:
   1. zero or positive.
   2. Percentage of top/bottom/left/right resolved wrt logical width of containing block.
2. `border`: Same as `padding`
3. `margin`: Same as `padding` +
   1. Negative is possible.
   2. `auto` is possible.

## Position the Box Tree

For the vast majority of the elements, `display` property is the primary property to generate the box tree from the element tree. Box tree is the hierarchy of visual boxes generated from the element tree.

1. `display: none`: The element and its children are removed from the box tree.
2. `display: contents`: The element itself is removed from the box tree and replaced by its children under its parent. This does not affect semantics and inheritance.
3. display outside: Participates in the block formatting context as a block or inline box. Force converted to block in flex and grid formatting contexts.
4. display inside:
   1. `flow`: Establishes normal flow.
      1. Conditionally establishes a new block formatting context if other proerties force it (e.g. `float`, `position`, `overflow` etc.).
      2. Optional `list-item`: Also generates an optional marker box addressed by `::marker`.
   2. `flow-root`: Just like `flow` + ALWAYS generates a new block formatting context by itself.
   3. `table`: Establishes table formatting context. Ignored for this doc.
   4. `flex`: Establishes flex formatting context. Ignored for this doc.
   5. `grid`: Establishes grid formatting context if not a subgrid. Ignored for this doc.
   6. `ruby`: Ignored completely. by us. :D

# Layout of the element tree

Includes position and bounding rects for content, padding, border and margin for all the boxes

## Basic scenario: display inside = `flow`, no `float`, no `position`, no replaced elements

Box tree generation:

1. Start with mirroring the element tree
2. Anonymous block boxes: For every block, any inline sibling is surrounded by anonymous block box.
3. Direct text within blocks are surrounded by anonymous inline boxes.

Width:

`width` property = width of the content area

1. Inline, non-replaced: Based on contents.
   1. 'width' => width of the contents, `auto` margins => 0
2. Block, non-replaced: Based on containing block.
   1. The width equation is defined as:
      1. 'margin-left' + 'border-left-width' + 'padding-left' + 'width' + 'padding-right' + 'border-right-width' + 'margin-right' = width of containing block.
   2. The width equation should be true at ALL times.
   3. `auto` width and margin are resolved as:
      1. If 'width' = `auto`, `auto` margins => 0, 'width' => using the width equation.
      2. If 'width' is not `auto`,
         1. If LHS of width equation excluding `auto` margins > RHS, `auto` margins => 0.
         2. If single margin is `auto`, it is calculated using the width equation.
         3. If both margins are `auto`, both of them are equal and calculated using width equation.
   4. After step 3all the `auto` values are resolved. None of them is computed to be less than zero. Now, if the width equation is not satisfied, we ignore the existing value of `margin-right` and make it a free variable to satisfy the width equation (including making it negative).
3. inline-blocks, non-replaced:
   1. if 'width' = `auto`, 'width' => shrink-to-fit width
      1. shrink-to-fit width = min(max(preferred minimum width, available width), preferred width)
      2. where:
         1. available width = width of containing block - padding - border - margin - scrollbars etc.
         2. preferred width = max width of content: no line breaks
         3. preferred minimum width = min width of content: all line breaks
   2. else, 'width' is used as is.

Height:

This is trickier because content expansion happens in this direction.

1. Inline, non-replaced elements:
   1. If 'height' =
2. Block, non-replaced:
   1. `auto` margin => 0
   2. `auto` height:
      1. inline formatting context:
         1. till bottom of the last line box.
      2. block formatting context:
         1. if bottom margin does NOT collapse with ( bottom ) margin of last in-flow child:
            1. till the child's bottom margin edge
         2. if bottom margin does NOT collapse with ( top ) margin of last in-flow child:
            1. till the child's bottom border edge
            2. Note: this implies all children are of zero height.
         3. else ZERO

# Collapsing Margins

Adjoining margins:

1. Belong to same block formatting context
   1. Note: margins of an element with BFC belong outside it
2. No line boxes, clearance, padding or border separate them
3. Both belong to vertically adjacent boxes:
   1. top margin of a box and top margin of its first in-flow child
   2. bottom margin of a box and top margin of its next in-flow sibling
   3. bottom margin of a box and bottom margin of its last in-flow child AND 'height' = `auto` for the parent
