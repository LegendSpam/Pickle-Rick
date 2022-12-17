# Try Hack Me - Pickle Rick

>## LegendSpam(@mr!t G!r!) | Oct 07 2022

Today we are going to be attempting to complet the Pickel Rick from from Try Hack Me!
https://tryhackme.com/room/picklerick

## Disclaimer - My IP address will be different from yours!

># Task 1 Pickle Rick 
![image](https://i.imgur.com/o9pyhyU.jpg)

This Rick and Morty themed challenge requires you to exploit a webserver to find 3 ingredients that will help Rick make his potion to transform himself back into a human from a pickle.

Deploy the virtual machine on this task and explore the web application: `10.10.44.156`

You can also access the web app using the following link: `https://10-10-44-156.p.thmlabs.com` (this will update when the machine has fully started)

# Scanning & Enumeration
## Nmap
```yaml
┌──(kali㉿kali)-[~/Documents/THM/Pickle Rick/nmap]
└─$ nmap -sC -sV -Pn -vv -T4 -oN nmap/initial 10.10.44.156
# Nmap 7.92 scan initiated Fri Oct  7 13:19:27 2022 as: nmap -sC -sV -Pn -vv -T4 -oN nmap/initial 10.10.44.156
Nmap scan report for 10.10.44.156
Host is up, received user-set (0.18s latency).
Scanned at 2022-10-07 13:19:27 EDT for 30s
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 70:eb:db:e2:c1:74:52:5a:4d:50:b6:58:f7:7b:6f:3f (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDmNZ/LRtFzoExAveXjcyinEc8V0ub+8PTkh20JexjMf1UnxZP0Svfu7rHhnJ9+7yAbOlxtaOKJbYo7CxaQv7GIVOtncRkQYp20R/Y60COF0S+4Jji8HQnMuCPofnq4bN9xPXiWC6wDPtPcsjAmBYUki4GK/5VKLLVtHq3nIbUAYiApZJMyEdkGCilc3+KnNMoZsl3tmCn4hKHFciu94FfucoQPYs6GudXb2eIKHFZDtEfpdKYIc5quWgLNzLL4kUi47r/8blOwPwQQ2Hs1g/AExSAdboDPZ5MTRArc5NyA5qgH5kWvmkjHb8s0DyPsQQDTesMiwRgVbBhIXO2ye+Zr
|   256 31:7b:bc:86:ba:b4:d3:ba:42:c9:c6:d3:8f:22:5c:5d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBO/ALQrxvy7ApoXK7cGYQN0KjqECM2LJe5nWvHM0ULX1kmznXJm+F71yrydzD5p625jSsoGO9pm+z/L8BaKa0F0=
|   256 6c:6e:e1:5b:b7:43:ce:38:e9:72:08:f6:94:7a:3a:a7 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAID+V2UKEbTGCvtAk/LhcrL4yjNut9k3D2xTbnlpBA7B4
80/tcp open  http    syn-ack Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Rick is sup4r cool
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Oct  7 13:19:57 2022 -- 1 IP address (1 host up) scanned in 30.16 seconds
                                                                                     
┌──(kali㉿kali)-[~/Documents/THM/Pickle Rick/nmap]
└─$ 

```
# Enumerating Services

### Port 22- ssh

Here we are able to see that our SSH port is open. This port isn't too vulnerable unless we have found someones credentials. Se we will not mess with it.

### Port 80- http

It is here were we find a webservice. Lets poke around and see if there is anyting interesting.

![image](https://raw.githubusercontent.com/CCrashZer0/PickleRick/master/images/webService.png)

It looks as if we have a web page. It also seems that Rick really does need our help.
Checking out the source code of a page can reveal all sorts of things. 
```yaml
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Rick is sup4r cool</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="assets/bootstrap.min.css">
  <script src="assets/jquery.min.js"></script>
  <script src="assets/bootstrap.min.js"></script>
  <style>
  .jumbotron {
    background-image: url("assets/rickandmorty.jpeg");
    background-size: cover;
    height: 340px;
  }
  </style>
</head>
<body>

  <div class="container">
    <div class="jumbotron"></div>
    <h1>Help Morty!</h1></br>
    <p>Listen Morty... I need your help, I've turned myself into a pickle again and this time I can't change back!</p></br>
    <p>I need you to <b>*BURRRP*</b>....Morty, logon to my computer and find the last three secret ingredients to finish my pickle-reverse potion. The only problem is,
    I have no idea what the <b>*BURRRRRRRRP*</b>, password was! Help Morty, Help!</p></br>
  </div>

  <!--

    Note to self, remember username!

    Username: R1ckRul3s <--- THIS IS OUR USER NAME 

  -->

</body>
</html>

```
Great it looks like we have found a user name. Lets take note of that and keep digging, maybe we will get lucky and find the password.

# Finding hidden directories

We are going to be using gobuster to try and locate any directories that may have been hidden from us.
Command: `gobuster -u http://10.10.82.95 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x php,sh, txt,cgi,html,css,js,py`

## Command Breakdown:
```yaml
-u: Full URL (including scheme), or base domain name.

-w: Path to the wordlist used for brute forcing (use – for stdin).

-x: List of extensions to check for, if any.  
```
```yaml
┌──(kali㉿kali)-[~/Documents/THM/Pickle Rick]
└─$ gobuster dir -u "http://10.10.44.156" -w /usr/share/dirbuster/wordlists/directory-list-1.0.txt -x php,sh, txt,cgi,html,css,js,py
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.44.156
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-1.0.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php,sh,
[+] Timeout:                 10s
===============================================================
2022/10/07 13:26:42 Starting gobuster in directory enumeration mode
===============================================================
/assets               (Status: 301) [Size: 313] [--> http://10.10.44.156/assets/]
/portal.php           (Status: 302) [Size: 0] [--> /login.php]                   
/login.php            (Status: 200) [Size: 882]                                  
Progress: 38364 / 566836 (6.77%)
                         ^C
===============================================================
2022/10/07 13:58:42 Finished
===============================================================
```
It looks like GoBuster was able to find a few directories for us.
How about we go and check out the login.php. 

![image](https://raw.githubusercontent.com/CCrashZer0/PickleRick/master/images/login.png)

Eariler we found a username but at this moment we still do not have a password.
Most websites will have a robots.txt file that will tell a browser what is and isn't allowed to index. So lets see if there is one for this site.
Success! It seems that there is a robots.txt for this site and in that file is a very obvious reference to the show Rick and Morty! Lets try this and 
![image](https://raw.githubusercontent.com/CCrashZer0/PickleRick/master/images/robots.png)see if it our password.

YES! We where able to get loged in and we where brought to the command portal. 
![image](https://raw.githubusercontent.com/CCrashZer0/PickleRick/master/images/commandPortal.png)

Now since this page is called the command portal lets try and actually enter in some command and see what happens.
If we enter in the ls command we can see that some results are being returned. It would seem that we have also found our first ingredient.
![image](https://raw.githubusercontent.com/CCrashZer0/PickleRick/master/images/lsCommand.png)
I wonder if we can access the Sup3rS3cretPickl3Ingred.txt the same way we did our robots.txt file. 


# Answer the questions below

## What is the first ingredient Rick needs?
Now type Command Panel followin command:
```yaml
 ls
 less Sup3rS3cretPickl3Ingred.txt

```
## `@nswer`
```yaml
 **. ******* ****
```
## Whats the second ingredient Rick needs?
Now type Command Panel followin command:
```yaml
 less '/home/rick/second ingredients'
```
## `@nswer`
```yaml
 * ***** ****
```

## Whats the final ingredient Rick needs?
Now type Command Panel followin command:
```yaml
 sudo less /root/3rd.txt
```
## `@nswer`
```yaml
 ***** *****
```
