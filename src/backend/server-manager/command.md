## Linux Shell

### ls && lsof 
+ 查看端口活动

```
$ lsof -i :27017
```
+ 查看文件权限

```
$ lsof -l Wired*
```
+ 查看权限和软连接
> 前面带有 `l` 的就表示是一个软连接
```
$ ls -al /usr/bin | python

// output
lrwxrwxrwx    1 root root           9 8月  29 2017 python2 -> python2.7
-rwxr-xr-x    1 root root        7136 11月  6 2016 python2.7
```

### systemctl

+ 查看通过 `systemctl` 启动的所有服务
  ```
  $ systemctl list-units --type=service
  ```
+ 添加/删除 开机启动项
  ```
  $ systemctl enable/disable mongod.service
  ```
+ `systemctl` 重载和修改命令
  > 参考资料：
  > 
  > + [How to set environment variable in systemd service?  - stackoverflow](https://serverfault.com/questions/413397/how-to-set-environment-variable-in-systemd-service)
  > + [How to write a systemd script?](http://fedoraproject.org/wiki/Packaging:Systemd)
  > + [How do I override or configure systemd services?](https://askubuntu.com/questions/659267/how-do-i-override-or-configure-systemd-services)

  首先需要明确的是 `systemctl services` 的配置文件一般是在 `/lib/systemd/system/` 路径下，而重载或者自己创建的配置文件是在 `/etc/systemd/system/` 下面。

  > 下面以重载 `mongod service` 为例
  
  ```
  $ systemctl edit mongod
  ```
  运行之后会在 `/etc/systemd/system/` 下面新建 `mongod.service.d` 文件夹，并在该文件夹下面新建一个文件，只要把需要的 `systemctl script` 写入该文件当中并保存为 `mongod.conf` 。

  ```
  $ systemctl daemon-reload
  ```
  运行之后会 `reload` 所有配置文件，然后再重新启动服务即可。

### Others
+ 修改文件权限
```
// 直接修改读写权限
$ chmod 664 WiredTigerLAS.wt
// 修改所属的组用户
$ chmod mongod:mongod WiredTiger.turtle
```
+ 杀掉进程

```
$ kill -9 processId
```
+ 查看软连接实际地址

```
$ which mongod
```
+ 进行软连接
> 生成 `python` 命令的软连接，指向 `python2`
```
$ ln -s /usr/bin/python2 /usr/bin/python
```