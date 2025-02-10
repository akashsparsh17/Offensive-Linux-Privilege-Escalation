# **Introduction**

 Exploiting vulnerabilities in the kernel or kernel modules is a common method of privilege escalation. This typically requires an unpatched kernel with known vulnerabilities that allow users to escalate privileges.
 ---


## **1. Basic System Information**

  Kernel Version : 

  ```bash
  uname -r
  ```
    
   ```bash
   cat /proc/version
   ```


  OS Release Information : 

   ```bash
   cat /etc/os-release
   ```


   ```bash  
   cat /etc/redhat-release     - For RHEL-based distributions
   ```


   ```bash
   cat /etc/*-release
   ```

  Distribution Information : 
  
  ```bash
  lsb_release -a
  ```


## **2. Advanced System Details**


  System Hardware Details : 

  ```bash
  dmesg | grep -i linux
  ```

  System Information Summary : 

   ```bash
   hostnamectl
   ```

   ```bash  
   hostname
   ```

  System Uptime and Load : 

   ```bash
   uptime
   ```

  Boot Logs for Kernel Messages : 

   ```bash
   journalctl -k 
  ```

   ```bash  
   sysctl -a | grep kernel 
   ```

## **3. Kernel Package Management**

 
  Query Installed Kernel Packages : 

   ```bash
   rpm -q kernel 
   ```

  Query All Kernel-Related Packages : 

   ```bash
   rpm -qa | grep kernel
   ```

  List Installed Kernel Packages (Debian-based) :

   ```bash
   dpkg -l | grep kernel
  ```


## **4. Kernel and Boot Information**

  
  Kernel Boot Messages :

   ```bash
   dmesg | grep -i linux
  ```

   ```bash
   dmesg | grep -i kernel
   ```

  Kernel Files in /boot :

  ```bash
  ls /boot | grep vmlinuz-
  ```

## **5. Additional Useful Commands**


  List Active Kernel Modules :

  ```bash
  lsmod
  ```

  View Kernel Command Line Parameters  :

  ```bash
  cat /proc/cmdline
  ```

  Check Kernel Configuration (If Available) :

  ```bash
  zcat /proc/config.gz | grep CONFIG_
  ```

# **Checking Vulnerable Kernel Versions**

## Overview 

This script extracts and lists vulnerable Linux kernel versions based on the data from the [lucyoa/kernel-exploits](https://github.com/lucyoa/kernel-exploits) repository. It retrieves the README file and filters out kernel versions mentioned as vulnerable.
---

**Usage :**

   ```bash
   curl https://raw.githubusercontent.com/lucyoa/kernel-exploits/refs/heads/master/README.md 2>/dev/null | grep "Kernels: " | cut -d ":" -f 2 | cut -d "<" -f 1 | tr -d "," | tr ' ' '\n' | grep -v "^\d\.\d$" | sort -u -r | tr '\n' ' '  
   ```

**Check for a Specific Kernel Version :**

```bash
curl https://raw.githubusercontent.com/lucyoa/kernel-exploits/refs/heads/master/README.md 2>/dev/null | grep "Kernels: " | cut -d ":" -f 2 | cut -d "<" -f 1 | tr -d "," | tr ' ' '\n' | grep -v "^\d\.\d$" | sort -u -r | tr '\n' ' ' | grep 3.8.2
 ```
