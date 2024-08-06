安装完系统后执行  
`apt update && apt upgrade -y`  
更新系统的相关软件



## Q:在Windows中使用WSL的Ubuntu时，使用systemctl命令报错：
hh@LAPTOP-O6A604DC:~$ systemctl start docker
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down

- **问题分析**  
可能是因为这个Ubuntu系统并没有使用systemd，可能使用的是SysV init（sysvinit）初始化系统。
可以通过如下命令查看：
`ps -p 1`  
果真如此，CMD 列，显示 init，可以确定使用的是SysV init初始化系统。
SysV init：如果您的系统使用 SysVinit 作为 init 系统，您可以使用 service 命令来管理和检查服务状态。例如，要检查 Docker 服务状态，可以运行：
`sudo service docker status`  
什么是Systemd?  
Systemd是Linux系统的一套基本构建模块。它提供了一个系统和服务管理器，作为PID 1运行并启动系统的其余部分。  
systemctl作为systemd的一个工具，提供与Linux机器上的服务交互

- **问题解决**  
方案1：  
还是想要使用systemd，在WSL中启用systemd  
先决条件：确保运行的是来自 Microsoft Store 且版本号为0.67.6及以上版本的WSL  
更新WSL到最新版:  
`wsl --update`  
查看版本:  
`wsl --version`  
将 WSL 2 设置为默认版本 如果是WSL1，也是默认有wls.conf文件，但是就是不支持systemtcl命令，必须设置成WLS2:  
`wsl --set-default-version 2`  
运行命令查看版本:  
`wsl -l -v` 
在Ubuntu实例中，打开或者添加/etc/wsl.conf文件（我这里已经安装这个了，没有的话手动添加），并编辑内容如下：
```
[boot]
systemd=true
```
然后使用`wsl --shutdown`命令，将运行中的Linux发行版关闭。
重新运行Ubuntu。
再次查看初始化信息，可以看出已经使用了systemd，

运行查询systemctl版本，非常完美，已经可以用了

已启动成功，查询正常


3.2.方案2：
这个错误表明系统没有以 systemd 作为 init 系统来引导（PID 1）。这可能是因为系统正在使用其他 init 系统（如 Upstart 或 SysVinit）。因此，在尝试使用 systemctl 命令时会出现问题，因为该命令是与 systemd 相关的。

如果系统没有使用 systemd 作为 init 系统，需要使用适合你的 init 系统的命令来检查服务状态。以下是一些常见 init 系统的示例：

Upstart：如果您的系统使用 Upstart 作为 init 系统，您可以使用 initctl 命令来检查服务状态。例如，要检查 Docker 服务状态，可以运行：

sudo initctl status docker
1
SysVinit：如果您的系统使用 SysVinit 作为 init 系统，您可以使用 service 命令来管理和检查服务状态。例如，要检查 Docker 服务状态，可以运行：

sudo service docker status
1
结果可用生效

已启动成功，查询正常


4.验证两种命令都可查询使用，完美

## 在linux系统中连接移动硬盘/U盘
windows下使用接入U盘时会系统会自动为U盘指定一个盘符（如F盘），然后就可以直接操作了。但linux系统没有盘符的概念，所以需要手动将U盘挂载在某个目录下才能使用。

- 什么是挂载?
linux系统将所有的外部设备都视作为文件，所以将U盘插入后系统会将其识别为一个目录(虚拟的)，然后需要手动把这个目录映射到真实的目录（挂载点）中去，这样才能通过这个真实目录去访问这个设备，这个步骤叫就做挂载。   
注意：外部设备必须挂载在根目录下的/mnt（子）文件夹中，为了方便区分，实际操作一般会在mnt中创建不同的子文件夹代表去挂载不同的设备，这里创建的是/mnt/mydev。

- 具体步骤   
1.插入U盘，执行如下指令后能看到设备则说明连接成功    
`sudo fdisk -l `   
#查看外接设备名称，一般为/dev/sd...，这里假设为/dev/sdc1   
2.在/mnt下创建挂载点，进行挂载   
`sudo mkdir /mnt/mydev` #创建挂载点
`sudo mount /dev/sdc1 /mnt/mydev` #将/dev/sdc1挂载到mnt中的挂载点去   
`sudo df -h` #查看是否挂载成功   
3.此时挂载文件夹/mnt/mydev里面就是你U盘的内容了，可以随意访问   
4.取消挂载，这一步相当于windows下的弹出U盘   
`sudo umount /mnt/mydev` #取消挂载，拔出硬盘   
`sudo rmdir –p /mnt/mydev`  #删除挂载目录（选做）   
注意：一定一定一定一定要确保这个步骤执行！！！别问我为什么知道，问就是移动硬盘崩了。
