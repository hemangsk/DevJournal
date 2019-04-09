---
layout: post
title:  "OverTheWire: Bandit Walkthrough"
date:   2019-04-06 22:01:43 +0530
categories: [OverTheWire, CTF]
---
OverTheWire Bandit - The challenges are a great way of practicing Linux commands. There are 34 challenges in total to complete. Each challenge give you the password to the next one and so on.

[OverTheWire: Bandit - link]

<br>

<h2>Bandit 0:</h2>

The first challenge goal is login via SSH over port 2220

{% highlight shell_session %}
ssh -p 2220 bandit0@bandit.labs.overthewire.org

bandit0@bandit:~$ ls -lrt
total 4
-rw-r----- 1 bandit1 bandit0 33 Oct 16 14:00 readme

bandit0@bandit:~$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
bandit0@bandit:~$
{% endhighlight %}
The readme file outputted the password above.

<br>
<h2>Bandit 1:</h2>

The file uses a `-` which cannot be viewed by just typing in `cat -`. The relative path has to be used since the `-` is used for STDIN.

{% highlight shell_session %}
bandit1@bandit:~$ ls -l
total 4
-rw-r----- 1 bandit2 bandit1 33 Oct 16 14:00 -

bandit1@bandit:~$ pwd
/home/bandit1

bandit1@bandit:~$ cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
bandit1@bandit:~$
{% endhighlight %}

<br>
<h2>Bandit 2:</h2>

The file can be viewed by just entering a few letters of the file and pressing tab to auto complete, instead of manually typing in the `\`.

{% highlight shell_session %}
bandit2@bandit:~$ ls -l
total 4
-rw-r----- 1 bandit3 bandit2 33 Oct 16 14:00 spaces in this filename

bandit2@bandit:~$ cat spaces\ in\ this\ filename
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
bandit2@bandit:~$
{% endhighlight %}

<br>
<h2>Bandit 3:</h2>

The `ls` shows the inhere directory.

{% highlight shell_session %}
bandit3@bandit:~$ ls -l
total 4
drwxr-xr-x 2 root root 4096 Oct 16 14:00 inhere

bandit3@bandit:~$ cd inhere/
{% endhighlight %}
<br>
Inside the `inhere` directory there, the `-a` flag on `ls` shows the hidden file.

{% highlight shell_session %}
bandit3@bandit:~/inhere$ ls -la
total 12
drwxr-xr-x 2 root    root    4096 Oct 16 14:00 .
drwxr-xr-x 3 root    root    4096 Oct 16 14:00 ..
-rw-r----- 1 bandit4 bandit3   33 Oct 16 14:00 .hidden

bandit3@bandit:~/inhere$ cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
bandit3@bandit:~/inhere$
{% endhighlight %}

<br>
<h2>Bandit 4:</h2>

When viewing inside the `inhere` directory there are 9 files with one containing the password.

{% highlight shell_session %}
bandit4@bandit:~$ ls -l
total 4
drwxr-xr-x 2 root root 4096 Oct 16 14:00 inhere
bandit4@bandit:~$ cd inhere/
bandit4@bandit:~/inhere$ ls -l
total 40
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file00
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file01
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file02
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file03
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file04
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file05
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file06
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file07
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file08
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file09
{% endhighlight %}
<br>

All files begin with `-file`. The `*` (all) displays anything beginning with `-file`. The output shows `koReBOKuIDDepwhWk7jZC0RTdopnAYKh` which is the only human readable thing in the files.

{% highlight shell_session %}
bandit4@bandit:~/inhere$ cat ./-file*
??????????~%	C[?걱>??| ????U"7?w???H??ê?Q??(???#????T?v??(?ִ?????A*?
2J?Ş؇_?y7?.A??u??#???w$N?c?-??Db3??=???=<?W?????ht?Z??!??{?U
                                                            ?+??pm???;??:D??^??@?gl?Q??@?%@???ZP*E??1?V???̫*????koReBOKuIDDepwhWk7jZC0RTdopnAYKh
FPn?
    '?U???M??/u
               XS
?mu?z???хN?{??Y?d4????]3?????9(?
bandit4@bandit:~/inhere$
{% endhighlight %}

<br>
<h2>Bandit 5:</h2>

When viewing inside the `inhere` directory there are 9 files with one containing the password.

{% highlight shell_session %}
bandit4@bandit:~$ ls -l
total 4
drwxr-xr-x 2 root root 4096 Oct 16 14:00 inhere
bandit4@bandit:~$ cd inhere/
bandit4@bandit:~/inhere$ ls -l
total 40
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file00
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file01
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file02
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file03
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file04
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file05
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file06
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file07
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file08
-rw-r----- 1 bandit5 bandit4 33 Oct 16 14:00 -file09
{% endhighlight %}
<br>

<h2>Bandit 6:</h2>

Navigation all the files takes a long time, the `find` command is used to find and filter the results.
The search paramters are a 'file' and is '1033' bytes inside the inhere directory. The following filters are applied:<br>
1) The `-type -f` searches for a file.<br>
2) The `-size 1033c` searchs for a file with 1033 bytes.

{% highlight shell_session %}
bandit5@bandit:~$ find inhere/ -type f -size 1033c
inhere/maybehere07/.file2
bandit5@bandit:~$ cat inhere/maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
{% endhighlight %}
<br>

<h2>Bandit 7:</h2>

The password file contains 3 paramaters:<br>
1) A size of 33 bytes `-size 33c`<br>
2) Owned by group name of bandit6 `-group bandit6`<br>
3) Owned by user bandit7 `-user bandit7`<br>

{% highlight shell_session %}
bandit6@bandit:~$ find / -size 33c -group bandit6 -user bandit7
find: ‘/run/lvm’: Permission denied
find: ‘/run/screen/S-bandit22’: Permission denied
find: ‘/run/screen/S-bandit18’: Permission denied
/var/lib/dpkg/info/bandit7.password
... many more files
{% endhighlight %}<br>

All the files were 'permission denied' except 1. This file contained the password
{% highlight shell_session %}
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
{% endhighlight %}
<br>

<h2>Bandit 8:</h2>

The password is next to the word 'millionth' in the file. Using the `grep` command, the password is easily found.

{% highlight shell_session %}
bandit7@bandit:~$ cat data.txt | grep millionth
millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
{% endhighlight %}<br>

<h2>Bandit 9:</h2>

The data.txt file can be filtered by piping it through `sort` to sort the lines. Then using `uniq -u`,
this will only display unique lines being displayed.

{% highlight shell_session %}
bandit8@bandit:~$ cat data.txt | sort | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
{% endhighlight %}<br>

<br>





[OverTheWire: Bandit - link]: http://overthewire.org/wargames/bandit/
