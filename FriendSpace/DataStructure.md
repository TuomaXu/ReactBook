# 产品数据设计

## 基础数据

### 账户数据

模型名:`account`

|字段名|类型|字段描述|
|:---|:---|:---|
|email|string|登录账号名|
|password|string|账号密码|
|access_token|string|登录令牌|

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('account',{
        email:DataTypes.STRING,
        password:DataTypes.STRING,
        access_token:DataTypes.STRING,
    });
}
```

### 用户数据

模型名:`user`

|字段名|类型|字段描述|
|:---|:---|:---|
|nickname|string|用户昵称|
|sign|string|用户个性签名|
|image|string|用户头像|

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('user',{
        nickname:DataTypes.STRING,
        image:DataTypes.STRING,
        sign:DataTypes.STRING,
    });
}
```

### 消息数据

模型名:`message`

|字段名|类型|字段描述|
|:---|:---|:---|
|content|string|消息内容|

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('message',{
        content:DataTypes.STRING,
    });
}
```

### 图片数据

模型名:`image`

|字段名|类型|字段描述|
|:---|:---|:---|
|url|string|图片地址|

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('image',{
        url:DataTypes.STRING,
    });
}
```

### 好友关系数据

模型名:`friendship`

|字段名|类型|字段描述|
|:---|:---|:---|
|info|string|关系描述|

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('friendship',{
        info:DataTypes.STRING,
    });
}
```

## 数据关系

* 账号数据与用户数据一对一关系
* 消息数据与图片数据一对多关系
* 用户数据与消息数据一对多关系
* 用户数据通过关系数据实现多对多关系

```
Account.belongsTo(User);

Message.hasMany(Image);
Message.belongsTo(User);

User.belongsToMany(User,{
  as:'followed',
  through:FriendShip,
});
```

