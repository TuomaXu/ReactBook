# Ant长列表

在WebApp中，经常需要渲染一些条目未知的列表页面，例如微信通讯录、微博或淘宝商品类别。在渲染这些条目未知的且具有刷新和分页功能的列表时，需要使用`ListView`组件。

>在渲染条目已知的类别页面时，推荐使用`List`。

渲染长列表时，首先要确定被渲染的数据单元格式，然后依据数据单元格式构建渲染数据单元的Item组件。

构造测试数据：

```
const data = [
    {
        id:1,
        name:'张三',
        tel:'18600000001'
    },
    {
        id:1,
        name:'李四',
        tel:'18600000001'
    },
    {
        id:1,
        name:'王五',
        tel:'18600000001'
    },
]
```

通过对元数据分析，构造展示元数据的Item组件：

```
import React, { Component } from 'react'

export default class Item extends Component {
  render() {
    return (
      <div>
        <h1>{this.props.name}</h1>
        <p>{this.props.tel}</p>
      </div>
    )
  }
}
```

Item组件由设计部分决定，在此，我们构建了一个非常简单的展示组件。

在使用`ListView`之前，还需要通过`ListView.DataSource`对数据进行处理。因`ListView`经常会有部分数据需要变化和刷新，`ListView.DataSource`对象主要功能为判定数据是否发生变化，然后通知`ListView`只对变化的部分进行刷新，从而对渲染效率进行优化，避免每次因一个数据变化而全部数据都需要刷新的情况。


`ListView.DataSource`需要在`constructor()`函数中进行初始化操作，在初始化的时候需要配置一个数据判定规则`rowHasChanged`默认为` (row1, row2) => row1 !== row2`，表示两个对象不相等即更新该条目数据。

```
  constructor(props) {
    super(props)

    const dataSource = new ListView.DataSource({
      rowHasChanged: (row1, row2) => row1 !== row2
    });
  
    this.state = {
      dataSource
    }
  }
```

在数据发送变化时，通过调用其`cloneWithRows()`更新数据，此时`ListView.DataSource`会将新旧数据进行对比，然后通知`ListView`更新相应的数据。一般我们在`componentDidMount()`中从API请数据，然后在将请求结果通过`cloneWithRows()`方法将数据进行更新：

```
async componentDidMount(){
    const res = await fetch('http://localhost:5000/api/allUsers');
    const result = await res.json();

    this.setState((preState)=>{
      dataSource:preState.dataSource.cloneWithRows(result.data)
    })

}
```

`ListView`基本使用方法需要配置两个属性：

* `dataSource`
* `renderRow`

`renderRow`


