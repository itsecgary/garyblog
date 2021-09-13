---
title: "  HSCTF 2020 Algorithms"
date: 2020-06-06T14:16:00+00:00
weight: 9999
# aliases: ["/first"]
tags: ["writeup", "algorithms", "programming", "hsctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "A writeup of algorithm challenges for HSCTF 2020"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

## Pythagorean Tree Fractal 1
**Category:** Algorithms

**Points:** 100

**Description:**
> Please see the attached file for more details (and ignore the red dots on the images).
>
> **Author:** Plate_of_Sunshine
>
> **Given:** pdf named "Pythagorean_Tree_Fractal"

### Writeup
This algorithm was pretty simple to make. After looking at the PDF given to us,
we can see that our goal is to see how many rectangles there are at stage 50!

Here is the math:
```
Stage 1 = 1 square
Stage 2 = 3 square
Stage 3 = 7 square

Number of rectangles at stage "x" = ((the number of rectangles from last stage)*2) + 1
```

I built a simple script to iterate through and give me the number I should put
in the flag since that is the format they want us to put it in.

### Flag
`flag{2251799813685245}`


---


## Pythagorean Tree Fractal 2
**Category:** Algorithms

**Points:** 100

**Description:**
> Because every good thing must have a sequel ;)
>
> Please see the attached file for more details (and ignore the red dots on the images).
>
> Note: Don't worry about overlapping squares!
>
> Author: Plate_of_Sunshine
>
> **Given:** pdf named "PTF2.pdf"

### Writeup
From the pdf given, we are given an area of the square in Stage 1
(**70368744177664**). We are also given an objective to find the total area
at stage 25, which includes all of the branches squares as well.

I assumed that the triangle made between the squares was an isosceles triangle.
This tells us the two sides of the branched squares are the same in each step.
The isosceles triangle shown in the PDF tells us the angles are 90, 45, and 45.
Here's the math:
```
side_stage_1 = sqrt(70368744177664)
θ = 45 deg
cos(θ) = adj/hyp
cos(45) = side_stage_1 / (2 * side_next)
side_next = side_stage_1 / (2 * cos(45))
```

Doing this repetitively and adding up all of the areas of each stage will bring
us to the answer. My script brought it to me in scientific notation, but I was
too lazy to change my script.

```
Total Area at 25 = 1.29902254294e+15
1.29902254294e+15
```

### Flag
`flag{1299022542940000}`


