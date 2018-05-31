# antd基础组件

### Button 按钮

按钮作为WebApp一个基础组件，其展现形式一般如下：

![12.png](../images/12.png)



常用API：

|属性|说明|类型|默认值|
|:---|:---|:---|
|type|按钮默认样式类型|string:`primary`、`ghost`、`warning`|空|
|size|按钮默认样式大小|string:`large`、`small`|`large`|
|onClick|点击按钮的点击回调函数|(e: Object): void|无|
|loading|设置按钮载入状态|boolean|false|
|disabled|设置按钮禁用状态|boolean|false|
|style|自定义样式|Object|无|
|activeStyle|点击反馈的自定义样式|Object|无|


在默认情况下，按钮的宽度和屏幕宽度相同。高度为47px。

Button组件自带四种样式，通过配置`type`属性控制：

* `default`
* `primary`
* `ghost`
* `warning`

```
import React, { Component } from 'react';

import { Button } from 'antd-mobile';

export default class App extends Component {

  render() {
    return (
        <div>
          <h1>按钮type演示</h1>
          <br/>
          <Button>default</Button>
          <br/>
          <Button type="primary">primary</Button>
          <br/>
          <Button type="warning">warning</Button>
          <br/>
          <Button type="ghost">warning</Button>
        </div>
    );
  }
}
```

运行结果：

![13.png](../images/13.png)

Button自带两种尺寸样式，通过配置`size`属性控制：

* `large`。默认尺寸（推荐使用）
* `small`。小尺寸（特点场景下使用）

```
import React, { Component } from 'react';

import { Button } from 'antd-mobile';

export default class App extends Component {

  render() {
    return (
        <div>
          <h1>按钮Size演示</h1>
          <br/>
          <Button type="primary" size='small'>primary-small</Button>
          <br/>
          <Button type="primary">primary</Button>
        </div>
    );
  }
}
```

运行结果：

![14.png](../images/14.png)


通过组件的`onClick`属性，可以响应Button的点击事件：

```
import React, { Component } from 'react';

import { Button } from 'antd-mobile';

export default class App extends Component {

  onButtonClick = ()=>{
    console.log('按钮被点击');
  }

  render() {
    return (
        <div>
          <h1>按钮点击事件演示</h1>
          <br/>
          <Button 
            type="primary"
            onClick={this.onButtonClick}
          >Click</Button>
        </div>
    );
  }
}
```
运行结果：

![15.png](../images/15.png)

点击按钮效果：

```
按钮被点击
```

通过配置组件的`loading`和`disabled`属性，可以在按钮在异步操作时暂时暂停交互：

```
import React, { Component } from 'react';

import { Button } from 'antd-mobile';

export default class App extends Component {

  constructor(props) {
    super(props)
  
    this.state = {
       isLoading:false,
       isDisabled:false
    }
  }
  

  onButtonClick = (e)=>{
    console.log('按钮被点击');
    this.setState({
      isLoading:true,
      isDisabled:true
    });
    setTimeout(()=>{
      this.setState({
        isLoading:false,
        isDisabled:false
      });
    },3000)
  }

  render() {
    return (
        <div>
          <h1>按钮加载演示</h1>
          <br/>
          <Button 
            type="primary"
            onClick={this.onButtonClick}
            loading={this.state.isLoading}
            disabled={this.state.isDisabled}
          >Click</Button>
        </div>
    );
  }
}
```

运行效果：

![16.png](../images/16.png)

点击之后效果：

![17.png](../images/17.png)

### 自定义样式

如Button组件默认样式不能满足产品要求，可以通过配置`style`和`activeStyle`属性来自定义样式：

```
import React, { Component } from 'react';

import { Button } from 'antd-mobile';

export default class App extends Component {

  render() {
    return (
        <div>
          <h1>按钮加载演示</h1>
          <br/>
          <Button 
            type="primary"
            onClick={this.onButtonClick}
            style={{
              backgroundColor:'red',
            }}
            activeStyle={{
              backgroundColor:'green',
            }}
          >Click</Button>
        </div>
    );
  }
}
```

运行效果：

![18.png](../images/18.png)

点击后效果：

![19.png](../images/19.png)


### Switch 滑动开关

Switch组件提供一个移动开关。

![20.png](../images/20.png)

>该组件的尺寸不推荐更改。

常用API：

|属性|说明|类型|默认值|
|:---|:---|:---|
|checked|开关选中状态|Boolean|false|
|onChange|change 事件触发的回调函数|(checked: bool): void|无|
|color|开关打开后的颜色|String|#4dd865|


该组件使用方式推荐为受控组件模式

```
import React, { Component } from 'react';

import { Button,Switch } from 'antd-mobile';

export default class App extends Component {

  constructor(props) {
    super(props)
  
    this.state = {
      checkedA:false,
      checkedB:false
    }
  }
  

  render() {
    return (
        <div>
          <h1>开关演示</h1>
          <br/>
          <Switch 
            checked={this.state.checkedA}
            onChange={(checkedA)=>{this.setState({checkedA})}}
          />
          <Switch 
            checked={this.state.checkedB}
            onChange={(checkedB)=>{this.setState({checkedB})}}
            color={'red'}
          />
        </div>
    );
  }
}
```

运行结果为：

![21.png](../images/21.png)


### Stepper 步进器

![22.png](../images/22.png)

常用API：

|属性|说明|类型|默认值|
|:---|:---|:---|
|min|	最小值|	Number|	-Infinity|
|max|	最大值|	Number|	Infinity|
|value|	当前值|	Number|	
|step	|每次改变步数，可以为小数|	Number 	|1|	
|onChange|	变化时回调函数|	(): void	|
|showNumber|是否显示数值，默认不显示|Boolean|false|


该组件推荐使用受控模式，使用案例：

```
import React, { Component } from 'react';

import { Button,Switch, Stepper} from 'antd-mobile';

export default class App extends Component {

  constructor(props) {
    super(props)
  
    this.state = {
      count:5,
    }
  }
  

  render() {
    return (
        <div>
          <h1>步进器演示1</h1>
          <br/>
          <Stepper 
            min={0}
            max={10}
            value={this.state.count}
            step={0.5}
            onChange={(count)=>{this.setState({count})}}
            showNumber={true}
          /> 
        </div>
    );
  }
}
```

![23.png](../images/23.png)

使用步进器控制器其他组件，使用案例：

```
import React, { Component } from 'react';

import { Button,Switch, Stepper} from 'antd-mobile';

export default class App extends Component {

  constructor(props) {
    super(props)
  
    this.state = {
      count:5,
    }
  }
  

  render() {
    return (
        <div>
          <h1>步进器演示2</h1>
          <br/>
          <p>{`步进器数值：${this.state.count}`}</p>
          <Stepper 
            min={0}
            max={10}
            value={this.state.count}
            step={0.5}
            onChange={(count)=>{this.setState({count})}}
            showNumber={false}
          /> 
        </div>
    );
  }
}
```

![24.png](../images/24.png)

