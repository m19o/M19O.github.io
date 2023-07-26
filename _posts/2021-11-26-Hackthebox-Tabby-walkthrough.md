---
published: true
title: Hackthebox Tabby walkthrough
date: 2021-11-26 04:07:00 +0200
categories: [HTB]
tags: HTB
---
<img src="https://i.ibb.co/8X9PmsQ/124295832-3653953797976480-4510901479630080213-o.jpg" alt="124295832-3653953797976480-4510901479630080213-o" border="0">


# Enumeration<
<blockquote>
ِNmap TIME!.
</blockquote>
  
<img src="https://i.ibb.co/3RXLC45/Scanning-0.png" alt="Scanning-0" border="0"> 
  
<blockquote>
ِِِAs you can see 80,22,8080 are open.
Let`s start checking
</blockquote>
  
<img src="https://i.ibb.co/9HS7cmz/http-1.png" alt="http-1" border="0">
<img src="https://i.ibb.co/VV786ZF/8080-2.png" alt="8080-2" border="0">
  
<blockquote>
ِAfter some recon i didn`t found anything in 8080.
ِBut i searched for megahosting exploit i found LFI so let`s try it.
</blockquote>

<img src="https://i.ibb.co/sRfr3Tn/LFI-3.png" alt="LFI-3" border="0">

<blockquote>  
ِBANG ! , we got credentials.  
After some search i found out that we can have a reverse shell by uploading it to manager page
</blockquote> 
  
# Exploitation
<img src="https://i.ibb.co/GHydR91/To-Create-Payload-and-upload-it-3.png" alt="To-Create-Payload-and-upload-it-3" border="0">
<blockquote>  
ِAfte uploading our shell let`s execute it.  
</blockquote>  

<img src="https://i.ibb.co/0yBtsS8/executing-the-payload-4.png" alt="executing-the-payload-4" border="0"> 
<img src="https://i.ibb.co/XXHFqSP/LISTENING-34-N-A5-OSH-3-LA-L-MAKNA-5.png" alt="LISTENING-34-N-A5-OSH-3-LA-L-MAKNA-5" border="0">  
  
<blockquote>
After some recon i found a backup file at /var/www/html
So i made an http server to download it to my machine and crack it 
Let`s crack it with FCRACKZIP  
</blockquote>  

  
<img src="https://i.ibb.co/XLQ7F1p/Crack-Backup-Zip-File.png" alt="Crack-Backup-Zip-File" border="0">  

# Privilege Escalation
<blockquote>
I didn`t find anything interesting in the file , In the recon phase i found ash user so i tried the password on it  
</blockquote>  
  
<img src="https://i.ibb.co/C0536bQ/User-hasheno.png" alt="User-hasheno" border="0">

<blockquote>  
BAM ! , we logged in and got user hash
I found this article about LXD privilege escalation and knowing that the user is in LXD group <a href="https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation">lxd privilege escalation</a> 
Let`s build !  

  
<img src="https://i.ibb.co/gRSk725/BUILDING-L-H3-ML-BEH-PRIV-ESC.png" alt="BUILDING-L-H3-ML-BEH-PRIV-ESC" border="0">
<img src="https://i.ibb.co/pnj3Dfw/start-server-to-priv-escalation.png" alt="start-server-to-priv-escalation" border="0">
<img src="https://i.ibb.co/kMg1mLr/Rooted.png" alt="Rooted" border="0">
 
  
# Thanks for Reading 🙏
