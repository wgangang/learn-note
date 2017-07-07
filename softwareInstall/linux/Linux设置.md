使用管理员登陆

#添加用户，并设置权限
useradd admin //添加admin用户
passwd admin //admin设置密码
vi /etc/ssh/sshd_config //进入ssh登陆设置
PermitRootLogin yes //设置为no 禁止root用户直接登录
mkdir /opt   //创建opt目录

＃安装ngnix
wget http://nginx.org/download/nginx-1.8.1.tar.gz  //下载nginx
sudo yum  -y install openssl openssl-devel  //安装编译
./configure --prefix=/opt/nginx && make && make install //安装nginx到opt下面
#部分配置说明
location ~*(system){
    proxy_pass http://localhost:8086;
    proxy_set_header   Host    $host;
    proxy_set_header   X-Real-IP   $remote_addr;
    proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
}



＃安装jdk
wget http://download.oracle.com/otn-pub/java/jdk/8u91-b14/jdk-8u91-linux-x64.tar.gz //下载jdk8
tar -zxvf jdk-8u91-linux-x64.tar.gz   //解压jsk
vi /etc/profile   //配置环境变量
export JAVA_HOME=/opt/jdk1.8.0_91
export JRE_HOME=/opt/jdk1.8.0_91/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$PATH
source /etc/profile     // 配置生效

＃mysql安装
wget http://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.12-linux-glibc2.5-x86_64.tar.gz  //下载mysql
sudo useradd mysql   //添加用户
usermod -G root mysql //添加mysql到root组
passwd mysql    //设置mysqly用户的密码
tar -zxvf mysql-5.7.12-linux-glibc2.5-x86_64.tar.gz  //解压
mkdir data  //
bin/mysqld --initialize --user=mysql --basedir=/home/mysql/mysql-5.7.12 --datadir=/home/mysql/mysql-5.7.12/data   //
#执行后返回密码 s>FTr;9q9Kj8
#mysql my.cnf  start
[client]
default-character-set=utf8
[mysqld]
#log=/home/mysql/mysql-5.7.12/log/mysqld_common.log
log-error=/home/mysql/mysql-5.7.12/log/mysqld_err.log
log-bin=/home/mysql/mysql-5.7.12/log/mysqld_bin.bin
character-set-server=utf8
init_connect='SET NAMES utf8'
basedir =/home/mysql/mysql-5.7.12
datadir =/home/mysql/mysql-5.7.12/data
# port = .....
server_id =mysql.pid
socket =/home/mysql/mysql-5.7.12/mysql.sock
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
join_buffer_size = 128M
sort_buffer_size = 2M
read_rnd_buffer_size = 2M
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
#mysql my.cnf  end
mkdir log //创建日志目录
bin/mysqld_safe --defaults-file=./my.cnf  //启动mysql
bin/mysql -uroot -p         //root登录，密码初始化生成的
set password=password('admin');     // 修改密码
create user admin identified by 'admin';   // 创建用户
grant all privileges on test.* to 'admin'@'%';  // 分配权限
#配置mysqlservice启动
cp mysql-5.7.12/support-files/mysql.server /etc/init.d/mysql //修改mysql根目录配置
cp mysql-5.7.12/my.cnf  /etc/my.cnf   //mysql配置文件存放位置


#tomcat安装
wget http://apache.fayea.com/tomcat/tomcat-8/v8.0.35/bin/apache-tomcat-8.0.35-windows-x64.zip //tomcat下载
unzip -o -d apache-tomcat-8.0.35 apache-tomcat-8.0.35-windows-x64.zip   //解压
netstat -tunlp |grep 8080 //查看端口是否占用
