# Ant消息响应组件

### ActivityIndicator 活动指示器

活动指示器。 表明某个任务正在进行中。

默认规则：

* 不要让活动指示器静止，用户会以为该任务停滞了。
* 在某些特定场景下，提供有意义的文案，帮助用户明白哪个任务正在进行中，eg：正在上传照片。
* 如果能知道用户的等待时间，可以使用组件 Progress 来替代。

组件API：

|属性|	说明|	类型	|默认值|
|:---|:---|:---|:---|
|animating|	显隐状态	|boolean|	true|
|size	|spinner大小，可选small/large|	string|	small|
|toast	|loading样式类型	|boolean	|false|
|ext	|loading文本|	string|	-|

使用参考：

```
<ActivityIndicator />
<ActivityIndicator color="white" />
<ActivityIndicator size="large" />
<ActivityIndicator text="正在加载" />
<ActivityIndicator toast />
<ActivityIndicator toast text="正在加载" />
```

![57.png](../images/57.png)

实现代码：

```
import React, { Component } from 'react';

import {ActivityIndicator,Toast, Icon,WhiteSpace,Button,List,Carousel,Card,WingBlank,Grid,NoticeBar,Tag} from 'antd-mobile';


export default class App extends Component {

  constructor(props) {
    super(props)
  
    this.state = {
      animating:false,
    }
  }
  
  

  render() {

    return (
        <div>
          <ActivityIndicator
            animating={this.state.animating}
            size={'large'}
            toast={true}
            text={'加载中...'}
          />
          <h1>{'ActivityIndicator 活动指示器'}</h1>
          <WhiteSpace size="lg" />
          <WingBlank>
            <WhiteSpace />
            <Button 
              onClick={()=>{
                this.setState({
                  animating:true
                });
                setTimeout(()=>{
                  this.setState({
                  animating:false
                });
                },3000)
              }}
            >
              开启指示器，3秒后自动关闭
            </Button>
          </WingBlank>
        </div>
    );
  }
}

```



### Toast 轻提示

一种轻量级反馈/提示，可以用来显示不会打断用户操作的内容，适合用于页面转场、数据交互的等场景中。

默认规则：

* 一次只显示一个 toast。
* 有 Icon 的 Toast，字数为 4-6 个；没有 Icon 的 Toast，字数不宜超过 14 个。

该组件提供了五个静态方法，用于展示Toast提示框：

* `Toast.success(content, duration, onClose, mask)`
* `Toast.fail(content, duration, onClose, mask)`
* `Toast.info(content, duration, onClose, mask)`
* `Toast.loading(content, duration, onClose, mask)`
* `Toast.offline(content, duration, onClose, mask)`

参数说明：

|属性|	说明|	类型	|默认值|
|:---|:---|:---|:---|
|content|	提示内容|	React.Element or String	|无|
|duration	|自动关闭的延时，单位秒	|number	|3|
|onClose	|关闭后回调|	Function|	无|
|mask	|是否显示透明蒙层，防止触摸穿透|	Boolean|	true|

>注： `duration = 0` 时，`onClose` 无效，`toast` 不会消失；隐藏 `toast` 需要手动调用 `hide()`

组件提供了全局配置和全局销毁方法：

* `Toast.hide()`

![56.png](../images/56.png)

参考代码实现：

```
import React, { Component } from 'react';

import {Toast, Icon,WhiteSpace,Button,List,Carousel,Card,WingBlank,Grid,NoticeBar,Tag} from 'antd-mobile';

function showToast() {
  Toast.info('This is a toast tips !!!', 1);
}

function showToastNoMask() {
  Toast.info('Toast without mask !!!', 2, null, false);
}

function successToast() {
  Toast.success('Load success !!!', 10);
}

function failToast() {
  Toast.fail('Load failed !!!', 1);
}

function offline() {
  Toast.offline('Network connection failed !!!', 1);
}

function loadingToast() {
  Toast.loading('Loading...', 1, () => {
    console.log('Load complete !!!');
  });
}

const customIcon = () => (
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 64 64" className="am-icon am-icon-md">
    <path fillRule="evenodd" d="M59.177 29.5s-1.25 0-1.25 2.5c0 14.47-11.786 26.244-26.253 26.244C17.206 58.244 5.417 46.47 5.417 32c0-13.837 11.414-25.29 25.005-26.26v6.252c0 .622-.318 1.635.198 1.985a1.88 1.88 0 0 0 1.75.19l21.37-8.545c.837-.334 1.687-1.133 1.687-2.384C55.425 1.99 53.944 2 53.044 2h-21.37C15.134 2 1.667 15.46 1.667 32c0 16.543 13.467 30 30.007 30 16.538 0 29.918-13.458 29.993-30 .01-2.5-1.24-2.5-1.24-2.5h-1.25" />
  </svg>
);

export default class App extends Component {

  componentDidMount() {
    Toast.loading('Loading...', 30, () => {
      console.log('Load complete !!!');
    });

    setTimeout(() => {
      Toast.hide();
    }, 3000);
  }

  render() {

    return (
        <div>
          <h1>{'Toast 轻提示'}</h1>
          <WhiteSpace size="lg" />
          <WingBlank>
            <WhiteSpace />
            <Button onClick={showToast}>text only</Button>
            <WhiteSpace />
            <Button onClick={showToastNoMask}>without mask</Button>
            <WhiteSpace />
            <Button onClick={() => Toast.info(customIcon(), 1)}>
              cumstom icon
            </Button>
            <WhiteSpace />
            <Button onClick={successToast}>success</Button>
            <WhiteSpace />
            <Button onClick={failToast}>fail</Button>
            <WhiteSpace />
            <Button onClick={offline}>network failure</Button>
            <WhiteSpace />
            <Button onClick={loadingToast}>loading</Button>
            <WhiteSpace />
          </WingBlank>
        </div>
    );
  }
}

```

### Modal 对话框

用作显示系统的重要信息，并请求用户进行操作反馈，eg：删除某个重要内容时，弹出 Modal 进行二次确认。

* 尽可能少用。Modal 会打断用户操作，只用在重要的时候。
* 标题应该简明，不能超过 1 行；描述内容应该简明、完整，一般不多于 2 行。
* 操作按钮最多到 3 个（竖排），一般为 1-2 个（横排）；3 个以上建议使用组件 ActionSheet 来完成。
* 一般将用户最可能点击的按钮，放在右侧。另外，取消按钮应当始终放在左侧。

