# MySQL

MySQL的层次结构

```
数据库管理系统DBMS(DataBase Management System)
数据库DB(DataBase)

Table	数据表(对应现实中的表格)

Record	数据表中的一行

Fields	数据表中的一列
```



### 关联关系的介绍

在关系型数据库中，数据表之间的关联关系是通过共享相同列的值（通常是主键和外键）来建立的。这种关联关系允许你跨多个表获取相关的数据。

常见的数据表关联关系有以下几种类型：

1. 一对一关系（One-to-One Relationship）：当一个表的每个记录（行）与另一个表的某个记录（行）具有唯一对应关系时，就存在一对一关系。在这种关系中，每个表的主键和外键都可以互相对应。例如，一个 "人" 表和一个 "身份证" 表，每个人只有一个唯一的身份证号码，每个身份证号码也只对应一个人。
2. 一对多关系（One-to-Many Relationship）：当一个表的每个记录（行）与另一个表的多个记录（行）具有关联关系时，就存在一对多关系。在这种关系中，一个表的主键对应另一个表的外键。例如，一个 "顾客" 表和一个 "订单" 表，一个顾客可以有多个订单，但每个订单只属于一个顾客。
3. 多对多关系（Many-to-Many Relationship）：当一个表的多个记录（行）与另一个表的多个记录（行）具有关联关系时，就存在多对多关系。在这种关系中，通常需要使用一个中间表（也称为连接表）来建立关系。例如，一个 "学生" 表和一个 "课程" 表，一个学生可以选择多门课程，一门课程也可以有多个学生选择。

## 数据库

```
每一条语句后必须使用;
```

- 查看数据库
  show databases;

- 创建数据库
  create database 数据库名  【库选项】

  - 库选项
    charset 用于设置存储字符集
    collate  用于设置校验集

- 查看数据库的创建语句
  
  show create database 数据库名;
  
- 删除数据库

  drop database 数据库名;

- 选择数据库

  use 数据库名;

## 数据表

- 查看数据表
  show tables;

- 创建数据表(当创建一个表后，仅是创建了一个表的结构(表头))

  ​	create  table  表名(

  ​	字段名 列类型 【列属性】,

  ​	字段名 列类型 【列属性】,

  ​    ….

  )【表选项】

   【表选项】

  ​		charset		置存储字符集

  ​		collate 		设置校验集

  ​		engine		 存储引擎

  [![sNtUht.png](https://s3.ax1x.com/2021/01/13/sNtUht.png)](https://imgchr.com/i/sNtUht)

  [![sNNtrF.png](https://s3.ax1x.com/2021/01/13/sNNtrF.png)](https://imgchr.com/i/sNNtrF)

- 查看表结构（只能查看字段信息）

  desc 表名;

- 查看表的创建语句

  show create table 表名;


- 删除表

​	   drop table 表名;

- 增加数据

  insert  into 表名【(字段列表)】 values(值列表)

  ```
  字段列表中字段名不需要加引号
  
  值列表中的字符型的值必须加引号。
  
  字段列表中字段的个数要与值列表中值的个数一致，而且顺序也一要致。
  
  字段列表也可以省略，如果省略那表示全部字段插入数据。值列表中值的顺序要与表中的字段的顺序一致。
  ```

  [![sNUgS0.png](https://s3.ax1x.com/2021/01/13/sNUgS0.png)](https://imgchr.com/i/sNUgS0)

- 查看数据
  select *|字段列表 from 表名 【where子句】【group by子句】【having子句】【order by子     句】【limit子句】

  - *：所有的字段
    字段列表：用于查看部分字段。

  - where子句：根据where子句的表达式，对记录进行筛选。即是表达式就会涉及到运算符

  - MySQL的运算符：、<、>=、<=、<>、=、and、or、not、between m and n、in(值列表)、is null、like

    - =	用于判断是不相等，同也是赋值运算符
    - and、or					   		逻辑与、逻辑或
    - between m and n	 	  字段的值介于m与n之间
    - in(值列表)				     	  字段的值等于值列表中的某一个
    - is null					         	  专用于判断null值的运算符
    - like						       		用于模糊查询，必须与两个占位符进匹配使用
    - 占位符：%、_
       %			代表当前位置及其后0个或多个字符
       _			代表当前位置的1个字符

    

- 修改数据

  update 表名 set 字段1=值, 字段2=值,… 【where 子句】

  ```
  mysql> update  'runoob_tbl'  set  'submission_date'='2016-05-06' where  'runoob_id'=3;
  ```

- 删除数据

  delete from 表名 【where子句】

## 修改表结构

- 增加字段

  alter table 表名 add 【column】 字段名 列类型 列属性 【first| afterp字段名】

- 删除字段

  alter table 表名 drop 【column】 字段名;

- 修改字段名

  alter table 表名 change 原字段名 新字段名 列类型 列属性。

- 修改表名

  alter table 表名 rename to 新名

## 键

primary  主要的意思

key		 键，字段

primary key 重要的主要的字段。此处的重要并不说对用户重要，而是对MySQL来说重要。主要体现在对记录的查找上。



现实中学生与班级之间具有关系，如何在数据表中之间也将这种关系表示出来呢？

解决方案：在学生表中添加一个字段，用于存储这个学生所属的班级的id，这样的话就建立了表与表之间关系，也就是学生与班级之间的关系。

## 视图

# Docker

## 安装

https://www.runoob.com/docker/windows-docker-install.html

## 容器使用

- docker查看Docker 客户端的所有命令选项。

  ![img](https://www.runoob.com/wp-content/uploads/2016/05/docker27.png)

- 命令 **docker command --help** 更深入的了解指定的 Docker 命令使用方法

  - ```
    docker stats --help
    ```

    ![img](https://www.runoob.com/wp-content/uploads/2016/05/docker28.png)

- 获取镜像

  如果我们本地没有 ubuntu 镜像，我们可以使用 docker pull 命令来载入 ubuntu 镜像：

  ```
  $ docker pull ubuntu
  $ docker pull ubuntu:13.10
  ```

- 启动容器

  以下命令使用 ubuntu 镜像启动一个容器，参数为以命令行模式进入该容器：

  ```
  $ docker run -it ubuntu /bin/bash
  $ docker run -t -i ubuntu:15.10 /bin/bash 
  ```

- 查看所有的容器命令如下：

  ```
  $ docker ps -a
  ```

- 查看我们正在运行的容器

  ```
  docker ps 
  ```

- 使用 docker start 启动一个已停止的容器：

  ```
  $ docker start id
  ```

- 进入容器

  在使用 **-d** 参数时，容器启动后会进入后台。此时想要进入容器，可以通过以下指令进入：

  - **docker attach**

  - **docker exec**：推荐大家使用 docker exec 命令，因为此退出容器终端，不会导致容器的停止。

    ```
    docker exec -it id /bin/bash
    ```

    - **-i**: 交互式操作。
    - **-t**: 终端。

- 删除容器

  删除容器使用 **docker rm** 命令：

  ```
  $ docker rm -f id
  ```

## 镜像使用

- 列出镜像列表

  ```
  docker images
  ```

- 查找镜像

  - 我们可以从 Docker Hub 网站来搜索镜像，Docker Hub 网址为： https://hub.docker.com/

  - ```
     docker search httpd
    ```

- 删除镜像

  镜像删除使用 **docker rmi** 命令，比如我们删除 hello-world 镜像：

  ```
  $ docker rmi hello-world
  ```