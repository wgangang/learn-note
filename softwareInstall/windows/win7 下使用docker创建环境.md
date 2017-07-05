#win7 下使用docker创建环境
**docker toolsbox 下载**

[下载地址](https://github.com/docker/toolbox/releases/download/v1.12.4/DockerToolbox-1.12.4.exe)

下载完成后，点击安装。

**检查docker安装时候成功**

运行如下代码

```
docker --version
docker-machine --version
docker-compose --version
```

如图所示：

![运行结果](http://img.blog.csdn.net/20161215095646672?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2dnb3JraW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**docker加速器配置**

配置代码

```
docker-machine ssh default
sudo sed -i "s|EXTRA_ARGS='|EXTRA_ARGS='--registry-mirror=加速地址 |g" /var/lib/boot2docker/profile
exit
docker-machine restart default
```

如图所示
![运行结果](http://img.blog.csdn.net/20161215100551274?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2dnb3JraW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

至此docker安装完成

**有问题请加QQ群： 106320003**
