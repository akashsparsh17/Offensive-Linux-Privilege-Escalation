# Linux Privilege Escalation: Password Mining

## **Introduction**
Password mining is a crucial technique in Linux privilege escalation where attackers extract sensitive credentials from various system locations to gain elevated access. This document covers different methods of password mining along with relevant commands and exploitation techniques.

## **1. Extracting Browser Stored Passwords**

Web browsers like Chrome, Firefox, Chromium, Opera, and others store saved passwords securely in encrypted formats. Below is a detailed breakdown of where these passwords are stored, how they are protected, and methods for accessing them during penetration testing.

## **1.1 Google Chrome**

### Password Location:

 -  Windows
    ` : C:\Users\<Username>\AppData\Local\Google\Chrome\User Data\Default\Login Data `
 -  Linux: 
     ` ~/.config/google-chrome/Default/Login Data `
 -  MacOS: 
     ` ~/Library/Application Support/Google/Chrome/Default/Login Data `

### Storage Format:

 -  Stored in an SQLite database file (Login Data) under the logins table.
 -  Encrypted using the OS’s API:
       - Windows: DPAPI (Data Protection API).
       - Linux: GNOME Keyring or KWallet.
       - MacOS: Keychain.

### Extraction & Decryption:

 Extract Passwords using SQLite:

```bash 
sqlite3 "Login Data" "SELECT origin_url, username_value, password_value FROM logins;" 
```

 -  Decrypt Passwords:
       - Windows: Tools like NirSoft ChromePass or Mimikatz.
       - Linux: GNOME Keyring using secret-tool.
       - MacOS: Use the security command to retrieve Keychain passwords.

## **1.2 Mozilla Firefox**

### Password Location:

 - Windows: 
    ` C:\Users\<Username>\AppData\Roaming\Mozilla\Firefox\Profiles\<profile_name>\logins.json `
 - Linux: 
    ` ~/.mozilla/firefox/<profile_name>/logins.json `
 - MacOS: 
    ` ~/Library/Application/Support/Firefox/Profiles/<profile_name>/logins.json `

### Storage Format:

 -  Passwords are stored in logins.json (encrypted).
 -  Master password protection uses Mozilla’s NSS (Network Security Services) encryption.

### 	Extraction & Decryption:

 - Decrypt the key4.db file, which stores encryption keys.
 - Use tools like Firefox [firefox_decrypt.py](https://github.com/unode/firefox_decrypt) 

  `firefox_decrypt.py -d ~/.mozilla/firefox/<profile_name>` 

## **1.3 Chromium (Open-Source Chrome)**

### Password Location:

 -  Linux: ~/.config/chromium/Default/Login Data

### 	Storage & Decryption:

 -  Stored in the SQLite Login Data file.
 -  Follows the same decryption methods as Google Chrome.

## **1.4 Opera**

### Password Location:

- Windows: 
  ` C:\Users\<Username>\AppData\Roaming\Opera Software\Opera Stable\Login Data `
- Linux:
  `  ~/.config/opera/Default/Login Data `
- MacOS:
  ` ~/Library/Application Support/com.operasoftware.Opera/Login Data `

### Storage & Decryption:

 - Opera uses the same structure as Google Chrome since it’s Chromium-based.
 - Decrypt using tools or scripts designed for Chromium.

## **1.5 Microsoft Edge (Chromium-Based)**

### Password Location:

 - Windows: 
   ` C:\Users\<Username>\AppData\Local\Microsoft\Edge\User Data\Default\Login Data `
 - Linux:
   ` ~/.config/microsoft-edge/Default/Login Data `
 - MacOS:
   ` ~/Library/Application Support/Microsoft Edge/Default/Login Data `

### 	Storage & Decryption:

 - Same process as Google Chrome.

## **1.6 Are Passwords Stored as Hashes?**

 - No, browsers typically do not store passwords as hashes. Instead:
 - Passwords are encrypted using OS-based APIs like DPAPI, GNOME Keyring, or Keychain.
 - Hashes are unsuitable because browsers need the plaintext password to auto-fill login forms.

## **1.7 Cracking Browser Passwords**

### Decryption Tools:

 - Browser-Specific Tools:
         • NirSoft Tools (e.g., ChromePass, WebBrowserPassView): For decrypting Chrome, Firefox, and Edge passwords on Windows.
         • Firefox Decrypt: Specifically for Firefox passwords.

### Manual Decryption:

 - Extract SQLite data.
 - Decrypt using Python scripts that interact with DPAPI, Keyring, or Keychain.

### Example Python Script for Windows (DPAPI):

```bash	
import win32crypt
import sqlite3

conn = sqlite3.connect("Login Data")
cursor = conn.cursor()
cursor.execute("SELECT origin_url, username_value, password_value FROM logins")

for row in cursor.fetchall():
   url = row[0]
   username = row[1]
   encrypted_password = row[2]
   decrypted_password = win32crypt.CryptUnprotectData(encrypted_password, None, None, None, 0)[1]
   print(f"URL: {url}, Username: {username}, Password: {decrypted_password.decode()}")  
```

## **1.8 Automating Password Mining**

 - For automation, tools like LaZagne can be used:
 - GitHub Link: [LaZagne](https://github.com/AlessandroZ/LaZagne)
            - LaZagne extracts saved credentials from multiple applications, including browsers.            
 - For more browsers , Applications and additional password extraction techniques, you can refer to LaZagne .

## 2.Configuration files which can cantains the passwords:-   

During penetration testing , configuration files can often contain hardcoded credentials, API keys, and other sensitive data. Below is a categorized list of such files based on their respective services.

### 2.1 System Authentication Files

- `/etc/passwd` - Contains user account information (passwords are usually stored in /etc/shadow).
- `/etc/shadow` - Stores encrypted user passwords (requires root access to read).

### 2.2 SSH (Secure Shell)

- `/etc/ssh/ssh_config` - Configuration for SSH client, might contain key paths or directives.
- `/etc/ssh/sshd_config` - SSH server configuration.
- `~/.ssh/id_rsa` - Private SSH key (if not properly secured).
- `~/.ssh/authorized_keys` - List of authorized public keys.

### 2.3 User Shell & Environment Files

- `~/.bashrc` - User shell configuration file, sometimes stores credentials for convenience.
- `~/.bash_profile` - User shell profile file.
- `~/.profile` - Another shell configuration file.
- `.env` - Environment file used by many frameworks to store environment-specific variables, including sensitive data like database credentials and API keys.
- `credentials.json` - JSON file often used to store API keys, service account credentials, and other sensitive information for accessing external services.

### 2.4 Database Configuration Files

- `/etc/my.cnf` - MySQL configuration file, may contain database credentials.
- `~/.my.cnf` - User-specific MySQL configuration file.
- `config.inc.php` - Configuration or logic file focused on security settings, such as encryption keys or secure connections.
- `wp-config.php` - WordPress configuration file containing database credentials and secret keys.
- `db-config.php` - Database configuration file storing connection details.
- `dbconnect.php` - PHP script for establishing a database connection, usually includes sensitive credentials.
- `database.php` - Used for database configuration in many PHP applications.
- `conn.php` - Another common file name for database connection scripts.

### 2.5 Web Server Configuration Files

- `/var/www/html/config.php` - Common in web applications, stores database credentials.
- `/etc/nginx/nginx.conf` - Nginx configuration file.
- `/etc/nginx/sites-available/` - Site-specific Nginx configuration.
- `/etc/httpd/conf/httpd.conf` - Apache main configuration file.
- `/etc/apache2/apache2.conf` - Apache configuration for Debian-based systems.
- `.htaccess` - Apache web server configuration file. Used for directory-level configurations, such as URL rewriting, access control, and custom error messages.
- `web.config` - IIS web server configuration file for ASP.NET applications, containing settings like authentication, authorization, and connection strings.

### 2.6 Mail Server Configuration Files

- `/etc/postfix/main.cf` - Postfix mail server configuration, might contain SMTP credentials.
- `/etc/dovecot/dovecot.conf` - Dovecot mail server configuration.
- `/var/mail/root` - Mailbox for the root user, contains system-generated messages.
- `/var/mail/armour` - Mailbox for a user named "armour," possibly containing sensitive information.
- `/var/spool/mail/root` - Another location for the root user's mailbox.
- `/var/spool/mail/armour` - Another mailbox location for the "armour" user.

### 2.7 Application Configuration Files

- `settings.php` - General configuration file, can contain various application settings including database credentials and API keys.
- `env.php` - Configuration file for storing environment variables, similar to .env files, used to manage sensitive information.
- `appsettings.json` - JSON configuration file commonly used in .NET applications, often containing database connection strings, API keys, and other settings.
- `secrets.php` - PHP file specifically for storing sensitive data such as API keys, passwords, or encryption keys.
- `admin.php` - BBackend administration script, potentially containing sensitive logic or access to administrative functions.
- `init.php` - Initialization script, often used to set up application settings, include necessary files, or initialize configurations.
- `boot.php` - Bootstrap file for initializing the application, loading configurations, and setting up necessary components.
- `localsettings.php` - Configuration file, especially used in applications like MediaWiki, storing database settings, and other local configurations.
- `server.php` - PHP script that might contain server configurations, routing logic, or initialization code.
- `global.php` - Configuration file for global settings applicable throughout the application.
- `main.php` - Main script or configuration file, potentially containing the primary logic or settings for the application.
- `secure.php` - Configuration or logic file focused on security settings, such as encryption keys or secure connections.
- `default.php` -  Default configuration or logic script, used as a fallback or to set default application behavior.
- `user-config.php` - User-specific configuration file, might contain personalized settings or credentials.
- `auth.php` - Authentication script, often containing logic for user authentication and may include sensitive information like secret keys.
- `vars.php` - File for defining variables used across the application, potentially containing sensitive settings.
- `setup.php` - Script used for initial application setup, might include database initialization or configuration setup.
- `login.php` - Login script, handling user authentication, and potentially containing sensitive logic related to user access.
- `install.php` - Installation script for setting up the application, often containing logic for initializing databases and configuring settings.

## 3.History Files ( This files can cantain sensetive information )
- history - Displays the current user's shell command history from the current session or the .bash_history file.
### 3.1 User Command History Files

- `cat ~/.bash_history` - Shows the bash shell command history stored in the .bash_history file in the user's home directory.
- `cat ~/.zsh_history` - Displays the command history for the Zsh shell, if used by the user.

### 3.2 Editor History Files

- `cat ~/.nano_history` - Shows the history of commands and files opened with the Nano text editor.
- `cat ~/.viminfo` - Displays information and history from the Vim text editor, such as recently opened files and search strings.

### 3.3 Database History Files

- `cat ~/.mysql_history` - Displays the MySQL command history for the user.

### 3.4 Application-Specific History Files

- `cat ~/.atftp_history` - Shows the command history for the AtFTP client.
- `cat ~/.php_history` - Displays the command history for the PHP interactive shell.

### 3.5 User Profile & Logout History Files

- `cat ~/.profile` - Shows the contents of the user's .profile file, which contains shell configuration settings executed at login.
- `cat ~/.bash_logout` - Displays the contents of the .bash_logout file, which contains commands executed when a user logs out from a bash session.

### 3.6 Mail-Related History Files

- `cat /var/mail/root` - Displays the contents of the root user's mail file, typically containing system notifications and messages.
- `cat /var/spool/mail/root` - Similar to the previous command, it shows the root user's mail, often stored in the spool directory for the mail system.

### 3.7 Authentication & System Logs

- `cat /var/log/auth.log` - Shows authentication logs, including successful and failed login attempts and sudo usage.
- `cat /var/log/lastlog` - Displays the most recent login records for all users on the system.
- `cat /var/log/faillog` - Shows failed login attempts, which can be useful for detecting unauthorized access attempts.

### 3.8 General System Logs

- `cat /var/log/syslog` - Displays general system logs, which may contain information about system events, errors, and warnings.
- `cat /var/log/messages` - Similar to /var/log/syslog, but it focuses on system-wide messages, including kernel logs and other event logs.
- `cat /var/log/dmesg` - Displays kernel ring buffer messages, which contain information related to the system's hardware and kernel-level events.
- `cat /var/log/boot.log` - Shows the system boot logs, useful for identifying startup issues or services that failed to start.

### 3.9 Cron Job & Firewall Logs

- `cat /var/log/cron` - Shows logs related to cron jobs, which can be useful for auditing scheduled tasks or identifying unusual job executions.
- `cat /var/log/ufw.log` - Displays logs related to the Uncomplicated Firewall (UFW), providing insights into firewall activities.

### 3.10 Mail Server Logs

- `cat /var/log/mail.log` - Displays the mail log, useful for reviewing email-related events, such as sent/received emails and errors.

### 3.11 Web Server Logs

- `cat /var/log/apache2/access.log` - Displays the access log for the Apache web server, showing HTTP request details like client IP addresses, request URLs, and response codes.
- `cat /var/log/apache2/error.log` - Shows the error log for the Apache web server, which can provide details on server issues, misconfigurations, or errors.
- `cat /var/log/nginx/access.log` - Displays the access log for the Nginx web server, showing HTTP request details.
- `cat /var/log/nginx/error.log` - Displays the error log for the Nginx web server, providing information on any errors encountered.

### 3.12 Database Logs

- `cat /var/log/mysql/mysql.log` - Displays the general query log for MySQL, which can be helpful for reviewing SQL queries executed by MySQL users.

## 4.Backup File Enumeration & Analysis

### 4.1 Identifying Backup File Formats

Before searching for backup files, it’s important to recognize the common formats in which backups are stored:

- `.zip` → ZIP archive
- `.tar `→ Tarball archive
- `.7z `→ 7-Zip compressed file
- `.rar `→ RAR compressed file
- `.bz2` → Bzip2 compressed file
- `.gz `→ Gzip compressed file
- `.tbz `→ Tarball compressed with Bzip2
- `.tgz` → Tarball compressed with Gzip

### 4.2 Finding Backup Files in the System

To search for backup files across the system, use the following command:

```bash
find / -type f \( -iname "*.zip" -o -iname "*.tar" -o -iname "*.7z" -o -iname "*.rar" -o -iname "*.bz2" -o -iname "*.gz" -o -iname "*.tbz" -o -iname "*.tgz" \)
```
#### Explanation:

- `find / `→ Searches from the root directory (/)
- `-type f `→ Looks for files only (ignoring directories)
- `-iname` → Performs a case-insensitive search
- `*.zip, *.tar, *.7z, etc.` → Matches various backup file extensions

### 4.3 Saving the List of Backup Files

To save the output of the above command for further analysis:
```bash
find / -type f \( -iname "*.zip" -o -iname "*.tar" -o -iname "*.7z" -o -iname "*.rar" -o -iname "*.bz2" -o -iname "*.gz" -o -iname "*.tbz" -o -iname "*.tgz" \) > backupfiles.txt
```
#### Explanation:

- This command redirects the list of found backup files to a file named `backupfiles.txt`
- You can later review or `filter this file as needed`

### 4.4 Filtering Unwanted Backup Files

In some cases, system-related backup files may not be relevant. To remove such files from the list:

```bash
 cat backupfiles.txt | grep -vE '(backup/armour|usr/share/|usr/lib)' 
```

#### Explanation:

- `cat backupfiles.txt` → Reads the list of backup files
- `grep -vE` '(backup/armour|usr/share/|usr/lib)' →
           -v → Excludes the matching lines
           -E → Enables extended regex
           The regex pattern (backup/armour|usr/share/|usr/lib) filters out unwanted system directories

To filter out only specific system directories (usr/share/ and usr/lib/), use:

```bash 
cat backupfiles.txt | grep -vE '(usr/share/|usr/lib)'
 ```

#### Explanation:

- Excludes backup files stored in common system directories
- Helps in focusing on potentially sensitive backup files

## 5. Password Mining in Memory

Password mining in memory refers to the process of extracting sensitive information, such as passwords, from the system's memory. This technique is often used in security testing or by attackers to recover credentials from running processes, applications, or the operating system's memory. Since many programs store user passwords temporarily in memory for authentication, they can be retrieved using various tools and methods.

### Accessing System Memory for Password Extraction

#### Using /dev/mem and /dev/kmem

On Linux systems, /dev/mem and /dev/kmem provide access to system memory and kernel memory, respectively. If an attacker has sufficient privileges, they can scan these memory areas for sensitive data, including passwords.

#### Example Command:

```bash
 strings /dev/mem | grep -i PASS
 ```

This command scans the system memory (/dev/mem) for occurrences of the keyword "PASS," potentially revealing stored credentials.

## Conclusion

- Browsers encrypt passwords using OS-level APIs.
- Configuration files often contain sensitive information.
- Tools like LaZagne, Mimikatz, Firefox Decrypt, and NirSoft tools help in extracting stored passwords.
- Environment variables (.env, config.php, etc.) should be secured properly to prevent credential leaks.

## Here are the references for the content:
[Armour Infosec ](https://www.armourinfosec.com/)
[LaZagne](https://github.com/AlessandroZ/LaZagne)
[Firefox Decrypt](https://github.com/unode/firefox_decrypt/)
[Chatgpt](https://chatgpt.com)
