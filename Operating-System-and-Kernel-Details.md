**1. Basic System Information**

  Kernel Version : 

    $ uname -r
  
    $ cat /proc/version

  OS Release Information : 

    $ cat /etc/os-release

    $ cat /etc/redhat-release     - For RHEL-based distributions

    $ cat /etc/*-release

  Distribution Information : 

    $ lsb_release -a


**2. Advanced System Details**

  System Hardware Details : 

    $ dmesg | grep -i linux

  System Information Summary : 

    $ hostnamectl

    $ hostname

  System Uptime and Load : 

    $ uptime

  Boot Logs for Kernel Messages : 

    $ journalctl -k 

    $ sysctl -a | grep kernel 

**3. Kernel Package Management **

  Query Installed Kernel Packages : 

    $ rpm -q kernel 

  Query All Kernel-Related Packages : 

    $ rpm -qa | grep kernel

  List Installed Kernel Packages (Debian-based) :

    $ dpkg -l | grep kernel



**4. Kernel and Boot Information
**
  Kernel Boot Messages :

    $ dmesg | grep -i linux

    $ dmesg | grep -i kernel

  Kernel Files in /boot :

    $ ls /boot | grep vmlinuz-


**5. Additional Useful Commands**

  List Active Kernel Modules :

    $ lsmod

  View Kernel Command Line Parameters  :

    $ cat /proc/cmdline

  Check Kernel Configuration (If Available) :

   $ zcat /proc/config.gz | grep CONFIG_
