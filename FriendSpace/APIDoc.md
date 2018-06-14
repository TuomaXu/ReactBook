# API接口设计

## 元数据

### 账户

```
{
	id:0
	email:'',
	password:'',
	access_token:'',
}
```

### 用户信息

```
{
	"id": 3,
	"nickname": "tom2",
	"image": "55aff85258799e536c099689b69d81e9.png",
	"sign": "wwww",
}
```

### 消息

```
{
	"id": 10,
	"content": "ceshi333",
	"createdAt": "2017-11-23T00:19:26.000Z",
	"images": [
	    {
	        "id": 2,
	        "url": "c2436882682da92fbcdbf56025cb4db8.png"
	    }
	],
	"user": {
	    "id": 2,
	    "nickname": "tom3",
	    "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
	}
}
```

## 错误代码

|错误代码|内容|
|:---|:---|
|10001|用户名已存在|
|10002|用户名或密码错误|
|10003|access_token无效|
|10004|未初始化用户信息|
|10005|UserId无效|
|10006|旧密码错误|

## 请求基础地址

请求地址根据api启动时，本机ip


##接口

###注册

请求链接：`/api/register`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|email|用户名，邮箱|
|password|密码|

```
{
    "success": true,
    "data": {
        "access_token": "63b743fedb6dd5859f70b9f17d29f1a0"
    }
}
```

### 登录

请求链接：`/api/login`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|email|账户名，邮箱，此名用于登录|
|password|密码|

```
{
    "success": true,
    "data": {
        "access_token": "c512cff01724cbc2ef186a73a072d916",
        "userId": null,//用户信息，null，表示无用户信息
    }
}

```

### 修改密码

请求链接：`/api/changePassword`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|old_password|当前密码|
| new_password |新密码|

```
{
    "success": true
}
```

### 创建用户信息

请求链接：`/api/createUser`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|nickname|用户名，此名用于显示在页面上|
|sign|个性签名|
|image|用户头像，png文件|

```
{
    "success": true,
    "data": {
        "id": 3,
        "nickname": "tom2",
        "sign": "wwww",
        "image": "55aff85258799e536c099689b69d81e9.png",
        "updatedAt": "2017-11-22T08:03:41.010Z",
        "createdAt": "2017-11-22T08:03:41.010Z"
    }
}
```

### 获取用户信息

请求链接：`api/getUser`

请求方式：`POST `

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|userId|用户ID，可选参数，如不填或为0则获取登录用户信息|

```
{
    "id": 3,
    "nickname": "tom2",
    "image": "55aff85258799e536c099689b69d81e9.png",
    "sign": "wwww",
    "createdAt": "2017-11-22T08:03:41.000Z",
    "updatedAt": "2017-11-22T08:03:41.000Z"
}
```



### 更新用户信息

请求链接：`/api/updateUser`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|nickname|用户昵称|
|sign|用户签名|
|image|用户头像，png文件|

```
{
    "success": true,
    "data": {
        "id": 2,
        "nickname": "tom3",
        "image": "b80f9ccf334cda880cde0a25c6b68ead.png",
        "sign": "qqq",
        "createdAt": "2017-11-22T08:02:18.000Z",
        "updatedAt": "2017-11-23T01:08:53.715Z"
    }
}
```

### 发Message

请求链接：`/api/postMessage`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|content|消息内容|
|imageN|消息图片，N值为1-9，文件类型|

```
{
    "id": 12,
    "content": "ceshi444",
    "createdAt": "2017-11-23T00:22:24.000Z",
    "updatedAt": "2017-11-23T00:22:24.000Z",
    "userId": 2,
    "images": [
        {
            "id": 4,
            "url": "3eaa652014fdffd63f781c62deba1410.png"
        },
        {
            "id": 5,
            "url": "d911da87cbb0eac478b2747e512b560a.png"
        }
    ]
}
```

### 删除Message

请求链接：`/api/deleteMessage`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|messageId|消息ID|

```
{
    "success": true
}
```

### 通过昵称查找用户

请求链接：`/api/findUser`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|nickname|用户昵称，精确查找非模糊|


```
{
    "success": true,
    "data": [
        {
            "id": 2,
            "nickname": "tom3",
            "image": "b80f9ccf334cda880cde0a25c6b68ead.png",
            "sign": "qqq",
            "createdAt": "2017-11-22T08:02:18.000Z",
            "updatedAt": "2017-11-23T01:08:53.000Z"
        }
    ]
}
```

### 关注好友

请求链接：`/api/follow`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|userId|好友ID|

```
{
    "success": true
}
```


### 取消关注好友

请求链接：`/api/follow`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|userId|好友ID|

```
{
    "success": true
}
```

### 获取好友列表

请求链接：`/api/getFollow`

请求方式：`POST `

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|

```
{
    "success": true,
    "data": [
        {
            "id": 3,
            "nickname": "tom2",
            "image": "55aff85258799e536c099689b69d81e9.png",
            "sign": "wwww",
            "createdAt": "2017-11-22T08:03:41.000Z",
            "updatedAt": "2017-11-22T08:03:41.000Z",
            "friendship": {
                "info": null,
                "createdAt": "2017-11-23T02:30:31.000Z",
                "updatedAt": "2017-11-23T02:30:31.000Z",
                "userId": 2,
                "followedId": 3
            }
        },
        {
            "id": 6,
            "nickname": "tom5",
            "image": "574a4d1004770d2743d72d35dd094f78.png",
            "sign": "wwww",
            "createdAt": "2017-11-23T02:29:46.000Z",
            "updatedAt": "2017-11-23T02:29:46.000Z",
            "friendship": {
                "info": null,
                "createdAt": "2017-11-23T06:20:19.000Z",
                "updatedAt": "2017-11-23T06:20:19.000Z",
                "userId": 2,
                "followedId": 6
            }
        }
    ]
}
```


### 请求用户Message

请求链接：`/api/getMessage`

请求方式：`POST `

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|userId|可选参数，如有返回该userId下的Message|
|minId|可选参数，如有返回小于此messageId的Message|
|count|可选参数，默认返回20条|

```
{
    "success": true,
    "data": [
        {
            "id": 5,
            "content": "ceshi222",
            "createdAt": "2017-11-22T23:57:25.000Z",
            "updatedAt": "2017-11-22T23:57:25.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        }
    ]
}
```

### 请求HomeMessage

请求链接：`/api/homeMessage`

请求方式：`POST `

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|minId|可选参数，如有返回小于此messageId的Message|
|count|可选参数，默认返回20条|

```
{
    "success": true,
    "data": [
        {
            "id": 14,
            "content": "ceshi666",
            "createdAt": "2017-11-23T01:46:29.000Z",
            "updatedAt": "2017-11-23T01:46:29.000Z",
            "userId": 3,
            "images": [],
            "user": {
                "id": 3,
                "nickname": "tom2",
                "image": "55aff85258799e536c099689b69d81e9.png"
            }
        },
        {
            "id": 13,
            "content": "ceshi555",
            "createdAt": "2017-11-23T01:46:13.000Z",
            "updatedAt": "2017-11-23T01:46:13.000Z",
            "userId": 3,
            "images": [],
            "user": {
                "id": 3,
                "nickname": "tom2",
                "image": "55aff85258799e536c099689b69d81e9.png"
            }
        },
        {
            "id": 11,
            "content": "ceshi444",
            "createdAt": "2017-11-23T00:21:36.000Z",
            "updatedAt": "2017-11-23T00:21:36.000Z",
            "userId": 2,
            "images": [
                {
                    "id": 3,
                    "url": "67097108fb46fead358e188b61410f24.png"
                }
            ],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 10,
            "content": "ceshi333",
            "createdAt": "2017-11-23T00:19:26.000Z",
            "updatedAt": "2017-11-23T00:19:26.000Z",
            "userId": 2,
            "images": [
                {
                    "id": 2,
                    "url": "c2436882682da92fbcdbf56025cb4db8.png"
                }
            ],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 9,
            "content": "ceshi222",
            "createdAt": "2017-11-23T00:16:56.000Z",
            "updatedAt": "2017-11-23T00:16:56.000Z",
            "userId": 2,
            "images": [
                {
                    "id": 1,
                    "url": "b09175e064d2d0f8244787b8d10bdccd.png"
                }
            ],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 8,
            "content": "ceshi222",
            "createdAt": "2017-11-23T00:07:10.000Z",
            "updatedAt": "2017-11-23T00:07:10.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 7,
            "content": "ceshi222",
            "createdAt": "2017-11-23T00:06:00.000Z",
            "updatedAt": "2017-11-23T00:06:00.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 6,
            "content": "ceshi222",
            "createdAt": "2017-11-22T23:58:07.000Z",
            "updatedAt": "2017-11-22T23:58:07.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 5,
            "content": "ceshi222",
            "createdAt": "2017-11-22T23:57:25.000Z",
            "updatedAt": "2017-11-22T23:57:25.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        }
    ]
}
```