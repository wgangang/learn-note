#	Linux 安装 docker

官方文档
[https://docs.docker.com/engine/install/centos/]


- 更新yum包
``` sudo yum update ```

- 安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的

```  sudo yum install -y yum-utils device-mapper-persistent-data lvm2 ```

- 设置yum源

``` sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo ```

- 查看所有仓库中所有docker版本

``` yum list docker-ce --showduplicates | sort -r ```

- 安装docker

``` $ sudo yum install docker-ce  #由于repo中默认只开启stable仓库，故这里安装的是最新稳定版17.12.0``` 

``` $ sudo yum install <FQPN>  # 例如：sudo yum install docker-ce-17.12.0.ce ```

- 启动

``` $ sudo systemctl start docker ```

- 开机启动

``` $ sudo systemctl enable docker ```

- docker-compose 安装

[https://docs.docker.com/compose/install/]

