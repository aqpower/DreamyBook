---
creation date: 2023-04-24 18:40 
---
 [[2023-04-24-星期一]]  #🌱发芽

axios. get () 是一个异步的函数，它会返回一个 promise 对象。如果你使用 await 处理 promise，那么你需要使用 try…catch 语句来捕获错误，例如：
```javascript
try {
  let response = await axios.get(url);
  // 处理响应数据
} catch (error) {
  // 处理错误
  console.log(error.response.data); // 这里可以获取错误的响应数据
}
```







