---
layout: post
title:  "Kioptrix Level 2 Walkthrough"
date:   2019-04-10 13:00:02 +0530
categories: [vulnhub, vulnerable-machine]
---
Kioptrix level 2 vulnhub link below: <br>
[Kioptrix Level 2 download page]
<br><br>
<h2>1. Enumeration</h2>
The first plan is starting with a port scan to see what services are running.
{% highlight shell_session %}
nmap -T4 -sV -A -oA nmapscan 192.168.0.70
Nmap scan report for 192.168.0.70
Host is up (0.12s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 3.9p1 (protocol 1.99)
| ssh-hostkey:
|   1024 8f:3e:8b:1e:58:63:fe:cf:27:a3:18:09:3b:52:cf:72 (RSA1)
|   1024 34:6b:45:3d:ba:ce:ca:b2:53:55:ef:1e:43:70:38:36 (DSA)
|_  1024 68:4d:8c:bb:b6:5a:bd:79:71:b8:71:47:ea:00:42:61 (RSA)
|_sshv1: Server supports SSHv1
80/tcp   open  http     Apache httpd 2.0.52 ((CentOS))
|_http-server-header: Apache/2.0.52 (CentOS)
|_http-title: Site doesnt have a title (text/html; charset=UTF-8).
111/tcp  open  rpcbind  2 (RPC #100000)
| rpcinfo:
|   program version   port/proto  service
|   100000  2            111/tcp  rpcbind
|   100000  2            111/udp  rpcbind
|   100024  1            647/udp  status
|_  100024  1            650/tcp  status
443/tcp  open  ssl/http Apache httpd 2.0.52 ((CentOS))
|_http-server-header: Apache/2.0.52 (CentOS)
|_http-title: Site doesnt have a title (text/html; charset=UTF-8)
| Not valid before: 2009-10-08T00:10:47
|_Not valid after:  2010-10-08T00:10:47
|_ssl-date: 2019-04-09T18:03:07+00:00; -3h09m40s from scanner time.
| sslv2:
|   SSLv2 supported
|   ciphers:
|     SSL2_RC4_64_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|_    SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
631/tcp  open  ipp      CUPS 1.1
| http-methods:
|_http-server-header: CUPS/1.1
|_http-title: 403 Forbidden
3306/tcp open  mysql    MySQL (unauthorized)
Host script results:
{% endhighlight %}<br>

The port scan results show SSH, HTTP, rpcbind, SLL, ipp and mysql running. The next is to perform
enumeration on these ports.
<br><br>
<h3>1.1 SSH</h3>
SSH is running the same versions as level 1 which are not vulnerable and searchsploit
came back with nothing on the SSH versions.
<br><br>
<h3>1.2 HTTP</h3>
The HTTP server is running apache 2.0.52 which is vulnerable to a `GET Denial of Service`
exploit. However, this will not help get a reverse shell. Dirb and Nikto does not reveal any useful information.
<br><br>
<h3>1.3 ipp CUPS 1.1 631</h3>
CUPS 1.1 has a remote command execution exploit, however i could not get this exploit to work.
<br><br>
<h2> 2.1 Exploitation of web server</h2>
The webpage contains a login promopt which could potentially be connected to the SQL database since
mysql is running on port 3306. SQL Injection could be tried here.<br><br>
![Kioptrix level 2 login prompt](/assets/images/Kioptrix/Kioptrix2-loginprompt.png)
<br><br>
An assumption is made that that the login query is using `SELECT * FROM users WHERE username='random' AND password='password'`
or something similar. To test for SQL Injection a random username was entered, and in the password field the following `1' or '1' = '1'#` was used. The query checks if the password is equal to '1' or 1=1. When clicking 'Login', the SQL Injection is
executed and login is successful.
<br><br>
{:refdef: style="text-align: center;"}
![Kioptrix level 2 Command Execution](/assets/images/Kioptrix/Kioptrix2-ping.png)
{: refdef}
<br><br>
A admin web console appears with a 'ping a network' input field. This looks similar to DVWA's command
execution part. An attempt is made to use a `;` after the IP address to try and execute the `id` command.
This works! The id is returned as `uid=48(apache) gid=48(apache) groups=48(apache)`. Since i do not have
root access, privilege escalation has to be done to try and get root.
<br><br>
<h2> 3.1 Post exploitation</h2>
After looking through the file system serveral useful information has been found.<br>
-> passwd file is viewable `cat /etc/passwd` which contains a list of all the users on the system. <br>
-> PHP file in www/html contains the SQL login username / password. <br>
-> The Kernel version is retrieved using `uname -r` which is running 2.6.9-55.EL and the system is running CentOS.
<br><br>
Searchsploit is used to find kernel exploits of `2.6.9`, a local exploit is found: <br><br>
[LINK: ExploitDB - Linux Kernel 2.4.x/2.6 Local Privilege Escalation]
<br><br>
Even though an exploit has been found, the exploit needs to be downloaded on the system.
After looking around, i found that the /tmp directory has write permissions for everyone.
Using `wget`, the exploit is downloaded from an external HTTP server that i setup. The exploit is
then compiled using `gcc exploit.c -o exploit` which allows the exploit to be executed. When exploit
is executed, the privileges are escalated and i have root access (shown below).
<br><br>
![Kioptrix level 2 Root access gained ](/assets/images/Kioptrix/Kioptrix2-root-access-gained.png)



[Kioptrix Level 2 download page]: https://www.vulnhub.com/entry/kioptrix-level-11-2,23/
[LINK: ExploitDB - Linux Kernel 2.4.x/2.6 Local Privilege Escalation]: https://www.exploit-db.com/exploits/9545
