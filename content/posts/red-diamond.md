---
title: "Red Diamond"
date: 2022-10-12T14:06:24+01:00
draft: true
description: "https://ctf.gdgalgiers.com/challenges"
tags : ['gdgalgiers','easy', 'ruby','jail']
aliases: [/ctf/gdg/jail/postal/]

---
## Preview:

![](https://github.com/0xWerz/CTF-writeups/blob/main/GDG-Algiers-2022/jail/red-diamond/img/desc.png?raw=true)

## Walkthrough
Connecting to the server:
> nc -v jail.chal.ctf.gdgalgiers.com 1303

if we pass `"` as a input it response with a error, and we see that it handled with the eval function:

> The Eval function evaluates the string expression and returns its value. For example, Eval("1 + 1") returns 2. If you pass to the Eval function a string that contains the name of a function, the Eval function returns the return value of the function.

![](https://github.com/0xWerz/CTF-writeups/blob/main/GDG-Algiers-2022/jail/red-diamond/img/res_err.png?raw=true)

So if i try to pass the system function with a system commmand it might work fine.


![](https://github.com/0xWerz/CTF-writeups/blob/main/GDG-Algiers-2022/jail/red-diamond/img/ls.png?raw=true)

We can cat the flag by replacing `ls` with `cat flag.txt` again.  