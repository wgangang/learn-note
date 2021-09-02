#	Linux 安装 elasticsearch 集群

官方文档
[https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html]

- 添加用户

``` 
  useradd elasticsearch -d /opt/elasticsearch -s /sbin/nologin
```
- 下载elasticsearch

```
  wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.14.1-linux-x86_64.tar.gz
```

- elasticsearch 安装

```
  tar -zxvf elasticsearch-7.14.1-linux-x86_64.tar.gz

  chown elasticsearch:elasticsearch /home/elasticsearch/ -R

  chown elasticsearch:elasticsearch /data/esdata/ -R

```

- elasticsearch 配置说明

```
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: es-cluster
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: node-1
#
# Add custom attributes to the node:
#
node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
path.data: /esdata/data
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: es-cluster
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: node-1
#
# Add custom attributes to the node:
#
node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
path.data: /esdata/data
#
# Path to log files:
#
path.logs: /esdata/logs/es01
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# By default Elasticsearch is only accessible on localhost. Set a different
# address here to expose this node on the network:
#
network.host: 172.19.236.43
#
# By default Elasticsearch listens for HTTP traffic on the first free port it
# finds starting at 9200. Set a specific HTTP port here:
#
http.port: 9200
transport.port: 9300
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
discovery.seed_hosts: ["172.19.236.43:9300", "172.19.236.43:9301", "172.19.236.43:9302"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
cluster.initial_master_nodes: ["172.19.236.43:9300", "172.19.236.43:9301", "172.19.236.43:9302"]

http.cors.enabled: true
http.cors.allow-origin: "*"

# 开启本地用户
xpack.security.enabled: true
# xpack的版本
#xpack.license.self_generated.type: basic

# 开启transport.ssl认证
xpack.security.transport.ssl.enabled: true
# xpack认证方式 full为主机或IP认证及证书认证，certificate为证书认证，不对主机和IP认证，默认为full
xpack.security.transport.ssl.verification_mode: certificate
# xpack包含私钥和证书的PKCS＃12文件的路径
xpack.security.transport.ssl.keystore.path: certs/cert.p12
# xpack包含要信任的证书的PKCS＃12文件的路径
xpack.security.transport.ssl.truststore.path: certs/cert.p12
# 本机只允行启3个实例
node.max_local_storage_nodes: 3
# 只要有2台数据或主节点已加入集群，就可以恢复
gateway.recover_after_nodes: 2
#
# For more information, consult the discovery and cluster formation module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
#action.destructive_requires_name: true

```

- elasticsearch 加密通行配置

```
  # --days: 表示有效期多久
  sudo -u elasticsearch ./bin/elasticsearch-certutil ca --days 3660
  #输入密码
  
  #生成证书文件夹
  mkdir -p config/certs 
  chown elasticsearch:elasticsearch config/certs/ -R


  # 每一个实例一个证书
  # --ca CA证书的文件名，必选参数
  # --dns 服务器名，多服务器名用逗号隔开，可选参数
  # --ip 服务器IP，多IP用逗号隔开，可选参数
  # --out 输出到哪里，可选参数
  # --days 有效期多久，可选参数
  sudo -u elasticsearch ./bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12 --ip ${本机IP},127.0.0.1 --out config/certs/cert.p12 --days 3660

```

- 创建密钥库
```
  # 每一个实例都要操作
  # 创建密钥库
  sudo -u elasticsearch ./bin/elasticsearch-keystore create
  # PKCS＃12文件的密码
  sudo -u elasticsearch ./bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password
  # 信任库的密码
  sudo -u elasticsearch ./bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password
  # 查看密钥库
  sudo -u elasticsearch ./bin/elasticsearch-keystore list
```

- 启动es集群

```
  sudo -u elasticsearch /home/elasticsearch/es01/bin/elasticsearch -d
  sudo -u elasticsearch /home/elasticsearch/es02/bin/elasticsearch -d
  sudo -u elasticsearch /home/elasticsearch/es03/bin/elasticsearch -d
```


-  生成默认用户密码

```
  sudo -u elasticsearch ./bin/elasticsearch-setup-passwords interactive

```

# kibana 安装

- 添加用户

``` 
  useradd kibana -d /home/kibana -s /sbin/nologin
```
- kibana 

```
  wget https://artifacts.elastic.co/downloads/kibana/kibana-7.14.1-linux-x86_64.tar.gz
```

- kibana 安装

```
  tar -zxvf kibana-7.14.1-linux-x86_64.tar.gz
  chown kibana:kibana /home/kibana/ -R

```
- kibana 配置

```
  server.port: 5601
  server.host: "0.0.0.0"
  elasticsearch.hosts: ["http://172.19.236.43:9200","http://172.19.236.43:9201","http://172.19.236.43:9202"]
  elasticsearch.username: "kibana_system"
  elasticsearch.password: "密码"

```


- 启动kibana

```
  nohup kibana/bin/kibana &
```




