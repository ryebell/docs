---
title: Resetting MySQL Root Passwords
subject: Database
---

# Resetting MySQL Root Passwords

If you forget your password to your MySQL or MariaDB database, this procedure shows you how to gain access in order to reset the password. It requires a user with sudo access to execute.

## Step 1: Identify your database version
Depending on your choice between MySQL and MariaDB as your operating system, some commands, file paths, and system services will differ. If necessary, you can check your version by issuing:
```shell
mysql --version
```
If you're running MySQL, the output will resemble:
```shell
mysql  Ver 14.14 Distrib 5.7.16, for Linux (x86_64) using EditLine wrapper
```
If you're running MariaDB, it will be something like:
```shell
mysql  Ver 15.1 Distrib 5.5.52-MariaDB, for Linux (x86_64) using readline 5.1
```
If you're using MySQL, continue to Step 2 below. If you're using MariaDB, see Step 2 in [Resetting MariaDB root passwords](https://www.thermo.io/how-to/security/resetting-mariadb-root-passwords).
## Step 2: Stop the database server
Before changing the root password, you must first terminate the database server:
```shell
sudo systemctl stop mysql
```

## Step 3: Restart the database server without permissions
To gain root access without a password, you can start MySQL while preventing them from loading the grant tables that store user privilege information.
**Attention:** Because this step contains potential security risk, it is best practice to disable networking as shown below. Additionally, the ampersand at the end is required to allow you to continue to use your console by pushing the process to the background.

To do so, issue:
```shell
sudo mysqld_safe --skip-grant-tables --skip-networking &
```
Then, to connect to the server as the root user without the password, issue:
```shell
mysql -u root
```

## Step 4: Change the root password
First, reload the grant tables, which you will need for Step 5:
```sql
FLUSH PRIVILEGES;
```
Then, change the root password with one of the following two commands, but replace `<new_password>` and the angled brackets (`<>`) with your actual password:
* For MySQL 5.7.6 and later, use:
```sql
ALTER USER ‘root’@’localhost’ IDENTIFIED BY ‘<new_password>’;
```
* For MySQL 5.7.5 and earlier, use:
```sql
SET PASSWORD FOR ‘root’@’localhost’ = PASSWORD(‘<new_password>’);
```
If successful, you will see the output:
```shell
Query OK, 0 rows affected (0.00 sec)
```

## Step 5: Restart your database server
First, stop the database server you launched in Step 3:
```shell
sudo pkill 'mysql'
```
Next, restart the service using `systemctl`:
```shell
sudo systemctl start mysql
```
Finally, check your work by confirming the new password works as intended:
```shell
mysql -u root -p
```
Your work is done! You now have regained administrative access to your MySQL server.

**_For 24-hour assistance any day of the year, contact a Thermo Physicist [by email](mailto:physicists@thermo.io) or [through the Client Portal](https://core.thermo.io/login/)._**
