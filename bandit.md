# Bandit
The beginner challenges.

## Level 0
Connect to the shell using:
```sh
ssh bandit0@bandit.labs.overthewire.org -p 2220
```
with password `bandit0` (provided in task).

The password for the next level is stored in a file called `readme`in home directory.
```sh
$ cat readme
NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL
```
This is the password for the next level, using `bandit1` as username.

## Level 1
The next password is also in a file in the home directory, named `-`:
```sh
$ cat < -
rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi
```

## Level 2
Password is stored in a file called `spaces in this filename`:
```sh
$ cat spaces\ in\ this\ filename
aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG
```

## Level 3
Password is in a hidden file in `./inhere/`:
```sh
$ ls -a
.  ..  .hidden
$ cat .hidden
2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe
```

## Level 4
Password is in the only human-readable file in the `inhere`directory. This `find` command goes through all the files (and subdirectories, were there any) and prints the content with the filename.
```sh
$ find ./ -type f | xargs tail -n +1
==> ./-file03 <==
EQ"p
4}]GAu[/9
==> ./-file06 <==
'cwk^jM;,co9
==> ./-file08 <==
׉ǰ6=>>ӫw<U'=@Zxj
==> ./-file07 <==
lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR

==> ./-file04 <==
MrjSr_E,G+h|+
=>KQ
==> ./-file00 <==
ŰBηb<QȠ+ViO1[5{
==> ./-file01 <==
jmDB0DtQ*)AV ]Ȕl
==> ./-file02 <==
x(z.T26 F8qqlYvFN#'~
==> ./-file09 <==
?3[ٲN|?G|bG[8y-́*
==> ./-file05 <==
2]o-p8q츑D.~&ϯ"PTI
```
Password is thus `lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR`.

## Level 5
Password is in a file in `inhere` with these properties:
- human-readable
- 1033 bytes in size
- not executable

```sh
$ find ./ -type f -size 1033c -not -executable -exec file {} \; | grep "ASCII text"
./maybehere07/.file2: ASCII text, with very long lines (1000)
$ cat maybehere07/.file2
P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU
```

## Level 6
Password is SOMEWHERE on the server. Hop into the root directory, then use a `find` command that finds what we are looking for, while removing error outputs:
```sh
$ find ./ -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
./var/lib/dpkg/info/bandit7.password
$ cat ./var/lib/dpkg/info/bandit7.password
z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S
```

## Level 7
Password is stored in a large text file, `data.txt`, next to the word `millionth`.
```sh
$ grep "millionth" data.txt
millionth       TESKZC0XvTetK0S9xNwm25STk5iWrBvP
```
## Level 8
Password is in the only line in `data.txt` that only occurs once.
```sh
$ sort data.txt | uniq -u
EN632PlfYiZbn3PhVK3XOGSlNInNE00t
```

## Level 9
Password is in `data.txt`, in one of the few human-readable strings, preceded by several `=` characters.
```sh
$ strings data.txt | grep "==="
4========== the#
========== password
========== is
========== G7w8LIi6J3kTb8A7j9LgrywtEUlyyp6s
```

## Level 10
Password is in `data.txt`, which contains base64-encoded data.
```sh
$ base64 -d data.txt
The password is 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM
```

## Level 11
Password is in `data.txt`, where content has been ROT13-encrypted (Letters have been shifted forward by 13 spaces). We can use the `tr` command for this.
```sh
$ tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
The password is JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv
```

## Level 12
The password is in `data.txt`, whichis a hexdump of a file that has been repeatedly compressed. The task suggests to do work in a /tmp/ directory, so let's do that:
```sh
$ mkdir /tmp/seb
$ cp data.txt /tmp/seb
$ cd /tmp/seb
```

Let's reverse the hex dump with `xxd`: 
```sh
$ xxd -r data.txt > output
$ file output
output.txt: gzip compressed data, was "data2.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 581
```
So this is a gzip compressed file. Let's rename it to a `.gz` file and uncompress:
```sh
$ mv output data2.gz
$ gzip -d data2.gz
$ file data2
data2: bzip2 compressed data, block size = 900k
```
Alright, so now it's bzip2 compressed. Repeat last step:
```sh
$ mv data2 data3.bz
$ bzip2 -d data3.bz
$ file data3
data3: gzip compressed data, was "data4.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 20480
```
Repeat:
```sh
$ mv data3 data4.gz
$ gzip -d data4.gz
$ file data4
data4: POSIX tar archive (GNU)
```
This time it's a tar archive.
```sh
$ mv data4 data5.tar
$ tar -xf data5.tar
$ file data5.bin
data5.bin: POSIX tar archive (GNU)

$ mv data5.bin data6.tar
$ tar -xf data6.tar
$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k

$ mv data6.bin data7.bz
$ bzip2 -d data7.bz
$ file data7
data7: POSIX tar archive (GNU)

$ mv data7 data8.tar
$ tar -xf data8.tar
$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Sun Apr 23 18:04:23 2023, max compression, from Unix, original size modulo 2^32 49

$ mv data8.bin data9.gz
$ gzip -d data9.gz
$ file data9
data9: ASCII text
$ cat data9
The password is wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw
```

## Level 13
The password is in `/etc/bandit_pass/bandit14`, which can only be accessed by user ´bandit14´. Logging on as `bandit13`gives us a private SSH key that we can use to log in. Let's save this key as `key` on our own system.
```sh
$ cat sshkey.private
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAxkkOE83W2cOT7IWhFc9aPaaQmQDdgzuXCv+ppZHa++buSkN+
gg0tcr7Fw8NLGa5+Uzec2rEg0WmeevB13AIoYp0MZyETq46t+jk9puNwZwIt9XgB
ZufGtZEwWbFWw/vVLNwOXBe4UWStGRWzgPpEeSv5Tb1VjLZIBdGphTIK22Amz6Zb
ThMsiMnyJafEwJ/T8PQO3myS91vUHEuoOMAzoUID4kN0MEZ3+XahyK0HJVq68KsV
ObefXG1vvA3GAJ29kxJaqvRfgYnqZryWN7w3CHjNU4c/2Jkp+n8L0SnxaNA+WYA7
jiPyTF0is8uzMlYQ4l1Lzh/8/MpvhCQF8r22dwIDAQABAoIBAQC6dWBjhyEOzjeA
J3j/RWmap9M5zfJ/wb2bfidNpwbB8rsJ4sZIDZQ7XuIh4LfygoAQSS+bBw3RXvzE
pvJt3SmU8hIDuLsCjL1VnBY5pY7Bju8g8aR/3FyjyNAqx/TLfzlLYfOu7i9Jet67
xAh0tONG/u8FB5I3LAI2Vp6OviwvdWeC4nOxCthldpuPKNLA8rmMMVRTKQ+7T2VS
nXmwYckKUcUgzoVSpiNZaS0zUDypdpy2+tRH3MQa5kqN1YKjvF8RC47woOYCktsD
o3FFpGNFec9Taa3Msy+DfQQhHKZFKIL3bJDONtmrVvtYK40/yeU4aZ/HA2DQzwhe
ol1AfiEhAoGBAOnVjosBkm7sblK+n4IEwPxs8sOmhPnTDUy5WGrpSCrXOmsVIBUf
laL3ZGLx3xCIwtCnEucB9DvN2HZkupc/h6hTKUYLqXuyLD8njTrbRhLgbC9QrKrS
M1F2fSTxVqPtZDlDMwjNR04xHA/fKh8bXXyTMqOHNJTHHNhbh3McdURjAoGBANkU
1hqfnw7+aXncJ9bjysr1ZWbqOE5Nd8AFgfwaKuGTTVX2NsUQnCMWdOp+wFak40JH
PKWkJNdBG+ex0H9JNQsTK3X5PBMAS8AfX0GrKeuwKWA6erytVTqjOfLYcdp5+z9s
8DtVCxDuVsM+i4X8UqIGOlvGbtKEVokHPFXP1q/dAoGAcHg5YX7WEehCgCYTzpO+
xysX8ScM2qS6xuZ3MqUWAxUWkh7NGZvhe0sGy9iOdANzwKw7mUUFViaCMR/t54W1
GC83sOs3D7n5Mj8x3NdO8xFit7dT9a245TvaoYQ7KgmqpSg/ScKCw4c3eiLava+J
3btnJeSIU+8ZXq9XjPRpKwUCgYA7z6LiOQKxNeXH3qHXcnHok855maUj5fJNpPbY
iDkyZ8ySF8GlcFsky8Yw6fWCqfG3zDrohJ5l9JmEsBh7SadkwsZhvecQcS9t4vby
9/8X4jS0P8ibfcKS4nBP+dT81kkkg5Z5MohXBORA7VWx+ACohcDEkprsQ+w32xeD
qT1EvQKBgQDKm8ws2ByvSUVs9GjTilCajFqLJ0eVYzRPaY6f++Gv/UVfAPV4c+S0
kAWpXbv5tbkkzbS0eaLPTKgLzavXtQoTtKwrjpolHKIHUz6Wu+n4abfAIRFubOdN
/+aLoRQ0yBDRbdXMsZN/jvY44eM+xRLdRVyMmdPtP8belRi2E2aEzA==
-----END RSA PRIVATE KEY-----
```
Then we can just log in:
```sh
ssh -i key bandit14@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
```
and find the password:
```sh
$ cat /etc/bandit_pass/bandit14
fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq
```

## Level 14
Using the password we just obtained, we submit this to `localhost:30000`:
```sh
$ echo "fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq" | nc localhost 30000
Correct!
jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt
```

## Level 15
This time we submit the last password to `localhost:30001` using SSL encryption. We will use `openssl`, but we want to use `-ign_eof` to easily view the password:
```sh
$ echo "jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt" | openssl s_client -connect localhost:30001 -ign_eof
CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 CN = localhost

...

read R BLOCK
Correct!
JQttfApK4SeyHwDlI9SXGR50qclOAil1

closed
```

## Level 16
The creds for the next level can be found by submitting the previous password to an open port in the range `31000-32000`.
```sh
$ nmap -sC -p 31000-32000 localhost
Starting Nmap 7.80 ( https://nmap.org ) at 2023-09-26 18:54 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00016s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2023-09-26T18:06:57
|_Not valid after:  2023-09-26T18:07:57
31691/tcp open  unknown
31790/tcp open  unknown
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2023-09-26T18:06:57
|_Not valid after:  2023-09-26T18:07:57
31960/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 2.86 seconds
```

Only `31518` and `31790` speak SSL, so these are the only interesting ones. Let's try `31790` - why would it be the first one?
```sh
$ echo "JQttfApK4SeyHwDlI9SXGR50qclOAil1" | openssl s_client -connect localhost:31790 -ign_eof
CONNECTED(00000003)
Can't use SSL_get_servername

...

read R BLOCK
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----

closed
```

## Level 17
Connect:
```sh
ssh -i key2 bandit17@bandit.labs.overthewire.org -p 2220
```
We are given two files: `passwords.old` and `passwords.new`. The password is in the only line that has changed between these two files:
```sh
$ diff passwords.old passwords.new
42c42
< glZreTEH1V3cGKL6g4conYqZqaEj0mte
---
> hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg
```
The last one is the next password.

## Level 18
Someone has modified `./bashrc` to log us out when logging in with SSH. We can use the SSH to just read the file:
```sh
$ ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat ./readme"
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password:
awhqfNnAbc1naukrpqDYcF95h7HoMTrC
```

## Level 19
We are given a `setuid` binary that let us run commands as another user. We are also told that the password is in `/etc/bandit_pass`:
```sh
$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do id

$ ./bandit20-do cat /etc/bandit_pass/bandit20
VxCazJaVykI6W36BkBU0mJTCM8rR95XT
```

## Level 20
We are given a `setuid` binary that makes a connection to localhost on a specified port, then it reads a line of text and compares it to the previous password. This means that we want to set up a listener that will return the password:
```sh
echo "VxCazJaVykI6W36BkBU0mJTCM8rR95XT" | nc -lp 5555 &
[1] 185448
```
This is now running in the background (because of the &). We can use the binary we got to connect to that port:
```sh
$ ./suconnect 5555
Read: VxCazJaVykI6W36BkBU0mJTCM8rR95XT
Password matches, sending next password
NvEJF7oVjkddltPSrdKEFOllh9V1IBcq
```

## Level 21
A program is running at regular intervals from `cron`. Look in `/etc/cron.d/` for the config and see what commands are being executed.
```sh
$ cd /etc/cron.d
$ ls
cronjob_bandit15_root  cronjob_bandit22  cronjob_bandit24       e2scrub_all  sysstat
cronjob_bandit17_root  cronjob_bandit23  cronjob_bandit25_root  otw-tmp-dir
$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```
Let's just keep following the paths:
```sh
$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
WdDozAdTM2z9DiFEQ2mGlwngMfj4EZff
```

## Level 22
More with `/etc/cron.d/`. Additional note: "Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints."

```sh
$ cd /etc/cron.d
$ ls
cronjob_bandit15_root  cronjob_bandit22  cronjob_bandit24       e2scrub_all  sysstat
cronjob_bandit17_root  cronjob_bandit23  cronjob_bandit25_root  otw-tmp-dir
$ cat cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

```sh
$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```
Here are the inner workings for how it makes the /tmp/ location where it puts the password. Let's do this with `bandit23`:
```sh
$ $(echo I am user bandit23 | md5sum | cut -d ' ' -f 1)
8ca319486bfbbc3663ea0fbe81326349: command not found
$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G
```

## Level 23
Similar to last level. Additional note: we are going to make our own shell script.
```sh
$ cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo || exit 1
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -rf ./$i
    fi
done
```
The script runs all scripts in `/var/spool/bandit24/foo`. Let's run a script that `cat`s `bandit24`'s password. We also give every user permission to edit it, as well as the file we write the output to:
```sh
$ mkdir /tmp/seb2
$ cd /tmp/seb2
$ vim script.sh
$ cat script.sh
#!/bin/bash

cat /etc/bandit_pass/bandit24 > /tmp/seb2/output
$ chmod 777 script.sh
$ touch output
$ chmod 666 output
$ cp script.sh /var/spool/bandit24/foo
```
Now we wait, and finally:
```sh
$ cat output
VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar
```

## Level 24
A port is listening on port `300002`and will give us the password for `bandit25` if given the previous password and a secret numeric 4-digit code. We need to brute force this. Let's just make a simple script that does this. We'll do this in a shell script for practice, but it seems we also have python.
```sh
$ mkdir /tmp/seb3
$ cd /tmp/seb3
$ vim script.sh
$ cat script.sh
#!/bin/bash

exec 3<>/dev/tcp/127.0.0.1/30002

read -r INIT <&3

for i in {0000..9999}; do
        echo "Trying $i"
        echo "VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar $i" >&3
        read -r REPLY <&3
        echo "$REPLY"
        if [[ ! $REPLY == *"Wrong"* ]]; then
                break
        fi
done
```
It's a bit slow, but it does the job.
```sh
...
Trying 9707
Wrong! Please enter the correct pincode. Try again.
Trying 9708
Correct!
```
Whoops, it cut off the rest of the response.
```sh
$ echo "VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar 9708" | nc localhost 30002
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Correct!
The password of user bandit25 is p7TaowMYrmu23Ol8hiZh9UvD0O9hpx8d

Exiting.
```

## Level 25
The shell for `bandit26` is not `/bin/bash`, but something else. Let's break out of it.
We are given a key:
```sh
$ ls
bandit26.sshkey
$ cat bandit26.sshkey
-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEApis2AuoooEqeYWamtwX2k5z9uU1Afl2F8VyXQqbv/LTrIwdW
pTfaeRHXzr0Y0a5Oe3GB/+W2+PReif+bPZlzTY1XFwpk+DiHk1kmL0moEW8HJuT9
/5XbnpjSzn0eEAfFax2OcopjrzVqdBJQerkj0puv3UXY07AskgkyD5XepwGAlJOG
xZsMq1oZqQ0W29aBtfykuGie2bxroRjuAPrYM4o3MMmtlNE5fC4G9Ihq0eq73MDi
1ze6d2jIGce873qxn308BA2qhRPJNEbnPev5gI+5tU+UxebW8KLbk0EhoXB953Ix
3lgOIrT9Y6skRjsMSFmC6WN/O7ovu8QzGqxdywIDAQABAoIBAAaXoETtVT9GtpHW
qLaKHgYtLEO1tOFOhInWyolyZgL4inuRRva3CIvVEWK6TcnDyIlNL4MfcerehwGi
il4fQFvLR7E6UFcopvhJiSJHIcvPQ9FfNFR3dYcNOQ/IFvE73bEqMwSISPwiel6w
e1DjF3C7jHaS1s9PJfWFN982aublL/yLbJP+ou3ifdljS7QzjWZA8NRiMwmBGPIh
Yq8weR3jIVQl3ndEYxO7Cr/wXXebZwlP6CPZb67rBy0jg+366mxQbDZIwZYEaUME
zY5izFclr/kKj4s7NTRkC76Yx+rTNP5+BX+JT+rgz5aoQq8ghMw43NYwxjXym/MX
c8X8g0ECgYEA1crBUAR1gSkM+5mGjjoFLJKrFP+IhUHFh25qGI4Dcxxh1f3M53le
wF1rkp5SJnHRFm9IW3gM1JoF0PQxI5aXHRGHphwPeKnsQ/xQBRWCeYpqTme9amJV
tD3aDHkpIhYxkNxqol5gDCAt6tdFSxqPaNfdfsfaAOXiKGrQESUjIBcCgYEAxvmI
2ROJsBXaiM4Iyg9hUpjZIn8TW2UlH76pojFG6/KBd1NcnW3fu0ZUU790wAu7QbbU
i7pieeqCqSYcZsmkhnOvbdx54A6NNCR2btc+si6pDOe1jdsGdXISDRHFb9QxjZCj
6xzWMNvb5n1yUb9w9nfN1PZzATfUsOV+Fy8CbG0CgYEAifkTLwfhqZyLk2huTSWm
pzB0ltWfDpj22MNqVzR3h3d+sHLeJVjPzIe9396rF8KGdNsWsGlWpnJMZKDjgZsz
JQBmMc6UMYRARVP1dIKANN4eY0FSHfEebHcqXLho0mXOUTXe37DWfZza5V9Oify3
JquBd8uUptW1Ue41H4t/ErsCgYEArc5FYtF1QXIlfcDz3oUGz16itUZpgzlb71nd
1cbTm8EupCwWR5I1j+IEQU+JTUQyI1nwWcnKwZI+5kBbKNJUu/mLsRyY/UXYxEZh
ibrNklm94373kV1US/0DlZUDcQba7jz9Yp/C3dT/RlwoIw5mP3UxQCizFspNKOSe
euPeaxUCgYEAntklXwBbokgdDup/u/3ms5Lb/bm22zDOCg2HrlWQCqKEkWkAO6R5
/Wwyqhp/wTl8VXjxWo+W+DmewGdPHGQQ5fFdqgpuQpGUq24YZS8m66v5ANBwd76t
IZdtF5HXs2S5CADTwniUS5mX1HO9l5gUkk+h0cH5JnPtsMCnAUM+BRY=
-----END RSA PRIVATE KEY-----
```
```sh
$ ssh -i key bandit26@bandit.labs.overthewire.org -p 2220
...
  _                     _ _ _   ___   __
 | |                   | (_) | |__ \ / /
 | |__   __ _ _ __   __| |_| |_   ) / /_
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/
Connection to bandit.labs.overthewire.org closed.
```
Hmm... it closed. A user's shell information is in /etc/passwd, so:
```sh
$ cat /etc/passwd | grep bandit26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
bandit25@bandit:~$ cat /usr/bin/showtext
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```
Now, this challenge is fucked up. Apparently we have to resize the terminal so that the program doesn't exit, then press `v` to open a vim terminal. From here, we can do `:set shell=/bin/bash` and `:shell` to get our shell.

```sh
$ cat /etc/bandit_pass/bandit26
c7GvcKlw9mC7aUQaPx7nwFstuAIBw1o1
```

## Level 26
Now we need to get the password for `bandit27`. We are conveniently given a `bandit27-do` that will help us get it:
```sh
$ ls
bandit27-do  text.txt
$ file bandit27-do
bandit27-do: setuid ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, BuildID[sha1]=c148b21f7eb7e816998f07490c8007567e51953f, for GNU/Linux 3.2.0, not stripped
$ ./bandit27-do
Run a command as another user.
  Example: ./bandit27-do id
$ ./bandit27-do cat /etc/bandit_pass/bandit27
YnQpBuifNMas1hcUFk70ZmqkhUU2EuaS
```

## Level 27
"There is a git repository at `ssh://bandit27-git@localhost/home/bandit27-git/repo` via the port `2220`. The password for the user `bandit27-git` is the same as for the user `bandit27`." 
Alright, simple enough. Log in to `bandit27` and make a `/tmp/` dir to clone the repo into:

```sh
$ mkdir /tmp/seb5
$ cd /tmp/seb5
```

When cloning the repo, note that the port is specified after `localhost`:
```sh
$ git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo
Cloning into 'repo'...
The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit27/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit27/.ssh/known_hosts).
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

bandit27-git@localhost's password:
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
$ cd repo
$ ls
README
$ cat README
The password to the next level is: AVanL161y9rsbcJIsFHuw35rjaOM19nR
```

## Level 28
This level is some of the same: log in to `bandit28` and clone `ssh://bandit28-git@localhost:2220/home/bandit28-git/repo` with the same pass.
```sh
$ cd repo
$ ls
README.md
$ cat README.md
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx
```
They have obviously leaked the password earlier. Use `git log` and `git show`:
```sh
$ git log README.md
commit 899ba88df296331cc01f30d022c006775d467f28 (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Sun Apr 23 18:04:39 2023 +0000

    fix info leak

commit abcff758fa6343a0d002a1c0add1ad8c71b88534
Author: Morla Porla <morla@overthewire.org>
Date:   Sun Apr 23 18:04:39 2023 +0000

    add missing data

commit c0a8c3cf093fba65f4ee0e1fe2a530b799508c78
Author: Ben Dover <noone@overthewire.org>
Date:   Sun Apr 23 18:04:39 2023 +0000

    initial commit of README.md
$ git show 899ba88df296331cc01f30d022c006775d467f28
commit 899ba88df296331cc01f30d022c006775d467f28 (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Sun Apr 23 18:04:39 2023 +0000

    fix info leak

diff --git a/README.md b/README.md
index b302105..5c6457b 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials

 - username: bandit29
-- password: tQKvmcwNYcFS6vmPHIUSI3ShmsrQZK8S
+- password: xxxxxxxxxx
```


## Level 29
Same as earlier, `$ git clone ssh://bandit29-git@localhost:2220/home/bandit29-git/repo`, etc.
This time the `README.md` looks like this:
```sh
$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: <no passwords in production!>
```
"no passwords in production"? Let's check out the branches:
```sh
$ git branch -r
  origin/HEAD -> origin/master
  origin/dev
  origin/master
  origin/sploits-dev
$ git checkout origin/dev
Note: switching to 'origin/dev'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 13e7356 add data needed for development
$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: xbhV3HpNGlTIdnjUrdAlPzc2L6y9EOnS
```
Too free.

## Level 30
`$ git clone ssh://bandit30-git@localhost:2220/home/bandit30-git/repo`, make `/tmp/`, etc.

```sh
$ ls
README.md
$ cat README.md
just an epmty file... muahaha
```
Alright, `git log` and `git branch` didn't help.
Use `git tag` and `git show`:
```sh
$ git tag
secret
$ git show secret
OoffzGDlzhAlerFJ2cAiz1D41JW1Mhmt
```
Too free!

## Level 31
`$ git clone ssh://bandit31\-git@localhost:2220/home/bandit31-git/repo`, make `/tmp/`, etc.
```sh
$ ls
README.md
$ cat README.md
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
```
Alright, we need to push `key.txt`.
```sh
$ vim key.txt
$ cat key.txt
May I come in?
$ git add key.txt
The following paths are ignored by one of your .gitignore files:
key.txt
hint: Use -f if you really want to add them.
hint: Turn this message off by running
hint: "git config advice.addIgnoredFile false"
```
kys
```sh
$ rm .gitignore
$ git add key.txt
$ git commit -m "key"
[master 400414a] key
 1 file changed, 1 insertion(+)
 create mode 100644 key.txt
 $ git push
The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit31/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit31/.ssh/known_hosts).
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

bandit31-git@localhost's password:
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 2 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 318 bytes | 318.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
remote: ### Attempting to validate files... ####
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: rmCBvG56y58BXzv98yZGdO7ATVL5dW8y
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
To ssh://localhost:2220/home/bandit31-git/repo
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'ssh://localhost:2220/home/bandit31-git/repo'
```

## Level 32
Finally a level that isn't `git`. It's a shell escape. We're put into an `UPPERCASE SHELL` upon logging in as `bandit32`:

```sh
WELCOME TO THE UPPERCASE SHELL
>> ls
sh: 1: LS: Permission denied
```
Hmm... it converts the text to uppercase. We can use `$0`:
```sh
>> $0
$ ls
uppershell
$ cat /etc/bandit_pass/bandit33
odHo63fHiFqcWWJG9rLiLDtPm45KzUKy
```

That marks the end of the `bandit` challenges:
```sh
$ cat README.txt
Congratulations on solving the last level of this game!

At this moment, there are no more levels to play in this game. However, we are constantly working
on new levels and will most likely expand this game with more levels soon.
Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.

If you have an idea for an awesome new level, please let us know!
```