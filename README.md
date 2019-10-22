# 静态文件服务模块设计文档

对静态文件的管理, 组织, 上传, 下载等

## 系统配置

需要在 application.ini 文件中确保

- `application.storage.driver = 1` 表示用户上传的文件保存在本地
- `application.storage.local.file_max_size = "10"` 单个上传的文件最大为 10M
- `application.storage.local.root = "storage/uploads"` 指定文件在本地的保存路径的根目录. 表示文件都保存在 ROOT/storage/uploads 文件夹中
- 为了用户可以访问本地存储的文件, 需要在 public 文件夹中创建一个名为 `storage` 的 symlink, 指向本地的保存路径的根目录: ROOT/storage/uploads

## 如果使用云服务来保存用户上传的文件

- `application.storage.driver = 2` 表示使用阿里云的 OSS 保存
- `application.storage.driver = 3` 表示使用七牛云存贮服务保存

## 静态文件目录的结构设计

### 学生与教师用户

- 系统默认每个学生有一个根目录, 每个目录中的文件限定为如下的组织结构

```bash
user-root-folder
    - year-2018
        - 数学课
            - 第一节课笔记-作业-附件
                - 课堂笔记.doc
                - 作业.doc
                - 参考材料.pdf
                - 视频参考资料.MP4
            - 第二节课笔记-作业-附件
            - ...
        - 电子电路
        - ...
    - year-2019
    - ...
```

- 系统的数据库中, 对学生用户将严格遵循以上的结构并进行了实现
- 文件的持久化存储, 依赖阿里云的 OSS 服务
- 每个学生的空间上限为 500M/用户, 需要在学生上传文件时对其进行限制, 包括文件格式, 空间大小等
- 教师用户空间上线为2G

### 文件目录的种类

- 文件系统根目录 `TYPE_ROOT`, 类型的数字值为 0
- 某个学校的根目录 `TYPE_SCHOOL_ROOT`, 类型的数字值为 1
- 某个用户的根目录 `TYPE_USER_ROOT`, 类型的数字值为 2
- 某个用户某个学年的根目录 `TYPE_USER_YEAR_ROOT`, 类型的数字值为 3
- 某个用户某个科目的根目录: 比如数学, 挖掘机 `TYPE_USER_SUBJECT_ROOT`, 类型的数字值为 4
- 某个用户某堂课的根目录: 比如 高等数学2019 年度上学期第三堂课 `TYPE_USER_LESSON_ROOT`, 类型的数字值为 5

## 接口设计

[进入文档](/api_contracts/)

## 数据库设计

[进入文档](/database/)

## 功能测试

[进入文档](/unit_tests/)
