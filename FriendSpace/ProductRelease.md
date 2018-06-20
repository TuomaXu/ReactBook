# 产品发布与上线

产品上线需要部署在Linux服务器上，服务器需要安装一下组件：

* MySQL
* Nodejs

通过npm全局安装`babel-cli`。

### 修改`listen`配置

部署服务器时，需要手动添加服务器IP，在`listen`函数中。

```
//开启监听服务
const server = app.listen(60004,'60.205.141.116',()=>{
	console.log('开启成功，访问http://60.205.141.116:60004');
});
```


### 后台开启服务

通过指令对API服务进行后台开启：

```
nohup  babel-node index.js  2>/tmp/1.log   &
```

### 对WebApp工程进行编译打包

通过运行`bulid`脚本，对App工程进行编译打包：

在工程根目录下运行

```
npm run build
```

编译结束之后，在工程根目录下生产一个`build`文件夹，该文件夹中内容即为编译输出结果。

### 配置虚拟主机

工程编译结果需要放在Web更目录下才能正常访问，所以需要配置虚拟主机提供根目录。

配置`httpd.conf`（位置在`/etc/httpd/conf/httpd.conf`）文件

```
Listen 60083
NameVirtualHost 60.205.141.116:60083

<VirtualHost 60.205.141.116:60083>
     DocumentRoot /var/www/FriendShapeApp/
    ServerName 60.205.141.116
    ErrorLog  logs/dummy-host.example.com1-error_log
    CustomLog  logs/dummy-host.example.com1-access_log common
</VirtualHost>
```

保存退出。`/etc/init.d/httpd restart`
