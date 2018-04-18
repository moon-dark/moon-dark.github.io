---
title: cannot login as mysql user root from normal user account
date: 2018-04-07 21:31:00
tags:
  - code
  - database
---

- 1.First, connect in sudo mysql

```
sudo mysql -u root
```

- 2.Check your accounts present in your db
```
SELECT User,Host FROM mysql.user;
+------------------+-----------+
| User             | Host      |
+------------------+-----------+
| admin            | localhost |
| debian-sys-maint | localhost |
| magento_user     | localhost |
| mysql.sys        | localhost |
| root             | localhost |
```
- 3.Delete current root@localhost account
```
mysql> DROP USER 'root'@'localhost';
Query OK, 0 rows affected (0,00 sec)
```
- 4.Recreate your user
```
mysql> CREATE USER 'root'@'%' IDENTIFIED BY '';
Query OK, 0 rows affected (0,00 sec)
```
- 5.Give permissions to your user (don't forget to flush privileges)
```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0,00 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0,01 sec)
```
- 6.Exit MySQL and try to reconnect without sudo.
