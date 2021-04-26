---
title: "redpwnCTF 2020 Reversing"
date: 2020-06-29T14:16:00+00:00
weight: 9997
# aliases: ["/first"]
tags: ["writeup", "reversing", "reversing"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "Writeups on reversing challenges for redpwnCTF 2020"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

<img src="/img/reverse.png">

---

## bubbly
**Category:** Rev

**Points:** 395

**Description:**
> It never ends
>
> `nc 2020.redpwnc.tf 31039`
>
> **Author:** dns
>
> **Given:** bubbly

### Writeup
The first thing I did here was run the program. Seems like we have to do some
kind of sorting.
```
$ ./bubbly
I hate my data structures class! Why can't I just sort by hand?
1
2
32
Try again!
```

After playing with it for a minute or so, I decided to open it up in **Ghidra**.
Looks like we have the methods **main**, **check**, and **print_flag**:

**main:**
```
int main(void) {
  uint32_t i;
  int unused;
  _Bool pass;

  setbuf(stdout,(char *)0x0);
  setbuf(stdin,(char *)0x0);
  setbuf(stderr,(char *)0x0);
  puts("I hate my data structures class! Why can\'t I just sort by hand?");
  pass = false;
  while( true ) {
    __isoc99_scanf(&DAT_00102058);
    if (8 < i) break;
    nums[i] = nums[i] ^ nums[i + 1];
    nums[i + 1] = nums[i + 1] ^ nums[i];
    nums[i] = nums[i] ^ nums[i + 1];
    pass = check();
  }
  if (pass == false) {
    puts("Try again!");
  }
  else {
    puts("Well done!");
    print_flag();
  }
  return 0;
}
```

**check:**
```
_Bool check(void) {
  uint32_t i;
  _Bool pass;

  i = 0;
  while( true ) {
    if (8 < i) {
      return true;
    }
    if (nums[i + 1] < nums[i]) break;
    i = i + 1;
  }
  return false;
}
```

**print_flag:**
```
void print_flag(void) {
  int unused;

  system("cat flag.txt");
  return;
}
```

Seems like the goal here is to reach this **print_flag** method, but we have to
find out how. The while loop in the **main** method seems to be taking an already-defined
array and swapping two of the values.
```
while( true ) {
  __isoc99_scanf(&DAT_00102058);
  if (8 < i) break;
  nums[i] = nums[i] ^ nums[i + 1];
  nums[i + 1] = nums[i + 1] ^ nums[i];
  nums[i] = nums[i] ^ nums[i + 1];
  pass = check();
}
```

The check method being called at the end of the while loop pretty much just makes
sure this array is *sorted*. It looks like the only way to break out of this loop
is to enter a number greater than 8. Let's take a look at our array:
```
nums
00104060 [0]    1h,   Ah,   3h,   2h
00104070 [4]    5h,   9h,   8h,   7h
00104080 [8]    4h,   6h
```

nums = [1, 10, 3, 2, 5, 9, 8, 7, 4, 6]

Now, our job here is to sort the array by calling an index of the array for the
program to swap that index with the index (i) with the index after it (i+1).
There are many routes to take for sorting this, but here is mine (the bold values
are the *swapped* values):

  *0  1  2  3  4  5  6  7  8  9 --- index values*

[1, 10, 3, 2, 5, 9, 8, 7, 4, 6] --- original

[1, **3**, **10**, 2, 5, 9, 8, 7, 4, 6] --- (enter "1")

[1, 3, **2**, **10**, 5, 9, 8, 7, 4, 6] --- (enter "2")

[1, 3, 2, **5**, **10**, 9, 8, 7, 4, 6] --- (enter "3")

[1, 3, 2, 5, **9**, **10**, 8, 7, 4, 6] --- (enter "4")

[1, 3, 2, 5, 9, **8**, **10**, 7, 4, 6] --- (enter "5")

[1, 3, 2, 5, 9, 8, **7**, **10**, 4, 6] --- (enter "6")

[1, 3, 2, 5, 9, 8, 7, **4**, **10**, 6] --- (enter "7")

[1, 3, 2, 5, 9, 8, 7, 4, **6**, **10**] --- (enter "8")

[1, **2**, **3**, 5, 9, 8, 7, 4, 6, 10] --- (enter "1")

[1, 2, 3, 5, 9, 8, **4**, **7**, 6, 10] --- (enter "6")

[1, 2, 3, 5, 9, **4**, **8**, 7, 6, 10] --- (enter "5")

[1, 2, 3, 5, **4**, **9**, 8, 7, 6, 10] --- (enter "4")

[1, 2, 3, **4**, **5**, 9, 8, 7, 6, 10] --- (enter "3")

[1, 2, 3, 4, 5, 9, 8, **6**, **7**, 10] --- (enter "7")

[1, 2, 3, 4, 5, 9, **6**, **8**, 7, 10] --- (enter "6")

[1, 2, 3, 4, 5, **6**, **9**, 8, 7, 10] --- (enter "5")

[1, 2, 3, 4, 5, 6, 9, **7**, **8**, 10] --- (enter "7")

[1, 2, 3, 4, 5, 6, **7**, **9**, 8, 10] --- (enter "6")

[1, 2, 3, 4, 5, 6, 7, **8**, **9**, 10] --- (enter "6")

**nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]**

And now it's sorted! Let's get the flag:
```
$ nc 2020.redpwnc.tf 31039
I hate my data structures class! Why can't I just sort by hand?
1
2
3
4
5
6
7
8
1
6
5
4
3
7
6
5
7
6
7
999999
Well done!
flag{4ft3r_y0u_put_u54c0_0n_y0ur_c011ege_4pp5_y0u_5t1ll_h4ve_t0_d0_th15_57uff}
```

### Flag
`flag{4ft3r_y0u_put_u54c0_0n_y0ur_c011ege_4pp5_y0u_5t1ll_h4ve_t0_d0_th15_57uff}`

### Resources
[Ghidra](https://ghidra-sre.org/)

---

## r1sc
**Category:** Rev

**Points:** 487

**Description:**
> Look, Mum, no opcodes!
>
> **Author:** imyxh
>
> **Given:** r1sc

### Writeup
First thing I do here is run the program. Nothing much besides one input.
```
$ ./r1sc
Enter access code: test
Access denied.
```

Next thing I did was open this sucker up in Ghidra to see what we are working
with. I couldn't understand fully what this program was doing cause im a n00b, but
it seems like based off the input, its either access denied, or something else.
From our one trial run above, I'm sure we want the other route.
```
undefined  [16] entry(undefined8 param_1) {
  char *pcVar1;

  FUN_00101068(param_1,&DAT_00103001);
  syscall();
  FUN_0010107e(0,0,0x30);
  if (ram0x00103038 == 0) {
    pcVar1 = &DAT_00103015;
    FUN_00101068();
  }
  else {
    pcVar1 = s_Access_denied._00103029;
    FUN_00101068();
  }
  syscall();
  syscall();
  return CONCAT88((ulong)(byte)pcVar1[-1],1);
}
```

*UPDATE* - This is **probably** not the intended route for this challenge

I found this cool tool that I guess I've been missing out on called **angr**. To
my understanding, this is basically a brute-forcer for binaries. Maybe that's not
*exactly* the purpose of it, butttttt that's sure as hell what we are going to
use it for.

The code is pretty simple for **angr**. After importing the necessary modules,
all you really have to do is pick the spot(s) where you want the program to hit
during execution (like printing the flag) and pick the spot(s) where you want the
program to avoid. In our case, we want to avoid the access denied **(0x0040103b)**.

I inspected the program in Ghidra to find an address I want it to run so that I
could, you know, get the flag. I picked the point where the if statement is passed
and not into the else where the access denied part is **(0x00401050)**.

Here is the script I created for **angr**. I will also provide resources and videos
on **angr** if someone needs them. This took about 15-20 minutes to run.
```
import angr

p = angr.Project("r1sc")
good = 0x00401050
bad = 0x0040103b

sm = p.factory.simulation_manager()
print(sm.explore(find=good, avoid=bad))

for f in sm.found:
    print(f.posix.dumps(0))
    print(f.posix.dumps(1))
```

**Output:**
```
$ python3 angry.py
WARNING | 2020-06-26 21:14:39,371 | cle.backends.elf.elf | Segment PT_LOAD is empty at 0x002000!
WARNING | 2020-06-26 21:14:39,372 | cle.loader | The main binary is a position-independent executable. It is being loaded with a base address of 0x400000.
<SimulationManager with 5454 active, 1 found, 6 avoid>
b'flag{actually_3_instructions:_subleq,_ret,_int3}'
b'Enter access code: '
```

https://www.youtube.com/watch?v=kzoeRIf4hVs

### Flag
`flag{actually_3_instructions:_subleq,_ret,_int3}`

### Resources
[Video using angr](https://www.youtube.com/watch?v=9dQFM5O4KFk&list=PL-nPhof8EyrGKytps3g582KNiJyIAOtBG)

[Lecture on angr](https://www.youtube.com/watch?v=XgHZ6QnZkgc)

[Lots of examples from past CTFs](https://docs.angr.io/examples)

[angr documentation](https://github.com/angr/angr)

---

