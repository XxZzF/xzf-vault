
```javascript
app.mount('#app')
```
`.mount()` 方法应该始终在整个应用配置和资源注册完成后被调用。同时请注意，不同于其他资源注册方法，它的返回值是根组件实例而非应用实例。


在网站上动态渲染任意 HTML 是非常危险的，因为这非常容易造成 [XSS 漏洞](https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%B6%B2%E7%AB%99%E6%8C%87%E4%BB%A4%E7%A2%BC)。请仅在内容安全可信时再使用 `v-html`，并且**永远不要**使用用户提供的 HTML 内容。


在 Vue 模板内，JavaScript 表达式可以被使用在如下场景上：
	1. 在文本插值中 (双大括号)
	2. 在任何 Vue 指令 (以 `v-` 开头的特殊 attribute) attribute 的值中


当使用 DOM 内嵌模板 (直接写在 HTML 文件里的模板) 时，我们需要避免在名称中使用大写字母，因为浏览器会强制将其转换为小写：
```Plain Text
<a :[someAttr]="value"> ... </a>
```


ref持有非原始值，将通过 [`reactive()`](https://cn.vuejs.org/guide/essentials/reactivity-fundamentals.html#reactive) 转换为响应式代理。


当你修改了响应式状态时，DOM 会被自动更新。但DOM 更新不是同步的。Vue 会在“next tick”更新周期中缓冲所有状态的修改，以确保不管你进行了多少次状态修改，每个组件都只会被更新一次。
要等待 DOM 更新完成后再执行额外的代码，可以使用 [nextTick()](https://cn.vuejs.org/api/general.html#nexttick) 全局 API：
``` js
import { nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // 现在 DOM 已经更新了
}
```


`reactive()` 将深层地转换对象：当访问嵌套对象时，它们也会被 `reactive()` 包装。
依靠深层响应性，响应式对象内的嵌套对象依然是代理：
```javascript
const proxy = reactive({})
const raw = {}
proxy.nested = raw
console.log(proxy.nested === raw) // false
```
