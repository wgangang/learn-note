# docker 基本命令

** 删除所有容器 **

'''
docker rm $(docker ps -a -q)
'''

** 删除所有镜像 **

```
docker rmi $(docker images -q)
```

** 镜像重命名 **
```
docker tag d583c3ac45fd myname/server:latest
```
