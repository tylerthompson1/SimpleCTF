<h1 align="center">TryHackMe SimpleCTF Walkthrough</h1><br>


![SimpleCTF](https://i.imgur.com/VTwCI9O.png)

## Introduction

SimpleCTF is a beginner level CTF designed to demonstrate necessary skills needed when it comes to all CTFs such as scanning, enumeration, research, explotation, and privilege escalation

In this CTF I will walk through each question along with the steps I took to find the answers.

We are given an IP address to start. Upon visiting the site we are met with the following:
![HomePage](https://i.imgur.com/34bLQe8.png)

## Task 1
## How many services are running under port 1000?

Let's start with an nmap scan:

<code>nmap -sS -Pn -T4 -p- IP Address</code>

-sS allows us to do a stealth scan, avoiding the 3 part handshake in order to avoid detection.
-Pn Will disable ping and only scan for open ports in order to avoid detection.
-T4 quickens the scan, however, it is more aggressive.
-p- scans all ports. 

![Task1](https://i.imgur.com/4qNOCFV.png)

As we can see, there are two ports running under port 1000.

## Task 2
## What is running on the higher port?

Let's use the -A flag to learn more about these ports.

<code>nmap -A -T4 -p21,80,2222 IP Address</code>

This is what we get in return:

![Task2](https://i.imgur.com/UFruCfJ.png)

As we can see, port 2222 is running SSH.

## Task 3
## What's the CVE you're using against this application?

Here we need to some more enumeration. Using gobuster we'll find out about files and directories on the server.

<code>gobuster dir -w /usr/share/wordlists/dirb/common.txt -u IP Address</code>

![Task3](https://i.imgur.com/FcA5lnI.png)

Here we see a number of things, however, the /simple page is interesting.

Upon visiting it we see it is a webpage using CMS Made Simple v2.2.8.

![CMS1](https://i.imgur.com/1oFx5Gx.png)<br>![CMS2](https://i.imgur.com/Z6ykdGX.png)

Using searchsploit will tell us if there's any exploits we can use.

<code>searchsploit cms made simple 2.2.8</code>

Which returns:<br>
![searchsploit](https://i.imgur.com/yFIdut5.png)
If we google "CMS Made Simple 2.2.8 exploit" we find a page on Exploit-DB that tells us the CVE is CVE-2019-9053.<br>
![CVE](https://i.imgur.com/O7fNWEY.png)

## Task 4 
## To what kind of vulnerability is the application vulnerable?

Looking at the page on exploit-db we can see it is vulnerable to SQL Injection.

## Task 5
## What's the password?

We're going to download the script from exploit-db using -wget.
<code>wget https://www.exploit-db.com/download/46635</code>

Looking at the script, we see we need to use python2 since the print statements are using python2 syntax.

Upon running the exploit initially we also see we need to install the requests and termcolor modules. After doing so we run the following command as outlined in the script:<br>
<code>python2 exploit.py -u URL --crack -w /usr/share/wordlists/rockyou.txt</code><br>

![exploit](https://i.imgur.com/aGuaRcG.png)

We got it!

## Task 6
## Where can you login with the details obtained?

First, let's try to SSH into port 2222 since we know it is open.

<img width="716" alt="Screenshot 2024-01-19 at 9 59 44 AM" src="https://github.com/tylerthompson1/SimpleCTF/assets/53204601/d3fb2341-e8c6-4c75-826f-1912fd240156">
<br>
And we're in.

## Task 7
## What's the user flag?

If we use ls we see there is a user.txt. Using <code>cat user.txt</code> we can see the user flag is: G00d j0b, keep up!<br>
<img width="712" alt="image" src="https://github.com/tylerthompson1/SimpleCTF/assets/53204601/70e6f47c-ab30-45b8-b947-afa9cce76082">

## Task 8
## Is there any other user in the home directory? What's its name?

If we try to move up in the file structure using .. and use ls we see another user called "sunbath."<br>

<img width="696" alt="image" src="https://github.com/tylerthompson1/SimpleCTF/assets/53204601/26f73d59-ed62-4fea-8688-3692869e246a">

## Task 9
## What can you leverage to spawn a privileged shell?

Let's use -l to see the list of commands this user can run.<br>
<img width="718" alt="image" src="https://github.com/tylerthompson1/SimpleCTF/assets/53204601/b769ca19-224b-41a3-b737-2200c4dd83eb">

vim is the answer.

## Task 9
## What's the root flag?

We're going to use vim to get access to the root shell. 

We can access the filesystem using: <code>sudo vim -c ':!/bin/sh'</code><br>
Now all we need to do is change directories to the root directory.<br>
Using ls here we see a root.txt and using cat we find the root flag.<br>
<img width="709" alt="image" src="https://github.com/tylerthompson1/SimpleCTF/assets/53204601/6e23c761-1a54-4730-b921-e8f2bb0e85b4">


All done!
