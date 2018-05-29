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

### React元素事件参数

在React中响应元素事件时，React框架会在事件中携带一个合成事件对象`event`。使用该对象可以对React元素进行更加准确的操作。

**在 React 中必须明确的使用 preventDefault。**

例如，传统的 HTML 中阻止链接默认打开一个新页面，你可以这样写：

```
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>

```


在 React，应该这样来写：

```

import React, { Component } from 'react'

export default class App extends Component {
  render() {
    return (
      <div>
        <a href="#" onClick={this.handleClick}>
            Click me
        </a>
      </div>
    )
  }

  handleClick = (event)=>{
    event.preventDefault();
    console.log('The link was clicked.');
  }
}

```

>在编写代码中，通常使用`e`来代替`event`


React标准化了事件，使其在不同的浏览器中拥有一致的属性。

您的事件处理函数将会接收`SyntheticEvent`的实例，一个基于浏览器原生事件的跨浏览器实现。它拥有和浏览器原生事件一样的接口，包括`stopPropagation()`和`preventDefault()`，除了那些所有浏览器功能一样的事件。

如果由于某些原因，你得使用一些底层的浏览器事件，只需用`nativeEvent`的属性就能找到。每个`SyntheicEvent`对象都有如下属性：

```
boolean bubbles
boolean cancelable
DOMEventTarget currentTarget
boolean defaultPrevented
number eventPhase
boolean isTrusted
DOMEvent nativeEvent
void preventDefault()
boolean isDefaultPrevented()
void stopPropagation()
boolean isPropagationStopped()
DOMEventTarget target
number timeStamp
string type
```

`SyntheticEvent`是共享的。那就意味着在调用事件回调之后，SyntheticEvent对象将会被重用，并且所有属性会被置空。这是出于性能因素考虑的。 因此，您无法以**异步方式**访问事件。

```
function onClick(event) {
  console.log(event); // => nullified object.
  console.log(event.type); // => "click"
  const eventType = event.type; // => "click"

  setTimeout(function() {
    console.log(event.type); // => null
    console.log(eventType); // => "click"
  }, 0);

  // Won't work. this.state.clickEvent will only contain null values.
  this.setState({clickEvent: event});

  // You can still export event properties.
  this.setState({eventType: event.type});
}
```

下面的事件处理函数由冒泡阶段的事件触发。在事件名后面加Capture就能在事件捕获阶段注册事件处理函数。举个例子，你可以使用`onClickCapture`代替`onClick`在事件捕获阶段来处理点击事件。


* `Clipboard Events`
* `Keyboard Events`
* `Focus Events`
* `Form Events`
* `Touch Events`



### 事件参考

`Clipboard Events`

事件名:
```
onCopy 
onCut 
onPaste
```

属性:

```
DOMDataTransfer 
clipboardData
```



`Composition Events`

事件名:

```
onCompositionEnd 
onCompositionStart 
onCompositionUpdate
```

属性:

```
string 
data
```

`Keyboard Events`

事件名:
```
onKeyDown 
onKeyPress 
onKeyUp
```

属性:

```
boolean altKey
number charCode
boolean ctrlKey
boolean getModifierState(key)
string key
number keyCode
string locale
number location
boolean metaKey
boolean repeat
boolean shiftKey
number which
```

`Focus Events`

事件名:

```
onFocus 
onBlur
```

>这些焦点事件适用于React DOM中的所有元素，而不仅仅是表单元素。

属性:

```
DOMEventTarget 
relatedTarget
```

`Form Events`

事件名:
```
onChange 
onInput 
onSubmit
```

`Touch Events`

事件名:

```
onTouchCancel 
onTouchEnd 
onTouchMove 
onTouchStart
```

属性:
```
boolean altKey
DOMTouchList changedTouches
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
boolean shiftKey
DOMTouchList targetTouches
DOMTouchList touches
```




