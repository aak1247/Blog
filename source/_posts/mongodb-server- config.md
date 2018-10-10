title: linux ubuntu 下 mongodb 4.0 安装和配置远程连接

category: 配置

tags:
 - mongodb
 - 配置

date: 2018/10/10
---

# 安装

搬运： [安装教程](https://docs.mongodb.com/manual/administration/install-on-linux/)

选择对应版本，复制粘贴命令即可，这里就不涉及了。

<!--more-->

安装完成后启动服务：
``sudo service mongod start``

如果提示``Unit mongod.service not found``，可以尝试以下命令：
```shell
sudo systemctl enable mongod
sudo systemctl daemon-reload
```
以上命令无效，可以创建 ``/lib/systemd/systemd/mongod.service`` 文件，然后填入以下内容：
```service
[Service]
User=mongodb
Group=mongodb
EnvironmentFile=-/etc/default/mongod
ExecStart=/usr/bin/mongod --config /etc/mongod.conf
PIDFile=/var/run/mongodb/mongod.pid
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

然后再输入前面两条命令激活服务。


服务启动完成后可以输入``sudo service mongod status``查看当前状态。

# 配置远程连接

编辑配置文件

```shell
sudo vim /etc/mongod.conf
```

4.0的配置文件采用yaml格式, 设定也有所不同。这里将 ``net.bindIp`` 设为 ``0.0.0.0``即可。

``:x``退出，然后``sudo service mongod restart``重启服务即可。

# 配置用户

用户配置与之前版本无差异，使用``db.createUser()``创建用户即可。

```js
use admin
db.createUser({
    user: "admin",
    pwd: "admin",
    roles: [
        {
            role: "dbAdminAnyDatabase",
            db: "admin"
        }
    ]
})
```

# 启用认证

修改配置文件，添加：
```yaml
security:
    authorization: enabled
```

然后重启服务。

测试登录：
```shell
mongo --port 27017 -u admin -p admin --authenticationDatabase admin
```

配置成功就可以正常进入mongo shell了。

# 坑

## 数据不兼容

从旧版本升级到``mongodb 4.0``可能会存在数据不兼容的问题，对应的服务错误码``exitCode=62/n/a``。可以先将现有数据导出，，然后进入/var/lib/mongodb, 删除目录下文件。启动完成后再导入历史数据。


如：
```shell
cd
mongodump -u admin -p admin -d test -o data # -d制定要导出的数据库名称
#......导出当前的所有库
cd /var/lib/mongodb
sudo rm ./* -r
sudo service mongod start
cd
mongorestore -u admin -p admin --dir data
```

admin库的导入导出可能存在问题，如果出现无法登录的问题，可以先关闭认证，然后``db.changeUserPassword("username", "password")``修改为之前的密码，完成后再启用认证。


# 参考文献

- 更详细的配置： [配置](https://docs.mongodb.com/manual/reference/configuration-options/)
- 错误码定义： [错误码](https://github.com/mongodb/mongo/blob/master/src/mongo/util/exit_code.h)
- 备份和恢复： [back up and restore tools](https://docs.mongodb.com/manual/tutorial/backup-and-restore-tools/)
- 用户和角色管理: [Mange Users and Roles](https://docs.mongodb.com/manual/tutorial/manage-users-and-roles/)