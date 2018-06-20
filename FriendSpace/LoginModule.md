# 登录注册模块

注册登录模块由以下模块组成：

* `LoginScreen`
* `RegisterScreen`
* `RegisterScreen`

### `LoginScreen`

登录页面允许用户输入用户名和密码，登录或跳转到注册页面。如是已经登录状态，自动跳转到首页。

```
import React, { Component } from 'react'

import { 
    Button,
    Toast,
    WingBlank, 
    WhiteSpace ,
    List,
    InputItem,
    Flex
} from 'antd-mobile';

import accountManager from '../DataServer/AccountManager';

export default class LoginScreen extends Component {

    componentDidMount(){
        if(accountManager.isLogin() === true){
            this.props.history.replace('/TabBarScreen');
        }
    }

    constructor(props) {
      super(props)
    
      this.state = {
         email:'',
         password:''
      }
    }
    

  render() {
    return (
      <div>
        <img
            alt=''
            src={require('../images/logo.jpg')}
            style={{width:'100%'}}
        />
        <List>
            <InputItem
                type={'text'}
                value={this.state.email}
                onChange={(email)=>{this.setState({email})}}
                placeholder={'请输入登录密码'}
            >
                邮箱
            </InputItem>
            <InputItem
                type={'password'}
                value={this.state.password}
                onChange={(password)=>{this.setState({password})}}
                placeholder={'请输入登录密码'}
            >
                密码
            </InputItem>
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Flex>
                <Button
                    style={{flex:1,marginRight:5}}
                    type={'primary'}
                    onClick={async()=>{
                        const result = await accountManager.login(this.state.email,this.state.password);
                        console.log(result);
                        if(result.success === false){
                            Toast.fail(result.errorMessage,1);
                            return;
                        }

                        if(!result.data.userId){
                            this.props.history.push('/CreateUserScreen');
                            return;
                        }

                        this.props.history.replace('/TabBarScreen');

                    }}
                >
                    登录
                </Button>
                <Button
                    style={{flex:1,marginLeft:5}}
                    type={'primary'}
                    onClick={async()=>{
                        this.props.history.push('/RegisterScreen');
                    }}
                >
                    注册
                </Button>  
            </Flex>
        </WingBlank>
      </div>
    )
  }
}

```

### RegisterScreen

注册页面允许用户输入用户名和密码进行注册。

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
    Icon
} from 'antd-mobile';

import accountManager from '../DataServer/AccountManager';



export default class RegisterScreen extends Component {


    constructor(props) {
      super(props)
    
      this.state = {
         username:'',
         password:''
      }
    }
    

  render() {
    return (
      <div>
        <NavBar
            mode="dark"
            icon={<Icon type="left" />}
            onLeftClick={() => {this.props.history.goBack()}}
        >注册</NavBar>
        <WhiteSpace/>
        <List>
            <InputItem
                type={'text'}
                value={this.state.username}
                onChange={(username)=>{this.setState({username})}}
                placeholder={'请输入注册用户名'}
            >
                用户名
            </InputItem>
            <InputItem
                type={'text'}
                value={this.state.password}
                onChange={(password)=>{this.setState({password})}}
                placeholder={'请输入注册密码'}
            >
                密码
            </InputItem>
        </List>
        <WhiteSpace/>
        <WingBlank>
            <Button
                type={'primary'}
                onClick={async()=>{
                    const result = await accountManager.register(this.state.username,this.state.password);
                    console.log(result);
                    if(result.success === false){
                        Toast.fail(result.errorMessage);
                        return;
                    }
                    this.props.history.push('/CreateUserScreen');
                    
                }}
            >
                提交注册
            </Button>
        </WingBlank>
      </div>
    )
  }
}

```

### `CreateUserScreen`

注册账号信息之后，需要提交用户的基础信息：

* 头像
* 昵称
* 个性签名

以继续完成创建用户的步骤。

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



export default class CreateUserScreen extends Component {


    constructor(props) {
      super(props)
    
      this.state = {
         nickname:'',
         sign:'',
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
        >完善用户信息</NavBar>
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

                    if(this.state.files.length === 0){
                        Toast.fail('请选择头像',1);
                        return;
                    }

                    Toast.loading('内容上传中...',0);
                    const resutl = await userManager.createUser(this.state.nickname,this.state.sign,this.state.files[0]);
                    Toast.hide();
                    if(resutl.success === false){
                        Toast.fail(resutl.errorMessage);
                        return;
                    }
                    Modal.alert('成功','点击确认键进入主页',[{
                        text:'确认',
                        onPress:()=>{this.props.history.replace('/TabBarScreen')}
                    }])
                    
                }}
            >
                完成注册
            </Button>
        </WingBlank>
      </div>
    )
  }
}
```