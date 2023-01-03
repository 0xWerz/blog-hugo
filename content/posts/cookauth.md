---
title: "GDG-2022 -- Cookauth"
date: 2022-10-12T14:06:01+01:00
draft: true
description: "Cookauth is a web easy challange"
tags : ['gdgalgiers','misconfiguration','very easy', 'web']
aliases: [/ctf/gdg/web/cookauth/]
---


## Preview
![](https://github.com/0xWerz/CTF-writeups/blob/main/GDG-Algiers-2022/web/cookauth/img/pre.png?raw=true)


## Walkthrough

After visiting the webpage, if we check the cookies given `_info_user` is looking like holding a hexdecimal value

![](./img/sess.png) 

Trying to decode it to a ascii:

![](./img/dec.png)

Changing `guest` to `admin` then encode it the hex again


![](./img/en.png)

visiting the admin page with it, and it is valid! 