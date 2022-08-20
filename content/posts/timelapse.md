---
---
title: "Timelapse -- HTB walkthrough"
Author : '0xWerz'
description: "https://app.hackthebox.com/machines/Undetected"
date: 2022-03-26T06:24:55+13:00
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
werz@ctf01:~/ctf/htb/timelapse$ nmap -sC -sV -T4 10.10.11.152 -p-
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

 Since smb port-445 is open, as always I'll check for some accessible content im using [smbmap](https://www.google.com/search?q=smbmap&oq=smbmap&aqs=chrome..69i57.262j0j7&sourceid=chrome&ie=UTF-8):

```bash
werz@ctf01:~/ctf/htb/timelapse$ smbmap -H 10.10.11.152 -u ff                                                                                           INT ✘  06:58:25 PM 

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
 -----------------------------------------------------------------------------
     SMBMap - Samba Share Enumerator | Shawn Evans - ShawnDEvans@gmail.com   
                     https://github.com/ShawnDEvans/smbmap


[+] IP: 10.10.11.152:445    Name: timelapse.htb0          Status: Guest session       
        Disk                                                      Permissions    Comment
    ----                                                      -----------    -------
    ADMIN$                                                NO ACCESS    Remote Admin
    C$                                                    NO ACCESS    Default share
    IPC$                                                  READ ONLY    Remote IPC
    NETLOGON                                              NO ACCESS    Logon server share 
    Shares                                                READ ONLY    
    SYSVOL                                                NO ACCESS    Logon server share
```

So we have access to the shares disk! I'll dig in with smbclient | `-N` for none password you also can leave it empty. 

```bash
werz@ctf01:~/ctf/htb/timelapse$ smbclient \\\\10.10.11.152\\shares -N                                                                                  INT ✘  07:02:08 PM 
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Mon Oct 25 16:39:15 2021
  ..                                  D        0  Mon Oct 25 16:39:15 2021
  Dev                                 D        0  Mon Oct 25 20:40:06 2021
  HelpDesk                            D        0  Mon Oct 25 16:48:42 2021

        6367231 blocks of size 4096. 2043344 blocks available
```

Dev folder have a single file, which I'll grab:

```bash
smb: \> cd dev
smb: \dev\> ls
  .                                   D        0  Mon Oct 25 20:40:06 2021
  ..                                  D        0  Mon Oct 25 20:40:06 2021
  winrm_backup.zip                    A     2611  Mon Oct 25 16:46:42 2021
get qw
        6367231 blocks of size 4096. 2086053 blocks available
smb: \dev\> get winrm_backup.zip 
getting file \dev\winrm_backup.zip of size 2611 as winrm_backup.zip (3.4 KiloBytes/sec) (average 3.4 KiloBytes/sec)
```

HelpDesk folder have some files about LAPS they may be helpful:

```bash
smb: \> dir helpdesk/
  .                                   D        0  Mon Oct 25 16:48:42 2021
  ..                                  D        0  Mon Oct 25 16:48:42 2021
  LAPS.x64.msi                        A  1118208  Mon Oct 25 15:57:50 2021
  LAPS_Datasheet.docx                 A   104422  Mon Oct 25 15:57:46 2021
  LAPS_OperationsGuide.docx           A   641378  Mon Oct 25 15:57:40 2021
  LAPS_TechnicalSpecification.docx      A    72683  Mon Oct 25 15:57:44 2021

        6367231 blocks of size 4096. 2085269 blocks available
```

## Shell as legacyy

```bash

```
