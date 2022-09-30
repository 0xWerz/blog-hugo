---
title: "Shoppy"
date: 2022-09-18T12:27:01+13:00
draft: true
description: "https://app.hackthebox.com/machines/shoppy"
tags : ['htb', 'linux', 'easy', 'active']
aliases: ['/ctf/htb/boxes/shoppy/']
---


The official [box page](https://app.hackthebox.com/machines/shoppy) on HTB

{{< img-border-radius src="https://github.com/0xWerz/CTF-writeups/raw/main/HTB/Machines/shoppy/img/shoppy.png" radius="3%" >}}

### The writeup:

#### System Scan | **IP: 10.10.11.152**

let's add the ip to to the `/etc/hosts` file and name it `timelapse.htb`

> `echo '10.10.11.152 timelapse.htb ' >> /etc/hosts`

startup full ports nmap scan | **-sC for the default set of scripts**. | **-sV for Enables version detection**. | **-T4 for sending the traffic fast**.

> nmap -sC -sV -T4 10.10.11.180 -p-