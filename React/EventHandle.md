# React事件处理

React元素的事件处理和 DOM元素的很相似。但是有一点语法上的不同:

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

  //推荐用法
  send2 = ()=>{
    console.log('发送信息2');
  }
  
}

```

在React中响应元素事件时，React框架会在事件中携带一个合成事件对象`event`。使用该对象可以对React元素进行更加准确的操作。


例如，传统的 HTML 中阻止链接默认打开一个新页面，你可以这样写：

```
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>

```


在 React 中必须明确的使用 preventDefault。不能用返回`false`

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

>在编写代码中，通常使用`e`来代替`event`，


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

### 事件Target

在元素事件携带的`event`事件对象中包含一个`target`属性，通过该属性可以访问事件元素对象，通过该对象可以访问其属性值。

例如：

```
import React, { Component } from 'react'

export default class App extends Component {
  render() {
    return (
      <div>
        <button 
            id={'1'}
            onClick={this.onBtn}
        >按钮</button>
      </div>
    )
  }

  onBtn = (e)=>{
    const btn = e.target;
    console.log(btn.id);
  }
}
```

通过此特性，可以为多个按钮配置同一个事件响应函数，以此提升开发效率和代码的质量。

例如构造三个按钮，为这三个按钮配置同一个事件响应函数，在函数中通过`e.target.id`来区分该事件是由哪个按钮触发，并进行指导处理。

* 点击按钮A，输出1
* 点击按钮B，输出2
* 点击按钮C，输出3


```
import React, { Component } from 'react'

export default class App extends Component {
  render() {
    return (
      <div>
        <button 
            id={'a'}
            onClick={this.onBtn}
        >按钮A</button>
        <button 
            id={'b'}
            onClick={this.onBtn}
        >按钮B</button>
        <button 
            id={'c'}
            onClick={this.onBtn}
        >按钮C</button>
      </div>
    )
  }

  onBtn = (e)=>{
    const btn = e.target;
    switch (btn.id) {
        case 'a':{
            console.log(1);
        }break;
        case 'b':{
            console.log(2);
        }break;
        case 'c':{
            console.log(3);
        }break;
    
        default:
            break;
    }
  }
}
```


### 受控组件事件处理

HTML表单元素与React中的其他DOM元素有所不同,因为表单元素生来就保留一些内部状态。例如，下面这个表单只接受一个唯一的name。

```
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```


但在WebApp中，我们都会构造一个处理提交表单并可访问用户输入表单数据的函数。实现这一点的标准方法是使用一种称为“受控组件”的技术。

在HTML当中，像`<input>`,`<textarea>`, 和 `<select>`这类表单元素会维持自身状态，并根据用户输入进行更新。但在React中，可变的状态通常保存在组件的状态属性中，并且只能用 setState() 方法进行更新。

我们通过使react变成一种单一数据源的状态来结合二者。React负责渲染表单的组件仍然控制用户后续输入时所发生的变化。相应的，其值由React控制的输入表单元素称为“受控组件”。

实现受控组件的模式需要三个步骤：

* 初始化受控组件初始状态
* 为受控组件的`value`指定数据源
* 响应受控组件的`onChange`事件，并在事件中对受控组件的数据源进行更新


```
import React, { Component } from 'react'

export default class App extends Component {


    constructor(props) {
      super(props)
    
      this.state = {
         name:''
      }
    }
    

    render() {
        return (
            <div>
                <label>
                    <input 
                        type="text" 
                        value={this.state.value} 
                        onChange={(e)=>{this.setState({name:e.target.value})}} 
                    />
                </label>
            </div> 
        )
    }

  
}

```

上述代码中，首先在`constructor`方法中对受控组件的数据进行初始化

```
constructor(props) {
      super(props)
    
      this.state = {
         name:''
      }
}
```
然后为受控组件指定数据源：

```
<input 
    type="text" 
    value={this.state.value} 
/>
```

最后为受控组件指定`onChange`事件响应函数：

```
<input 
    type="text" 
    value={this.state.value} 
    onChange={(e)=>{this.setState({name:e.target.value})}} 
/>
```

`onChange`响应函数中，通过对该事件携带的参数进行解析，获取用户输入内容，并使用`this.setState()`函数对数据源进行修改。在数据源被修改之后，`render()`方法会被重新触发，新的`render()`方法中会使用被修改后的数据源数据更新`input`展示内容，从而在用户的角度看到的就是`input`标签对输入的实时响应。

但在React运行过程中，数据流是按照如下方向进行传递：


1. 用户输入
2. `onChange`事件被触发
3. 通过事件携带的参数获取用户输入的内容
4. 通过`this.setState()`将用户输入的内容放入`this.state`对象中
5. `render()`方法被触发，`this.state`中的数据写入`input`的`value`中
6. React框架将React元素的更新内容写入DOM中
7. 用户在页面上看到输入框内容的更新

当需要获取用户输入内容时，只需要访问`this.state`对象即可。

例如，我们添加一个按钮，当点击此按钮时打印输出用户的输入内容：

```
import React, { Component } from 'react'

export default class App extends Component {


    constructor(props) {
      super(props)
    
      this.state = {
         name:''
      }
    }
    

    render() {
        return (
            <div>
                <label>
                    Name:
                    <input 
                        type="text" 
                        value={this.state.value} 
                        onChange={(e)=>{this.setState({name:e.target.value})}} 
                    />
                </label>
                <br/>
                <button
                    onClick={()=>{
                        const name = this.state.name;
                        console.log(name);
                    }}
                >打印</button>
            </div> 
        )
    }
}
```


### 文件选择事件处理



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




