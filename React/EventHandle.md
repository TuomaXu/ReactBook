# React事件处理

React 元素的事件处理和 DOM元素的很相似。但是有一点语法上的不同:

* React事件绑定属性的命名采用驼峰式写法，而不是小写。
* 如果采用 JSX 的语法你需要传入一个函数作为事件处理函数，而不是一个字符串(DOM元素的写法)

例如，传统的 HTML：

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>React App</title>
    <script>
        function send() {
            console.log('发送消息')
        }
    </script>
  </head>
  <body>
    <button onclick="send()">
        发送
    </button>
  </body>
</html>
```

在传统的HTML，需要首先在`<script>`标签中通过JavaScript声明一个函数，然后在HTML标签中为`onclick`属性赋值，该属性值类型为字符串类型，内容为要执行的函数调用。

React中稍稍有点不同。React中没有DOM的概念，所有元素都是通过组件构造处理，所以在React对元素的事件响应在相应的类组件中进行：


```
import React, { Component } from 'react'

export default class App extends Component {
  render() {
    return (
      <div>
        <button
            onClick={this.send}
        >
            {'发送'}
        </button>
      </div>
    )
  }

  //定义一个方法用来响应元素事件
  send(){
      console.log('发送信息');
  }
}
```
在React元素中通过配置`onClick`事件属性进行事件响应，事件属性值为一个函数对象，在该事件被触发时，`onClick`中的函数对象值中的函数会被调用。

```
<button
    onClick={this.send}
>
    {'发送'}
</button>
```

### 处理事件绑定this

在JSX回调函数中的`this`是一个比较特殊的地方，类的方法默认是不会绑定`this`的。如果你忘记绑定`this.send` 并把它传入`onClick`, 当你调用这个函数的时候 `this` 的值会是 `undefined`。

手动绑定`this`有两种方法：

* 使用`bind`手动绑定`this`
* 使用箭头函数自动绑定(推荐使用)

```
import React, { Component } from 'react'

export default class App extends Component {
  render() {
    return (
      <div>
        <button
            onClick={this.send1.bind()}
        >
            {'发送1'}
        </button>
        <button
            onClick={this.send2}
        >
            {'发送2'}
        </button>
      </div>
    )
  }

  send1(){
      console.log('发送信息1');
  }

  send2 = ()=>{
    console.log('发送信息2');
  }
  
}

```







