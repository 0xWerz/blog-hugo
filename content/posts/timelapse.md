---
---
title: "Timelapse -- HTB walkthrough"
Author : '0xWerz'
description: "https://app.hackthebox.com/machines/Undetected"
date: 2022-02-19T06:24:55+13:00
draft: true
tags : ['htb', 'linux', 'medium', 'retired']
aliases: [/ctf/htb/boxes/timelapse/]
---
---

The official [box page](https://app.hackthebox.com/machines/Timelapse) on HTB

{{< img-border-radius src="https://github.com/0xWerz/CTF-writeups/raw/main/HTB/Machines/timelapse/img/Timelapse.png" radius="3%" >}}

### The writeup:

#### System Scan | **IP: 10.10.11.152**

let's add the ip to to the `/etc/hosts` file and name it `undetected.htb`

> `echo '10.10.11.152 undetected.htb ' >> /etc/hosts`

startup full ports nmap scan | **-sC for the default set of scripts**. | **-sV for Enables version detection**. | **-T4 for sending the traffic fast**.

> `nmap -sC -sV -T4 10.10.11.152 -p-

```bash
werz@ctf01:~/ctf/htb/undetected$ nmap -sC -sV -T4 10.10.11.152 -p-
Starting Nmap 7.80 ( https://nmap.org ) at 2022-07-06 02:41 +01
Nmap scan report for 10.10.11.152
Host is up (0.29s latency).
Not shown: 998 filtered ports 

PORT      STATE SERVICE           VERSION
53/tcp    open  domain            Simple DNS Plus
88/tcp    open  kerberos-sec      Microsoft Windows Kerberos (server time: 2022-07-15 10:09:34Z)
135/tcp   open  msrpc             Microsoft Windows RPC
139/tcp   open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp   open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?
3268/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
3269/tcp  open  globalcatLDAPssl?
5986/tcp  open  ssl/http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
| ssl-cert: Subject: commonName=dc01.timelapse.htb
| Not valid before: 2021-10-25T14:05:29
|_Not valid after:  2022-10-25T14:25:29
|_ssl-date: 2022-07-15T10:11:07+00:00; +7h59m59s from scanner time.
| tls-alpn: 
|_  http/1.1
9389/tcp  open  mc-nmf            .NET Message Framing
49667/tcp open  msrpc             Microsoft Windows RPC
49673/tcp open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc             Microsoft Windows RPC
49696/tcp open  msrpc             Microsoft Windows RPC
56370/tcp open  msrpc             Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 7h59m58s, deviation: 0s, median: 7h59m57s
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2022-07-15T10:10:30
|_  start_date: N/A
```



## SMB enumeration

 Since smb port-445 is open, as always I'll check for some accessible content  
