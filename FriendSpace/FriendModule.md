# 好友模块

FriendScreen默认展示已关注的好友列表。同时可以通过最上方的搜索框进行好友搜索。搜索时，下方显示为搜索结果。点击好友之后可以进入好友信息，在好友信息页面中可以浏览信息并关注该好友。

该模块由一下Screen组成

* `FriendScreen`
* `AddFriendScreen`


### 构建列表展示Item

```
import React, { Component } from 'react'


import {
    imageBaseURL
} from '../DataServer/URLConfig';

import './FriendListItem.css'


import { 
    WingBlank, 
    WhiteSpace ,
    Card,
    SwipeAction,
} from 'antd-mobile';

export default class HomeListItem extends Component {
  render() {


    return (
        <SwipeAction
            autoClose={true}
            right={[
                {
                    text:'取消关注',
                    style:{
                        backgroundColor:'red'
                    },
                    onPress:(e)=>{
                        if(this.props.del){
                            this.props.del(this.props.id);
                        }
                    }
                },
            ]}

        >
            <WingBlank>
                <WhiteSpace/>
                <Card
                    onClick={()=>{
                        if(this.props.onItemClick){
                            this.props.onItemClick();
                        }   
                    }}
                >
                    <Card.Header
                        title={this.props.nickname}
                        thumb={imageBaseURL+this.props.image}
                        thumbStyle={{width:'40px',height:'40px'}}
                        extra={this.props.sign}
                    />
                </Card>
            </WingBlank>
        </SwipeAction>
    )
  }
}
```

CSS样式文件：

```
.am-card{
    min-height: 50px;
}

span{
    word-break:normal;
    width:auto; 
    display:block; 
    white-space:pre-wrap;
    word-wrap : break-word ;
    overflow: hidden ;
}  
```

### FriendScreen

下拉刷新事件：

```
onRefresh = async()=>{
    try {
        this.setState({refreshing:true});
        const result = await friendManager.getFollows()
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
```

搜索事件响应：

```
onSearch = async ()=>{
    this.setState({
        isSearchData:true,
    })
    Toast.loading('查询中，请稍后',0);
    const result = await friendManager.findUser(this.state.nickname);
    Toast.hide();

    if(result.success === false){
        Toast.fail(result.errorMessage,1);
        return;
    }

    this.setState((preState)=>{
        return{
            dataSource:preState.dataSource.cloneWithRows(result.data)
        }   
    })
}

```
取消事件响应：

```
onCancel = async ()=>{
    this.setState({
        nickname:'',
        isSearchData:false
    })
    const result = await friendManager.getFollows()
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
```

取消关注事件响应：

```
onDel = async (id)=>{
    Toast.loading('操作中',0);
    const result = await friendManager.unFollowUser(id);
    Toast.hide();
    
    if(result.success === false){ 
        Toast.fail(result.errorMessage,1);
        return;
    }

    const result1 = await friendManager.getFollows();
    if(result1.success === false){
        Toast.fail(result1.errorMessage,1);
        return;
    }

    this.setState((preState)=>{
        return{
            dataSource:preState.dataSource.cloneWithRows(result1.data)
        }   
    },()=>{
        Toast.hide(); 
    })
}
```

完整参考代码：

```
import React, { Component } from 'react'

import { 
    Toast,
    NavBar,
    ListView,
    PullToRefresh,
    SearchBar
} from 'antd-mobile';

import accountManager from '../DataServer/AccountManager';
import friendManager from '../DataServer/FriendManager';

import FriendListItem from '../ViewComponent/FriendListItem';

export default class FriendScreen extends Component {

    async componentDidMount(){
        
        if(accountManager.isLogin() === false){
            return;
        }

        const result = await friendManager.getFollows()
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
            refreshing:false,
            nickname:'',
            isSearchData:false,
        }
    }

    onRefresh = async()=>{
        try {
            this.setState({refreshing:true});
            const result = await friendManager.getFollows()
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

    onSearch = async ()=>{
        this.setState({
            isSearchData:true,
        })
        Toast.loading('查询中，请稍后',0);
        const result = await friendManager.findUser(this.state.nickname);
        Toast.hide();

        if(result.success === false){
            Toast.fail(result.errorMessage,1);
            return;
        }

        this.setState((preState)=>{
            return{
                dataSource:preState.dataSource.cloneWithRows(result.data)
            }   
        })
    }
    onCancel = async ()=>{
        this.setState({
            nickname:'',
            isSearchData:false
        })
        const result = await friendManager.getFollows()
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

    onDel = async (id)=>{
        Toast.loading('操作中',0);
        const result = await friendManager.unFollowUser(id);
        Toast.hide();
        
        if(result.success === false){ 
            Toast.fail(result.errorMessage,1);
            return;
        }

        const result1 = await friendManager.getFollows();
        if(result1.success === false){
            Toast.fail(result1.errorMessage,1);
            return;
        }

        this.setState((preState)=>{
            return{
                dataSource:preState.dataSource.cloneWithRows(result1.data)
            }   
        },()=>{
            Toast.hide(); 
        })
    }

  render() {
    return (
      <div>
        <NavBar
            mode="dark"
        >朋友</NavBar>
        <SearchBar
            placeholder={'输入好友昵称'}
            value={this.state.nickname}
            onChange={(nickname)=>{this.setState({nickname})}}
            onSubmit={this.onSearch}
            onCancel={this.onCancel}
        />
        <ListView
            useBodyScroll={true}
            dataSource={this.state.dataSource}
            pullToRefresh={
                this.state.isSearchData?null:(
                    <PullToRefresh
                        refreshing={this.state.refreshing}
                        onRefresh={this.onRefresh}
                    />
                )
                
            }
            renderRow={(user)=>{
                return (
                    <FriendListItem 
                        {...user}
                        onItemClick={()=>{
                            this.props.history.push('/UserScreen',user)
                        }} 
                        del={this.onDel}
                    />
                )
            }}
        />
      </div>
    )
  }
}
```

### AddFriendScreen

点击好友条目，跳转到好友页面，在该页面可以进行关注

```
import React, { Component } from 'react'

import { 
    Toast,
    NavBar,
    ListView,
    SearchBar,
    Icon
} from 'antd-mobile';

import accountManager from '../DataServer/AccountManager';
import friendManager from '../DataServer/FriendManager';

import FriendListItem from '../ViewComponent/FriendListItem';

export default class AddFriendScreen extends Component {

    componentDidMount(){
        
        if(accountManager.isLogin() === false){
            this.props.history.replace('/');
            return;
        }
    }


    constructor(props) {
        super(props)

        const dataSource = new ListView.DataSource({
            rowHasChanged:(row1, row2) => row1 !== row2,
        })

        this.state = {
            dataSource,
            nickname:''
        }
    }

    onSearch = async ()=>{
        Toast.loading('查询中，请稍后',0);
        const result = await friendManager.findUser(this.state.nickname);
        Toast.hide();

        if(result.success === false){
            Toast.fail(result.errorMessage,1);
            return;
        }

        this.setState((preState)=>{
            return{
                dataSource:preState.dataSource.cloneWithRows(result.data)
            }   
        })
    }


  render() {
    return (
      <div>
        <NavBar
            mode="dark"
            icon={<Icon type="left" />}
            onLeftClick={() => {this.props.history.goBack()}}
        >添加朋友</NavBar>
        <SearchBar
            placeholder={'输入好友昵称'}
            value={this.state.nickname}
            onChange={(nickname)=>{this.setState({nickname})}}
            onSubmit={this.onSearch}
        />
        <ListView
            useBodyScroll={true}
            dataSource={this.state.dataSource}
            renderRow={(user)=>{
                return (
                    <FriendListItem 
                        {...user}
                        onItemClick={()=>{
                            this.props.history.push('/UserScreen',user)
                        }} 
                    />
                )
            }}
        />
      </div>
    )
  }
}

```
