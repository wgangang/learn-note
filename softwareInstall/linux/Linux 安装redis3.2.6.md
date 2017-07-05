#Linux 安装redis3.2.6

**获取redis**
```
 wget http://download.redis.io/releases/redis-3.2.6.tar.gz
```
**解压redis**
```
tar -zxvf redis-3.2.6.tar.gz
```
**编译**
```
cd redis-3.2.6  #进入目录
make   #编译
```
**设置redis**
```
mkdir /usr/redis #创建redis操作目录
cp src/redis-server src/redis-cli /usr/redis/  #复制redis服务和命令
cp redis.conf /usr/redis/   #复制redis配置文件
cd /usr/redis
redis-server redis.conf   #启动redis
```
**创建快捷键**
```
vi ~/.bashrc
alias redis='/usr/redis/redis-cli'  #添加快捷键
source ~/.bashrc   #使生效
```
**有问题请加QQ群： 106320003**
