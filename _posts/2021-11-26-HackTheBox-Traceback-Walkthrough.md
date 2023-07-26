---
published: true
title: Hackthebox Tracback walkthrough
date: 2021-11-26 00:24:00 +0200
categories: [HTB]
tags: HTB
---

<img src="https://i.ibb.co/Y833pYB/Trace-Back.jpg" alt="Trace-Back" border="0">

# Enumeration

<blockquote>
ِWe will use NMAP for enumeration phase, So let`s GO !.
</blockquote>

<img src="https://i.ibb.co/7j6CNBQ/Enumeration.png" alt="Enumeration" border="0">
  
<blockquote>
ِSo NMAP found that port 22 and port 80 are open.
Let`s Check port 80. 
</blockquote>

<img src="https://i.ibb.co/zQw9qBK/Port-80.png" alt="Port-80" border="0">
  
<blockquote>
ِOuch! look like someone was here before us.
ِLet`s view the source code.
</blockquote>
  
<img src="https://i.ibb.co/nrqgsZL/1-b-U1oqc-2-WAFbyzo4-CS6-Z6g.png" alt="1-b-U1oqc-2-WAFbyzo4-CS6-Z6g" border="0">

<blockquote>
ِHe left a backdoor for us.
Now search for Xh4h web shell
</blockquote>

<img src="https://i.ibb.co/vYKQgxh/Capture.png" alt="Capture" border="0">

<img src="https://i.ibb.co/nrqgsZL/1-b-U1oqc-2-WAFbyzo4-CS6-Z6g.png" alt="1-b-U1oqc-2-WAFbyzo4-CS6-Z6g" border="0">
  
<blockquote>
Now let`s clone it and try them.
</blockquote>
 
<img src="https://i.ibb.co/HDVcDyS/tj5AChI.png" alt="tj5AChI" border="0">
<img src="https://i.ibb.co/R9f62Hj/image.png" alt="image" border="0">
<img src="https://i.ibb.co/ZVQMC8V/image.png" alt="image" border="0">
 
<blockquote>
So it`s smevk.php webshell.
let`s open the shell and see what is in it.
</blockquote>
 
# Foothold
  
<img src="https://i.ibb.co/kSQ1sjt/image.png" alt="image" border="0">

<blockquote>
We found the password.
let`s login.
</blockquote>

<img src="https://i.ibb.co/0jSLmVs/1-D9x-Hd7-S-k-La-Rzy-Guznj-RBg.png" alt="1-D9x-Hd7-S-k-La-Rzy-Guznj-RBg" border="0">

<blockquote>
We are logged as WebAdmin.
let`s discover what we can do.
</blockquote>
 
<img src="https://i.ibb.co/BcfnG3x/image.png" alt="image" border="0">  
<img src="https://i.ibb.co/ZWwRBVr/i9uJTaY.png" alt="i9uJTaY" border="0">
<img src="https://i.ibb.co/fnJQcdJ/image.png" alt="image" border="0">  

<blockquote>
So i found that i can log in as webadmin by SSH.
let`s upload our public key.
</blockquote>

<img src="https://i.ibb.co/JygDGcc/1-iv-UY-AD4zm-MI2-Hu-NMno-Q4w.jpg" alt="1-iv-UY-AD4zm-MI2-Hu-NMno-Q4w" border="0"> 

<blockquote>
Execute this to use your public key.
echo “your-publickey” >> authorized_keys in Execute option in /home/webadmin/.ssh/ directory
</blockquote>  
  
<blockquote>
Let`s log in now !. 
</blockquote>
  
<img src="https://i.ibb.co/bdLjz9F/image.png" alt="image" border="0">

# Privilege Escalation
 
<blockquote>
We need to see what i can do without sudo password. 
We can switch to sysadmin
</blockquote>  
  
<img src="https://i.ibb.co/QpLcmL4/image.png" alt="image" border="0">  

# User hash
  
<img src="https://i.ibb.co/vD5bYX2/image.png" alt="image" border="0">
  
<blockquote>
Let`s run Pspy to see runing proccess. 
</blockquote>   

<img src="https://i.ibb.co/N7jYx3c/image.png" alt="image" border="0">
  
<blockquote>
Gotcha !. 
</blockquote>
  
<img src="https://i.ibb.co/wCBrqjP/image.png" alt="image" border="0">
  
<blockquote>
00-header displays when we log by ssh as webadmin so we need to make our reverse shell. 
I used <a href="http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet">Pentestmonkey</a> cheatsheet  
</blockquote>
  
<img src="https://i.ibb.co/ygS70XQ/image.png" alt="image" border="0">
  
<blockquote>
Start you nc and log as webadmin and you will get root access. 
</blockquote> 

<img src="https://i.ibb.co/f0kbR6D/1-9f-Z-f-RTAn3opew-C01-QSh-HQ.png" alt="1-9f-Z-f-RTAn3opew-C01-QSh-HQ" border="0">  

# Thanks for Reading 🙏
