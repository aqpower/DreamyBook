---
creation date: 2023-05-08 16:24 
---
 [[2023-05-08-星期一]]  #🌱发芽 #Vue #前端 #状态管理
 # Pinia
符合直觉的 Vue.js 状态管理库
类型安全、可扩展性以及模块化设计。 甚至让你忘记正在使用的是一个状态库。
[Pinia | The intuitive store for Vue.js](https://pinia.vuejs.org/zh/)
Pinia 是 Vue 的专属状态管理库，它允许你跨组件或页面共享状态。
## 核心概念
### 定义 Store
Store 是用 `defineStore()` 定义的，它的第一个参数要求是一个**独一无二的**名字，第二个参数可接受两类值：Setup **函数**或 Option **对象**。
```js
export const useCounterStore = defineStore("counter", () => {
	const count = ref(0);
	function increment() {
	count.value++;
}
  
return { count, increment };
});
```
### State  ref ()
初始状态
### Getter  computed ()
类似于计算属性
 ### Action function ()
提供一些函数操作状态







