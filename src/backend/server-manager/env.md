## SSL
## MongoDB
> 3.2.19

### Path
+ `mongod.conf`
  > `mongod` 全局设置文件，[设置文件说明文档](https://docs.mongodb.com/v3.2/reference/configuration-options/)
  ```
  $ /etc/mongod.conf
  ```
+ `mongod.log`
  > 可以在 `mongod.conf` 中进行配置，下面给出默认路径，
  > 可以很方便的通过 `log` 信息进行排错
  ```
  $ /var/log/mongodb/mongod.log
  ```
+ `mongod`
  > `mongodb` 的执行文件
  ```
  $ /usr/bin/mongod
  ```

### Authorize
+ 创建 `admin account`

  ```
  $ mongo
  $ use admin
  $ db.createUser({user:"admin",pwd:"password",roles:["root"]})
  ```
+ 创建 `db owner`

  ```
  $ mongo
  // 需要先进行登录
  $ use admin
  $ db.auth("admin", "password")
  // 切到需要分配角色的数据库，这里以 test 为例
  $ use test
  $ db.createUser({user: "testOnwer", pwd: "password", roles: [{ role: "dbOwner", db: "testOnwer" }]})
  ```
+ 如果忘记 `admin` 密码

  ```
  // 先去掉 systemctl script Environment 中的 -auth
  $ systemctl daemon-reload
  $ systemctl restart mongod
  $ mongo
  $ use admin
  $ db.system.users.remove({})
  $ db.createUser({user:"admin",pwd:"password",roles:["root"]})
  // 恢复 systemctl script Environment 中的 -auth
  $ systemctl daemon-reload
  $ systemctl restart mongod
  ```

### Systemctl launcher script

```
[Unit]
Description=MongoDB Database Server
After=network.target
Documentation=https://docs.mongodb.org/manual

[Service]
User=mongod
Group=mongod
Environment="OPTIONS=-f /etc/mongod.conf -auth"
ExecStart=/usr/bin/mongod $OPTIONS
ExecStartPre=/usr/bin/mkdir -p /var/run/mongodb
ExecStartPre=/usr/bin/chown mongod:mongod /var/run/mongodb
ExecStartPre=/usr/bin/chmod 0755 /var/run/mongodb
PermissionsStartOnly=true
PIDFile=/var/run/mongodb/mongod.pid
Type=forking
# file size
LimitFSIZE=infinity
# cpu time
LimitCPU=infinity
# virtual memory size
LimitAS=infinity
# open files
LimitNOFILE=64000
# processes/threads
LimitNPROC=64000
# locked memory
LimitMEMLOCK=infinity
# total threads (user+kernel)
TasksMax=infinity
TasksAccounting=false
# Recommended limits for for mongod as specified in
# http://docs.mongodb.org/manual/reference/ulimit/#recommended-settings

[Install]
WantedBy=multi-user.target
```
### Issue && Solution

+ 外部服务器连接
  
  
  因为默认设置的 `bindIp` 设置是 `127.0.0.1` 所以只能在本机上进行连接。如果需要 `bind` 所有的 `IPv4 IP`，可以设置为 `0.0.0.0`

+ 数据库文件备份、恢复、导入和导出
  >在本地对本地数据库进行操作均可忽略 `-h`，`--port` 参数

  备份
  ```
  $ mongodump -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件存在路径
  ```

  恢复
  ```
  $ mongorestore -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 --drop 文件存在路径
  ```

  导出
  ```
  $ mongoexport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 -f 字段
  ```
  + `-f: ` 导出指字段，以字号分割，`-f a,b,c` 导出3个字段，如果不填则导出整张表
  + `-o: ` 导出的文件名，可以加后缀名
  + `-q: ` 导出的 `filter`， `-q '{ "_id" : "1" }'` 只导出 `_id="1"` 的数据
  + `--csv: ` 表示导出的文件格式为 `csv`

  导入
  ```
  $ mongoimport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 --upsert --drop 文件名
  ```
  以上命令是导入 **非csv文件** ，如果是 **csv文件** 用 `--headerline` 替换 `--upsert`，如果不是整张表而是字段，则用 `--upsertFields a,b,c` 替换 `--upsert`


## Nginx
