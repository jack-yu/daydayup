### Ubuntu学习


##### 如何换 国内 软件源 比如阿里云 Ubuntu 24.04 为例

```
sudo sed -i 's|http://archive.ubuntu.com/|http://mirrors.aliyun.com/|g' /etc/apt/sources.list.d/ubuntu.sources
```

或者手动修改 配置文件

```
sudo vi /etc/apt/sources.list.d/ubuntu.sources
```

然后更新软件源

```
sudo -i
apt update -y
apt upgrade -y
```