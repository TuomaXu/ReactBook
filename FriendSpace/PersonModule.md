# 个人信息模块

个人信息模块由一下Screen组成：

* PersonScreen展示个人信息
* ChangePersonInfoScreen修改个人信息
* ChangePassWordScreen修改密码

### PersonScreen展示个人信息

该模块展示用户头像、昵称和个性签名。同时提供修改个人资料按钮、修改密码按钮和退出登录按钮。

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
    Flex
} from 'antd-mobile';

import {
  imageBaseURL
} from '../DataServer/URLConfig';

import accountManager from '../DataServer/AccountManager';
import userManager from '../DataServer/UserManager';

export default class PersonScreen extends Component {


  async componentDidMount(){

    if(accountManager.isLogin() === false){
        return;
    }

    const result = await userManager.getUserInfo();

    if(result.success === false){
        Toast.fail(result.errorMessage,1);
        return;
    }

    this.setState({user:result.data});

  }


  constructor(props) {
    super(props)
  
    this.state = {
        user:{
          nickname:'',
          sign:'',
          image:''
        },
    }
  }
    

  render() {

    return (
      <div>
        <NavBar
            mode="dark"
        >个人信息</NavBar>
        <Flex
          justify={'center'}
          style={{backgroundColor:'#ffffff'}}
        >
          <img
            alt={''}
            src={imageBaseURL+this.state.user.image}
            style={{width:'100px',height:'100px',margin:'5px'}}
          />
        </Flex>
        <List>
            <InputItem
                type={'text'}
                editable={false}
                value={this.state.user.nickname}
                onChange={(nickname)=>{this.setState({user:{nickname}})}}
                placeholder={'请输入昵称'}
            >
                昵称
            </InputItem>
            <InputItem
                type={'text'}
                editable={false}
                value={this.state.user.sign}
                onChange={(sign)=>{this.setState({user:{sign}})}}
                placeholder={'请输入个性签名'}
            >
                签名
            </InputItem>
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Button
                onClick={async()=>{
                    this.props.history.push('/ChangePersonInfoScreen',this.state.user);
                }}
            >
              修改个人资料
            </Button>
            <WhiteSpace/>
            <Button
                onClick={async()=>{
                    this.props.history.push('/ChangePassWordScreen');
                }}
            >
                修改密码
            </Button>
            <WhiteSpace/>
            <WhiteSpace/>
            <Button
                type={'warning'}
                onClick={async()=>{
                      accountManager.logout();
                      this.props.history.replace('/');
                      
                }}
            >
                推出登录
            </Button>
        </WingBlank>
      </div>
    )
  }
}
```

###  ChangePersonInfoScreen

该页面提供修改头像、昵称和签名的功能。

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
    Icon,
    ImagePicker,
    Modal
} from 'antd-mobile';

import userManager from '../DataServer/UserManager';



export default class ChangePersonInfoScreen extends Component {


    constructor(props) {
      super(props)
    
      this.state = {
         nickname:props.location.state.nickname,
         sign:props.location.state.sign,
         files:[]
      }
    }
    


  render() {
    return (
      <div>
        <NavBar
            mode="dark"
            icon={<Icon type="left" />}
            onLeftClick={() => {this.props.history.goBack()}}
        >修改用户信息</NavBar>
        <WhiteSpace/>
        <ImagePicker
            files={this.state.files}
            onChange={(files)=>{this.setState({files})}}
            selectable={this.state.files.length < 1}
        />
        <List>
            <InputItem
                type={'text'}
                value={this.state.nickname}
                onChange={(nickname)=>{this.setState({nickname})}}
                placeholder={'请输入昵称'}
            >
                昵称
            </InputItem>
            <InputItem
                type={'text'}
                value={this.state.sign}
                onChange={(sign)=>{this.setState({sign})}}
                placeholder={'请输入个性签名'}
            >
                签名
            </InputItem>
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Button
                type={'primary'}
                onClick={async()=>{

                    Toast.loading('内容上传中...',0);
                    const userinfo = {
                        nickname:this.state.nickname,
                        sign:this.state.sign
                    }
                    if(this.state.files.length !== 0){
                        userinfo.image = this.state.files[0];
                    } 
                    const result = await userManager.updateUser(userinfo);
                    console.log(result)

                    
                    
                    Toast.hide();
                    if(result.success === false){
                        Toast.fail(result.errorMessage);
                        return;
                    }
                    Modal.alert('修改成功','点击确认返回',[{
                        text:'确认',
                        onPress:()=>{this.props.history.goBack()}
                    }])
                    
                }}
            >
                提交修改
            </Button>
        </WingBlank>
      </div>
    )
  }
}

```

### ChangePassWordScreen

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    NavBar,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
    Icon,
    Modal
} from 'antd-mobile';

import accountManager from '../DataServer/AccountManager';



export default class ChangePassWordScreen extends Component {


    constructor(props) {
      super(props)
    
      this.state = {
        old_password:'',
        new_password:''
      }
    }
    

  render() {
    return (
      <div>
        <NavBar
            mode="dark"
            icon={<Icon type="left" />}
            onLeftClick={() => {this.props.history.goBack()}}
        >修改密码</NavBar>
        <WhiteSpace/>
        <List>
            <InputItem
                type={'text'}
                value={this.state.old_password}
                onChange={(old_password)=>{this.setState({old_password})}}
                placeholder={'请输入旧密码'}
            >
                旧密码
            </InputItem>
            <InputItem
                type={'text'}
                value={this.state.new_password}
                onChange={(new_password)=>{this.setState({new_password})}}
                placeholder={'请输入新密码'}
            >
                新密码
            </InputItem>
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Button
                type={'primary'}
                onClick={async()=>{
                    const result = await accountManager.changePassword(this.state.old_password,this.state.new_password);
                    console.log(result);
                    if(result.success === false){
                        Toast.fail(result.errorMessage);
                        return;
                    }
                    Modal.alert('修改成功','点击确认键返回',[{
                        text:'确认',
                        onPress:()=>{this.props.history.goBack()}
                    }])
                    
                }}
            >
                提交修改
            </Button>
        </WingBlank>
      </div>
    )
  }
}

```
