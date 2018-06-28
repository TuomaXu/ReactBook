# 综合实战2-备忘录App

### 产品原型

登录注册模块：

提供用户登录和注册功能

![98.png](../images/98.png)
![99.png](../images/99.png)


事项管理模块：

提供发布代办事项、展示代办事项、删除代办事项和标记代办事项已完成功能。

![100.png](../images/100.png)
![101.png](../images/101.png)
![102.png](../images/102.png)
![103.png](../images/103.png)



### API文档

**元数据**

账户对象

```
{
	id:0
	username:'',
	password:'',
	access_token:'',
}
```

Todo条目对象

```
{
	id:0
	title:'',
	content:'',
	isFinish:true OR false,
}
```

**错误代码**

|错误代码|内容|
|:---|:---|
|10001|系统错误|
|10002|用户名错误|
|10003|密码错误|
|10004|access_token无效|
|10005|TodoID无效|
|10006|参数无效|
|10007|用户名已存在|

错误返回值：

```
{
    success:false,
    errorCode:10001,
    errorMessage:'系统错误'
}
```



**接口服务**

请求基础地址

`http://localhost:5000`

注册

请求链接：`/api/register`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|username|用户名|
|password|密码|

```
{
    "success": true,
    "data": {
        "id": 2,
        "username": "tom1",
        "password": "1234",
        "access_token": "16e088f3d62c4c132e8728ef79eaf353",
        "updatedAt": "2018-06-06T08:05:13.664Z",
        "createdAt": "2018-06-06T08:05:13.664Z"
    }
}
```

登录

请求链接：`/api/login`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|username|账户名|
|password|密码|

```
{
    "success": true,
    "data": {
        "id": 2,
        "username": "tom1",
        "password": "1234",
        "access_token": "8e58260409dc169009b87800dfe5128f",
        "createdAt": "2018-06-06T08:05:13.000Z",
        "updatedAt": "2018-06-06T08:11:18.442Z"
    }
}

```

提交备忘录条目

请求链接：`/api/postTodoItem`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|title|标题|
|content|内容|

```
{
    "success": true,
    "data": 
}

```

修改备忘录条目：

请求链接：`/api/updateTodoItem`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|id|条目ID|
|title|标题（可选）|
|content|内容（可选）|

```
{
    "success": true,
    "data": 
}

```

切换标记备忘录条目完成状态

请求链接：`/api/finishTodoItem`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|id|条目ID|

```
{
    "success": true,
    "data": 
}

```

删除备忘录条目

请求链接：`/api/deleteTodoItems`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|id|条目ID|

```
{
    "success": true,
}
```

### 数据服务层构建

1，安装API文档中的接口路径，构建`URLConfig.js`文件：

```
const host = 'http://localhost:';
const port = 5000;

const loginURL = host+port+'/api/login';
const registerURL = host+port+'/api/register';
const postTodoItemURL = host+port+'/api/postTodoItem';
const updateTodoItemURL = host+port+'/api/updateTodoItem';
const deleteTodoItemURL = host+port+'/api/deleteTodoItem';
const getTodoItemsURL = host+port+'/api/getTodoItems';
const finishTodoItemURL = host+port+'/api/finishTodoItem';

export {
    loginURL,
    registerURL,
    postTodoItemURL,
    updateTodoItemURL,
    deleteTodoItemURL,
    getTodoItemsURL,
    finishTodoItemURL
}
```


2，构建`UserManager`数据服务对象，对用户相关操作进行封装。该数据对象提供以下功能支撑：

* 用户注册（异步）
* 用户登录（异步）
* 保持登录信息
* 查询当前登录状态
* 登出操作（清空登录信息）

构建`UserManager`单例：

```
import { loginURL,registerURL } from './URLConfig';

class UserManager {

}

export default new UserManager();
```

构建注册功能模块。该模块通过控制器传入的用户名和密码向API发起网络请求，将用户名和密码提交给服务器，服务器通过业务系统进行处理，返回注册结果。如注册成功，该模块内使用`localStorage`对`access_token`登录令牌进行本地保存，以维持登录状态。并返回登录结果。

```
async register(username,password){
    try {
        const user = {
            username,
            password
        }

        const res = await fetch(registerURL,{
            method:'POST',
            headers:{
                'Accept':'application/json',
                'Content-Type':'application/json'
            },
            body:JSON.stringify(user)
        });

        const result = await res.json();

        if(result.success === true){
            localStorage.access_token = result.data.access_token 
        }

        return result;

    } catch (error) {
        return {
            success:false,
            errorMessage:'网络错误'
        }
    }
}
```

构建登录功能模块。该模块通过控制器传入的用户名和密码向API发起网络请求，将用户名和密码提交给服务器，服务器通过业务系统进行处理，返回注册结果。如注册成功，该模块内使用`localStorage`对`access_token`登录令牌进行本地保存，以维持登录状态。并返回登录结果。

```
async login(username,password){
    try {
        const user = {
            username,
            password
        }

        const res = await fetch(loginURL,{
            method:'POST',
            headers:{
                'Accept':'application/json',
                'Content-Type':'application/json'
            },
            body:JSON.stringify(user)
        });

        const result = await res.json();

        if(result.success === true){
            localStorage.access_token = result.data.access_token 
        }

        return result;

    } catch (error) {
        return {
            success:false,
            errorMessage:'网络错误'
        }
    }
}
```

构建查询登录状态功能模块。通过判断`localStorage`中`access_token`的值的情况，如果其值为空或者`''`空字符串，就表示当前为登出状态。

```
isLogin(){
    if(localStorage.access_token === '' || !localStorage.access_token){
        return false;
    } else {
        return true;
    }
}
```

构建登出功能。在该功能中对`localStorage`中`access_token`的值进行清除操作。

```
logout(){
    localStorage.access_token = '';
}
```

完整参考代码：

```
import { loginURL,registerURL } from './URLConfig';

class UserManager {
    
    async login(username,password){
        try {
            const user = {
                username,
                password
            }

            const res = await fetch(loginURL,{
                method:'POST',
                headers:{
                    'Accept':'application/json',
                    'Content-Type':'application/json'
                },
                body:JSON.stringify(user)
            });

            const result = await res.json();

            if(result.success === true){
                localStorage.access_token = result.data.access_token 
            }

            return result;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }

    logout(){
        localStorage.access_token = '';
    }

    isLogin(){
        if(localStorage.access_token === '' || !localStorage.access_token){
            return false;
        } else {
            return true;
        }
    }

    async register(username,password){
        try {
            const user = {
                username,
                password
            }

            const res = await fetch(registerURL,{
                method:'POST',
                headers:{
                    'Accept':'application/json',
                    'Content-Type':'application/json'
                },
                body:JSON.stringify(user)
            });

            const result = await res.json();

            if(result.success === true){
                localStorage.access_token = result.data.access_token 
            }

            return result;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }

}

export default new UserManager();

```

3，构建待办事项数据管理对象，该对象提供以下功能支撑：

* 发布待办事项（异步）
* 删除待办事项（异步）
* 修改待办事项（异步）
* 维护待办事项完成状态（异步）
* 获取当前用户下的所有待办事项（异步）

构建`TodoManager`单例对象：

```
import {
    postTodoItemURL,
    updateTodoItemURL,
    deleteTodoItemURL,
    getTodoItemsURL,
    finishTodoItemURL
} from './URLConfig';

class TodoManager {

}

export default new TodoManager();
```

构建发布待办事项功能模块。

```
async postTodo(title,content){
    try {
        const todo = {
            access_token:localStorage.access_token,
            title,
            content
        }

        const res = await fetch(postTodoItemURL,{
            method:'POST',
            headers:{
                'Accept':'application/json',
                'Content-Type':'application/json'
            },
            body:JSON.stringify(todo)
        });

        const result = await res.json();

        return result;

    } catch (error) {
        return {
            success:false,
            errorMessage:'网络错误'
        }
    }
}
```

构建删除待办事项功能模块：

```
async deleteTodo(id){
    try {
        const todo = {
            access_token:localStorage.access_token,
            id,
        }

        const res = await fetch(deleteTodoItemURL,{
            method:'POST',
            headers:{
                'Accept':'application/json',
                'Content-Type':'application/json'
            },
            body:JSON.stringify(todo)
        });

        const result = await res.json();

        return result;

    } catch (error) {
        return {
            success:false,
            errorMessage:'网络错误'
        }
    }
}
```

构建修改待办事项的功能模块：

```
async updateTodo(id,title,content){
    try {
        const todo = {
            access_token:localStorage.access_token,
            id,
            title,
            content
        }

        const res = await fetch(updateTodoItemURL,{
            method:'POST',
            headers:{
                'Accept':'application/json',
                'Content-Type':'application/json'
            },
            body:JSON.stringify(todo)
        });

        const result = await res.json();

        return result;

    } catch (error) {
        return {
            success:false,
            errorMessage:'网络错误'
        }
    }
}
```

构建标记待办事项完成的功能模块：

```
async finishTodo(id){
    try {
        const todo = {
            access_token:localStorage.access_token,
            id,
        }

        const res = await fetch(finishTodoItemURL,{
            method:'POST',
            headers:{
                'Accept':'application/json',
                'Content-Type':'application/json'
            },
            body:JSON.stringify(todo)
        });

        const result = await res.json();

        return result;

    } catch (error) {
        return {
            success:false,
            errorMessage:'网络错误'
        }
    }
}
```

构建获取当前用户下所有代待办事项的功能模块：

```
async getTodos(){
    try {
        const todo = {
            access_token:localStorage.access_token,
        }

        const res = await fetch(getTodoItemsURL,{
            method:'POST',
            headers:{
                'Accept':'application/json',
                'Content-Type':'application/json'
            },
            body:JSON.stringify(todo)
        });

        const result = await res.json();

        return result;

    } catch (error) {
        return {
            success:false,
            errorMessage:'网络错误'
        }
    }
}
```

完整实现代码：

```
import {
    postTodoItemURL,
    updateTodoItemURL,
    deleteTodoItemURL,
    getTodoItemsURL,
    finishTodoItemURL
} from './URLConfig';

class TodoManager {

    async postTodo(title,content){
        try {
            const todo = {
                access_token:localStorage.access_token,
                title,
                content
            }

            const res = await fetch(postTodoItemURL,{
                method:'POST',
                headers:{
                    'Accept':'application/json',
                    'Content-Type':'application/json'
                },
                body:JSON.stringify(todo)
            });

            const result = await res.json();

            return result;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }

    async updateTodo(id,title,content){
        try {
            const todo = {
                access_token:localStorage.access_token,
                id,
                title,
                content
            }

            const res = await fetch(updateTodoItemURL,{
                method:'POST',
                headers:{
                    'Accept':'application/json',
                    'Content-Type':'application/json'
                },
                body:JSON.stringify(todo)
            });

            const result = await res.json();

            return result;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }

    async deleteTodo(id){
        try {
            const todo = {
                access_token:localStorage.access_token,
                id,
            }

            const res = await fetch(deleteTodoItemURL,{
                method:'POST',
                headers:{
                    'Accept':'application/json',
                    'Content-Type':'application/json'
                },
                body:JSON.stringify(todo)
            });

            const result = await res.json();

            return result;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }

    async finishTodo(id){
        try {
            const todo = {
                access_token:localStorage.access_token,
                id,
            }

            const res = await fetch(finishTodoItemURL,{
                method:'POST',
                headers:{
                    'Accept':'application/json',
                    'Content-Type':'application/json'
                },
                body:JSON.stringify(todo)
            });

            const result = await res.json();

            return result;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }

    async getTodos(){
        try {
            const todo = {
                access_token:localStorage.access_token,
            }

            const res = await fetch(getTodoItemsURL,{
                method:'POST',
                headers:{
                    'Accept':'application/json',
                    'Content-Type':'application/json'
                },
                body:JSON.stringify(todo)
            });

            const result = await res.json();

            return result;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }
}

export default new TodoManager();
```

### 构建Screen导航路由

根据产品原型构建Screen对象并在`App`组件中构建导航路径。

构造以下Screen对象：

* `LoginScreen`
* `RegisterScreen`
* `HomeScreen`
* `CreateTodoScreen`


在`App.js`中引入上述Screen对象，并配置路由。应用的根路径为'/'，在配置根路径对应的Screen对象时，需要对`exact`的值设置为`true`，以保证该路径不和其他保护'/'的路径发生混淆。

```
import React, { Component } from 'react';
import { Route } from "react-router-dom";

import LoginScreen from './Screen/LoginScreen';
import RegisterScreen from './Screen/RegisterScreen';
import HomeScreen from './Screen/HomeScreen';
import CreateTodoScreen from './Screen/CreateTodoScreen';

class App extends Component {
  render() {
    return (
      <div>
        <Route exact={true} path={'/'} component={LoginScreen} />
        <Route path={'/RegisterScreen'} component={RegisterScreen} />
        <Route path={'/HomeScreen'} component={HomeScreen} />
        <Route path={'/CreateTodoScreen'} component={CreateTodoScreen} />
      </div>
    );
  }
}

export default App;

```


### 构建LoginScreen

1，构建文件模板，引入相关依赖，配置State初值

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
} from 'antd-mobile';

import userManager from '../DataServer/UserManager';

export default class LoginScreen extends Component {

    constructor(props) {
      super(props)
    
      this.state = {
         username:'',
         password:''
      }
    }
    

  render() {
    return (
      <div>
        
      </div>
    )
  }
}
```

2，在`render()`函数中构建页面：

* NavBar
* 用户名输入框
* 密码输入框
* 登录按钮
* 注册按钮

```
render() {
    return (
      <div>
        <NavBar
            mode="dark"
        >
            登录
        </NavBar>
        <WhiteSpace/>
        <List>
            <InputItem
                type={'text'}
                placeholder={'请输入登录用户名'}
            >
                用户名
            </InputItem>
            <InputItem
                type={'password'}
                placeholder={'请输入登录密码'}
            >
                密码
            </InputItem>
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Button
                type={'primary'}
            >
                登录
            </Button>
            <WhiteSpace/>
            <Button
                type={'primary'}
            >
                注册
            </Button>
        </WingBlank>
      </div>
    )
  }
```

3，添加输入框受控组件机制：

```
<InputItem
    type={'text'}
    value={this.state.username}
    onChange={(username)=>{this.setState({username})}}
    placeholder={'请输入登录用户名'}
>
    用户名
</InputItem>
```

```
<InputItem
    type={'password'}
    value={this.state.password}
    onChange={(password)=>{this.setState({password})}}
    placeholder={'请输入登录密码'}
>
    密码
</InputItem>
```

4，添加页面响应事件

登录按钮事件，点击之后通过`userManager`对象进行登录操作，判断登录结果，成功跳转`HomeScree`失败提示用户原因。

```
onLoginClick = async ()=>{
    const reslut = await userManager.login(this.state.username,this.state.password);
    console.log(reslut);
    if(reslut.success === false){
        Toast.fail(reslut.errorMessage);
        return;
    }
    this.props.history.replace('/HomeScreen');
}
```

```
<Button
    type={'primary'}
    onClick={this.onLoginClick}
>
    登录
</Button>
```

注册按钮，点击跳转注册页面

```
<Button
    type={'primary'}
    onClick={()=>{
        this.props.history.push('/RegisterScreen')
    }}
>
    注册
</Button>
```

5，声明周期函数配置

当用户直接通过链接访问该页面时，如当时为已登录状态，自动跳转到Home主页：

```
componentDidMount(){
    if(userManager.isLogin() === true){
        this.props.history.replace('/HomeScreen');
    }
}
```


完整参考代码：

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
} from 'antd-mobile';

import userManager from '../DataServer/UserManager';

export default class LoginScreen extends Component {

    constructor(props) {
      super(props)
    
      this.state = {
         username:'',
         password:''
      }
    }
    

    componentDidMount(){
        if(userManager.isLogin() === true){
            this.props.history.replace('/HomeScreen');
        }
    }

    

    render() {
        return (
            <div>
            <NavBar
                mode="dark"
            >
                登录
            </NavBar>
            <WhiteSpace/>
            <List>
                <InputItem
                    type={'text'}
                    value={this.state.username}
                    onChange={(username)=>{this.setState({username})}}
                    placeholder={'请输入登录用户名'}
                >
                    用户名
                </InputItem>
                <InputItem
                    type={'password'}
                    value={this.state.password}
                    onChange={(password)=>{this.setState({password})}}
                    placeholder={'请输入登录密码'}
                >
                    密码
                </InputItem>
            </List>
            <WhiteSpace/>
            <WingBlank>
                <Button
                    type={'primary'}
                    onClick={this.onLoginClick}
                >
                    登录
                </Button>
                <WhiteSpace/>
                <Button
                    type={'primary'}
                    onClick={()=>{
                        this.props.history.push('/RegisterScreen')
                    }}
                >
                    注册
                </Button>
            </WingBlank>
            </div>
        )
    }

    onLoginClick = async ()=>{
        const reslut = await userManager.login(this.state.username,this.state.password);
        if(reslut.success === false){
            Toast.fail(reslut.errorMessage);
            return;
        }
        this.props.history.replace('/HomeScreen');
    }
}
```

### 构建RegisterScreen

1，构建文件模板，引入相关依赖，配置State初值

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
    Icon
} from 'antd-mobile';

import userManager from '../DataServer/UserManager';

export default class RegisterScreen extends Component {

    constructor(props) {
      super(props)
    
      this.state = {
         username:'',
         password:''
      }
    }

    render(){

    }
}
```

2，在`render()`函数中构建页面：

* NavBar
* 用户名输入框
* 密码输入框
* 注册按钮

```
render() {
    return (
      <div>
        <NavBar
            mode="dark"
            icon={<Icon type="left" />}
        >注册</NavBar>
        <WhiteSpace/>
        <List>
            <InputItem
                type={'text'}
                placeholder={'请输入注册用户名'}
            >
                用户名
            </InputItem>
            <InputItem
                type={'text'}
                placeholder={'请输入注册密码'}
            >
                密码
            </InputItem>
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Button
                type={'primary'}
            >
                提交注册
            </Button>
        </WingBlank>
      </div>
    )
  }
```

3，添加输入框受控组件机制

```
<InputItem
    type={'text'}
    value={this.state.username}
    onChange={(username)=>{this.setState({username})}}
    placeholder={'请输入注册用户名'}
>
    用户名
</InputItem>
```

```
<InputItem
    type={'text'}
    value={this.state.password}
    onChange={(password)=>{this.setState({password})}}
    placeholder={'请输入注册密码'}
>
    密码
</InputItem>
```

4，添加页面响应事件

点击注册按钮，通过数据服务对象进行注册，注册成功跳转Home，失败提示用户原因

```
  onRegister = async()=>{
    const reslut = await userManager.register(this.state.username,this.state.password);
    console.log(reslut);
    if(reslut.success === false){
        Toast.fail(reslut.errorMessage,1);
        return;
    }
    this.props.history.replace('/HomeScreen');
  }
```

5，声明周期函数配置

当用户直接通过链接访问该页面时，如当时为已登录状态，自动跳转到Home主页：

```
componentDidMount(){
    if(userManager.isLogin() === true){
        this.props.history.replace('/HomeScreen');
    }
}
```

完整参考代码：

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
    Icon
} from 'antd-mobile';

import userManager from '../DataServer/UserManager';

export default class RegisterScreen extends Component {

    componentDidMount(){
        if(userManager.isLogin() === true){
            this.props.history.replace('/HomeScreen');
        }
    }


    constructor(props) {
      super(props)
    
      this.state = {
         username:'',
         password:''
      }
    }
    

  render() {
    return (
      <div>
        <NavBar
            mode="dark"
            icon={<Icon type="left" />}
            onLeftClick={() => {this.props.history.goBack()}}
        >注册</NavBar>
        <WhiteSpace/>
        <List>
            <InputItem
                type={'text'}
                value={this.state.username}
                onChange={(username)=>{this.setState({username})}}
                placeholder={'请输入注册用户名'}
            >
                用户名
            </InputItem>
            <InputItem
                type={'text'}
                value={this.state.password}
                onChange={(password)=>{this.setState({password})}}
                placeholder={'请输入注册密码'}
            >
                密码
            </InputItem>
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Button
                type={'primary'}
                onClick={this.onRegister}
            >
                提交注册
            </Button>
        </WingBlank>
      </div>
    )
  }

  onRegister = async()=>{
    const reslut = await userManager.register(this.state.username,this.state.password);
    console.log(reslut);
    if(reslut.success === false){
        Toast.fail(reslut.errorMessage,1);
        return;
    }
    this.props.history.replace('/HomeScreen');
  }
}

```

### 构建HomeScreen

HomeScreen为App内容主页，在该页面展示当前登录用户的所有待办事项。通过该页面可以对代表实现进行标记已完成和删除操作。同时在该页面可以跳转到添加待办事项的页面。

1，构建文件模板，引入相关依赖，配置State初值

```
import React, { Component } from 'react'

import { 
    Toast,
    NavBar,
    ListView,
} from 'antd-mobile';

import todoManager from '../DataServer/TodoManager';
import userManager from '../DataServer/UserManager';

export default class HomeScreen extends Component {

    constructor(props) {
        super(props)
        const dataSource = new ListView.DataSource({
            rowHasChanged:(row1, row2) => row1 !== row2,
        })
        this.state = {
            dataSource,
        }
    }

    render(){

    }
}
```

2，在`render()`函数中构建页面元素：

* NavBar，并配置Header按钮的跳转事件
* ListView

```
render() {
    return (
      <div>
        <NavBar
            mode="dark"
            leftContent={[
                <span
                    key={1}
                    onClick={()=>{
                        this.props.history.replace('/');
                        userManager.logout();
                    }}
                >退出</span>
            ]}
            rightContent={[
                <span
                    key={2}
                    onClick={()=>{
                        this.props.history.push('/CreateTodoScreen');
                    }}
                >添加</span>
            ]}
        >备忘录</NavBar>
        <ListView
            useBodyScroll={true}
            dataSource={this.state.dataSource}
        />
      </div>
    )
  }
```

3，配置ListView的`renderRow`属性：

在此只需要将内容展示即可，后续将构建Item组件对内容展示样式进行优化。

```
renderRow = (todo)=>{
    return(
        <p>{`${todo.id}:${todo.title}`}</p>
    )
  }
```

```
<ListView
    useBodyScroll={true}
    dataSource={this.state.dataSource}
    renderRow={this.renderRow}
/>
```

4，声明周期函数配置

在页面加载完成之后，通过`userManager`对象判断登录状态，如是非登录状态则返回登录页，如是登录状态则请求页面数据。

```
async componentDidMount(){

    if(userManager.isLogin() === false){
        this.props.history.replace('/');
        return;
    }

    const result = await todoManager.getTodos();
    console.log(result)
    if(result.success === false){
        Toast.fail(result.errorMessage);
        //如access_token过期，也返回登录页面
        if(result.errorCode === 10004){
            userManager.logout();
            this.props.history.replace('/');
        }
        return;
    }

    this.setState((preState)=>{
        return{
            dataSource:preState.dataSource.cloneWithRows(result.data)
        }   
    })

}
```

5，构建HomeListItem组件

HomeListItem组件负责对待办事项条目进行展示，同时封装滑动删除和标记事件。

a，构建文件模板，引入相关依赖

```
import React, { Component } from 'react'

//时间日期处理组件
import moment from 'moment';

import './HomeListItem.css'

import { 
    WingBlank, 
    WhiteSpace ,
    Card,
    SwipeAction
} from 'antd-mobile';

export default class HomeListItem extends Component {
    render(){

    }
}

```

b，在`itemBody()`函数中构建页面元素：

```
itemBody = ()=>{
      return(
            <WingBlank>
                <WhiteSpace/>
                <Card>
                    <Card.Header
                        title={this.props.title}
                        extra={this.props.isFinish?'已完成':'未完成'}
                    />
                    <Card.Body>
                        <span>
                        {this.props.content}
                        </span>
                    </Card.Body>
                    <Card.Footer
                        content={moment(this.props.createdAt).format('YYYY-MM-DD HH:mm')}
                    />
                </Card>
                <WhiteSpace/>
            </WingBlank>
      )
  }
```

c，添加Item滑动事件：

```
render() {
    return (
        <SwipeAction
            autoClose={true}
            right={[
                {
                    text:this.props.isFinish?'标记未完成':'标记已完成',
                    style:{
                        backgroundColor:this.props.isFinish?'yellow':'green',
                    },
                },
                {
                    text:'删除',
                    style:{
                        backgroundColor:'red'
                    },
                },
            ]}

        >
            {this.itemBody()}
        </SwipeAction>
    )
  }
```

d，定义事件接口属性

* `toggleFinish`，切换待办事项完成状态事件
* `del`，删除该待办事项事件

在Item事件中触发上述事件，并传递事件ID：


```
render() {
    return (
        <SwipeAction
            autoClose={true}
            right={[
                {
                    text:this.props.isFinish?'标记未完成':'标记已完成',
                    style:{
                        backgroundColor:this.props.isFinish?'yellow':'green',
                    },
                    onPress:()=>{
                        if(this.props.toggleFinish){
                            this.props.toggleFinish(this.props.id);
                        }
                    }
                },
                {
                    text:'删除',
                    style:{
                        backgroundColor:'red'
                    },
                    onPress:()=>{
                        if(this.props.del){
                            this.props.del(this.props.id);
                        }
                    }
                },
            ]}

        >
            {this.itemBody()}
        </SwipeAction>
    )
  }
```

HomeListItem完整参考代码：

```
span{
    word-break:normal;
    width:auto; 
    display:block; 
    white-space:pre-wrap;
    word-wrap : break-word ;
    overflow: hidden ;
}  
```

```
import React, { Component } from 'react'

import moment from 'moment';

import './HomeListItem.css'

import { 
    WingBlank, 
    WhiteSpace ,
    Card,
    SwipeAction
} from 'antd-mobile';

export default class HomeListItem extends Component {
  render() {
    return (
        <SwipeAction
            autoClose={true}
            right={[
                {
                    text:this.props.isFinish?'标记未完成':'标记已完成',
                    style:{
                        backgroundColor:this.props.isFinish?'yellow':'green',
                    },
                    onPress:()=>{
                        if(this.props.toggleFinish){
                            this.props.toggleFinish(this.props.id);
                        }
                    }
                },
                {
                    text:'删除',
                    style:{
                        backgroundColor:'red'
                    },
                    onPress:()=>{
                        if(this.props.del){
                            this.props.del(this.props.id);
                        }
                    }
                },
            ]}

        >
            {this.itemBody()}
        </SwipeAction>
    )
  }

  itemBody = ()=>{
      return(
            <WingBlank>
                <WhiteSpace/>
                <Card>
                    <Card.Header
                        title={this.props.title}
                        extra={this.props.isFinish?'已完成':'未完成'}
                    />
                    <Card.Body>
                        <span>
                        {this.props.content}
                        </span>
                    </Card.Body>
                    <Card.Footer
                        content={moment(this.props.createdAt).format('YYYY-MM-DD HH:mm')}
                    />
                </Card>
                <WhiteSpace/>
            </WingBlank>
      )
  }
}

```

6，使用HomeListItem展示内容

重新改下`renderRow`函数：

```
renderRow = (todo)=>{
    return(
        <HomeListItem 
            {...todo} 
        />
    )
  }
```

7，响应待办事项事件

更改待办事项完成状态：

```
onToggleFinish = async (id)=>{
    Toast.loading('操作中',0);
    const result = await todoManager.finishTodo(id);
    Toast.hide();

    if(result.success === false){ 
        Toast.fail(result.errorMessage);
        if(result.errorCode === 10004){
            userManager.logout();
            this.props.history.replace('/');
        }
        return;
    }
    Toast.loading('刷新数据',0);
    const result1 = await todoManager.getTodos();
    Toast.hide();

    if(result1.success === false){
        Toast.fail(result1.errorMessage);
        if(result.errorCode === 10004){
            userManager.logout();
            this.props.history.replace('/');
        }
        return;
    }

    this.setState((preState)=>{
        return{
            dataSource:preState.dataSource.cloneWithRows(result1.data)
        }   
    })
}
```

删除待办事项事件：

```
onDel = async (id)=>{
    Toast.loading('操作中',0);
    const result = await todoManager.deleteTodo(id);
    Toast.hide();

    if(result.success === false){
        Toast.fail(result.errorMessage);
        if(result.errorCode === 10004){
            userManager.logout();
            this.props.history.replace('/');
        }
        return;
    }

    Toast.loading('刷新数据',0);
    const result1 = await todoManager.getTodos();
    Toast.hide();

    if(result1.success === false){
        Toast.fail(result1.errorMessage);
        if(result.errorCode === 10004){
            userManager.logout();
            this.props.history.replace('/');
        }
        return;
    }

    this.setState((preState)=>{
        return{
            dataSource:preState.dataSource.cloneWithRows(result1.data)
        }   
    })
}
```

配置事件响应函数：

```
<HomeListItem 
    {...todo} 
    toggleFinish={this.onToggleFinish}
    del={this.onDel}
/>
```

完整实现代码：

```
import React, { Component } from 'react'

import { 
    Toast,
    NavBar,
    ListView,
} from 'antd-mobile';

import todoManager from '../DataServer/TodoManager';
import userManager from '../DataServer/UserManager';

import HomeListItem from '../ViewComponent/HomeListItem';


export default class HomeScreen extends Component {

    async componentDidMount(){

        if(userManager.isLogin() === false){
            this.props.history.replace('/');
            return;
        }

        const result = await todoManager.getTodos();

        if(result.success === false){
            Toast.fail(result.errorMessage);
            if(result.errorCode === 10004){
                userManager.logout();
                this.props.history.replace('/');
            }
            return;
        }

        this.setState((preState)=>{
            return{
                dataSource:preState.dataSource.cloneWithRows(result.data)
            }   
        })

    }

    constructor(props) {
        super(props)

        const dataSource = new ListView.DataSource({
            rowHasChanged:(row1, row2) => row1 !== row2,
        })

        this.state = {
            dataSource,
        }
    }

    onToggleFinish = async (id)=>{
        Toast.loading('操作中',0);
        const result = await todoManager.finishTodo(id);
        Toast.hide();

        if(result.success === false){ 
            Toast.fail(result.errorMessage);
            if(result.errorCode === 10004){
                userManager.logout();
                this.props.history.replace('/');
            }
            return;
        }
        Toast.loading('刷新数据',0);
        const result1 = await todoManager.getTodos();
        Toast.hide();

        if(result1.success === false){
            Toast.fail(result1.errorMessage);
            if(result.errorCode === 10004){
                userManager.logout();
                this.props.history.replace('/');
            }
            return;
        }

        this.setState((preState)=>{
            return{
                dataSource:preState.dataSource.cloneWithRows(result1.data)
            }   
        })
    }

    onDel = async (id)=>{
        Toast.loading('操作中',0);
        const result = await todoManager.deleteTodo(id);
        Toast.hide();

        if(result.success === false){
            Toast.fail(result.errorMessage);
            if(result.errorCode === 10004){
                userManager.logout();
                this.props.history.replace('/');
            }
            return;
        }

        Toast.loading('刷新数据',0);
        const result1 = await todoManager.getTodos();
        Toast.hide();

        if(result1.success === false){
            Toast.fail(result1.errorMessage);
            if(result.errorCode === 10004){
                userManager.logout();
                this.props.history.replace('/');
            }
            return;
        }

        this.setState((preState)=>{
            return{
                dataSource:preState.dataSource.cloneWithRows(result1.data)
            }   
        })
    }
    
    
  render() {
    return (
      <div>
        <NavBar
            mode="dark"
            leftContent={[
                <span
                    key={1}
                    onClick={()=>{
                        this.props.history.replace('/');
                        userManager.logout();
                    }}
                >退出</span>
            ]}
            rightContent={[
                <span
                    key={2}
                    onClick={()=>{
                        this.props.history.push('/CreateTodoScreen');
                    }}
                >添加</span>
            ]}
        >备忘录</NavBar>
        <ListView
            useBodyScroll={true}
            dataSource={this.state.dataSource}
            renderRow={this.renderRow}
        />
      </div>
    )
  }

  renderRow = (todo)=>{
    return(
        <HomeListItem 
            {...todo} 
            toggleFinish={this.onToggleFinish}
            del={this.onDel}
        />
    )
  }
}
```


### 构建CreateTodoScreen

1，构建文件模板，引入相关依赖，配置State初值

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
    Icon,
    TextareaItem,
    Modal
} from 'antd-mobile';

import todoManager from '../DataServer/TodoManager';
import userManager from '../DataServer/UserManager';


export default class CreateTodoScreen extends Component {

    constructor(props) {
      super(props)
    
      this.state = {
         title:'',
         content:''
      }
    }

    render(){

    }
}
```

2，在`render()`函数中构建页面元素：

* NavBar，并配置Header按钮的调整事件
* 标题输入框
* 内容输入框
* 提交按钮

```
render() {
    return (
      <div>
        <NavBar
            mode="dark"
            icon={<Icon type="left" />}
            onLeftClick={() => {this.props.history.goBack()}}
        >添加事项</NavBar>
        <WhiteSpace/>
        <List>
            <InputItem
                type={'text'}
                placeholder={'请输入事项标题'}
            >
                标题
            </InputItem>
            <TextareaItem
                type={'text'}
                placeholder={'请输入内容'}
                autoHeight={true}
            />
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Button
                type={'primary'}
            >
                提交
            </Button>
        </WingBlank>
      </div>
    )
  }
```

3，添加输入框受控组件机制

```
<InputItem
    type={'text'}
    value={this.state.title}
    onChange={(title)=>{this.setState({title})}}
    placeholder={'请输入事项标题'}
>
    标题
</InputItem>
<TextareaItem
    type={'text'}
    value={this.state.content}
    onChange={(content)=>{this.setState({content})}}
    placeholder={'请输入内容'}
    autoHeight={true}
/>
```

4，添加页面响应事件

```
sendTodo = async ()=>{
    Toast.loading('内容上传中...',0);
    const result = await todoManager.postTodo(this.state.title,this.state.content);
    Toast.hide();
    if(result.success === false){
        Toast.fail(result.errorMessage);
        if(result.errorCode === 10004){
            userManager.logout();
            this.props.history.replace('/');
        }
        return;
    }
    Modal.alert('提交成功','点击确认键返回',[{
        text:'确认',
        onPress:()=>{this.props.history.goBack()}
    }])
  }
```

```
<Button
    type={'primary'}
    onClick={this.sendTodo}
>
    提交
</Button>
```

5，声明周期函数配置

在页面加载完成之后，判断登录状态，如为登录，跳转到登录页面

```
componentDidMount(){
    if(userManager.isLogin() === false){
        this.props.history.replace('/');
    }
}
```

完整实现代码：

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
    Icon,
    TextareaItem,
    Modal
} from 'antd-mobile';

import todoManager from '../DataServer/TodoManager';
import userManager from '../DataServer/UserManager';


export default class CreateTodoScreen extends Component {


    componentDidMount(){
        if(userManager.isLogin() === false){
            this.props.history.replace('/');
        }
    }

    constructor(props) {
      super(props)
    
      this.state = {
         title:'',
         content:''
      }
    }
    
  render() {
    return (
      <div>
        <NavBar
            mode="dark"
            icon={<Icon type="left" />}
            onLeftClick={() => {this.props.history.goBack()}}
        >添加事项</NavBar>
        <WhiteSpace/>
        <List>
            <InputItem
                type={'text'}
                value={this.state.title}
                onChange={(title)=>{this.setState({title})}}
                placeholder={'请输入事项标题'}
            >
                标题
            </InputItem>
            <TextareaItem
                type={'text'}
                value={this.state.content}
                onChange={(content)=>{this.setState({content})}}
                placeholder={'请输入内容'}
                autoHeight={true}
            />
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Button
                type={'primary'}
                onClick={this.sendTodo}
            >
                提交
            </Button>
        </WingBlank>
      </div>
    )
  }

  sendTodo = async ()=>{
    Toast.loading('内容上传中...',0);
    const result = await todoManager.postTodo(this.state.title,this.state.content);
    Toast.hide();
    if(result.success === false){
        Toast.fail(result.errorMessage);
        if(result.errorCode === 10004){
            userManager.logout();
            this.props.history.replace('/');
        }
        return;
    }
    Modal.alert('提交成功','点击确认键返回',[{
        text:'确认',
        onPress:()=>{this.props.history.goBack()}
    }])
  }
}

```