---
title: "  HSCTF 2020 Reversing"
date: 2020-06-06T14:16:00+00:00
weight: 9999
# aliases: ["/first"]
tags: ["writeup", "reversing", "hsctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "Writeups on reversing challenges for HSCTF 2020"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
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
`flag{n0t_t00_b4d_r1ght}`


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
`flag{ic3_cr34m_byt3s_4r3_4m4z1n9_tr34ts}`



