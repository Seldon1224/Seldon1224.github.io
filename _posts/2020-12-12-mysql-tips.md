---
title: MySQL-8.0.22安装配置
categories: [MySQL, 安装配置]
tags: [数据库]
---

# MySQL-8.0.22安装及其配置

环境：Ubuntu 20.04LTS

```shell
sudo apt update
sudo apt install mysql-server
```

安装完成后，直接sudo mysql即可进入，进入后正确修改root密码方式：

* ```mysql
  use mysql
  ```

* 设密码为空后修改（mysql-8版本要求密码设空后才可修改）

  ```sql
  update user set authentication_string='' where user='root';
  ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'xxxx';
  ```

  

## 坑点

* mysql-8中authentication_string:字段表示用户密码，而authentication_string字段下只能是mysql加密后的41位字符串密码。所以只能用以下方式来修改root密码：

  ```sql
  ALTER user 'root'@'localhost' IDENTIFIED BY 'newpassword';
  ```

* MySql 从8.0开始修改密码有了变化，在user表加了字段authentication_string，修改密码前先检查authentication_string是否为空。

