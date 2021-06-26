---
layout: post
title: "HackTheBox - Spectra"
subtitle: "Spectra - some common vulns."
date: 2021-06-25 23:45:13 -0400
background: '/img/blue_wave.jpg'
---

# Introduction

This is a write-up for a retired "easy" HackTheBox machine - Spectra.

# Enumeration

Starting of with a standard nmap scan :

```bash
sudo nmap -sV -sC -oA spectra 10.10.10.229
```

This reveals :

```
22/tcp   open  ssh              OpenSSH 8.1 (protocol 2.0)
| ssh-hostkey: 
|_  4096 52:47:de:5c:37:4f:29:0e:8e:1d:88:6e:f9:23:4d:5a (RSA)
80/tcp   open  http             nginx 1.17.4
|_http-server-header: nginx/1.17.4
|_http-title: Site doesn't have a title (text/html).
3306/tcp open  mysql            MySQL (unauthorized)
8081/tcp open  blackice-icecap?
| fingerprint-strings: 
|   FourOhFourRequest, GetRequest: 
|     HTTP/1.1 200 OK
|     Content-Type: text/plain
|     Date: Tue, 15 Jun 2021 12:52:15 GMT
|     Connection: close
|     Hello World
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Content-Type: text/plain
|     Date: Tue, 15 Jun 2021 12:52:21 GMT
|     Connection: close
|_    Hello World
```

After checking port 80 we get the following webpage :

![Port 80](/img/posts/htb-spectra/port80.png)

Clicking on any link opens a new tab - however we cant access it until we add spectra.htb to our /etc/hosts :

![Domain not added to /etc/hosts](/img/posts/htb-spectra/page-not-found.png)

So we can add it :

![Adding new entry to /etc/hosts](/img/posts/htb-spectra/adding-new-hosts.png)

After this we can check the first link and it will lead us to wordpress website :

![Wordpress revealed](/img/posts/htb-spectra/domain-init.png)

After manually enumerating the webpage and login pageI came up with no useful information other than that the wordpress username is *administrator* which is quite common. I decided to use gobuster :

![Gobuster reveals /testing directory](/img/posts/htb-spectra/gobuster.spectra.png)

Gobuster reveals /testing directory. Lets check it out :

!["testing" directory](/img/posts/htb-spectra/testing-dir.png)

Checking the file **wp.content.save** opens up a new blank page. However after checking the source code with CRTL+U it reveals php code with cleartext credentials :

![Cleartext credentials](/img/posts/htb-spectra/cleartext-creds.png)

The intersting thing is *devteam01* which could be the password for the wordpress page at *http://spectra.htb/main/wp-login.php*
Trying the credentials administrator:devteam01 we are in :

![Entered administrator's account](/img/posts/htb-spectra/wplogin.png)

# Exploitation


Now that we are in i decided to use wordpwn.py script - which will create a malicious plugin which we can install and which will send us a reverse shell. Source code available [here](#Resources).


![Generating shell](/img/posts/htb-spectra/making-the-payload.png)

Running this will create **malicious.zip** in the current directory and automatically run the *multi/handerl* stub from Metasploit.

Only thing we need to do is :

```
1. Upload plugin
2. Install Plugin
3. Activate Plugin
4. Go to http://spectra.htb/main/wp-content/plugins/malicious/wetw0rk_maybe.php
5. Check our previously set-up listener
```

Uploading plugin :

![Uploading the plugin](/img/posts/htb-spectra/uploadingplugin.png)

After this click the blue *Install* button, and after the process is done go to the previously mentioned address:

![Reverse shell activation](/img/posts/htb-spectra/reverse-shell-activation.png)

Then we can check our listener: 

![Listener](/img/posts/htb-spectra/meterpreter-opened.png)


# Privilege Escalation To User

After speding some time enumerating i found a file *passwd* in /etc/autologin. It contained a password:

```
SummerHereWeCome!!
```

Checking the /etc/password we can see one user :

```
katie:x:20156:20157::/home/katie:/bin/bash
```

After this lets try to SSH as user *katie*:

![User](/img/posts/htb-spectra/katie-user.png)

Success.

# Privilege Escalation To Root

First things first. Lets check if we can run something as root - sudo -l: 

![Sudo -l successful](/img/posts/htb-spectra/sudo.png)

So we can manage user binaries with initctl and have root privilege by doing so. This is a critical vulnerability which can be easily exploited. All we need to do is:

1. Edit the configuration file of the service at /etc/init
2. Start the same service with root privileges


Edit the /etc/initd/test.conf and put the following code:

![Editing /etc/init/test](/img/posts/htb-spectra/script-edit.png)

Now start the service and execute /bin/bash with -p flag:

![Root](/img/posts/htb-spectra/root.png)

Successfully rooted.


# Conclusion & Tips

Interesting box which combines the common misconfigurations - plaintext passwords out in the open and a common user having root privileges to a binary which controls the configuration of other binaries. All in all - a good practice.

# Resources

[Wordpress Malicious Plugin](https://github.com/wetw0rk/malicious-wordpress-plugin)

[SUDO Exploit - /sbin/init](https://isharaabeythissa.medium.com/sudo-privileges-at-initctl-privileges-escalation-technique-ishara-abeythissa-c9d44ccadcb9)

<head>
  <!-- add the button style & script -->
  <link rel="stylesheet" href="/assets/applause-button.css" />
  <script src="/assets/applause-button.js"></script>
</head>
<body>
  <!-- add the button! -->
  <applause-button style="width: 58px; height: 58px;" color="blue" url="https://p4r490n.github.io/2021/06/06/TryHackMe-Archangel.html"/>
</body>