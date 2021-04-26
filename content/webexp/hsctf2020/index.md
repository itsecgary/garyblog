---
title: "HSCTF 2020   Web"
date: 2020-06-06T14:16:00+00:00
weight: 9999
# aliases: ["/first"]
tags: ["web", "writeup", "hsctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "Writeups on web exploitation challenges for HSTCT 2020"
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
`flag{glasses_are_useful}`


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
`flag{y0u_f0uND_m3333333_123123123555554322221}`

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
`flag{n1ce_j0b_0p3n1nG_th3_1nsp3ct0r_g4dg3t}`


---


## Very Safe Login
**Category:** Web

**Points:** 100

**Description:**
> Bet you can't log in.
>
> https://very-safe-login.web.hsctf.com/very-safe-login
>
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
`flag{cl13nt_51de_5uck5_135313531}`

