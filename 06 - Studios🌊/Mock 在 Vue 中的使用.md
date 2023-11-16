---
creation date: 2023-05-08 14:12 
---
 [[2023-05-08-星期一]]  #🌱发芽 #Vue #Mock #前端 

## 使用案例
```JavaScript
// main.js
import './mock/mock.js'

// mock.js
import Mock from "mockjs";
import { mockUserInfo, mockLogin } from "./response/user";

/**
* Mock拦截api/v1/user/{id}的Get请求，并返回mockUserInfo提供的数据
*/
Mock.mock(/\/api\/v1\/user\/\d+/, "get", mockUserInfo);  


/**
* Mock拦截api/v1/user/login的Post请求，并返回mockLogin提供的数据
*/
Mock.mock('/api/v1/user/login','post',mockLogin)

export default Mock;

// user.js
import Mock from "mockjs";
import { Random } from "mockjs";
  
// 生成随机用户信息
export const mockUserInfo = (options) => {
const template = {
name: "@cname",
"age|18-30": 0,
gender: "@gender",
avatar: Random.image("200x200", "#50B347", "#FFF", "Mock.js"),
};
return Mock.mock(template);
};
 
// 一个模拟登录请求并返回模拟数据对象的函数
// TODO 根据options中的code 区分登录状态
export const mockLogin = (options) => {
const template = {
token: "@string",
user_id: "@integer(1, 100)",
name: "@cname",
storeid: "@integer(1, 100)",
position: "@city",
};

return Mock.mock(template)
}
```

## 注意事项
import 后括号的使用规范!!！









