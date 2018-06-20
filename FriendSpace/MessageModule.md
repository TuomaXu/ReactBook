# 消息浏览与发送模块

消息由文字和最多9张图片组成。

### 发送消息

发送消息模块主要使用以下两个组件：

* `TextareaItem`，多行输入组件
* `ImagePicker`，图片选择组件

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    Icon,
    TextareaItem,
    Modal,
    ImagePicker
} from 'antd-mobile';

import messageManager from '../DataServer/MessageManager';
import accountManager from '../DataServer/AccountManager';


export default class CreateMessageScreen extends Component {

    constructor(props) {
      super(props)
    
      this.state = {
         content:'',
         files:[]
      }
    }

    componentWillMount(){
        if(!accountManager.isLogin()){
            this.props.history.replace('/');
        }
    }
    

  render() {
    return (
      <div>
        <NavBar
            mode="dark"
            icon={<Icon type="left" />}
            onLeftClick={() => {this.props.history.goBack()}}
        >发消息</NavBar>
        <WhiteSpace/>
        <List>
            <TextareaItem
                type={'text'}
                value={this.state.content}
                onChange={(content)=>{this.setState({content})}}
                placeholder={'请输入内容'}
                rows={5}
            />
        </List>
        <WhiteSpace/>
        <WingBlank>
            <ImagePicker
                files={this.state.files}
                onChange={(files)=>{this.setState({files})}}
                selectable={this.state.files.length <= 9}
            />
            <WhiteSpace/>
            <Button
                type={'primary'}
                onClick={async()=>{
                    Toast.loading('内容上传中...',0);
                    const result = await messageManager.postMessage(this.state.content,this.state.files);
                    Toast.hide();
                    console.log(result)
                    if(result.success === false){
                        Toast.fail(result.errorMessage);
                        return;
                    }
                    Modal.alert('提交成功','点击确认键返回',[{
                        text:'确认',
                        onPress:()=>{this.props.history.goBack()}
                    }])

                }}
            >
                提交
            </Button>
        </WingBlank>
      </div>
    )
  }
}

```


消息首页：

首先构造Item展示组件：

```
import React, { Component } from 'react'

import moment from 'moment';

import {
    imageBaseURL
} from '../DataServer/URLConfig';

import './HomeListItem.css'


import { 
    WingBlank, 
    WhiteSpace ,
    Card,
    Grid
} from 'antd-mobile';

export default class HomeListItem extends Component {
  render() {

    const images = this.props.images.map((image)=>{
        return {
            icon:imageBaseURL+image.url
        }
    })

    return (
        <div
            onClick={()=>{
                if(this.props.onItemClick){
                    this.props.onItemClick();
                }   
            }}
        >
            <WingBlank>
                <WhiteSpace/>
                <Card>
                    <Card.Header
                        title={this.props.user.nickname}
                        thumb={imageBaseURL+this.props.user.image}
                        thumbStyle={{width:'40px',height:'40px'}}
                    />
                    <Card.Body>
                        <span>
                        {this.props.content}
                        </span>
                        <Grid
                            data={images}
                            columnNum={3}
                            hasLine={false}
                            renderItem={(image)=>{
                                return (
                                    <div style={{
                                        width: '88px',
                                        height: '88px',
                                        background: `url(${image.icon}) center center /  86px 86px no-repeat` }}
                                    />
                                )
                                
                            }}
                        />
                    </Card.Body>
                    <Card.Footer
                        content={moment(this.props.createdAt).format('YYYY-MM-DD HH:mm')}
                    />
                </Card>
            </WingBlank>
        </div>
    )
  }
}

```

CSS样式文件：

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

在HomeScreen中使用Item组件展示数据内容：



```
import React, { Component } from 'react'

import { 
    Toast,
    NavBar,
    ListView,
    PullToRefresh
} from 'antd-mobile';

import accountManager from '../DataServer/AccountManager';
import messageManager from '../DataServer/MessageManager';

import HomeListItem from '../ViewComponent/HomeListItem';


export default class HomeScreen extends Component {

    async componentDidMount(){
        
        if(accountManager.isLogin() === false){
            return;
        }

        const result = await messageManager.homeMessage()
        if(result.success === false){
            Toast.fail(result.errorMessage);
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
            refreshing:false
        }
    }

    onRefresh = async()=>{
        try {
            this.setState({refreshing:true});
            const result = await messageManager.homeMessage()
            if(result.success === false){
                Toast.fail(result.errorMessage);
                this.setState({refreshing:false});
                return;
            }
            this.setState((preState)=>{
                return{
                    dataSource:preState.dataSource.cloneWithRows(result.data),
                    refreshing:false
                }   
            })
        } catch (error) {
            Toast.fail(`${error}`);
            this.setState({refreshing:false});
        }

    }

  render() {
    return (
      <div>
        <NavBar
            mode="dark"
            rightContent={[
                <span
                    key={2}
                    onClick={()=>{
                        this.props.history.push('/CreateMessageScreen');
                    }}
                >发消息</span>
            ]}
        >FriendSpace</NavBar>
        <ListView
            useBodyScroll={true}
            dataSource={this.state.dataSource}
            pullToRefresh={
                <PullToRefresh
                    refreshing={this.state.refreshing}
                    onRefresh={this.onRefresh}
                />
            }
            renderRow={(message)=>{
                return (
                    <HomeListItem 
                        {...message}
                    />
                )
            }}
        />
      </div>
    )
  }
}
```