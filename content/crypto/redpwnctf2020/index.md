---
title: "redpwnCTF 2020 Crypto"
date: 2020-06-29T14:16:00+00:00
# weight: 1
# aliases: ["/first"]
tags: ["writeup", "crypto", "redpwnctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "Writeups of crypto challenges for redpwnCTF 2020"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

<img src="/img/crypto.png">

---

## pseudo-key
**Category:** Crypto

**Points:** 341

**Description:**
> Keys are not always as they seem...
>
> **Note:** Make sure to wrap the plaintext with `flag{}` before you submit!
>
> **Author:** Boolean
>
> **Given:** pseudo-key-output.txt && pseudo-key.py

### Writeup
First thing I do here is look at the files given to us. Looks like we are given
the ciphertext and some "pseudo-key".
```
Ciphertext: z_jjaoo_rljlhr_gauf_twv_shaqzb_ljtyut
Pseudo-key: iigesssaemk
```

From the program itself, it looks like our ciphertext is encrypted with some key, but
we are given the key encrypted with itself. Interesting...
```
#!/usr/bin/env python3

from string import ascii_lowercase

chr_to_num = {c: i for i, c in enumerate(ascii_lowercase)}
num_to_chr = {i: c for i, c in enumerate(ascii_lowercase)}

def encrypt(ptxt, key):
    ptxt = ptxt.lower()
    key = ''.join(key[i % len(key)] for i in range(len(ptxt))).lower()
    ctxt = ''
    for i in range(len(ptxt)):
        if ptxt[i] == '_':
            ctxt += '_'
            continue
        x = chr_to_num[ptxt[i]]
        y = chr_to_num[key[i]]
        ctxt += num_to_chr[(x + y) % 26]
    return ctxt

with open('flag.txt') as f, open('key.txt') as k:
    flag = f.read()
    key = k.read()

ptxt = flag[5:-1]

ctxt = encrypt(ptxt,key)
pseudo_key = encrypt(key,key)

print('Ciphertext:',ctxt)
print('Pseudo-key:',pseudo_key)
```

The encryption method pretty much takes the alphabetical number value of each
character of the "plaintext", adds it to the alphabetical number value of the key
and wraps it around if the sum is greater than 26.

This is where I was very twisted for a day or so. I overthought the hell out of
this problem. We realized before that the key given to us is completely wrong and is
just the result of encrypting the key with itself. I figured out that each of these
characters in the key has a total of 2 possible values it could actually be.

Let's take a look at the possible values of all 26 chars in the alphabet:
```
a: (0 + 0) % 26 = 0 (a)
b: (1 + 1) % 26 = 2 (c)
c: (2 + 2) % 26 = 4 (e)
...
...
m: (12 + 12) % 26 = 24 (y)
n: (13 + 13) % 26 = 0 (a)
o: (14 + 14) % 26 = 2 (c)
p: (15 + 15) % 26 = 4 (e)
```

We can see here that there are really only 13 possible characters that could be
in the key. Let's get each possibility for our characters.
```
i = e | r
i = e | r
g = d | q
e = c | p
s = j | w
s = j | w
s = j | w
a = a | n
e = c | p
m = g | t
k = f | s
```

Now, I'm not sure what other people had for their approach, but I straight-up
iterated through all possible combinations of this key. There are 2^11 (2048)
key possibilities. I wrote a script to do this for me and decrypt the flag as
well. So, this will basically output 2048 different flag options. This is harder
since I don't have the "flag{}" wrapper here to help me, but oh well.

**Script:**
```
def get_plain(ct, key):
    plain = ''
    key = ''.join(key[i % len(key)] for i in range(len(ct)))
    for i in range(len(ct)):
        if ct[i] == '_':
            plain += ct[i]
            continue
        c = ord(ct[i]) - 97
        k = ord(key[i]) - 97
        if c > k:
            plain += chr(c-k+97)
        elif c < k:
            plain += chr(26-k+c+97)
        else:
            plain += chr(c+97)
    return plain


psuedo_key = "iigesssaemk"
ct = "z_jjaoo_rljlhr_gauf_twv_shaqzb_ljtyut"

i = ['e', 'r']
g = ['d', 'q']
e = ['c', 'p']
s = ['j', 'w']
a = ['a', 'n']
m = ['g', 't']
k = ['f', 's']
possible_keys = []

for ii in i:
    for ii2 in i:
        for gg in g:
            for ee in e:
                for ss in s:
                    for ss2 in s:
                        for ss3 in s:
                            for aa in a:
                                for ee2 in e:
                                    for mm in m:
                                        for kk in k:
                                            test = ii + ii2 + gg + ee + ss + ss2 + ss3 + aa + ee2 + mm + kk
                                            possible_keys.append(test)

for key in possible_keys:
    plain = get_plain(ct, key)
    print(plain)
```

Probably a more efficient way of doing what I did, butttttt oh well.

**Output: (count)**
```
$ python3 solve.py > output && wc -l output
2048 output
```

Scrolling through the 2048 options, I could kind of tell what some of these words
were going to be. I did a search on a pair of words to narrow down the options.
```
$ cat output | grep "i_guess"
i_guess_pfeudo_keyf_nre_pseudb_fecure
i_guess_pfrudo_keyf_nee_pseudb_frcure
i_guess_pseudo_keyf_tre_pseudb_secure
i_guess_psrudo_keyf_tee_pseudb_srcure
i_guess_cfeudo_keyf_nre_pseudb_fecure
i_guess_cfrudo_keyf_nee_pseudb_frcure
i_guess_cseudo_keyf_tre_pseudb_secure
i_guess_csrudo_keyf_tee_pseudb_srcure
i_guess_pfeudo_keys_nre_pseudo_fecure
i_guess_pfrudo_keys_nee_pseudo_frcure
i_guess_pseudo_keys_tre_pseudo_secure
i_guess_psrudo_keys_tee_pseudo_srcure
i_guess_cfeudo_keys_nre_pseudo_fecure
i_guess_cfrudo_keys_nee_pseudo_frcure
i_guess_cseudo_keys_tre_pseudo_secure
i_guess_csrudo_keys_tee_pseudo_srcure
i_guess_pfeuqo_keyf_nre_pseudb_fechre
i_guess_pfruqo_keyf_nee_pseudb_frchre
i_guess_pseuqo_keyf_tre_pseudb_sechre
i_guess_psruqo_keyf_tee_pseudb_srchre
i_guess_cfeuqo_keyf_nre_pseudb_fechre
i_guess_cfruqo_keyf_nee_pseudb_frchre
i_guess_cseuqo_keyf_tre_pseudb_sechre
i_guess_csruqo_keyf_tee_pseudb_srchre
i_guess_pfeuqo_keys_nre_pseudo_fechre
i_guess_pfruqo_keys_nee_pseudo_frchre
i_guess_pseuqo_keys_tre_pseudo_sechre
i_guess_psruqo_keys_tee_pseudo_srchre
i_guess_cfeuqo_keys_nre_pseudo_fechre
i_guess_cfruqo_keys_nee_pseudo_frchre
i_guess_cseuqo_keys_tre_pseudo_sechre
i_guess_csruqo_keys_tee_pseudo_srchre
```

I'm guessing my script had a tiny error with the "are" part of the flag, but it
worked.

### Flag
`flag{i_guess_pseudo_keys_are_pseudo_secure}`

---

## 4k-rsa
**Category:** Crypto

**Points:** 389

**Description:**
> Only n00bz use 2048-bit RSA. True gamers use keys that are at least 4k bits
long, no matter how many primes it takes...
>
> **Author:** Boolean
>
> **Given:** 4k-rsa-public-key.txt

### Writeup
Opening up the attachment given, we see we have a modulus **(n)**, public exponent
**(e)**, and a ciphertext **(c)**.
```
n: 5028492424316659784848610571868499830635784588253436599431884204425304126574506051458282629520844349077718907065343861952658055912723193332988900049704385076586516440137002407618568563003151764276775720948938528351773075093802636408325577864234115127871390168096496816499360494036227508350983216047669122408034583867561383118909895952974973292619495653073541886055538702432092425858482003930575665792421982301721054750712657799039327522613062264704797422340254020326514065801221180376851065029216809710795296030568379075073865984532498070572310229403940699763425130520414160563102491810814915288755251220179858773367510455580835421154668619370583787024315600566549750956030977653030065606416521363336014610142446739352985652335981500656145027999377047563266566792989553932335258615049158885853966867137798471757467768769820421797075336546511982769835420524203920252434351263053140580327108189404503020910499228438500946012560331269890809392427093030932508389051070445428793625564099729529982492671019322403728879286539821165627370580739998221464217677185178817064155665872550466352067822943073454133105879256544996546945106521271564937390984619840428052621074566596529317714264401833493628083147272364024196348602285804117877
e: 65537
c: 3832859959626457027225709485375429656323178255126603075378663780948519393653566439532625900633433079271626752658882846798954519528892785678004898021308530304423348642816494504358742617536632005629162742485616912893249757928177819654147103963601401967984760746606313579479677305115496544265504651189209247851288266375913337224758155404252271964193376588771249685826128994580590505359435624950249807274946356672459398383788496965366601700031989073183091240557732312196619073008044278694422846488276936308964833729880247375177623028647353720525241938501891398515151145843765402243620785039625653437188509517271172952425644502621053148500664229099057389473617140142440892790010206026311228529465208203622927292280981837484316872937109663262395217006401614037278579063175500228717845448302693565927904414274956989419660185597039288048513697701561336476305496225188756278588808894723873597304279725821713301598203214138796642705887647813388102769640891356064278925539661743499697835930523006188666242622981619269625586780392541257657243483709067962183896469871277059132186393541650668579736405549322908665664807483683884964791989381083279779609467287234180135259393984011170607244611693425554675508988981095977187966503676074747171
```

The description provides us with a decent hint here. Saying that there are
multiple primes involved probably means that **n** is made from more than just
**p** and **q**!

There is a really nice website for factoring large **n** values. The website is
called **FactorDB** and they have a python module for the website as well.

Plugging our **n** in the website gives us a lot of primes, but the important
thing here is that it is fully factored!
```
Result:
status (?)	digits	number
FF	1225 (show)	5028492424...77<1225> =
9353689450544968301<19> · 9431486459129385713<19> · 9563871376496945939<19> · 9734621099746950389<19> · 9736426554597289187<19> · 10035211751896066517<20> · 10040518276351167659<20> · 10181432127731860643<20> · 10207091564737615283<20> · 10435329529687076341<20> · 10498390163702844413<20> · 10795203922067072869<20> · 11172074163972443279<20> · 11177660664692929397<20> · 11485099149552071347<20> · 11616532426455948319<20> · 11964233629849590781<20> · 11992188644420662609<20> · 12084363952563914161<20> · 12264277362666379411<20> · 12284357139600907033<20> · 12726850839407946047<20> · 13115347801685269351<20> · 13330028326583914849<20> · 13447718068162387333<20> · 13554661643603143669<20> · 13558122110214876367<20> · 13579057804448354623<20> · 13716062103239551021<20> · 13789440402687036193<20> · 13856162412093479449<20> · 13857614679626144761<20> · 14296909550165083981<20> · 14302754311314161101<20> · 14636284106789671351<20> · 14764546515788021591<20> · 14893589315557698913<20> · 15067220807972526163<20> · 15241351646164982941<20> · 15407706505172751449<20> · 15524931816063806341<20> · 15525253577632484267<20> · 15549005882626828981<20> · 15687871802768704433<20> · 15720375559558820789<20> · 15734713257994215871<20> · 15742065469952258753<20> · 15861836139507191959<20> · 16136191597900016651<20> · 16154675571631982029<20> · 16175693991682950929<20> · 16418126406213832189<20> · 16568399117655835211<20> · 16618761350345493811<20> · 16663643217910267123<20> · 16750888032920189263<20> · 16796967566363355967<20> · 16842398522466619901<20> · 17472599467110501143<20> · 17616950931512191043<20> · 17825248785173311981<20> · 18268960885156297373<20> · 18311624754015021467<20> · 18415126952549973977<20>
```

This is easy from here on out. In order
to find our private exponent **(d)**, we need to find **phi**, which is the
product of (every prime - 1). After finding phi, we can take the modular
multiplicative inverse of **e** and **phi** to find **d**.

*Note: I didn't feel like copying the 60-something primes into my script so I
used the python module for FactorDB :)*

**Script:**
```
import gmpy
from pwn import *
from Crypto.Util.number import long_to_bytes
import binascii
from factordb.factordb import FactorDB

n = int(open("n", "r").read())
c = int(open("c", "r").read())
e = 65537

f = FactorDB(n)
f.connect()
arr = f.get_factor_list()

phi = 1
for a in arr:
    phi *= (a-1)

d = gmpy.invert(e, phi)
flag = long_to_bytes(pow(c,d,n)).decode()
log.success("Flag: {}".format(flag))
```

**Output:**
```
$ python3 4k_rsa.py
[+] Flag: flag{t0000_m4nyyyy_pr1m355555}
```

### Flag
`flag{t0000_m4nyyyy_pr1m355555}`

### Resources
[FactorDB](http://factordb.com/index.php)

[RSA Info](https://en.wikipedia.org/wiki/RSA_(cryptosystem))

---

## 12-shades-of-redpwn
**Category:** Crypto

**Points:** 429

**Description:**
> Everyone's favorite guess god Tux just sent me a flag that he somehow
encrypted with a color wheel!
>
> I don't even know where to start, the wheel looks more like a clock than a
cipher... can you help me crack the code?
>
> **Author:** Boolean
>
> **Given:** ciphertext.jpg && color-wheel.jpg

### Writeup
This one took me a while to figure out for some reason (lol). The hint gave me
the idea that the colors in the chart were corresponding to the numbers on the
clock (1-12), but I was just a tiny bit wrong. The color wheel had values
**(0-11)**, but in hex *obviously...* (sarcasm). Here is my edited version:

<img src="color-wheel_EDITED.jpg" alt="Color Wheel">

Now, with this and our ciphertext, we can start plugging and chugging values. Our
result is the following:
```
86 90 81 87 a3 49 99 43 97 97 41 92 49 7b 41 97 7b 44 92 7b 44 96 98 a5
```

Translating this from hex to ASCII, doesn't give us anything but gunk...
```
....¢I.C..A.I{A.{D.{D..¥
```

After a while of staring at this stupuid color wheel and hex values I derived from
it, I started noticing that ` 86 90 81 87 ` represents ` flag ` and the `a3` and
`a5` values are two apart (have to be curly braces). It finally hit me that this is
in base12. This changes our ascii representation of these values:
```
41 -> 1          62 -> J          83 -> c          a4 -> |
42 -> 2          63 -> K          84 -> d          a5 -> }
43 -> 3          64 -> L          85 -> e
44 -> 4          65 -> M          86 -> f
45 -> 5          66 -> N          87 -> g
46 -> 6          67 -> O          88 -> h
47 -> 7          68 -> P          89 -> i
48 -> 8          69 -> Q          8a -> j
49 -> 9          6a -> R          8b -> k
4a -> :          6b -> S          90 -> l
4b -> ;          70 -> T          91 -> m
50 -> <          71 -> U          92 -> n
51 -> =          72 -> V          93 -> o
52 -> >          73 -> W          94 -> p
53 -> ?          74 -> X          95 -> q
54 -> @          75 -> Y          96 -> r
55 -> A          76 -> Z          97 -> s
56 -> B          77 -> [          98 -> t
57 -> C          78 -> \          99 -> u
58 -> D          79 -> ]          9a -> v
59 -> E          7a -> ^          9b -> w
5a -> F          7b -> _          a0 -> x
5b -> G          80 -> '          a1 -> y
60 -> H          81 -> a          a2 -> z
61 -> I          82 -> b          a3 -> {
```

Now decoding with our base12 ASCII representation gives us the flag.

### Flag
`flag{9u3ss1n9_1s_4n_4rt}`

### Resources
[ASCII Chart](http://www.asciitable.com/)

---

## itsy-bitsy
**Category:** Crypto

**Points:** 436

**Description:**
> The itsy-bitsy spider climbed up the water spout...
> ` nc 2020.redpwnc.tf 31284 `
>
> **Author:** Boolean
>
> **Given:** itsy-bitsy.py

### Writeup
This one was interesting. Don't really think I took the right path on this one
because I had to "guess" part of the flag, but you will see how I came to that.
Why write this writeup then? Because it might have been a different approach to
this problem and might be useful (maybe).

Anyways, I ran the program to see what we are working with:
```
$ nc 2020.redpwnc.tf 31284
Enter an integer i such that i > 0: 2
Enter an integer j such that j > i > 0: 3
Ciphertext: 0011011011001010101010101110000100000011100100010100001010101010000000111011010000100011010011100010001110100011001110011000100110110010111010010110100110001111010010100111000111010111100010101011101001000100110010000100110100101000011111001111110111000000000010010100001100100000010011110110010000010
```

I opened the script and analyzed the program. The general *function* of
this program was to read in two integers, and output a binary ciphertext. One of
the integers had to be bigger than the other one and they *both* had to be greater
than 0.
```
#!/usr/bin/env python3

from Crypto.Random.random import randint

def str_to_bits(s):
    bit_str = ''
    for c in s:
        i = ord(c)
        bit_str += bin(i)[2:]
    return bit_str

def recv_input():
    i = input('Enter an integer i such that i > 0: ')
    j = input('Enter an integer j such that j > i > 0: ')
    try:
        i = int(i)
        j = int(j)
        if i <= 0 or j <= i:
            raise Exception
    except:
        print('Error! You must adhere to the restrictions!')
        exit()
    return i,j

def generate_random_bits(lower_bound, upper_bound, number_of_bits):
    bit_str = ''
    while len(bit_str) < number_of_bits:
        r = randint(lower_bound, upper_bound)
        bit_str += bin(r)[2:]
    return bit_str[:number_of_bits]

def bit_str_xor(bit_str_1, bit_str_2):
    xor_res = ''
    for i in range(len(bit_str_1)):
        bit_1 = bit_str_1[i]
        bit_2 = bit_str_2[i]
        xor_res += str(int(bit_1) ^ int(bit_2))
    return xor_res

def main():
    with open('flag.txt','r') as f:
        flag = f.read()
    for c in flag:
        i = ord(c)
        assert i in range(2**6,2**7)
    flag_bits = str_to_bits(flag)
    i,j = recv_input()
    lb = 2**i
    ub = 2**j - 1
    n = len(flag_bits)
    random_bits = generate_random_bits(lb,ub,n)
    encrypted_bits = bit_str_xor(flag_bits,random_bits)
    print(f'Ciphertext: {encrypted_bits}')

if __name__ == '__main__':
    main()
```

This program reads in the flag server-side and encrypts it using our numbers
we provided. The numbers we provided are put in the exponent of 2, making an
upper-bound and a lower-bound. The length of the flag is passed in to make sure
that they return the same number of bits as the flag. After generating a random
bit pattern and converting the flag into bits, the program does an XOR with both
bit streams. This is what we are seeing for the binary ciphertext given to us.
Interesting...

I made it certain in my head that there wasn't really a way to get around the
randomness of the generated bits (keep in mind I haven't seen any other solutions
before writing this). My approach was to take the smallest numbers I could do and
generate cases for my ciphertext.

If I entered in a 1 and 2. The **lower-bound** would be **2^1** and the
**upper-bound** would be **(2^2) - 1**. Making the randomly-generated bits to being
either *10* or *11* (2 <= random <= 3).

If I entered in a 2 and 3. The **lower-bound** would be **2^2** and the
**upper-bound** would be **(2^3) - 1**. Making the randomly-generated bits to being
either *100*, *101*, *110*, or *111* (4 <= random <= 7).

I started to see a pattern with this. If I chose the first option (1 and 2), every
other bit would *HAVE* to be a **1**. Well I guess that covers half of the bits.
I wanted more coverage, so I got samples for **(1,2)**, **(2,3)**, **(4,5)**,
**(6,7)**, **(10,11)**, and **(12,13)**. The only problem with this was all of the
prime numbers wouldn't be found (along with a few stragglers).

**Script:**
```
from pwn import *
from sympy import *
from binascii import *

ctwo = "0011011000011001101100001101000010010011010011100100101001011000100001011100100011110110100100010000111101000101011001011001000011010001101001010110100101001100100000010101000000010001000000011110011011100110100001010101100000101101011011011100000011010001000001110000011010010000000100010110110101000"
cthree = "0111101111111100011110011100010100010111110000000110011110001100110010111111110010100011010111010010011100100111011100011000000100100010101100100010110110101001000110110101010011110101110110111001001101000100010100110000110100111110101011101111010011000110010010100000011000100000110011000100010000010"
cfive = "0011100110010100100101000001110100101011011100110100001100001001000011001001101110111000010110010000110010110100011110101101000010000000111001110110100010100000110100110101101011100110000110010101000110000101010101100111101100000101001011101100000101010000111100000000011111111000011111110111101001000"
cseven = "0110001010101000001000000110010000100011110111111010100100001010110010010111100001010001111010111000000000110000000101101101100010011001000001001000010110000011000100000111110011010100011100111100011010001010010110010000011101110100011011010100001010100110111010001001010100000001000001001000110010111"
celeven = "0100110110100001101011011001111100100010010010010000000110011011110100010101101001000010100000000000110011111000111101101000010000000001011110111011000000000101000110011011100100101110011000010101101101101110100000101110101011101110010011000101001111011111000011000100100111110111000100000101010000100"

reconstructed = ''
re_list = []
for i in range(len(ctwo)):
    if i % 2 == 0:
        val = int(ctwo[i])
        reconstructed += "0" if val == 1 else "1"
    elif i % 3 == 0:
        val = int(cthree[i])
        reconstructed += "0" if val == 1 else "1"
    elif i % 5 == 0:
        val = int(cfive[i])
        reconstructed += "0" if val == 1 else "1"
    elif i % 7 == 0:
        val = int(cseven[i])
        reconstructed += "0" if val == 1 else "1"
    elif i % 11 == 0:
        val = int(celeven[i])
        reconstructed += "0" if val == 1 else "1"
    elif isprime(i):
        reconstructed += 'p'
    else:
        reconstructed += '?'
    if len(reconstructed) == 7:
        reconstructed = "0" + reconstructed
        re_list.append(reconstructed)
        reconstructed = ''

print(re_list)
vals = ["0", "1"]
flag = []
one = []
two = []
three = []
four = []
for r in re_list:
    count = 0
    for c in r:
        if c == "?" or c == "p":
            count += 1
    if count == 0:
        one.append(r)
        two.append(r)
        three.append(r)
        four.append(r)
    elif count == 1:
        oned = r.replace("?", "0").replace("p", "0")
        twod = r.replace("?", "1").replace("p", "1")
        one.append(oned)
        two.append(twod)
        three.append(twod)
        four.append(oned)
    else:
        one.append(r.replace("p", "0"))
        two.append(r.replace("p", "0", 1).replace("p", "1", 1))
        three.append(r.replace("p", "1", 1).replace("p", "0", 1))
        four.append(r.replace("p", "1"))

flag1 = []
for h in one:
    if "?" in h:
        flag1.append("?")
    else:
        flag1.append(chr(int(str(h), 2)))

flag2 = []
for h in two:
    if "?" in h:
        flag2.append("?")
    else:
        flag2.append(chr(int(str(h), 2)))

flag3 = []
for h in three:
    if "?" in h:
        flag3.append("?")
    else:
        flag3.append(chr(int(str(h), 2)))

flag4 = []
for h in four:
    if "?" in h:
        flag4.append("?")
    else:
        flag4.append(chr(int(str(h), 2)))

print(''.join(flag1))
print(''.join(flag2))
print(''.join(flag3))
print(''.join(flag4))
```

Yeah.... I probably could have reduced the repetitive code, but I really didn't
feel like doing it at the time. Looking back at this, I probably could have
enumerated the options for the prime parts, but that is a *lot* of options.

**Output:**
```
$ python3 itsy.py
FlagSbIpq[DdajajG_MptKdn?j_`@e_?adE?]spkU?}
fmcw[cKts_Leckcng_OqtOlo?k_dHe\x7f?ctM?_wro]?\x7f
Fligsripy[dtajizG_mtt[d~?n_p`e_?ide?]sxku?}
fmkw{skt{_luckk~g_out_l\x7f?o_the\x7f?ktm?_wzo}?\x7f
```

At this point, I recognized that I was pretty close. You can kind of see the flag
forming. It was just a matter of those primes because they showed up in every byte.

From here, all I really did was guess between the four options and see what looked
and sounded like English (yikes). Not the most impressive route, but it got me there.

## Flag
`flag{bits_leaking_out_down_the_water_spout}`

## Post Note
Now I'm gonna go look at the other writeups for this challenge and realize how much time
I probably wasted in this approach lol

---

## primimity
**Category:** Crypto

**Points:** 450

**Description:**
> People claim that RSA with two 1024-bit primes is secure. But I trust no one.
That's why I use three 1024-bit primes.
>
> I even created my own prime generator to be extra cautious!
>
> **Author:** Boolean
>
> **Given:** primimity.py && primimity-public-key.txt

### Writeup
Looking at **priminity.py**, we see that the prime generation generates three
primes: **p**, **q**, and **r**. It picks a random 1024-bit number, then finds
the next prime - (d+1) primes away.
```
#!/usr/bin/env python3

from Crypto.Util.number import getRandomNBitInteger, isPrime

def find_next_prime(n):
    if n <= 1:
        return 2
    elif n == 2:
        return 3
    else:
        if n % 2 == 0:
            n += 1
        else:
            n += 2
        while not isPrime(n):
            n += 2
        return n

def prime_gen():
    i = getRandomNBitInteger(1024)
    d = getRandomNBitInteger(8)
    for _ in range(d):
        i = find_next_prime(i)
    p = find_next_prime(i)
    d = getRandomNBitInteger(8)
    for _ in range(d):
        i = find_next_prime(i)
    q = find_next_prime(i)
    d = getRandomNBitInteger(8)
    for _ in range(d):
        i = find_next_prime(i)
    r = find_next_prime(i)
    return (p,q,r)

def main():
    (p,q,r) = prime_gen()
    print(p)
    print(q)
    print(r)

if __name__ == '__main__':
    main()
```

Running this program once, we get an example output of:
```
$ python3 primimity.py
104857957995802113202799155043146383738317717869506487255733554982237834412439876073175028008556725572257508977034962752889399455584848024999705402597044673244677421623166376772490220536819588049922242374474030586464211019488836847121605902635938292012081202316987359553541112952345050585956025016297736305427
104857957995802113202799155043146383738317717869506487255733554982237834412439876073175028008556725572257508977034962752889399455584848024999705402597044673244677421623166376772490220536819588049922242374474030586464211019488836847121605902635938292012081202316987359553541112952345050585956025016297736436659
104857957995802113202799155043146383738317717869506487255733554982237834412439876073175028008556725572257508977034962752889399455584848024999705402597044673244677421623166376772490220536819588049922242374474030586464211019488836847121605902635938292012081202316987359553541112952345050585956025016297736606427
```

We can spot that the difference in the primes are very small (small enough to
enumerate). Basiclaly, we are just exploiting this prime gap to find our
**p**, **q**, and **r**.

**Script:**
```
from pwn import *
import gmpy2
import gmpy
from Crypto.Util.number import long_to_bytes

n = int(open("n", "r").read())
c = int(open("c", "r").read())
e = 65537

root, extra = gmpy2.iroot(n,3)
root = int(root)

p = root
while n % p != 0:
    p -= 1

qr = n // p
root2, extra2 = gmpy2.iroot(qr,2)
root2 = int(root2)

q = root2
while qr % q != 0:
    q -= 1

r = qr // q
print("P: {}".format(p))
print("Q: {}".format(q))
print("R: {}".format(r))

phi = (p-1)*(q-1)*(r-1)

d = gmpy.invert(e, phi)
flag = long_to_bytes(pow(c,d,n)).decode("utf-8")
log.success("Flag: {}".format(flag))
```

**Output:**
```
$ python3 solve.py
P: 139926822890670655977195962770726941986198973494425759476822219188316377933161673759394901805855617939978281385708941597117531007973713846772205166659227214187622925135931456526921198848312215276630974951050306344412865900075089120689559331322162952820292429725303619113876104177529039691490258588465409208581
Q: 139926822890670655977195962770726941986198973494425759476822219188316377933161673759394901805855617939978281385708941597117531007973713846772205166659227214187622925135931456526921198848312215276630974951050306344412865900075089120689559331322162952820292429725303619113876104177529039691490258588465409397803
R: 139926822890670655977195962770726941986198973494425759476822219188316377933161673759394901805855617939978281385708941597117531007973713846772205166659227214187622925135931456526921198848312215276630974951050306344412865900075089120689559331322162952820292429725303619113876104177529039691490258588465409494847
[+] Flag: flag{pr1m3_pr0x1m1ty_c4n_b3_v3ry_d4ng3r0u5}
```

### Flag
`flag{pr1m3_pr0x1m1ty_c4n_b3_v3ry_d4ng3r0u5}`

---

