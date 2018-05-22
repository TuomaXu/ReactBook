# React类组件

React类组件的概念与函数组件一致，但类组件可以提供`state`机制。通过`state`机制组件可以动态更新视图内容。在类组件中还可以使用声明周期函数，对组件进行更加精确的内容控制。

### 定义类组件

在React中，使用ES6的Class语法定义一个类组件。

```
//引入React框架
import React from 'react'

//类组件是由继承与React.Component基类构建
class Welcome extends React.Component {

    //通过类中的render方法，返回一个React元素
    render(){
        return(
            <h1>{'Hello'}</h1>
        )
    }
}
```

类组件的渲染与函数组件一样：

```
const e = <Welcome />
```

类组件在定义是，同样可以使用属性：

```
//引入React框架
import React from 'react'

//类组件是由继承与React.Component基类构建
class Welcome extends React.Component {

    //通过类中的render方法，返回一个React元素
    render(){
        return(
            <h1>{`Hello, ${this.props.name}`}</h1>
        )
    }
}
```

在类组件中，通过`this`对象访问其自身`props`属性对象。

通过类组件构造React元素时，也可以为其指定属性赋值：

```
const e = <Welcome name={'Tom'} />
```

完整代码如下：

```
//引入React框架
import React from 'react';

//引入ReactDOM框架
import ReactDOM from 'react-dom';

//使用JavaScriptDOM操作函数，通过id获取容器div
const div = document.getElementById('root');

//类组件是由继承与React.Component基类构建
class Welcome extends React.Component {
    
    //通过类中的render方法，返回一个React元素
    render(){
        return(
            <h1>{`Hello, ${this.props.name}`}</h1>
        )
    }
}

const e = <Welcome name={'Tom'} />

ReactDOM.render(e,div);
```

运行效果：

![7.png](../images/7.png)

### 组件状态

在类组件中，可以为组件添加一个状态，通过对状态的操作，可以动态更新页面内容。

组件状态有一个`state`的属性负责保存，在`render()`函数中可以范围`state`中的值并使用。如果需要修改`state`的值，则需要调用`this.setState()`方法进行，不能直接修改。因为在使用`this.setState()`方法修改`state`的值之后会告诉React框架重新调用`render()`方法，生成使用新的`state`值的React元素，从而实现页面的动态更新。

我们使用一个自动计数器来讲解`state`的使用方法。这个自动计数器可以每一秒钟自动实施+1操作，初始值为0.

使用`state`时，首先需要在`constructor()`方法中，声明`state`对象属性。将需要动态更新的字段初值以键值对方式放入该对象中。

```
class AutoCounter extends React.Component{

    //props是React.Component内置的属性，在React.Component子类中实现constructor()方法时，必须定义此形参
    constructor(props){
        //需要使用super将props对象传入父类进行初始化操作
        super(props);

        //声明state属性，其值为对象，通过键值对方式存储需要动态更新的内容
        this.state = {
            count:0,
        }
    }

    render(){

        //在render()中，使用this.state中的值构建React元素，以实现动态刷新内容
        return(
            <h1>{this.state.count}<h1>
        )
    }

}
```

此时通过`AutoCounter`构建的React元素还无法进行自动增加，只是一个静态元素。我们使用定时器来实现计数器的自增功能。

在`constructor()`方法中初始化一个定时器，让其每一秒钟将`state`中的`count`值+1。

class AutoCounter extends React.Component{

    //props是React.Component内置的属性，在React.Component子类中实现constructor()方法时，必须定义此形参
    constructor(props){
        //需要使用super将props对象传入父类进行初始化操作
        super(props);

        //声明state属性，其值为对象，通过键值对方式存储需要动态更新的内容
        this.state = {
            count:0,
        }

        //配置定时器，触发时间间隔为1000毫秒
        setInterval(()=>{
            //触发操作为state.count+1
            this.setState((preState)=>{
                return {
                    count:preState.count+1,
                }
            })
        },1000)
    }

    render(){

        //在render()中，使用this.state中的值构建React元素，以实现动态刷新内容
        return(
            <h1>{this.state.count}</h1>
        )
    }

}

因在React中，状态更新是异步操作，所以`this.setState()`是一个高阶函数，其参数为一个函数携带一个对象，该对象为上一次状态值。在需要基于上一次状态值进行更新时，可以使用参数函数携带的对象形参。参数函数返回一个对象，React框架会将参数函数返回的对象中的键值对与原`state`中的数据进行合并更新。

完整代码：

```
//引入React框架
import React from 'react';

//引入ReactDOM框架
import ReactDOM from 'react-dom';

//使用JavaScriptDOM操作函数，通过id获取容器div
const div = document.getElementById('root');

class AutoCounter extends React.Component{
    
        //props是React.Component内置的属性，在React.Component子类中实现constructor()方法时，必须定义此形参
        constructor(props){
            //需要使用super将props对象传入父类进行初始化操作
            super(props);
    
            //声明state属性，其值为对象，通过键值对方式存储需要动态更新的内容
            this.state = {
                count:0,
            }
    
            //配置定时器，触发时间间隔为1000毫秒
            setInterval(()=>{
                //触发操作为state.count+1
                this.setState((preState)=>{
                    return {
                        count:preState.count+1,
                    }
                })
            },1000)
        }
    
        render(){
    
            //在render()中，使用this.state中的值构建React元素，以实现动态刷新内容
            return(
                <h1>{this.state.count}</h1>
            )
        }
    
    }

ReactDOM.render(<AutoCounter/>,div);
```

运行结果：

![8.png](../images/8.png)

### 声明周期函数

//TODO