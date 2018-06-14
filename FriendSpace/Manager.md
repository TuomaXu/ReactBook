# App数据服务对象设计与实现

根据业务需求分析及API接口文档，需要构建如下数据服务对象：

* `AccountManager`
* `UserManager`
* `MessageManager`
* `FriendManager`

### 构建`URLConfig`模块

```
const host = 'http://60.205.141.116:'
const port = 60004;

const registerURL = host+port+'/api/register';
const loginURL = host+port+'/api/login';
const createUserURL = host+port+'/api/createUser';
const postMessageURL = host+port+'/api/postMessage';
const deleteMessageURL = host+port+'/api/deleteMessage';
const followURL = host+port+'/api/follow';
const unFollowURL = host+port+'/api/unFollow';
const getFollowURL = host+port+'/api/getFollow';
const updateUserURL = host+port+'/api/updateUser';
const getUserURL = host+port+'/api/getUser';
const getMessageURL = host+port+'/api/getMessage';
const homeMessageURL = host+port+'/api/homeMessage';
const findUserURL = host+port+'/api/findUser';
const changePasswordURL = host+port+'/api/changePassword';

const imageBaseURL = host+port+'/resource/image/';


export {
    registerURL,
    loginURL,
    changePasswordURL,

    createUserURL,
    updateUserURL,
    getUserURL,
    
    postMessageURL,
    getMessageURL,
    homeMessageURL,
    deleteMessageURL,

    findUserURL,
    followURL,
    unFollowURL,
    getFollowURL,  
    
    imageBaseURL,
}
```


### 构建`AccountManager`

该数据服务对象提供以下数据功能：

* 注册
* 登录
* 修改密码
* 查询登录状态
* 登出

```
import {
    loginURL,
    registerURL,
    changePasswordURL
} from './URLConfig';

import axios from 'axios';

class AccountManager {

    async register(email,password){
        try {
            const res = await axios.post(registerURL,{
                email,
                password
            });
            const result = res.data;
            if (result.success === true) {
                localStorage.access_token = result.data.access_token;
            }
            return result;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
        
    }

    async login(email,password){
        try {
            const res = await axios.post(loginURL,{
                email,
                password
            });
            const result = res.data;
            if (result.success === true) {
                localStorage.access_token = result.data.access_token;
            }
            return result;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
        
    }

    async changePassword(old_password,new_password){
        try {
            const access_token = localStorage.access_token;
            const res = await axios.post(changePasswordURL,{
                access_token,
                old_password,
                new_password
            });
            const result = res.data;
            return result;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
        
    }

    isLogin(){
        if(localStorage.access_token === '' || !localStorage.access_token){
            return false;
        } else {
            return true;
        }
    }

    logout(){
        localStorage.access_token = '';
    }

}

export default new AccountManager();
```


### 构建`UserManager`

该数据服务对象提供以下功能：

* 创建用户信息
* 更新用户信息
* 获取用户信息

```
import {
    createUserURL,
    updateUserURL,
    getUserURL,
} from './URLConfig';

import axios from 'axios';

class UserManager {
    async createUser(nickname,sign,image){
        try {
            const access_token = localStorage.access_token;
            const formData = new FormData();
            formData.append('access_token',access_token);
            formData.append('nickname',nickname);
            formData.append('sign',sign);
            formData.append('image',image.file);

            const res = await axios({
                url:createUserURL,
                method:'POST',
                data:formData,
                headers: {'Content-Type': 'multipart/form-data'}
            })
            return res.data;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }

    async updateUser(userInfo){
        try {
            const access_token = localStorage.access_token;
            const formData = new FormData();
            formData.append('access_token',access_token);
            if (userInfo.nickname) {
                formData.append('nickname',userInfo.nickname);
            }
            if (userInfo.sign) {
                formData.append('sign',userInfo.sign);
            }
            if (userInfo.image) {
                formData.append('image',userInfo.image.file);
            }

            const res = await axios({
                url:updateUserURL,
                method:'POST',
                data:formData,
                headers: {'Content-Type': 'multipart/form-data'}
            })
            return res.data;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }

    async getUserInfo(userId = 0){
        try {
            const access_token = localStorage.access_token;
            const res = await axios.post(getUserURL,{
                access_token,
                userId
            })
            return res.data;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }


}

export default new UserManager();
```


### 构建`MessageManager`

该数据服务对象提供以下功能：

* 发送消息
* 删除消息
* 获取指定用户消息
* 获取登录用户Home页消息

```
import axios from 'axios';

import {
    postMessageURL,
    getMessageURL,
    homeMessageURL,
    deleteMessageURL,
} from './URLConfig';

class MessageManager {
    async postMessage(content,images){
        try {
            const access_token = localStorage.access_token;
            const formData = new FormData();
            formData.append('access_token',access_token);
            formData.append('content',content);
            if (images) {
                images.map((image,index)=>{
                    formData.append(`image${index+1}`,image.file);
                    return '';
                })
            }

            const res = await axios({
                url:postMessageURL,
                method:'POST',
                data:formData,
                headers: {'Content-Type': 'multipart/form-data'}
            })
            return res.data;

        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }
    }

    async deleteMessage(messageId){
        try {
            const access_token = localStorage.access_token;
            const res = await axios.post(deleteMessageURL,{
                access_token,
                messageId
            })
            return res.data;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }  
    }

    async homeMessage(minId){
        try {
            const access_token = localStorage.access_token;
            const res = await axios.post(homeMessageURL,{
                access_token,
                minId
            })
            return res.data;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }  
    }

    async getUserMessage(userId,minId){
        try {
            const access_token = localStorage.access_token;
            const res = await axios.post(getMessageURL,{
                access_token,
                userId,
                minId
            })
            return res.data;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }  
    }

    


}

export default new MessageManager();
```

### 构建`FriendManager`

该数据服务对象提供以下功能：

* 查询用户
* 关注用户
* 取消关注
* 获取所有关注用户

```
import axios from 'axios';

import {
    findUserURL,
    followURL,
    unFollowURL,
    getFollowURL, 
} from './URLConfig';

class FriendManager {

    async findUser(nickname){
        try {
            const access_token = localStorage.access_token;
            const res = await axios.post(findUserURL,{
                access_token,
                nickname
            })
            return res.data;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }  
    }

    async followUser(userId){
        try {
            const access_token = localStorage.access_token;
            const res = await axios.post(followURL,{
                access_token,
                userId
            })
            return res.data;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }  
    }

    async unFollowUser(userId){
        try {
            const access_token = localStorage.access_token;
            const res = await axios.post(unFollowURL,{
                access_token,
                userId
            })
            return res.data;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }  
    }

    async getFollows(){
        try {
            const access_token = localStorage.access_token;
            const res = await axios.post(getFollowURL,{
                access_token,
            })
            return res.data;
        } catch (error) {
            return {
                success:false,
                errorMessage:'网络错误'
            }
        }  
    }


}

export default new FriendManager();


```