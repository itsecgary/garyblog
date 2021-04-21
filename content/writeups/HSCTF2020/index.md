---
title: "HSCTF2020"
date: 2020-05-06T15:07:00+00:00
# weight: 1
# aliases: ["/first"]
tags: ["ctf", "writeup", "compete", "hsctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "A writeup of some challeges from HSCTF2020"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
    image: "hsctf2020_logo.png" # image path/url
    alt: "HSCTF2020 Logo" # alt text
    caption: "" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: false # only hide on current single page
---

**Dates:**
June 1st - June 5th 2020

**Team Members:**
- itsecgary
- pwang00

https://ctftime.org/event/939

[pwang00's writeups](https://github.com/pwang00/CTF-Writeups/tree/master/2020/hsctf)


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
flag{2251799813685245}


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
flag{1299022542940000}


---



## Chonky E
**Category:** Crypto

**Points:** 100

**Description:**
> **E**
>
> **Note:** P>Q
>
> **Author:** AC
>
> **Given:** ChonkyE.txt

### Writeup
From the text file given, we see two different cryptosystems in play: RSA and
Schmidt-Samoa. We are given the ciphertext encrypted by the Schmidt-Samoa
cryptosystem and the **n** and **e** from the RSA cryptosystem. I have given
links to the Wiki pages for both cryptosystems below.

Schmidt Samoa encryption is very similar to RSA encryption. The main two
differences is the **e** *(encryption key)* and **n**.

I'm not sure about any other ways to get this information, but I used
**RsaCtfTool** to get the *p*, *q*, and *d* for the respective *e* and *n*.

Here is the output of the program:
```
~/ctfs/hsctf2020/crypto/samoa$ python3 ~/tools/RsaCtfTool/RsaCtfTool.py -n 156749047558583013960513267351769479915110440411448078412590565797031533622509813352093119636835511977253033854388466854142753776146092587825440445182008237325262012698034419137157047927918635897378973846177552961727126115560551970797370239385129543828686170774323306933202481728884019420422360360849592983818405154473369790181636472137741865440233383956571081122982223602667853668754338360008279002325576495573847568301584365514417593244726435632222027817410359417329310347952169273512510934251453361933794586716533950489973436393834189505450956622286216819440777162804798432330933357058175885674184582816364542591313 -e 91043118409828550796773745518585981151180206101005135117565865602978722878478494447048783557571813980525643725323377488249838860897784683927029906188947001149632101513367258267329961684034661252866484981926055087386190015432964608927947646476193251820354738640453947833718397360834701566765504916472450194494897616371452996381159817427887623703639133290358520498419049175941584678802701606995099241245926884172985004839801270005583030514286561971825047719421487004569752638468907609110285739083279629747310953086535889932550905065172805818862336335628248528993024112446002398466115161473573451161053837400091893285717 --private

[*] Testing key /tmp/tmpgs29c3l6.
Can't load boneh_durfee because sage is not installed
Can't load ecm because sage is not installed
Can't load ecm2 because sage is not installed
Can't load qicheng because sage is not installed
Can't load roca because sage is not installed
Can't load smallfraction because sage is not installed
[*] Performing comfact_cn attack on /tmp/tmpgs29c3l6.
[*] Performing cube_root attack on /tmp/tmpgs29c3l6.
[*] Performing factordb attack on /tmp/tmpgs29c3l6.
[*] Performing fermat attack on /tmp/tmpgs29c3l6.
[*] Performing londahl attack on /tmp/tmpgs29c3l6.
[*] Performing mersenne_primes attack on /tmp/tmpgs29c3l6.
[*] Performing noveltyprimes attack on /tmp/tmpgs29c3l6.
[*] Performing partial_q attack on /tmp/tmpgs29c3l6.
[*] Performing pastctfprimes attack on /tmp/tmpgs29c3l6.
[*] Performing pollard_p_1 attack on /tmp/tmpgs29c3l6.
[*] Performing primefac attack on /tmp/tmpgs29c3l6.
[*] Performing siqs attack on /tmp/tmpgs29c3l6.
[!] Warning: Modulus too large for SIQS attack module
[*] Performing smallq attack on /tmp/tmpgs29c3l6.
[*] Performing wiener attack on /tmp/tmpgs29c3l6.

Results for /tmp/tmpgs29c3l6:

Private key :
-----BEGIN RSA PRIVATE KEY-----
MIIEXwIBAAKCAQEE2bEPIRfdrVNyfmzmWZaB9ydQSeEAV+BqZicqXDhPj2wfJZg2
r1BGm2bFYOfWmZ7XBxzPX/jhmeKGrRgRjSoom3aGG3Pxvrs7Ld6c7uPitwJpXMvX
akLyyfBfeZ4vi2kusdxmw7OuPqnoLYC7neO7LHjfNGQdxLkU5kjbie2RWb2n9opJ
HYoJsNLeB7SysRUWrh4PSyerquUzBnipxATHXdWkUo2IU6XsxJUSLLmdMO9m9B3e
/B7EiNGADI4SYf0+1DgiatfsI4sG72N7MHc7uosPotJP4fm+bUBa3GvBiZjF1ijF
HF3Ysfc0WRWlHryJktKdbHiS7vOGORXuZ/iBUQKCAQEC0TNOpXV3GyrNfiwp0CyX
jUhp9t4ZDkSyKQmykstZ4rI2sGBk1Va9CfuQdz2pgObbN3/I8EVdn0L1k2+hkeUa
7VArlFTAd/2TQmVK9kcqqZPzRSQ0U1zh9nqJB8WGq4C7MtJ1jKPYLi5A9EhyJb18
TEQ5vWreS5D/zaRmcVPyGFFizV2eu5rhWRe8WMbin63tCckmwbo24Dsg/9UHs/8r
EuuKXa6gjTrsvnh8a6xcSkOLo8WwhTrW+M/+BnW/BuMaYLeAid4Pk1UWDTrFceP/
kUUA1x/iPafhvo5FnHZT8aJ/bUAl9CqK1h7wjSz5ArnSiCXR3l9Mjy5e1q7VOYHD
VQJAW67Pb58KG9KV6WULShC0pxfbAwIj6AP46WShirm8/AlUqPQQzAAXetn2oNWB
4Sxt/QZy3W5cPvN+EI6R05uk/QKBgQHiep/6oT3VUgLU1e+048tZqumrGHhQzOzr
E60AVmh1uNwGazrIyM+fWvItgXveXJA60gFCBUyz6nn8r8j3jKiPrtGZTZ8zN6qW
66Y3sWSXB3JjI/OiHHIaFM2sJl9lqYCRZDVWz8M3gx3bE4IeFp47fjcd83J/36hq
f1vVWTg7iQKBgQKS1TtgUTN8XMo4qHP0sf161qhoBj5ICe/2WL+q6To9uYeZ/7BQ
R7cwvlpB7J9ViGrLe5HBDiomprMo5ABY4K0biQ4udRCMB9LxvlUpkRhnTWbKVlD1
aVjrXX06zoc0o56T/YxnaPpZeO0d2KzEBergGrqMgSVlD/Xl3SM0u+k9iQJAW67P
b58KG9KV6WULShC0pxfbAwIj6AP46WShirm8/AlUqPQQzAAXetn2oNWB4Sxt/QZy
3W5cPvN+EI6R05uk/QJAW67Pb58KG9KV6WULShC0pxfbAwIj6AP46WShirm8/AlU
qPQQzAAXetn2oNWB4Sxt/QZy3W5cPvN+EI6R05uk/QKBgQHbAH/3Q83q2VcaB1MF
uroXJsMXRHbDwBLofgBb480QW1MFsoPS4Ac0O6OgvYh6agstMle5CYyf1qJtncXR
hV+4FqmRF+30r8uH/475p9pLNIN8rTXIQO/sG8Qw0A+AHYGjKVCr530Ncwf6m/Om
KAdcdl95Bui9gj9QyqDAwCDf2Q==
-----END RSA PRIVATE KEY-----
```

From the private key, we can get our *d*, *p* and *q*:
```
~/ctfs/hsctf2020/crypto/samoa$ python3 ~/tools/RsaCtfTool/RsaCtfTool.py --dumpkey --key priv.key
private argument is not set, the private key will not be displayed, even if recovered.
n: 156749047558583013960513267351769479915110440411448078412590565797031533622509813352093119636835511977253033854388466854142753776146092587825440445182008237325262012698034419137157047927918635897378973846177552961727126115560551970797370239385129543828686170774323306933202481728884019420422360360849592983818405154473369790181636472137741865440233383956571081122982223602667853668754338360008279002325576495573847568301584365514417593244726435632222027817410359417329310347952169273512510934251453361933794586716533950489973436393834189505450956622286216819440777162804798432330933357058175885674184582816364542591313
e: 91043118409828550796773745518585981151180206101005135117565865602978722878478494447048783557571813980525643725323377488249838860897784683927029906188947001149632101513367258267329961684034661252866484981926055087386190015432964608927947646476193251820354738640453947833718397360834701566765504916472450194494897616371452996381159817427887623703639133290358520498419049175941584678802701606995099241245926884172985004839801270005583030514286561971825047719421487004569752638468907609110285739083279629747310953086535889932550905065172805818862336335628248528993024112446002398466115161473573451161053837400091893285717
d: 4801820624110300567381264152630360984400101198006662778338105999190025449039653722546363948393959163699344836724430590700225590643966670154013435626235133
p: 338808278305491368568107597536870102903517054340801660200304926784154444523223906451699772927968482815828890482348342203845897909840260655384526983598744312581591533978845446602589686620835190303243711955190856932946979670202446096542521271004217036632261094082852110229243380763789393081471800046961479400329
q: 462648222004918001013626929700851985161214529015962355517097297750332107059692278343607439888140451722661722449586909096508950271217838478793469222136256780856060573039970361424138955569021582604733404145398646735820327194382610835536537670219091779958808528053471059443883883244638910795974245528935198178697
```

From the note at the beginning of this problem, we see that **P>Q**, meaning we
need to switch our P and Q here.

Since we know the ciphertext is encrypted with the Schmidt-Samoa cryptosystem,
we have to calculate a new *N* and *e* for this system. The *N* and *e* will be
the exact same in this cryptosystem.

My script calculates the encryption key, then uses modular inverse to find the
decryption key and uses it on the cipher text:
```
~/ctfs/hsctf2020/crypto/samoa$ python3 samoa-crypto.py

[*] Decrypting Schmidt-Samoa Ciphertext
[+] flag: flag{remarkably_superb_acronym}
```

### Flag
flag{remarkably_superb_acronym}

### Resources
- https://en.wikipedia.org/wiki/Schmidt-Samoa_cryptosystem

- "https://en.wikipedia.org/wiki/RSA_(cryptosystem)"

- https://www.wolframalpha.com/


---


## Unexpected
**Category:** Crypto

**Points:** 100

**Description:**
> Alice, Bob, and Carol are really close friends; in fact, they are so close, they even share the same primes in their RSA public keys! Alice > has N = PQ, Bob has N = QR, and Carol has N = PR, where P,Q,R are 1024 bit primes. All three also use the same public exponent e = 65537.
> Can you recover the three plaintexts?
>
> **Author:** AC
>
> **Given:** unexpected.txt

### Writeup
From the description and the text file given, we can see that this is some
twisted version of RSA. If you are unfamiliar with RSA, hang on because this
isn't too easy if you don't understand it yet. I encourage you to read the Wiki
I put in the resources as well as other websites giving examples.


Basically, RSA usually has an **N = P*Q**, but this is a little different. We
have three different ciphertexts and three different N's to deal with.


So I am assuming that the following below matches respectively to the .txt file
given.

<img src="https://render.githubusercontent.com/render/math?math=\large N_{1} = P \cdot Q">

<img src="https://render.githubusercontent.com/render/math?math=\large N_{2} = Q \cdot R">

<img src="https://render.githubusercontent.com/render/math?math=\large N_{3} = P \cdot R">


Since we have three equations and three knowns, we can probably work this out.
Here's the math:

<img src="https://render.githubusercontent.com/render/math?math=\large Q = \frac{N_{2}}{R}">

<img src="https://render.githubusercontent.com/render/math?math=\large P = \frac{N_{3}}{R}">

<img src="https://render.githubusercontent.com/render/math?math=\large N_{1} = \frac{N_{3}}{R} \cdot \frac{N_{2}}{R}">

<img src="https://render.githubusercontent.com/render/math?math=\large N_{1} = \frac{N_{3} \cdot N_{2}}{R^2}">

<img src="https://render.githubusercontent.com/render/math?math=\large R^2 = \frac{N_{3} \cdot N_{2}}{N_{1}}">

<img src="https://render.githubusercontent.com/render/math?math=\large R = \sqrt{\frac{N_{3} \cdot N_{2}}{N_{1}}}">


Following these steps to isolate P and Q can give us similar answers. You also
could divide the second N by R to get Q and the third N by R to get P.

I fiddled around with the math in **Wolfram Alpha** and Python to get the exact
P, Q, and R. I had troubles formatting in Python for a while, so I don't have my
implementations on how I got P, Q, and R, but I will include them in my script.

Since we had the P's, Q's, N's, C's, and E for all three parts, all we have to
do is find the decryption key (D), which found by modular arithmetic:

<img src="https://render.githubusercontent.com/render/math?math=\large\lambda(N)= lcm(P, Q)">
<img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/0cfb326fe7911a816ba3afecf0b4d469c48e9095">


In my script, I copied and pasted a few functions to help find the modular inverse.


**Here is the output to my script:**
```
-----------------------------------------------------------------------------------------------------------------
phi: 3895738302299059518129198422310169628530536557191890566210939781698372336257482186582163630847612416277492034959243510457939210010336159061758606919109259916143600981918456942199762738624796190838889500238780675229383463267807384154074134251073572174392024892486431125499446924573006208711810847272390619510267715478341382535579105584675673997982203778973283813503737131926142658191419941161164257547319794943883811280878885096660672763345495601914053572772464688944398278968911503771601753848356268487278305957060424301523898709351034955902146951194283368812207947125965045248566943026323694697043787381033801270628
d: 905024881402920200245922852429502915824304119554549855357455455336034893564248688385392088128155073284172547297778086374446869286164579118898863090215275069400282663986885376886207603270862596785359303006476277223055117387923881528690338190222242952150976989915710421376155140250911996698617882260740454736161648658891123321241312274389842937871416948210449762128177942743420082868675230849424383495703860079354120981298823955882306831590325625816584000571597340268527149507937159847454669916859547854171112626397988311804039822540999850521235139416405454783799471977551883881005107154367429875680785859533387618373
flag 1: flag{n0_0n3_3xp
-----------------------------------------------------------------------------------------------------------------
phi: 3036683903819675505741091164945461947189004916494633766372176282409409694958701211748277050499101511956962003835932755555293255586827283990400451317444723234406968971873530093281591689832798646915816609347861047534121792409030834659241904646743453387504496246791081682741245482378149293399372654558929658581960048798293605082264581070963505109295072607249172619834489529823110742182685123132307321795995115486491236524357398246542818317495137394745528263555605271324314200817069662802883230041385249726748989486591785200139057355527310020359454945749706230370552400570507369464671540537160407575553116627107873474612
d: 1949006439818224984329272283917496465883563693829650123058926270152539786670855380468556350994761842889447072147807185207169998775481270831564065852353287355369643649601237412663130617047423737510413873674398877617647511392054320433826574837968931746015252843870993008852175240326412465145762118630884206309916314945795869670190809368569478545131431696274195460703085785021430751307357436780967131188564361241867660740101701976659452933286509364712775307840447755126335172939994220462909155213555514583460377264362272622693275089576044698206810708196437022873743772604746196527037841366168396842249275137495437416461
flag 2: 3ct5_th3_sp4nis
-----------------------------------------------------------------------------------------------------------------
phi: 4793455677299549137382284585015750073239112414361680529255951318217960300841340399094743130287927996565298160174555422185410320841942637374406558835150138631140265626020072464652973386772727192540062051929655235552439145036105501434801984612127808829810146844869487529177642676245549299371487478280457673839585943066849022337520545603509204219821850868445721914238164436588614152654669526278241319272348057855708723349268650822215550667738881235874405982991956631703034012350958220927976873112789326373350047289917675374669209722838179591673306110922540875421581379673701918694867142296015241867468105452451023716116
d: 2833344309736941490820680535488962362285987102057506146792148590340225309885894141937106663123025996474457483998991835827371179772879043694550578698384821860828411581561035249335900072581929383167018993357202565189593475447589223104221421180472207431667385880900166438886179135334240028671631016580380081010694420900004538311348870036601302364579685657595715023784396813167672231648329769580031314005404871526228611701847338076979202614811004185655473075806680440040162527586728406279017472735145534965757268900267496355706484066150501259170247831376434509240295092631641132892896582972413116857072790469765899518057
flag 3: h_inquisiti0n!}
-----------------------------------------------------------------------------------------------------------------
```

### Flag
flag{n0_0n3_3xp3ct5_th3_sp4nish_inquisiti0n!}

### Resources
RSA Info: https://en.wikipedia.org/wiki/RSA_(cryptosystem)

LCM and GCD functions: https://gist.github.com/endolith/114336/eff2dc13535f139d0d6a2db68597fad2826b53c3

Wolfram Alpha: https://www.wolframalpha.com/


---


## Picture Lab: Activity 10
**Category:** Forensics

**Points:** 100

**Description:**
> Dear APCSA students,
>
> You thought you were done with Picture Lab.
>
> Unfortunately, you were wrong.
>
> We're sorry. We should not have pushed this challenge out, it was irresponsible for us to deploy a meme challenge in the middle of a very
> serious "Catch-The-Flag" competition. We originally wrote this challenge as a joke, hoping that it would poke fun at Collegeboard's APCSA
> "Picture Lab"; however, we now realize that this decision was insensitive and outright disrespectful to all those who have to solve the
> challenge. In the future, we promise that our challenges will not make you suffer as much as this trivial challenge does. We apologize in
> advance to all those who will suffer at the hands of this beginner-level challenge. I hope you are able to forgive us.
>
> Sincerely,
>
> AC/PMP/JC
>
> **Given:** mogodb & PictureLabStudentGuide.pdf

### Writeup
The PDF doesn't give us much information besides the big hint that this file is
probably a PNG file, which helps. First thing I usually do with files that don't
have an extension is run the **file** command on linux or open it up in **HxD**.

Taking a look at the "mogodb" file in **HxD**, we see that
there isn't any appropriate header for any specific file format.

![Image of Hex](https://github.com/itsecgary/CTFs/blob/master/HSCTF%202020/Picture-Lab-Activity-10/hex.PNG)

Now, since we are assuming this is a PNG, we can put the appropriate file header
and footer:
```
00 00 00 00 0D 0A 1A 0A  ---->  89 50 4E 47 0D 0A 1A 0A    (........ -> ‰PNG....)
49 00 00 00 AE 42 60 82  ---->  49 45 4E 44 AE 42 60 82   (I...®B`‚ -> IEND®B`‚)
```


After this, I find **pngcheck** pretty useful. In the resources tab, I also
provided a link to the PNG wiki which shows which values are essential in the
PNG header.

Running **pngcheck** the first time gives me:
```
~/ctfs/hsctf2020/forensics$ pngcheck -v mogodb

File: mogodb (253237 bytes)
  invalid chunk name "" (00 48 00 52)
ERRORS DETECTED in mogodb
```

Nice. We can see that we have an invalid chunk. Back in **Hxd** we look for the
invalid chunk and we can see it on the first row (first 16 bytes).
```
00 48 00 52  ---->  49 48 44 52    (.H.R -> IHDR)
```

Running **pngcheck** again after changes gives us:
```
~/ctfs/hsctf2020/forensics$ pngcheck -v mogodb

File: mogodb (253237 bytes)
  chunk IHDR at offset 0x0000c, length 13
    560 x 708 image, 32-bit RGB+alpha, non-interlaced
  invalid chunk name "I" (49 00 41 00)
ERRORS DETECTED in mogodb
```

We can see we have yet another invalid chunk. Now looking back at **HxD** we
can find this invalid chunk in the third row (bytes 33-48).
```
49 00 41 00  ---->  49 44 41 54    (I.A. -> IDAT)
```

Once again, let's run **pngcheck**:
```
~/ctfs/hsctf2020/forensics$ pngcheck -v mogodb

File: mogodb (253237 bytes)
  chunk IHDR at offset 0x0000c, length 13
    560 x 708 image, 32-bit RGB+alpha, non-interlaced
  chunk IDAT at offset 0x00025, length 8192
    zlib: deflated, 32K window, fast compression
No errors detected in mogodb (33 chunks, 84.0% compression).
```

No errors detected! Nice! Now let's open the PNG up (don't forget to add the
.png extension).

![Image of Flag](mogodb.png)

Seems to be some meme lol, but we get the flag here.

### Flag
flag{and_y0u_th0ught_p1ctur3_l4b_was_h4rd}

### Resources
- https://mh-nexus.de/en/hxd/

- https://en.wikipedia.org/wiki/Portable_Network_Graphics

- https://www.garykessler.net/library/file_sigs.html

- https://zoomadmin.com/HowToInstall/UbuntuPackage/pngcheck


---


## AP Lab: English Language
**Category:** Reverse

**Points:** 100

**Description:**
> The AP English Language activity will ask you to reverse a program about
  manipulating strings and arrays. Again, an output will be given where you
  have to reconstruct an input.
>
> **Author:** AC
>
> **Given:** EnglishLanguage.java & a pdf

### Writeup
We can see right away that we have a .java file. I loaded up Eclipse and
opened the .java file there.

Here's what I found:
```
import java.util.Scanner;
public class EnglishLanguage
{
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String inp = sc.nextLine();
        if (inp.length()!=23) {
            System.out.println("Your input is incorrect.");
            System.exit(0);
        }
        for (int i = 0; i<3; i++) {
            inp=transpose(inp);
            inp=xor(inp);
        }
        if (inp.equals("1dd3|y_3tttb5g`q]^dhn3j")) {
            System.out.println("Correct. Your input is the flag.");
        }
        else {
            System.out.println("Your input is incorrect.");
        }
    }
    public static String transpose(String input) {
        int[] transpose = {11,18,15,19,8,17,5,2,12,6,21,0,22,7,13,14,4,16,20,1,3,10,9};
        String ret = "";
        for (int i: transpose) {
            ret+=input.charAt(i);
        }
        return ret;
    }
    public static String xor(String input) {
        int[] xor = {4,1,3,1,2,1,3,0,1,4,3,1,2,0,1,4,1,2,3,2,1,0,3};
        String ret = "";
        for (int i = 0; i<input.length(); i++) {
            ret+=(char)(input.charAt(i)^xor[i]) ;
        }
        return ret;
    }
}
```

Doesn't seem too bad. Looks like it runs the *transpose* function then the *xor*
after the first one three total times. I guess we gotta put it in reverse.

For the **xor**, we know that if we XOR each char again, it will reverse that
sequence.

For the **transpose**, it mixes the data up a little more. I took the index of
the value of the index of my new array. For example, my first index for my new
array is 0 and the index of 0 in the Java array was 11. For 1, the index was 19,
for 2 it was 7, and so on...

You can find my script in this repo or right here:
```
def transpose(data):
    trans = [11,19,7,20,16,6,9,13,4,22,21,0,8,14,15,2,17,5,1,3,18,10,12]
    trans_new = []

    for ind, val in enumerate(trans):
        trans_new.append(data[val])
    return ''.join(trans_new)

def xor(data):
    xs = [4,1,3,1,2,1,3,0,1,4,3,1,2,0,1,4,1,2,3,2,1,0,3]
    new_data = []
    for i in range(len(data)):
        new_data.append(chr(ord(data[i]) ^ xs[i]))
    return ''.join(new_data)

if __name__ == '__main__':
    text = "1dd3|y_3tttb5g`q]^dhn3j"
    for i in range(3):
        text = xor(text)
        text = transpose(text)
    print("FLAG: {}".format(text))
```

### Flag
flag{n0t_t00_b4d_r1ght}


---


## Ice Cream Bytes
**Category:** Reverse

**Points:** 100

**Description:**
> Introducing the new Ice Cream Bytes machine! Here’s a free trial:
  IceCreamBytes.java Oh, and make sure to read the user manual: IceCreamManual.txt
>
> **Author:** wooshi
>
> **Given:** IceCreamManual.txt & IceCreamBytes.java

### Writeup
We see right away that we have a Java program we can look at. I use Eclipse to
open up the program and this is what I find:
```
import java.io.*;
import java.nio.file.*;
import java.util.Scanner;

public class IceCreamBytes {
    public static void main(String[] args) throws IOException {
        Path path = Paths.get("IceCreamManual.txt");
        byte[] manualBytes = Files.readAllBytes(path);

        Scanner keyboard = new Scanner(System.in);
        System.out.print("Enter the password to the ice cream machine: ");
        String userInput = keyboard.next();
        String input = userInput.substring("flag{".length(), userInput.length()-1);
        byte[] loadedBytes = toppings(chocolateShuffle(vanillaShuffle(strawberryShuffle(input.getBytes()))));
        boolean correctPassword = true;

        byte[] correctBytes = fillMachine(manualBytes);
        for (int i = 0; i < correctBytes.length; i++) {
            if (loadedBytes[i] != correctBytes[i]) {
                correctPassword  = false;
            }
        }
        if (correctPassword) {
            System.out.println("That's right! Enjoy your ice cream!");
        } else {
            System.out.println("Uhhh that's not right.");
        }
        keyboard.close();
    }

    public static byte[] fillMachine(byte[] inputIceCream) {
        byte[] outputIceCream = new byte[34];
        int[] intGredients = {27, 120, 79, 80, 147,
            154, 97, 8, 13, 46, 31, 54, 15, 112, 3,
            464, 116, 58, 87, 120, 139, 75, 6, 182,
            9, 153, 53, 7, 42, 23, 24, 159, 41, 110};
        for (int i = 0; i < outputIceCream.length; i++) {
            outputIceCream[i] = inputIceCream[intGredients[i]];
        }
        return outputIceCream;
    }

    public static byte[] strawberryShuffle(byte[] inputIceCream) {
        byte[] outputIceCream = new byte[inputIceCream.length];
        for (int i = 0; i < outputIceCream.length; i++) {
            outputIceCream[i] = inputIceCream[inputIceCream.length - i - 1];
        }
        return outputIceCream;
    }

    public static byte[] vanillaShuffle(byte[] inputIceCream) {
        byte[] outputIceCream = new byte[inputIceCream.length];
        for (int i = 0; i < outputIceCream.length; i++) {
            if (i % 2 == 0) {
                outputIceCream[i] = (byte)(inputIceCream[i] + 1);
            } else {
                outputIceCream[i] = (byte)(inputIceCream[i] - 1);
            }
        }
        return outputIceCream;
    }

    public static byte[] chocolateShuffle(byte[] inputIceCream) {
        byte[] outputIceCream = new byte[inputIceCream.length];
        for (int i = 0; i < outputIceCream.length; i++) {
            if (i % 2 == 0) {
                if (i > 0) {
                    outputIceCream[i] = inputIceCream[i - 2];
                } else {
                    outputIceCream[i] = inputIceCream[inputIceCream.length - 2];
                }
            } else {
                if (i < outputIceCream.length - 2) {
                    outputIceCream[i] = inputIceCream[i + 2];
                } else {
                    outputIceCream[i] = inputIceCream[1];
                }
            }
        }
        return outputIceCream;
    }

    public static byte[] toppings(byte[] inputIceCream) {
        byte[] outputIceCream = new byte[inputIceCream.length];
        byte[] toppings = {8, 61, -8, -7, 58, 55,
            -8, 49, 20, 65, -7, 54, -8, 66, -9, 69,
            20, -9, -12, -4, 20, 5, 62, 3, -13, 66,
            8, 3, 56, 47, -5, 13, 1, -7,};
        for (int i = 0; i < outputIceCream.length; i++) {
            outputIceCream[i] = (byte)(inputIceCream[i] + toppings[i]);
        }
        return outputIceCream;

    }    
}
```

We can see above that we get our output text to be from the IceCreamManual.txt
file given to us. That output text has to equal our input text (the flag) ran
through the strawberryShuffle, vanillaShuffle, chocolateShuffle, and toppings
functions.

To reverse this, we will need to write functions to reverse each of these
functions. I wrote a script to help with that.

**My script:**
```
import sys
import binascii

def getCorrect():
    ingredients = [27,120,79,80,147,154,97,8,13,46,31,54,15,112,3,464,116,
                   58,87,120,139,75,6,182,9,153,53,7,42,23,24,159,41,110]
    with open("IceCreamManual.txt", "r") as f:
        data = f.read()
        arr = [c for c in data]
        output_icecream = []
        for val in ingredients:
            output_icecream.append(ord(arr[val]))
        output = [chr(x) for x in output_icecream]
        print("Correct Bytes: {}".format(output))
    f.close()
    return output_icecream

def toppings(data):
    toppings = [8,61,-8,-7,58,55,-8,49,20,65,-7,54,-8,66,-9,69,
               20,-9,-12,-4,20,5,62,3,-13,66,8,3,56,47,-5,13,1,-7,]
    output_icecream = []
    for i in range(len(data)):
        output_icecream.append(data[i] - toppings[i])
    output = [chr(x) for x in output_icecream]
    print("Before Toppings: {}".format(output))
    return output_icecream

def chocolate(data):
    output_icecream = []
    for i in range(len(data)):
        val = 0
        if i % 2 == 0:
            if i < (len(data) - 2):
                val = data[i+2]
            else:
                val = data[0]
        else:
            if i > 1:
                val = data[i-2]
            else:
                val = data[len(data) - 1]
        output_icecream.append(val)
    output = [chr(x) for x in output_icecream]
    print("Before Chocolate: {}".format(output))
    return output_icecream

def vanilla(data):
    output_icecream = []
    for i in range(len(data)):
        if i % 2 == 0:
            output_icecream.append(data[i] - 1)
        else:
            output_icecream.append(data[i] + 1)

    output = [chr(x) for x in output_icecream]
    print("Before Vanilla: {}".format(output))
    return output_icecream

def strawberry(data):
    output_icecream = data[::-1]
    output = [chr(x) for x in output_icecream]
    print("Before Strawberry: {}".format(output))
    return output_icecream

if __name__ == '__main__':
    input1 = getCorrect()
    input2 = toppings(input1)
    input3 = chocolate(input2)
    input4 = vanilla(input3)
    output = strawberry(input4)
    output = ''.join([chr(x) for x in output])
    print("Flag: flag{" + "{}".format(output) + "}")
```

**My output:**
```
Correct Bytes: ['l', 'o', 'l', 'l', 'o', 'o', 'k', 'a', 't', 't', 'h', 'i', 's', 't', 'e', 'x', 't', 'i', 'g', 'o', 't', 'f', 'r', 'o', 'm', 't', 'h', 'e', 'm', 'a', 'n', 'u', 'a', 'l']
Before Toppings: ['d', '2', 't', 's', '5', '8', 's', '0', '`', '3', 'o', '3', '{', '2', 'n', '3', '`', 'r', 's', 's', '`', 'a', '4', 'l', 'z', '2', '`', 'b', '5', '2', 's', 'h', '`', 's']
Before Chocolate: ['t', 's', '5', '2', 's', 's', '`', '8', 'o', '0', '{', '3', 'n', '3', '`', '2', 's', '3', '`', 'r', '4', 's', 'z', 'a', '`', 'l', '5', '2', 's', 'b', '`', '2', 'd', 'h']
Before Vanilla: ['s', 't', '4', '3', 'r', 't', '_', '9', 'n', '1', 'z', '4', 'm', '4', '_', '3', 'r', '4', '_', 's', '3', 't', 'y', 'b', '_', 'm', '4', '3', 'r', 'c', '_', '3', 'c', 'i']
Before Strawberry: ['i', 'c', '3', '_', 'c', 'r', '3', '4', 'm', '_', 'b', 'y', 't', '3', 's', '_', '4', 'r', '3', '_', '4', 'm', '4', 'z', '1', 'n', '9', '_', 't', 'r', '3', '4', 't', 's']
Flag: flag{ic3_cr34m_byt3s_4r3_4m4z1n9_tr34ts}
```

### Flag
flag{ic3_cr34m_byt3s_4r3_4m4z1n9_tr34ts}


---


## Blurry Eyes
**Category:** Web

**Points:** 100

**Description:**
> I can't see :(
>
> https://blurry-eyes.web.hsctf.com
>
> Author: meow

### Writeup
After clicking on the link, it takes us to a website with information on
CTFs. We see that it says that our flag is ... and is then blurred out. Best
think to do is to Right-Click and "View page source".

Looking at the HTML, we don't see anything useful besides the CSS class used
for the flag. Going to the CSS reveals many classes. With a quick CTRL-F ->
CTRL-C -> CTRL-V, we can find out class, which then gives us the flag.

### Flag
flag{glasses_are_useful}


---


## Broken Tokens
**Category:** Web

**Points:** 100

**Description:**
> I made a login page, is it really secure?
>
> https://broken-tokens.web.hsctf.com/
>
> **Note:** If you receive an "Internal Server Error" (HTTP Status Code 500),
that means that your cookie is incorrect.
>
> **Author:** hmmm
>
> **Given:** main.py

### Writeup
This website is not up anymore, but when clicked on, it takes me to a simple
login page. From looking at the website and *main.py* we can see that our goal
is to login as admin. No matter what we put into the username and password
field (besides the admin credentials), we will be granted access as *"guest"*.

I stared at this problem a long time, but found a program to help me out. I used
the JWT Tool linked below to mess with the JSON Web Token in a way to fool the
website into giving me access as admin.

With the tool, I entered the JWT given to me as guest, manipulated the username
field to be "admin", then changed the encryption type, which gave me a different
JWT. I copied and pasted it into the cookie for the JWT and it let me in and
gave me the flag (didn't write it down and can't get it again...rip).

### Resources
JWT Linux Tool: https://github.com/ticarpi/jwt_tool

JWT: https://jwt.io/


---


## Debt Simulator
**Category:** Web

**Points:** 100

**Description:**
> https://debt-simulator.web.hsctf.com/
>
> Author: Madeleine

### Writeup
Clicking on the link provided takes us to a website that is literally a Debt
Simulator. Playing around with it a little doesn't hurt, but only showed that
debt sure can be a pain in the ass.

I clicked on Inspect Element and read through the source a little. Towards the
bottom there is a fetch call:
```
fetch("https://debt-simulator-login-backend.web.hsctf.com/yolo_0000000000001", {
    method: "POST",
    body: "function=" + (e ? "getCost" : "getPay"),
    headers: {
        "Content-type": "application/x-www-form-urlencoded"
    }
})
```

I clicked on the link and it fave me:
```
{"functions":["getPay","getCost","getgetgetgetgetgetgetgetgetFlag"]}
```

In the fetch code above, we can see that the post method is called with
Content-Type = "application/x-www-form-urlencoded" and body with format
"function=<function>".

I used **Postman** for this challenge (**Burp Suite** would work as well), which I find
super useful and easy to use for Web challenges like these. To see what would
happen, I did a POST request to the backend (yolo) website above entering in
"function=getPay" and changing the Content-Type. Boom. We got a number.

From that website above, the obvious route to go is to make the function call
equal to "function=getgetgetgetgetgetgetgetgetFlag" to receive the flag.

### Flag
flag{y0u_f0uND_m3333333_123123123555554322221}

### Resources

**Postman:**   https://www.postman.com/

**Burp Suite:**   https://portswigger.net/burp

You can install the basic community edition for free because otherwise it is
pricey!


---


## Inspector Gadget
**Category:** Web

**Points:** 100

**Description:**
> https://inspector-gadget.web.hsctf.com/
>
> Author: Madeleine

### Writeup
This was a pretty simple challenge and probably the easiest during this CTF
competition. Upon clicking the link, we see a simple website with just a home
page. The title of the problem gives us a big hint to open Inspect Element on
Chrome. After looking around, we find our flags in an HTML comment.

### Flag
flag{n1ce_j0b_0p3n1nG_th3_1nsp3ct0r_g4dg3t} 


---


## Very Safe Login
**Category:** Web

**Points:** 100

**Description:**
> Bet you can't log in.

> https://very-safe-login.web.hsctf.com/very-safe-login

> Author: Madeleine

### Writeup
Clicking on the link provided brings us to a website with only a login page.
It doesn't hurt to try a few simple username and password combinations to test
the functionality. Nothing here, so we should view the source code to see what
is going on.

We see an embedded script in the bottom of the HTML source:

```
var login = document.login;

function submit() {
    const username = login.username.value;
    const password = login.password.value;

    if(username === "jiminy_cricket" && password === "mushu500") {
        showFlag();
        return false;
    }
    return false;
}
```

Looks like it gives us the password to login. How nice of them! Inserting the
credentials and logging in gives us the flag.

### Flag
flag{cl13nt_51de_5uck5_135313531}


