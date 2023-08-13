---
title: Node学习笔记
description: Node学习笔记
date: 2020-12-08
tags:
  - Node
---

# Node内置模块

## require函数都做了什么？

 伪代码参考

```js
function require(modulePath){
    // 1.将modulePath转换为绝对路径: D:\\xxxx
    // 2.判断是都有该模块的缓存
    // if(require.cache["D:\\xxxx"]){
    // return require.cache["D:\\xxxx"
    // }
    // 3. 读取文件内容
    // 4. 包裹到函数环境中
    function __require(module,exports,require,__dirname,__filename){
        // 文件内容
    }
    // 创建module对象
    module.exports = {}
    const exports = module.exports
    
    // 绑定this 
    __require.call(module.exports,module,exports,require,module.path,module.filename)
    // 最终会返回module.exports
    return module.exports
}

```

## 基本内置模块

**os:**https://nodejs.org/docs/latest-v16.x/api/os.html

**path:** https://nodejs.org/docs/latest-v16.x/api/path.html

**url:** https://nodejs.org/docs/latest-v16.x/api/url.html

**util:**https://nodejs.org/docs/latest-v16.x/api/util.html

## 文件IO

fs模块：https://nodejs.org/dist/latest-v12.x/docs/api/fs.html



## node事件循环

![](D:\A桌面文件夹\vitePress\vitepress-blog-zaun-master\vitepress-blog-zaun-master\public\node事件循环.png)

- nextTick优先级最高 其次是 promise
- *timers* 阶段：这个阶段执行 *timer*（ *setTimeout、setInterval* ）的回调
- *I/O callbacks* 阶段：处理一些上一轮循环中的少数未执行的 *I/O* 回调
- *idle、prepare* 阶段：仅 *Node.js* 内部使用
- *poll* 阶段：获取新的 *I/O* 事件, 适当的条件下 *Node.js* 将阻塞在这里
- *check* 阶段：执行 *setImmediate( )* 的回调
- *close callbacks* 阶段：执行 *socket* 的 *close* 事件回调

# mysql

## 安装

**官方下载源：** https://dev.mysql.com/downloads/mysql/

**腾讯下载源：**https://pc.qq.com/detail/3/detail_1303.html

![](D:\A桌面文件夹\vitePress\vitepress-blog-zaun-master\vitepress-blog-zaun-master\public\mysql1.png)

一路next 如出现 Database initialization failed. 

**解决办法：**

- 把计算机名称改为全英文 
- 避免出现中文路径
- 卸载mysql 重启 重新安装



## 常用命令行

- mysql -uroot -p 使用密码登录超管命令交互
- show variables like 'character\_set\_%'；查看当前数据库字符编码   **修改my.ini文件中的默认字符编码 **
- net stop mysql80 停止服务
- net start mysql80 启动服务
- show databases：查看当前拥有的数据库

## SQL语句 

- 创建数据库  

  ```
  CREATE DATABASE `test`;
  ```

- 删除数据库

  ```
  DROP DATABASE `test`
  ```

- 切换数据库

  ```
  USE test
  ```





- 增加语句

  ```
  INSERT INTO `student`(stuno,`name`,birthday,sex,phone,classid)
  VALUES('100','李哥','1999-05-23',true,'15650585591',1),
  ('100','王哥','1999-05-22',true,'15682889969',1);
  ```

- 修改语句

  ```
  UPDATE student SET `name`='王蛋'
  WHERE id = 5
  ```

- 删除语句

  ```
  DELETE FROM student
  WHERE id = 5
  ```

- 查询语句

  - 单表查询

    - select from	

      ```
      SELECT id, loginid, loginpwd, 'abc' as '额外的一列' from `user`;
      
      SELECT ismale '性别' from `employee`;
      	
      SELECT *, 'abc' as 'extra' from `employee`;
      
      SELECT id, `name`, 
      case ismale
      when 1 then '男'
      else '女'
      end sex, 
      salary 
      FROM employee;
      
      SELECT id, `name`, 
      case
      when ismale = 1 then '男'
      else '女'
      end sex, 
      case
      when salary>=10000 then '高'
      when salary>=5000 then '中'
      else '低'
      end `level`,
      salary
      FROM employee;
      ```

    - where

      ```
      SELECT * FROM employee
      WHERE ismale = 1;
      
      SELECT * FROM department
      WHERE companyId in (1, 2);
      
      SELECT * from employee
      WHERE location is not null;
      
      SELECT * from employee
      WHERE location is null;
      
      SELECT * from employee
      WHERE salary>=10000;
      
      SELECT * from employee
      WHERE salary BETWEEN 10000 and 12000;
      
      SELECT * from employee
      WHERE `name` like '%袁%';
      
      SELECT * from employee
      WHERE `name` like '袁_';
      
      SELECT * from employee
      WHERE `name` like '张%' and ismale=0 and salary>=12000;
      
      SELECT * from employee
      WHERE `name` like '张%' and (ismale=0 and salary>=12000
      or
      birthday>='1996-1-1');
      ```

    - order by

      ```
      SELECT *, case ismale
      when 1 then '男'
      else '女'
      end sex from employee
      ORDER BY sex asc, salary desc;
      ```

    - limit

      ```
      SELECT * from employee
      limit 2,3;
      ```

​		执行顺序

​		from where select order by limit

##  表

常见字段类型:

- bit：占1位，0或1，false或true
- int：占32位，整数
- decimal(M,N)：能精确计算的实数，M是总共 的数字位数，N是小数位数
- char(n)：固定长度位n的字符
- varchar(n)：长度可变，最大长度位n的字符
- text：大量的字符
- date：仅日期
- datetime：日期和时间
- time：仅时间

 
