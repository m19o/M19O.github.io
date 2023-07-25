---
published: true
title: Hackthebox Tracback walkthrough
date: 2021-11-26 00:24:00 +0200
categories: [HTB]
tags: HTB
---

<img src="https://i.ibb.co/Y833pYB/Trace-Back.jpg" alt="Trace-Back" border="0">

<h1>Enumeration<h1>

<blockquote>
<p>ِWe will use NMAP for enumeration phase, So let`s GO !.</p>
</blockquote>

<img src="https://i.ibb.co/7j6CNBQ/Enumeration.png" alt="Enumeration" border="0">
  
<blockquote>
<p>ِSo NMAP found that port 22 and port 80 are open.</p>
<p>Let`s Check port 80.</p> 
</blockquote>

<img src="https://i.ibb.co/zQw9qBK/Port-80.png" alt="Port-80" border="0">
  
<blockquote>
<p>ِOuch! look like someone was here before us.</p>
<p>ِLet`s view the source code.</p>
</blockquote>
  
<img src="https://i.ibb.co/nrqgsZL/1-b-U1oqc-2-WAFbyzo4-CS6-Z6g.png" alt="1-b-U1oqc-2-WAFbyzo4-CS6-Z6g" border="0">

<blockquote>
<p>ِHe left a backdoor for us.</p>
<p>Now search for Xh4h web shell</p>
</blockquote>

<img src="https://i.ibb.co/vYKQgxh/Capture.png" alt="Capture" border="0">

<img src="https://i.ibb.co/nrqgsZL/1-b-U1oqc-2-WAFbyzo4-CS6-Z6g.png" alt="1-b-U1oqc-2-WAFbyzo4-CS6-Z6g" border="0">
  
<blockquote>
<p>Now let`s clone it and try them.</p>
</blockquote>
 
<img src="https://i.ibb.co/HDVcDyS/tj5AChI.png" alt="tj5AChI" border="0">
<img src="https://i.ibb.co/R9f62Hj/image.png" alt="image" border="0">
<img src="https://i.ibb.co/ZVQMC8V/image.png" alt="image" border="0">
 
<blockquote>
<p>So it`s smevk.php webshell.</p>
<p>let`s open the shell and see what is in it.</p>
</blockquote>
 
<h1>Foothold<h1>
  
<img src="https://i.ibb.co/kSQ1sjt/image.png" alt="image" border="0">

<blockquote>
<p>We found the password.</p>
<p>let`s login.</p>
</blockquote>

<img src="https://i.ibb.co/0jSLmVs/1-D9x-Hd7-S-k-La-Rzy-Guznj-RBg.png" alt="1-D9x-Hd7-S-k-La-Rzy-Guznj-RBg" border="0">

<blockquote>
<p>We are logged as WebAdmin.</p>
<p>let`s discover what we can do.</p>
</blockquote>
 
<img src="https://i.ibb.co/BcfnG3x/image.png" alt="image" border="0">  
<img src="https://i.ibb.co/ZWwRBVr/i9uJTaY.png" alt="i9uJTaY" border="0">
<img src="https://i.ibb.co/fnJQcdJ/image.png" alt="image" border="0">  

<blockquote>
<p>So i found that i can log in as webadmin by SSH.</p>
<p>let`s upload our public key.</p>
</blockquote>

<img src="https://i.ibb.co/JygDGcc/1-iv-UY-AD4zm-MI2-Hu-NMno-Q4w.jpg" alt="1-iv-UY-AD4zm-MI2-Hu-NMno-Q4w" border="0"> 

<blockquote>
<p>Execute this to use your public key.</p>
<p>echo “your-publickey” >> authorized_keys in Execute option in /home/webadmin/.ssh/ directory</p>
</blockquote>  
  
<blockquote>
<p>Let`s log in now !.</p> 
</blockquote>
  
<img src="https://i.ibb.co/bdLjz9F/image.png" alt="image" border="0">

<h1>Privilege Escalation<h1>
 
<blockquote>
<p>We need to see what i can do without sudo password.</p> 
<p>We can switch to sysadmin</p>
</blockquote>  
  
<img src="https://i.ibb.co/QpLcmL4/image.png" alt="image" border="0">  

<h1>User hash</h1>
  
<img src="https://i.ibb.co/vD5bYX2/image.png" alt="image" border="0">
  
<blockquote>
<p>Let`s run Pspy to see runing proccess.</p> 
</blockquote>   

<img src="https://i.ibb.co/N7jYx3c/image.png" alt="image" border="0">
  
<blockquote>
<p>Gotcha !.</p> 
</blockquote>
  
<img src="https://i.ibb.co/wCBrqjP/image.png" alt="image" border="0">
  
<blockquote>
<p>00-header displays when we log by ssh as webadmin so we need to make our reverse shell.</p> 
<p>I used <a href="http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet">Pentestmonkey</a> cheatsheet  </p>
</blockquote>
  
<img src="https://i.ibb.co/ygS70XQ/image.png" alt="image" border="0">
  
<blockquote>
<p>Start you nc and log as webadmin and you will get root access.</p> 
</blockquote> 

<img src="https://i.ibb.co/f0kbR6D/1-9f-Z-f-RTAn3opew-C01-QSh-HQ.png" alt="1-9f-Z-f-RTAn3opew-C01-QSh-HQ" border="0">  

<h1>Thanks for Reading 🙏</h1>
