#	Linux 安装 mysql5.7.16

**添加用户和用户组**

```
groupadd mysql
useradd -g mysql mysql 
passwd mysql  #设置mysql用户密码
```

  **获取mysql**
```
http://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.16-linux-glibc2.5-x86_64.tar.gz
```
**权限处理**

```
chmod 777  mysql-5.7.16-linux-glibc2.5-x86_64.tar.gz
```
**解压文件**

```
tar -zxvf mysql-5.7.16-linux-glibc2.5-x86_64.tar.gz ./mysql-5.7.16
```
**分配权限**

```
chown mysql:mysql ./mysql-5.7.16
```
**设置mysql配置文件**

```
cp support-files/my-default.cnf ./my.cnf
vi my.cnf
```
**配置文件内容**

```
[client]
# 设置mysql客户端默认字符集
default-character-set=utf8
[mysqld]
#log=/home/mysql/mysql-5.7.12/log/mysqld_common.log
log-error=/home/mysql/mysql-5.7.16/log/mysqld_err.log
log-bin=/home/mysql/mysql-5.7.16/log/mysqld_bin.log
character-set-server=utf8
init_connect='SET NAMES utf8'
lower_case_table_names=1
max_allowed_packet = 1024M

basedir=/home/mysql/mysql-5.7.16
datadir=/home/mysql/mysql-5.7.16/data
port=3306
server_id =mysql.pid
socket =/home/mysql/mysql-5.7.16/mysql
```
**执行安装命令**
```
bin/mysqld --initialize --user=mysql\ --basedir=/home/mysql/mysql-5.7.16 --datadir=/home/mysql/mysql-5.7.16/data
```
**运行结果如下，得到临时密码**

![这里写图片描述](http://img.blog.csdn.net/20161207113600501?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2dnb3JraW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**启动mysql**
```
bin/mysqld_safe --defaults-file=./my.cnf
```
**连接mysql**
```
bin/mysql -uroot -p -S /home/mysql/mysql-5.7.16/mysql.sock
```
然后输入生成的临时面

**修改密码设置数据库**
```
set password=password('root');  #修改密码
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION; #设置远程访问
FLUSH PRIVILEGES;  #刷新
create user pmpf identified by 'pmpf'; #新增用户
grant all privileges on recon.* to 'pmpf'@'%';#给pmpf分配recon库的所有权限
```
**添加启动项**
```
cp support-files/mysql.server /etc/init.d/mysql
```
然后编辑mysql文件，修改如下部分
```
basedir=/home/mysql/mysql-5.7.16
datadir=/home/mysql/mysql-5.7.16/data
conf=/home/mysql/mysql-5.7.16/my.cnf
```

**有问题请加QQ群： 106320003**
