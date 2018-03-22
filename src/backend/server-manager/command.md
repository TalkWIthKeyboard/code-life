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
+ 查看权限和软连接
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
## Redis

### CLI
+ `redis-server [conf_file_path]: ` 使用配置文件启动 `redis` 服务
+ `redis-cli -h host -p port: ` 连接 `redis` 服务

### SCAN
> `pattern`: 正则键值 `count`: 迭代的长度
+ `SCAN cursor [MATCH pattern] [COUNT count]: ` 从 `cursor` 开始进行游标查找，在生产环境下为了防止锁库可以使用该命令代替 `keys *`
+ `SSCAN key cursor [MATCH pattern] [COUNT count]: ` 用来迭代 `SET` 中的元素
+ `HSCAN key cursor [MATCH pattern] [COUNT count]: ` 用来迭代 `HASH` 中的元素
+ `ZSCAN key cursor [MATCH pattern] [COUNT count]: ` 用来迭代 `ZSET` 中的元素

### KEY
+ `KEYS key(regex): ` 显示所有符合 `key` 正则表达式的 `key` 名字
+ `EXISTS key1 key2: ` 检查给定的 `key` 是否存在，存在则输出1，不存在输出0。如果是多个 `key` 则输出存在的 `key` 的个数。  
+ `EXPIRE key: ` 设置过期时间
+ `TTL key: ` 查看剩余的过期时间，精确到秒，未设置过期时间则返回 `-1`
+ `PTTL key: ` 查看剩余的过期时间，精确到毫秒，未设置过期时间则返回 `-1`

### SET
+ `SMEMBER key: ` 输出给定集合的所有元素。
+ `SISMEMBER key item: ` 检查某元素是否在给定集合中
+ `SADD key item1 item2: ` 将元素加入到集合
+ `SDIFF key1 key2: ` 显示多个集合之间的差集，只有一个集合则返回本身
+ `SCARD key: ` 显示集合中元素的个数

### HASH
+ `HSET key field value: ` 设置指定 `field` 的 `value` 值
+ `HGET key field: ` 获field得指定 `field` 对应的 `value ` 值
+ `HSET key field1 value1 field2 value2: ` 设置多个指定的 `field` 的 `value` 值
+ `HMGET key field1 field2: ` 获得多个 `field` 对应的 `value` 值
+ `HINCRBY key field increment: ` 为哈希表 `key` 中的 `field` 的值加上增量 `increment`，对字符串操作会报错，如果 `key` 或者 `field` 不存在会新建以后执行 `HINCRBY` 操作

### LIST
### ZSET

## Influxdb
### CLI
+ `influx --host host --port port --database db: ` 连接数据库
+ `create database "db_name": ` 创建数据库
+ `show databases: ` 显示所有数据库
+ `drop database "db_name": ` 删除数据库
+ `use db_name: ` 使用数据库
+ `show measurements: ` 显示该数据库中所有的表
+ `insert test,host=127.0.0.1,monitor_name=test count=1: ` 创建表，直接在插入数据的时候指定表名
+ `drop measurement "measurement_name": ` 删除表
