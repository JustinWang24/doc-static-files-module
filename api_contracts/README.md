# 接口设计

::: danger

- 所有接口中规定的返回字段都必须被返回, 如果是空值, 则返回空字符串; 如果是空列表, 则返回空数组
- 对外提供服务的接口表示由外界发起呼叫, 本模块进行被动响应
- 在任何情况下, 本模块需要对外主动发起通信请求时需要实现的接口

:::

*约定: 本模块所有的返回数据中的状态码, 均以数字 3开头, 状态码范围为 3000 - 3999 之间的整数值.*

## 对外提供服务的接口

### 与一般用户(包括学生, 教职员, 商户用户)相关的目录接口

本接口响应来自网页端, 移动 APP 端的响应.

#### 1: 获取目录详情接口 api/category/view

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| uuid      | Yes      |   String | 要获取详情的目录的 uuid |
| user | Yes     |    String | 用户的uuid |

##### 响应数据: json 格式

```json
{
    "code": 3000,   // 操作结果的数字代码: 3000表示成功获取到
    "message": "",  // 操作结果的文字消息
    "data": {
        // 下一步操作的描述符接口, 非空表示系统强制前端进行某些操作
        "redirect": {
            "action": "Action describer",
            "params": {},
        },
        // 目录的详细数据
        "category":{
            "uuid": "user-uuid-string", // 目录的 UUID
            "name": "",               // 目录名称
            "type": "",               // 目录类型
            "created_at": "",         // 目录创建日期时间
            "children":[
                // 目录的子目录
                {
                    "uuid": "category1-uuid",
                    "name": "category name1",
                    "created_at": "",         // 目录创建日期时间
                },{
                    "uuid": "category2-uuid",
                    "name": "category name2",
                    "created_at": "",         // 目录创建日期时间
                }
                // ... More child categories
            ],
            // 当前目录的父级目录, 为 null 表示当前目录为该用户的顶级目录
            "parent": {
                "uuid": "category-parent-uuid",
                "name": "parent category name",
            },
            "files": [
                {
                    "uuid": "", // 文件的 uuid
                    "name": "", // 文件的名称
                    "created_at": "",   // 创建日期
                    "period": "120",   // 如果是视频或音频文件, 播放时长, 单位为秒
                    "type": "", // 图片, office 的文档, 音视频等
                    "size": 1500000, // 以字节为单位
                    "url": ""   // 文件所在的真实下载地址
                },
                // ... 更多文件
            ]
        }
    }
}
```

::: warning
本接口返回的数据中, 如果是最底层的目录, 将不会有子目录数组, 而只包含文件数组
:::

#### 2: 创建目录接口 /api/category/create

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| parent      | Yes      |   String | 被创建目录的上级目录 uuid |
| name      | Yes      |   String | 被创建目录的名称 |
| user | Yes     |    String | 用户的uuid |

##### 创建目录接口响应数据: json 格式

```json
{
    "code": 3000,  // 操作结果的数字代码: 3000表示成功获取到
    "message": "", // 消息
    "data": {
        "uuid": "uuid" // 新创建的目录的 uuid
    }
}
```

#### 3:  修改目录名称接口 /api/category/edit

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| uuid      | Yes      |   String | 被修改的目录的 uuid |
| name      | Yes      |   String | 目录的新名称 |
| user | Yes     |    String | 用户的uuid |

##### 修改目录名称接口响应数据: json 格式

```json
{
    "code": 1000,  // 操作结果的数字代码: 3000表示成功获取到
    "message": "" // 消息
}
```

#### 4:  删除目录接口 /api/category/delete

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| uuid      | Yes      |   String | 被删除的目录的 uuid |
| user | Yes     |    String | 用户的uuid |

##### 删除目录接口响应数据: json 格式

```json
{
    "code": 1000,  // 操作结果的数字代码: 3000表示成功获取到
    "message": ""  // 消息
}
```

---

### 与一般用户(包括学生, 教职员, 商户用户)相关的文件接口

本接口响应来自网页端, 移动 APP 端的登陆响应.

#### 1:  文件上传接口 /api/file/upload

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| category      | Yes      |   String | 上传到哪个目录, uuid |
| user | Yes     |    String | 用户的uuid |
| file |  Yes     |    file | 上传的文件 |
| keywords |  Optional     |    String | 文件的关键字, 用于查询 |
| description | Optional     |    String | 文件的描述文字 |

##### 文件上传接口的响应数据, json

```json
{
    "code": 1000,  // 操作结果的数字代码: 3000表示成功获取到
    "message": "",  // 消息
    "data": {
        "file": {
            "name": "name", // 新文件的名称
            "uuid": "uuid", // 新文件的 uuid
            "url": "url",   // 文件的下载地址
            "type": "type",         // 文件的类型
            "size": 1000000,        // 文件的大小, 字节数
            "period": 3600,       // 如果是视频或音频文件, 播放时长, 秒数
        },
        // 下一步操作的描述符接口, 非空表示系统强制前端进行某些操作
        "redirect": {
            "action": "Action describer",
            "params": {},
        }
    }
}
```

#### 2:  修改文件接口 /api/file/edit

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| uuid      | Yes      |   String | 被修改的文件的 uuid |
| user | Yes     |    String | 用户的uuid |
| name |  Yes     |    String | 文件的新名字 |
| keywords |  Optional     |    String | 文件的新关键字, 用于查询 |
| description | Optional     |    String | 文件的新描述文字 |

##### 修改文件接口的响应数据, json

```json
{
    "code": 1000,  // 操作结果的数字代码: 3000表示成功获取到
    "message": ""  // 消息
}
```

#### 3:  删除文件接口 /api/file/delete

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| uuid      | Yes      |   String | 被删除的文件的 uuid |
| user | Yes     |    String | 用户的uuid |

##### 删除文件接口的响应数据, json

```json
{
    "code": 1000,  // 操作结果的数字代码: 3000表示成功获取到
    "message": ""  // 消息
}
```

#### 4:  搜索文件接口 /api/file/search

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| category     | Yes      |   String | 要搜索的目标文件夹的 uuid |
| user | Yes     |    String | 用户的uuid |
| keyword | Yes     |    String | 要搜索的关键字 |

##### 搜索文件接口的响应数据, json

```json
{
    "code": 1000,  // 操作结果的数字代码: 3000表示成功获取到
    "message": "",  // 消息
    "data":{
        "files": [
            {
                "file_name": "name", // 文件的名称
                "uuid": "uuid", // 文件的 uuid
                "url": "url",   // 文件的下载地址
                "type": "type",         // 文件的类型
                "size": 1000000,        // 文件的大小, 字节数
                "period": 3600,       // 如果是视频或音频文件, 播放时长, 秒数
            },
            // ... more files
        ]
    }
}
```
---
