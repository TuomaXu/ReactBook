# 多页面导航

一个WebApp是由多个功能页面组成。例如，登录页面，注册页面，首页等。

我们需要根据用户的操作对这些页面的展示进行控制，因移动WebApp的屏幕限制，同一时间只能在屏幕上出现一个功能页面。

在React开发中，通常有两种页面的管理方法：

* StackRouter 堆栈导航
* Tabbar 标签栏导航

本章节主要通过三个案例对多页面导航及其基础知识进行综合运用。

### 会场登记管理系统

通过使用WebApp对参会人员进行登记和签到并记录基础数据，在需要是可以通过查询模块对数据进行多维度统计查询。

### 备忘录

该App提供备忘事件添加、删除标记已完成和修改功能。通过注册一个账号，可以在该账号下对备忘录事件进行操作

### 留言板

该App为社交类App，提供注册登录、发留言、评论留言和浏览留言功能。用户注册之后，可以发布属于自己的留言，其他用户可以对其留言进行评论。