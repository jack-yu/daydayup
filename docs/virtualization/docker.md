### Docker学习

[官方文档](https://docs.docker.com/)


#### Docker安装
在Ubuntu24.04下，使用如下命令进行安装

```
sudo snap install docker
```

#### Docker网络代理

修改docker配置文件

```
sudo vi /var/snap/docker/current/config/daemon.json
```

配置文件模板

```
{
  "proxies": {
    "http-proxy": "http://proxy.example.com:3128",
    "https-proxy": "https://proxy.example.com:3129",
    "no-proxy": "*.test.example.com,.example.org,127.0.0.0/8"
  }
}
```

修改后需要重启docker服务

```
sudo systemctl restart docker
```

如果使用snap安装的docker，需要使用如下命令重启docker服务

```
sudo systemctl restart snap.docker.dockerd
```

通过执行如下命令可以查看网络代理是否生效

```
sudo docker info
```


[官方文档参考](https://docs.docker.com/engine/daemon/proxy/)




#### 将docker镜像打包成单个文件

执行docker save命令

```
sudo docker save imageID >  zipfilename.tar
```

> 经过尝试，sudo docker save -o zipfilename.tar imageID命令会报权限错误，上述命令则不会

[stackoverflow参考案例](https://stackoverflow.com/questions/37404447/access-is-denied-while-docker-save)


#### Docker Images

[Python Images](https://hub.docker.com/_/python)

