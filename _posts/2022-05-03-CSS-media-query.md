---
layout: post
title: "CSS Media Queries: Notes from Specifications"
date: 2022-05-03 12:00:00 +0530
---

Summary of CSS media Query

Ref standard: https://www.w3.org/TR/mediaqueries-4/#intro [Draft standard]

Used as:

1. @media screen { ... }
2. @import "print-styles.css" print
3. <link rel="stylesheet" type="text/css" media="screen" href="style.css">

Examples:

1. only screen and (color), projection and (color)
   1. (screen AND color) OR (screen and color)
   2. Older: projection
      1. Ignores the first rule because of only
2. not screen and (color)
   1. NOT (SCREEN AND COLOR) = NOT SCREEN OR NOT COLOR
   2. Older: ignored = nested rules are ignored too
3. (color)
   1. All color media types
   2. Older: ignored

Media Types:

1. Supported: print, screen, all
2. Deprecated: all others. they are ignored.

Media Features:

1. width
2. height
3. aspect-ratio
4. orientation
   1. portrait | landscape
5. resolution
   1. <number> dpi/dppx/dpcm | infinite
6. scan
   1. interlace | progressive
7. grid
   1. 0 | 1
8. update
   1. none | slow | fast
9. overflow-block
   1. none | scroll | paged
10. overflow-inline
    1. none | scroll
11. color
12. color-index
13. monochrome
14. color-gamut
    1. srgb | p3 | rec2020
15. pointer / any-pointer
    1. none | coarse | fine
16. hover / any-hover
    1. none | hover
17.
