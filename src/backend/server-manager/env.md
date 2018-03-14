## SSL
## MongoDB
> 3.2.19

### Authorize
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

## Nginx
