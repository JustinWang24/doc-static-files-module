# 数据库设计

本模块的数据将主要围绕系统的用户, 用户的文件目录与上传的文件的管理功能.

## 数据库管理

处于安全考虑, 除超级管理员外, 类似 Laravel 框架, 我司不允许直接链接数据库进行编辑任何表结构与数据, 也不会提供外部访问的机制来访问数据库服务器. 任何针对数据的修改, 只能通过本模块提供的脚本程序来执行.

### 准备工作

由于基于命令行的方式, 加载 Yaf 框架不太方便, 因此对于在开发期间, 使用 migration 工具, 需要找到 `migrations/get_db_connection.example.php` 文件, 拷贝其中内容, 然后在本地生成每个人自己的 `migrations/get_db_connection.php`文件. 该文件被用来在命令行方式下, 正常的获取数据库链接供之后操作数据库时使用.

### 在本地开发环境创建所有数据库表

```bash
php73 migrations/create_tables.php dev
```

### 在本地开发环境生成测试数据

开发人员可根据需求, 按以下的命令, 来生成不同规模的数据

```bash
php73 migrations/database_seeder.php dev ## 创建少于 100 万条数据, 阶段 1
```

## 目录/文件模块

### 用户表 users: models\User.php

这个表不归属本模块的管理范围, 本模块只是作为使用者提供了读取用户数据的能力

### 学校用户关系表 grade_users: models\GradeUser.php

这个表不归属本模块的管理范围, 本模块只是作为使用者提供了读取的能力. 主要作用是根据用户获取学校的 school_id

### 目录表 categories: models\Category.php

- 本表为高频读
- 由于一个学生在学习期间, 可以创建很多的目录, 因此本表的记录, 可以很容易的达到千万的量级, 因此需要考虑分表的操作, 即一个学校一个目录表, 来提升查询效率. 原型开发期间, 采用单表.

| 字段名       | 字段类型       | 默认值  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| id     | BigInteger | 自增长 | id |
| uuid      | string(36)      |   not null | 目录的 uuid; 索引列 |
| owner_id | BigInteger     |    not null |  目录所有者的 id |
| school_id | BigInteger     |    not null |  目录所有者的学校 id |
| type | SmallInteger     |    2 |  表示用户的根目录类型 |
| parent_id | BigInteger     |     0 |  表示上级目录的 id; 默认 0 表示上层目录为顶级目录 |
| created_at | TIMESTAMP     |     null |  创建时间 |
| updated_at | TIMESTAMP     |     null |  更新时间 |
| name | VARCHAR(255)     |     not null |  目录名称 |

### 媒体文件表 medias: models\Media.php

- 本表为高频读
- 由于一个学生在学习期间, 可以创建很多的文件, 因此本表的记录, 可以很容易的达到千万的量级, 因此需要考虑分表的操作, 即一个学校一个文件表, 来提升查询效率. 原型开发期间, 采用单表.

| 字段名       | 字段类型       | 默认值  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| id     | BigInteger | 自增长 | id |
| uuid      | string(36)      |   not null | 文件的 uuid; 索引列 |
| user_id | BigInteger     |    not null |  文件所有者的 id |
| type | SmallInteger     |    not null |  表示文件的类型 |
| category_id | BigInteger     |     0 |  表示文件归属的目录id |
| size |  Integer     |     0 |  文件大小(字节数) |
| period |  Integer     |     0 |  多媒体文件的播放时长 |
| driver |  Integer     |     0 |  文件保存的位置: 1->本地, 2->阿里云, 3->七牛 |
| created_at | TIMESTAMP     |    not null |  创建时间 |
| file_name | VARCHAR(255)     |     not null |  文件名称, 此处保存上传文件的原名 |
| url | Text     |     not null |  文件的真实位置 |
| keywords | Text     |     not null |  文件的关键字 |
| description | Text     |     null |  文件的描述 |
