#Linux 安装 ZooKeeper

**添加用户和用户组**

```
sudo groupadd zookeeper  #添加用户组
sudo useradd zookeeper -g zookeeper  #添加用户
```

**获取ZooKeeper文件**
```
wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.9/zookeeper-3.4.9.tar.gz
```
**解压ZooKeeper**

```
tar -zxvf zookeeper-3.4.9.tar.gz
```
**创建ZooKeeper目录**

```
mkdir -p /home/zookeeper/local/zookeeper/data #创建所需data目录
mkdir -p /home/zookeeper/local/zookeeper/log  #创建所需log目录
```

**创建配置文件**

```
cp -r conf/zoo_sample.cfg conf/zoo.cfg
```
**修改配置文件内容如下**

```
dataDir=/home/zookeeper/local/zookeeper/data
dataLogDir=/home/zookeeper/local/zookeeper/log
```
**启动zookeeper服务**

```
sh bin/zkServer.sh start  #启动zookeeper
sh bin/zkServer.sh status  #查看状态
sh bin/zkServer.sh stop   #停止服务 
```
**检查端口是否监听**

```
netstat -lpn | grep 2181
```

**有问题请加QQ群： 106320003**

