---
title: mongodb
description: mongodb
date: 2023-08-18
tags:
  - mongodb
---
# mongodb数据恢复

安装 *Mongodb*：*[https://www.mongodb.com/](https://gitee.com/link?target=https%3A%2F%2Fwww.mongodb.com%2F)*

![image-20221109142847200](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-11-09-062847.png)

接下来下载 mongodb

![image-20221109142943156](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-11-09-062943.png)

*Mac* 系统建议放置到 /usr/local/mongodb

![image-20221109143105682](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-11-09-063106.png)

要启动 mongodb，需要 bin 目录下面的 mongod

后期可能会用到很多其他的可执行文件

![image-20221109143207171](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-11-09-063207.png)

新版本的 mongodb，有一个特点就是 bin 目录下面的可执行文件大大减少，如果想要补全，需要自己去官网下载，下载下来是一个压缩包，解压就会得到一堆可执行文件，放入到 *Mongodb* 安装目录的 *bin* 目录下面

![image-20221109143317367](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-11-09-063317.png)

## 启动 Mongodb

启动 *Mongodb*，使用 *Mongod* 可执行文件

```
./mongod -f 配置文件地址
例如：
./mongod -f /usr/local/mongodb/mongodb.conf
```

![image-20221109143605101](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-11-09-063605.png)

配置文件的内容如下：

```
systemLog:
  destination: file # 日志输出方式。file/syslog,如果是file，需指定path，默认是输出到标准输出流中
  path: /usr/local/mongodb/mongod.log  # 日志路径
  logAppend: true # 启动时，日志追加在已有日志文件内还是备份旧日志后，创建新文件记录日志, 默认false

net:
  port: 27017 # 监听端口，默认27017
  bindIp: 127.0.0.1 # 绑定监听的ip，设置为127.0.0.1时，只会监听本机
  maxIncomingConnections: 65536 # 最大连接数，可接受的连接数还受限于操作系统配置的最大连接数
  wireObjectCheck: true # 校验客户端的请求，防止错误的或无效BSON插入,多层文档嵌套的对象会有轻微性能影响,默认true

processManagement:
  fork: true  # 后台运行

security:
  authorization: disabled  # enabled/disabled # 开启客户端认证

storage:
  dbPath: /usr/local/mongodb/data # 数据库地址
```

> 注意：*Windows* 下面自带配置文件，后缀为 cfg，然后还有就是 *windows* 下面的配置文件的格式会有一些区别

建议安装一个数据库可视化工具，这个自由选择：

- *robo3t*
- *stduio3t*：基础功能是免费的，*[https://studio3t.com/](https://gitee.com/link?target=https%3A%2F%2Fstudio3t.com%2F)*
- *compass*：mongo 官方推出的可视化工具
- *navicat*

## 数据恢复

关于数据的恢复，这边需要使用到一个可执行命令，*mongorestore*，还需要保证 *mongodb* 的数据库服务器是启动起来的

![image-20221109144349195](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-11-09-064349.png)

```
mongorestore -h dbhost -d dbname --dir dbdirectory
例如：
./mongorestore -h localhost:27017 -d coderstation2 --dir /Users/jie/Desktop/coderstationData
```

如果你在恢复数据的时候，名字取了其他名字，服务器那边也需要修改成对应的名字：

![image-20221109144932400](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2022-11-09-064932.png)
