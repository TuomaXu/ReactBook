# React网络请求

WebApp为前后端分类设计，使用React框架构建一个前端App，但App所需要的数据需要通过后端API提供。ReactApp中使用`Fetch`在前端App中对后台API发起网络请求，获取所需要的数据。

在进行进行网络请求之前，首先需求构建一个后端API接口服务。这里我们不详细展开讲解后端API构造过程，但给出案例所需的API接口文档。安装文档的要求，便可以成功通过`fetch`请求到API提供的数据。


### API文档

后端API通常使用文档进行描述，供前端开发者使用。文档中通常包含以下信息：

* 元数据结构
* 错误编码表
* 功能描述
* 请求地址
* 请求类型
* 请求参数表
* 返回值实例


本文API文档如下：



**元数据结构**

```
{
    id:1,
    title:'消息标题',
    content:'消息内容'

}
```

**错误编码表**

|错误编码|错误描述|
|:---|:---|
|10001|服务器错误|
|10002|参数错误|
|10003|ID错误|

错误返回值样例：

```
{
    success:false,
    errorCode:10001,
    errorMessage:'服务器错误'
}
```


**获取Message列表**

获取所有的消息数据。

请求地址：`http://localhost:5000/api/allMessages`

请求类型：`GET`

请求参数：无

返回值：

```
{
    "success": true,
    "data": [
        {
            "id": 1,
            "title": "xxx",
            "content": "yyy",
            "createdAt": "2018-05-30T03:13:27.000Z",
            "updatedAt": "2018-05-30T03:13:27.000Z"
        },
        {
            "id": 2,
            "title": "xxx111",
            "content": "yyy222",
            "createdAt": "2018-05-30T03:13:40.000Z",
            "updatedAt": "2018-05-30T03:13:40.000Z"
        }
    ]
}
```


**获取指定Message数据**

根据消息ID获取指定Message数据。

请求地址：`http://localhost:5000/api/getMessage`

请求类型：`GET`

请求参数：

|参数名|参数描述|
|:---|:---|
|id|获取信息的id|

返回值：

```
{
    "success": true,
    "data": {
        "id": 1,
        "title": "xxx",
        "content": "yyy",
        "createdAt": "2018-05-30T03:13:27.000Z",
        "updatedAt": "2018-05-30T03:13:27.000Z"
    }
}
```


**提交Message数据**

提交本地Message数据。

请求地址：`http://localhost:5000/api/postMessage`

请求类型：`POST`

请求参数：

|参数名|参数描述|
|:---|:---|
|title|信息的title|
|content|信息的content|

返回值：

```
{
    "success": true,
    "data": {
        "id": 2,
        "title": "xxx111",
        "content": "yyy222",
        "updatedAt": "2018-05-30T03:13:40.902Z",
        "createdAt": "2018-05-30T03:13:40.902Z"
    }
}
```

**提交文件**

提交本地文件到服务器存储，返回服务端文件URL。

请求地址：`http://localhost:5000/api/postFile`

请求类型：`POST`

请求参数：

|参数名|参数描述|
|:---|:---|
|file|提交文件|

返回值：

```
{
    "success": true,
    "data": "fb16b39e42a689fbbb9b73a5596d5395.png"
}
```

### Fetch API

Fetch API 提供了一个 JavaScript接口，用于访问和操纵HTTP管道的部分，例如请求和响应。它还提供了一个全局 `fetch()`方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。

这种功能以前是使用  XMLHttpRequest实现的。Fetch提供了一个更好的替代方法，可以很容易地被其他技术使用，例如 Service Workers。Fetch还提供了单个逻辑位置来定义其他HTTP相关概念，例如CORS和HTTP的扩展。

请注意，`fetch`规范与`jQuery.ajax()`主要有两种方式的不同，牢记：

* 当接收到一个代表错误的 HTTP 状态码时，从 `fetch()`返回的 Promise 不会被标记为 reject， 即使该 HTTP 响应的状态码是 404 或 500。相反，它会将 Promise 状态标记为 resolve （但是会将 resolve 的返回值的 ok 属性设置为 false ），仅当网络故障时或请求被阻止时，才会标记为 reject。
* 默认情况下，fetch 不会从服务端发送或接收任何 cookies, 如果站点依赖于用户 session，则会导致未经认证的请求（要发送 cookies，必须设置 credentials 选项）。

`fetch()`函数有两个参数：

* `url`请求地址（必须）
* `options`请求配置项（可选）

请求配置项常用配置如下内容：

* `headers`请求头对象
* `method`请求方法
* `body`请求体

该函数为Promise对象封装的异步函数，使用`await`修饰后，返回`response`对象。通过调用该对象自带的解析方法，可以同该对象中获取响应格式的内容。

解析函数同样为Promise封装的异步操作，常用解析函数为以下三个：

* `json()`，解析`response`对象中的以JSON编码的数据
* `text()`，解析`response`对象中的以string编码的数据
* `blob()`，解析`response`对象中的以二进制编码的数据


### 无参数GET请求

我们使用获取Message列表API进行演示讲解如何使用`fetch`进行一个无参数的GET请求操作。

```
import React, { Component } from 'react';


export default class App extends Component {

  fetchGET1 = async ()=>{
    const response = await fetch('http://localhost:5000/api/allMessages');
    const result = await response.json();
    console.log(result);
  }

  render() {
    return (
        <div>
          <button
            onClick={this.fetchGET1}
          >GET测试</button>
        </div>
    );
  }
}
```

点击按钮之后的输出结果为:

```
{
    "success": true,
    "data": [
        {
            "id": 1,
            "title": "放假通知",
            "content": "各位老师：今天下午开始放假3天",
            "createdAt": "2018-05-30T07:18:13.000Z",
            "updatedAt": "2018-05-30T07:18:13.000Z"
        },
        {
            "id": 2,
            "title": "工资发放通知",
            "content": "本月15号发放工资，请各位同事及时查收",
            "createdAt": "2018-05-30T07:18:58.000Z",
            "updatedAt": "2018-05-30T07:18:58.000Z"
        }
    ]
}
```

### 带参数GET请求

在GET请求过程中，如果有参数需要携带，只需要将参数和参数值以下面的方式拼接到`url`后面即可。

```
http://localhost:5000/api/getMessage?id=1
```
如果有多个参数，参数直接用`&`分隔：

```
http://localhost:5000/api/getMessage?a=1&b=2&c=3
```

我们构建一个输入框，在输入框中输入ID，然后点击按钮进行请求：

```
import React, { Component } from 'react';

export default class App extends Component {

  constructor(props) {
    super(props)
  
    this.state = {
       id:''
    }
  }
  

  fetchGET2 = async ()=>{
    const url = `http://localhost:5000/api/getMessage?id=${this.state.id}`
    const response = await fetch(url);
    const result = await response.json();
    console.log(result);
  }

  render() {
    return (
        <div>
          <input
            type={'text'}
            value={this.state.id}
            onChange={(e)=>{this.setState({id:e.target.value})}}
          />
          <br/>
          <button
            onClick={this.fetchGET2}
          >GET参数测试</button>
        </div>
    );
  }
}
```
当在页面输入`1`之后点击按钮，输出结果为：

```
{
    "success": true,
    "data": {
        "id": 1,
        "title": "放假通知",
        "content": "各位老师：今天下午开始放假3天",
        "createdAt": "2018-05-30T07:18:13.000Z",
        "updatedAt": "2018-05-30T07:18:13.000Z"
    }
}
``` 

### 字段POST请求

在使用`fethc()`进行POST请求操作是，需要配置请求`options`中三个字段：

```
{
    //显示设计请求方法为POST，如不显示设置，默认为GET
    method:'POST',
    //在header中添加body格式说明
    headers:{
        'Content-Type':'application/json'
    },
    //需要将参数以键值对方式存入对象中，并将对象进行JSON之后放入body内
    body:JSON.stringify(参数对象)
}
```

本案例中，构造两个输入框和一个按钮，在输入框中分别输入消息的标题和内容，点击按钮提交消息到服务器。实现代码为：

```
import React, { Component } from 'react';

export default class App extends Component {

  constructor(props) {
    super(props)
  
    this.state = {
       title:'',
       content:''
    }
  }
  

  fetchPOST1 = async ()=>{

    const message = {
      title:this.state.title,
      content:this.state.content
    }

    const response = await fetch('http://localhost:5000/api/postMessage',{
      method:'POST',
      headers:{
        'Content-Type':'application/json'
      },
      body:JSON.stringify(message)
    });
    const result = await response.json();
    console.log(result);
  }

  render() {
    return (
        <div>
          <input
            type={'text'}
            value={this.state.title}
            onChange={(e)=>{this.setState({title:e.target.value})}}
          />
          <br/>
          <input
            type={'text'}
            value={this.state.content}
            onChange={(e)=>{this.setState({content:e.target.value})}}
          />
          <br/>
          <button
            onClick={this.fetchPOST1}
          >POST测试</button>
        </div>
    );
  }
}
```

运行后输出结果为：

```
{
    "success": true,
    "data": {
        "id": 2,
        "title": "工资发放通知",
        "content": "本月15号发放工资，请各位同事及时查收",
        "updatedAt": "2018-05-30T07:18:58.086Z",
        "createdAt": "2018-05-30T07:18:58.086Z"
    }
}
```

### 文件POST请求

在提交文件时，例如上传头像等操作过程中，需要使用POST对文件进行提交。

我们使用一个案例来演示和讲解操作过程:

```
import React, { Component } from 'react';

export default class App extends Component {


  fetchPOST2 = async ()=>{
    //获取文件对象
    const file = this.fileInput.files[0];
    //构建FormData对象，手动配置一个表单
    const formData = new FormData();
    //添加文件对象到表单
    formData.append('file',file);
    //使用formData对象作为body数据
    const response =  await fetch('http://localhost:5000/api/postFile',{
      method:"POST",
      body:formData
    }) 
    //对response进行JSON解析
    const result = await response.json();
    //打印结果
    console.log(result);
  }

  render() {
    return (
        <div>
          <input 
            type={'file'} 
            ref={input=>this.fileInput = input}
          />
          <br/>
          <button
            onClick={this.fetchPOST2}
          >POST文件测试</button>
        </div>
    );
  }
}
```

运行结果为：

```
{
    "success": true,
    "data": "5d41fb38beef437512c8187cd364c255.png"
}
```