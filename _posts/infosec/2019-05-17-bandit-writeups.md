---
layout: post
title:  "Bandit Writeup"
date:   2019-05-17 14:18:25 +0800
categories: bandit infosec
---
> Writeups for Overthewire Bandit (14-34)

This is a detailed write-up of my solutions to problems 14-34 for the Bandit wargame. This wargame is aimed at beginners, each level contains a password used to gain access to the next level. The game is linear, you start at Level 0 and move up towards Level 34. All passwords have been omitted out of respect for the wargame.

Due to the time of creating this post, I've decided to start the write up at level 14 and not re-do the previous levels.


# Level 14 → Level 15

First, we ssh into level 14 with the sshkey.private we got from the last level:

```bash
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org
```

Then, we find the password for the current level. It is stored in /etc/bandit_pass
```bash
cat /etc/bandit_pass/bandit14 
```

Now, we use nc to submit the password:

```bash
nc localhost 30000
```

And we're done!

> Things learnt: ssh, netcat, cat


# Level 15 → Level 16

For this level, we have to use OpenSSL. In essence, SSL is a layer on top of TCP. It offers socket-layer security by encrypting data between client and server.

First we connect to localhost port 30001 with the openssl command. Note: The s_client is a client that communicates thru ssl with the server.

```bash
openssl s_client -connect localhost:30001
```

We enter the password, and we're done!

> Things learnt: ssl, s_client


# Level 16 → Level 17

First, we scan for open ports in range 31000-32000 and find that port 31790 is open.

```bash
nc -z -v localhost 31000-32000
localhost [127.0.0.1] 31790 (?) open
```

Then, we openssl into the port, type in the password and we get the SSH file.

```bash
openssl s_client -connect localhost:31790
```

And, we're done!

> Things learnt: ssl, nc -z -v for port scanning


# Level 17 → Level 18

To get into the lab, we need to chmod 600 the ssh key first. Then, it is the matter of using a single command -> diff:

```bash
diff passwords.old passwords.new 
```

What's more interesting to me was why we always use chmod 600 for ssh keys. Here's what I found:

Turns out, file permissions are as follows. We use r(read) as 4, w(write) as 2 and x(execute) as 1, and according to the permission we want, we assign the total values:

rwx = 4 + 2 + 1 = 7
rw = 4 + 2 = 6
rx = 4 +1 = 5

Since it goes like user, group, others, 600 means only the user has permission to read and write.

> Things learnt: chmod permissions, diff


# Level 18 → Level 19

In this level, I'm assuming exit1 in .bashrc is hosing us out. So, we can either try entering with sftp or using -t command on ssh to open a pseudo-tty (a pseudo terminal, emulating a hardware text terminal).

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 -t cat readme
```

Note, we can also do that with *wechall* to log our points.

And, we're done!

> Things learnt: pseudo terminals (ssh -t), sftp


# Level 19 → Level 20

A quick lookup of what setuid gives us this:

>setuid() sets the effective user ID of the calling process. If the effective UID of the caller is root, the real UID and saved set-user-ID are also set.

Nice, now we just execute the command right after ./bandit20_do as it gives us privilege to access the password file as bandit20

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```
> Things learnt: setuid()


# Level 20 → Level 21

For this level, we have to open two sessions. For the first one, we echo the password of the current level and pipe it to a open port like this:

```bash
echo "GbKksEFF4yrVs6il55v6gwY5aVje5f0j" | nc -l -p 30001
```

Now, we open the second session and use the ./suconnect program.

```bash
./suconnect 30001
```

And we're done!

> Things learnt: nc -l -p


# Level 21 → Level 22

After cdíng into the directory, we see that the cronjob as follows:

```bash
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

Basically, a cronjob is used to schedule commands at a specific time. For this cronjob,it directs the output of */usr/bin/cronjob_bandit22.sh* into a null device (discards info)

So, we look into what the cronjob does:

```bash
cat /usr/bin/cronjob_bandit22.sh

#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

Since it directs its output to this fine, we will look into it and find the password!

> Things learnt: cronjobs, /dev/null


# Level 22 → Level 23

This is more or less the same as level 21. We look at the cronjob and find out it executes a shell script as follows:
```bash
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

Since it pipes the password to the temp file, we will reproduce the tmp/file with this command from mytarget:

```bash
mytarget=$(echo I am user bandit23 | md5sum | cut -d ' ' -f 1)
```

Now, we use the checksum and cat that file. And, we're done!

> Things learnt: bash scripts, md5sum, cut


# Level 23 → Level 24

As we look into the cronjob for bandit24, we can see the following script:

```bash
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        timeout -s 9 60 ./$i
        rm -f ./$i
    fi
done
```

From this, we know that every minute a script in */var/spool/bandit24* is being run and deleted. We simply have to create our own script to be run and voila.

```bash
#!/bin/bash

cat /etc/bandit_pass/bandit24 >> /tmp/abc22/bandit24pass
```

Then, we will find the bandit24 file in our directory. And, we're done!

# Level 24 - Level 25
