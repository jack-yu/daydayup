### Multipass官方资料
[官网](http://https://multipass.run/)

[官方文档](https://multipass.run/docs)


### 学习视频
[超简单 仅需一行代码 即可瞬间启动Ubuntu最新版 助力Windows优雅使用Docker环境！附小雅TVBOX搭建和使用技巧！](https://www.youtube.com/watch?v=HpFQGhWeshQ)

### 学习笔记

[Multipass 使用笔记](https://wkdaily.cpolar.cn/archives/multipass)

[轻量级虚拟化解决方案：掌握 Multipass 的使用技巧](https://blog.csdn.net/qq_35453788/article/details/141862741#:~:text=%E5%88%9B%E5%BB%BA%E8%99%9A%E6%8B%9F%E6%9C%BA%201%20%E5%90%AF%E5%8A%A8%E8%99%9A%E6%8B%9F%E6%9C%BA%EF%BC%9A%20%E4%BD%BF%E7%94%A8%E4%BB%A5%E4%B8%8B%E5%91%BD%E4%BB%A4%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA%E5%90%8D%E4%B8%BA%20vm1%20%E7%9A%84%20Ubuntu%20%E8%99%9A%E6%8B%9F%E6%9C%BA%EF%BC%9A,vm3%203%20%E6%9F%A5%E7%9C%8B%E5%B7%B2%E5%88%9B%E5%BB%BA%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA%EF%BC%9A%20%E4%BD%BF%E7%94%A8%E4%BB%A5%E4%B8%8B%E5%91%BD%E4%BB%A4%E6%9F%A5%E7%9C%8B%E6%89%80%E6%9C%89%E5%88%9B%E5%BB%BA%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA%EF%BC%9A%20multipass%20list%20%E8%BE%93%E5%87%BA%E5%B0%86%E6%98%BE%E7%A4%BA%E6%89%80%E6%9C%89%E8%99%9A%E6%8B%9F%E6%9C%BA%E7%9A%84%E7%8A%B6%E6%80%81%E3%80%81IP%20%E5%9C%B0%E5%9D%80%E5%92%8C%E5%88%9B%E5%BB%BA%E6%97%B6%E9%97%B4%E3%80%82)

### 学习摘要
#### 简单使用

|命令   |描述   |
|---|---|
|multipass launch --name <vm-name>   |创建新的虚拟机   |
|multipass list   |列出所有虚拟机   |
|multipass shell <vm-name>   |连接到指定虚拟机   |
|multipass start <vm-name>   |启动指定虚拟机   |
|multipass stop <vm-name>   |停止指定虚拟机   |
|multipass delete <vm-name>   |删除指定虚拟机   |
|multipass purge   |清理已删除虚拟机的所有数据   |
|multipass info <vm-name>   |查看指定虚拟机的详细信息   |

##### 查看支持的系统镜像列表

```
multipass find
```
##### 新建和运行 ubuntu
> multipass launch --name <虚拟机实例名称> <系统镜像名称(可选)>
> 举例，比如创建一个名为 vm1 的虚拟机实例，不写系统镜像这个参数，则表示最新版ubuntu 24.04

```
multipass launch --name vm1
```

##### 如何查看有哪些虚拟机及其状态？


```
multipass list 
```

##### 虚拟机启动与停止

启动

```
multipass start vm1
```

停止

```
multipass stop vm1
```

##### 删除虚拟机
要删除虚拟机，首先需要停止它，然后使用以下命令：
```
multipass delete vm1
multipass purge
```
- delete：删除指定的虚拟机。
- purge：清理已删除虚拟机的所有数据。


##### 如何调用虚拟机？
- 方法一 任务栏图标点击 **左键**后在对应虚拟机菜单中选择 —— Open in Multipass

- 方法二 运行指定虚拟机实例名称即可


```
multipass shell vm1
```

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

##### 如何删除虚拟机实例（分三步）


```
# 停止 vm1
multipass stop vm1
# 删除 vm1
multipass delete vm1
# 清理回收
multipass purge

# 附加

# 停止全部虚拟机
multipass stop --all
```

#### 进阶使用
##### 新建 4核心 4GB内存 300G虚拟磁盘的ubuntu 实例

```
multipass launch --name vm3 -c 4 -m 4G -d 300G
```

> vm3 虚拟机名称
> -c 4 代表虚拟4核心 这个要根据实际CPU核心数确定 不能随便写 比如本身2核心的cpu是无法虚拟4核心的
> -m 4G 代表虚拟4GB内存
> -d 300G 代表分配虚拟磁盘300GB

##### 设置桥接模式的网络

```
multipass set local.bridged-network=<name>
# 比如重命名以太网2为lan2
multipass set local.bridged-network=lan2
```

> <name> 就是网口的名称 比如 以太网，但是最好重命名为英文，比如lan1、lan2

##### 创建桥接模式的虚拟机vm4

```
multipass launch --name vm4 -c 4 -m 4G -d 300G --network bridged
```

#### 扩展知识
可能有小伙伴会问: 笔记本电脑没有有线网卡。只有Wifi 应该如何桥接呢？有时候，windows下的multipass可能打印不出wifi网卡。

遇到识别不出wifi 网卡的情况，其实还可以利用Hyper-V 管理器新建一个虚拟交换机。

> 打开hyper-v管理器。点击【虚拟交换管理器】-【新建虚拟网络交换机】-【外部】-【创建】 然后你勾选一下你的 wifi 无线网卡，然后 名称的话 改成英文吧，比如wifi。这样应用之后，你再去打印multipass networks 就能识别wifi啦。


#### 共享数据

##### 挂载共享目录

您可以使用 `multipass mount`命令在主机和实例之间共享数据，方法是使主机文件系统中的特定文件夹在实例的文件系统中可用，并具有读取和写入权限。挂载的路径是持久的，这意味着在显式卸载之前，它们将保持可用。

`mount` 命令的基本语法是：

```
multipass mount <local path> <instance name>
```

例如，要将 Linux 系统上的本地主目录（标识为 $HOME）映射到 keen-yak 实例，请运行以下命令：

~~~
multipass mount $HOME keen-yak
~~~

你可以查看运行 `multipass info keen-yak` 的结果：

```
…
Mounts:         /home/michal => /home/michal
```

此时，本地主目录 `/home/michal` 将在实例中可用。

如果要将本地目录挂载到实例中的其他路径，可以按如下方式指定目标路径：

```
multipass mount $HOME keen-yak:/some/path
```

> 如果 `/some/path` 目录已存在于实例的文件系统中，则其内容将被挂载的目录暂时隐藏（“覆盖”），但不会被覆盖。原始文件夹保持不变，如果卸载，则会显示原始文件夹。

> 因此，无法在实例的 $HOME 目录上挂载外部文件夹路径，因为它还包含访问实例所需的 SSH 密钥：隐藏它们后，您将无法再 shell 到实例中。

您还可以在创建实例时，使用带有 `--mount` 选项的 `multipass launch` 命令来定义挂载：

```
multipass launch --mount /local/path:/instance/path
```

##### 卸载共享目录

要卸载以前挂载的路径，请使用 `multipass umount` 命令。

您可以指定要卸载的文件夹路径：

```
multipass umount keen-yak:/home/michal
```

或者，如果您未指定任何路径，请一次卸载所有共享文件夹：

```
multipass umount keen-yak
```

##### 数据传输

您还可以使用 `multipass transfer` 命令将文件从本地文件系统复制到实例的文件系统，反之亦然。

要指示文件位于实例内，请在其路径前加上 `<instance name>：` 作为前缀。

例如，要将 `crontab` 和 `fstab` 文件从 `keen-yak` 实例上的 `/etc` 目录复制到主机文件系统中的 `/home/michal` 文件夹：

```
multipass transfer keen-yak:/etc/crontab keen-yak:/etc/fstab /home/michal
```

相反，如果要将这些文件从本地文件系统复制到实例中，请运行以下命令：

```
multipass transfer /etc/crontab /etc/fstab keen-yak:/home/michal
```


[如何与实例共享数据官方文档](https://multipass.run/docs/share-data-with-an-instance)


### Trouble Shooting

[排查启动/启动问题](https://discourse.ubuntu.com/t/troubleshoot-launch-start-issues/48104)

本主题介绍启动或启动实例时的常见问题，例如超时或“未知状态”错误。

这些问题的发生可能有多种不同的原因。由于 Multipass 依赖于在默认接口上具有 IP 地址的实例来建立 SSH 连接，因此它们通常（但并非总是）与 IP 分配或连接问题相关联。