---
title: "ZH3R0CTF 2020 Web"
date: 2020-06-18T14:16:00+00:00
weight: 9998
# aliases: ["/first"]
tags: ["web", "writeup", "zh3r0ctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "Writeups on web exploitation challenges for ZH3R0CTF 2020"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

<img src="/img/web.png">

---


## Tokens
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
`zh3r0{1et_7he_F0rce_8e_With_YoU}`

### Resources
Video - https://www.youtube.com/watch?v=FmXMGCRpw50

JS Code - https://pastebin.com/yPWZGzGB


---


## Web-Warmup
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

### Flag
`zh3r0{y3s_th1s_1s_w4rmup}`


