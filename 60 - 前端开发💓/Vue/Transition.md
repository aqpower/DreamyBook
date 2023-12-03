Vue 提供了内置组件，可以帮助你制作基于状态变化的过渡和动画。

[Transition | Vue.js](https://cn.vuejs.org/guide/built-ins/transition.html#css-transitions) 

## 官方对 `transition` 的定义

1.  自动嗅探目标元素是否使用了 CSS 过渡或动画，如果使用，会在合适的时机添加/移除 CSS 过渡 class。
2.  如果过渡组件设置了 [JavaScript 钩子函数](https://link.zhihu.com/?target=https%3A//vue.docschina.org/v2/guide/transitions.html%23JavaScript-Hooks)，这些钩子函数将在合适的时机调用。
3.  如果没有检测到 CSS 过渡/动画，并且也没有设置 JavaScript 钩子函数，插入和/或删除 DOM 的操作会在下一帧中立即执行

