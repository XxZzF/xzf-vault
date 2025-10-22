---
title: "vue的v-for循环key属性为什么不能用index？以上代码运行后，渲染出来是这样的： 然后我们给list的末尾加一 - 掘金"
source: "https://juejin.cn/post/7380179109291163686"
author:
published: 2024-06-15
created: 2025-05-08
description: "以上代码运行后，渲染出来是这样的： 然后我们给list的末尾加一个新元素： 加上新元素后，页面展示如下 其实一开始，是有一个虚拟dom列表的，list增加新元素后，又会生成一个新的虚拟dom列表 新旧"
tags:
  - "clippings"
---
![横幅](https://p6-piu.byteimg.com/tos-cn-i-8jisjyls3a/0bdb448b29434da59b1e21fcb970e11f~tplv-8jisjyls3a-image.image)

[凌晨三点钟](https://juejin.cn/user/3540906578547646/posts)

1,149 阅读4分钟

![](https://p9-piu.byteimg.com/tos-cn-i-8jisjyls3a/c676d36a15f248e8aedb339deddadb90~tplv-8jisjyls3a-image.image)

```html
const list = ref([
    {
      id:1,
      text:"aaa"
    },{
      id:2,
      text:"bbb"
    },{
      id:3,
      text:"ccc"
    }
])
```
```html
<ul>    
    <li v-for="(item, index) in list"  >{{ item.text }}</li>  
</ul>
```

以上代码运行后，渲染出来是这样的：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cb72986adcc5499a8295318d50a34f4e~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=298&h=139&s=2653&e=png&b=ffffff)

然后我们给list的末尾加一个新元素：

```php
list.value.push({  id:100,  text:"ddd"  })
```

加上新元素后，页面展示如下

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2ee938474d554a84bf7d3dea5df94527~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=301&h=143&s=3085&e=png&b=ffffff)

其实一开始，是有一个虚拟dom列表的，list增加新元素后，又会生成一个新的虚拟dom列表

新旧虚拟dom列表如下，左边的旧的，右边是新的

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc6763dad1a64247856ec8e89d8cccc7~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=471&h=167&s=13008&e=png&b=1f1f1f)

创建新虚拟dom列表后，与旧的虚拟dom比较，上图种，左边第0个与右边第0个是一样的，所以真实dom里的第0个li不变，

左边第1个与右边第1个是一样的，所以真实dom里的第1个li不变，

左边第2个与右边第2个是一样的，所以真实dom里的第2个li不变

右边有第三个，左边没有第三个，所以会在真实dom列表里新创建一个dom，里面显示ddd

接下来，看看另一种案例：

```php
const list = ref([    
    {      id:1,      text:"aaa"    },
    {      id:2,      text:"bbb"    },
    {      id:3,      text:"ccc"    }
])
```
```xml
<ul>    
    <li v-for="(item, index) in list"  >{{ item.text }}</li>  
</ul>
```

运行后页面如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cbd8b08523f7427cb3a2894505719b45~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=298&h=139&s=2653&e=png&b=ffffff)

然后，我们在list的index为1的位置插入一个新元素

```php
list.value.splice(1, 0 ,{  id:100,  text:"ddd"  })
```

然后页面显示如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99b8a1d4fe44471f9ff394dcb034c645~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=299&h=167&s=3161&e=png&b=ffffff)

往list里插入新元素后，新旧虚拟dom列表可以表示如下

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/da8936e0aff34ab6b81395327661f7bb~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=434&h=155&s=12860&e=png&b=1f1f1f)

然后vue会比较新旧列表

左边第0个与右边第0个是一样的，所以真实dom里的第0个li不变，

左边第1个与右边第1个不一样，所以真实dom里的第1个li会被修改，

左边第2个与右边第2个不一样，所以真实dom里的第2个li会被修改，

右边有第三个，左边没有第三个，所以会在真实dom列表里新创建一个dom，里面显示ccc

我们可以看到，往list里插入新元素后，真实dom里的第1个到第二个li都更新了，这样性能太差了

**接下来，再写一个案例，给v-for加key**

---

```php
const list = ref([    
    {      id:1,      text:"aaa"    },
    {      id:2,      text:"bbb"    },
    {      id:3,      text:"ccc"    }
])
```
```html
<ul>    
    <li v-for="(item, index) in list" :key="item.id" >{{ item.text }}</li>  
</ul>
```

注意，这里我给li加了个key属性，值为item.id

运行后页面是这样的：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d98c5847cc25495c8272ec65750e4e03~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=298&h=139&s=2653&e=png&b=ffffff)

然后，我们在list的index为1的位置插入一个新元素

```php
list.value.splice(1, 0 ,{  id:100,  text:"ddd"  })
```

然后页面显示如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a3e01857b09f40bcb0b56513301683b1~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=299&h=167&s=3161&e=png&b=ffffff)

新虚拟dom列表和旧虚拟dom列表可以表示如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2d138b20bd9244d79f527b9a85f27985~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=667&h=156&s=21969&e=png&b=1f1f1f)

这次，他们是有key属性的，在有key属性的情况下，新旧虚拟dom的比较过程是这样的：

key为0的旧虚拟dom和key为0的新虚拟dom一样，

key为1的旧虚拟dom和key为1的新虚拟dom一样，

key为2的旧虚拟dom和key为2的新虚拟dom一样

所以真实dom里的这三个li不会改变

新的虚拟dom列表里多了一个key为100的虚拟dom，并且在索引为1的位置

所以vue会在真实的dom里，索引为1的地方插入一个新的li，li里的内容是ddd

看到没，这次不像上一个案例里那样改变了2个li，这次只要增加一个li，原来的三个li完全不用改变，性能是不是比较好了

## 用index作为key

接下来，说说如果用index来作为key，会怎么样

```php
const list = ref([    
    {      id:1,      text:"aaa"    },
    {      id:2,      text:"bbb"    },
    {      id:3,      text:"ccc"    }
])
```
```html
<ul>    
    <li v-for="(item, index) in list" :key="index" >{{ item.text }}</li>  
</ul>
```

运行后页面是这样的：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5a772da74857476aa74976d9394ca300~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=298&h=139&s=2653&e=png&b=ffffff)

然后，我们在list的index为1的位置插入一个新元素

```php
list.value.splice(1, 0 ,{  id:100,  text:"ddd"  })
```

然后页面显示如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9d0fea1473094039b7e45cd3fef52548~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=299&h=167&s=3161&e=png&b=ffffff)

新虚拟dom列表和旧虚拟dom列表可以表示如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/35c649a54b60454d9c9e9e704a19473c~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=703&h=166&s=23315&e=png&b=1f1f1f)

他们是有key属性的，在有key属性的情况下，新旧虚拟dom的比较过程是这样的：

key为0的旧虚拟dom和key为0的新虚拟dom一样，所以真实dom里的第0个li不用改变

key为1的旧虚拟dom和key为1的新虚拟dom不一样，所以真实dom里的第1个li要改变

key为2的旧虚拟dom和key为2的新虚拟dom不一样，所以真实dom里的第2个li要改变

新的虚拟dom列表末尾多了一个key为3的虚拟dom，所以在真实li列表末尾创建一个新的li

你看，这样bbb和ccc这两个li都需要被改变，跟上一个案例比较一下，这次改变的真实dom比较多，所以不能用index来作为key的值

评论 4

![avatar](https://lf-web-assets.juejin.cn/obj/juejin-web/xitu_juejin_web/58aaf1326ac763d8a1054056f3b7f2ef.svg)

0 / 1000

即可发布评论！

[![两块两毛八的头像](https://p26-passport.byteacctimg.com/img/user-avatar/8101e7bcd42954349bb312739b5ee4df~70x70.awebp)](https://juejin.cn/user/976022056739623)

没吃过亏的都是用index，吃过一次亏，一辈子不会用index。

点赞

评论

[![凯撒大帝792的头像](https://p3-passport.byteacctimg.com/img/user-avatar/acd7ea2e3cb24f7afab6337ad02891fc~70x70.awebp)](https://juejin.cn/user/1502378870194064)

1

点赞

评论

[![宿老的头像](https://p6-passport.byteacctimg.com/img/mosaic-legacy/3793/3131589739~70x70.awebp)](https://juejin.cn/user/642581168662589)

可以用，只限于在展示的时候，不涉及增删改的情况下可以用

点赞

评论

查看全部 4 条评论

![](https://lf-web-assets.juejin.cn/obj/juejin-web/xitu_juejin_web/c12d6646efb2245fa4e88f0e1a9565b7.svg) 13

![](https://lf-web-assets.juejin.cn/obj/juejin-web/xitu_juejin_web/336af4d1fafabcca3b770c8ad7a50781.svg) 4

![](https://lf-web-assets.juejin.cn/obj/juejin-web/xitu_juejin_web/3d482c7a948bac826e155953b2a28a9e.svg) 收藏

![avatar](https://p9-passport.byteacctimg.com/img/user-avatar/ced58f3d426b8e535d435b1c891a1789~40x40.awebp)

为你推荐

- [vue不用index作key(diff算法)](https://juejin.cn/post/6997973393710514213 "vue不用index作key(diff算法)")
		[patchVnode、Vnode请看这篇。 示例 那么它的vnode也就是虚拟dom节点大概是： 假设更新以后，我们把子节点的顺序调换了一下： children部分是diff算法的重点。 首先响应式数](https://juejin.cn/post/6997973393710514213)
	- [
		九至久窒
		](https://juejin.cn/user/1161559545755853)
	- 412
	- 点赞
	- 评论
- [在vue和uniapp中实现虚拟列表组件，解决长列表性能问题](https://juejin.cn/post/7121301914205028359 "在vue和uniapp中实现虚拟列表组件，解决长列表性能问题")
		[展示长列表，DOM元素越多，性能也就越差。虚拟列表通过固定的dom元素，只展示可见部分的数据，可以解决dom元素过多带来的性能问题。](https://juejin.cn/post/7121301914205028359)
	- [
		Jewel105
		](https://juejin.cn/user/3118646567386007)
	- 12k
	- 51
	- 6
- [react渲染原理](https://juejin.cn/post/7012406822506070053 "react渲染原理")
		[渲染原理 渲染：生成用于显示的对象，以及将这些对象形成真实的DOM对象 React元素：React Element，通过React.createElement创建（语法糖：JSX） 例如： <div>](https://juejin.cn/post/7012406822506070053)
	- [
		MK麦客
		](https://juejin.cn/user/4476867080623565)
	- 297
	- 点赞
	- 评论
- [解读vue的diff算法原理](https://juejin.cn/post/6972407881790390308 "解读vue的diff算法原理")
		[1\. 当数据发生变化时，vue是怎么更新节点的？ 要知道渲染真实DOM的开销是很大的，比如有时候我们修改了某个数据，如果直接渲染到真实dom上会引起整个dom树的重绘和重排，有没有可能我们只更新我们修](https://juejin.cn/post/6972407881790390308)
	- [
		ZXM
		](https://juejin.cn/user/2154698521982014)
	- 1.8k
	- 2
	- 评论
	![解读vue的diff算法原理](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f654930b751f4fb79188319f6fb17eec~tplv-k3u1fbpfcp-jj:135:90:0:0:q75.avis)
- [Vue2.0 Diff算法](https://juejin.cn/post/6996234260092813348 "Vue2.0 Diff算法")
		[一、前言 1、什么是虚拟dom？ 虚拟dom就是真实DOM以对象的形式模拟成树形的一种结构 我们看一下真实DOM与虚拟DOM长啥什么样？ 真实Dom 对应的虚拟DOM 2、当数据发生变化时，vue是怎](https://juejin.cn/post/6996234260092813348)
	- [
		小鱼丸子
		](https://juejin.cn/user/1415826707324557)
	- 267
	- 点赞
	- 评论
- [miniVue3的简单实现-虚拟dom对比](https://juejin.cn/post/6940190146394488869 "miniVue3的简单实现-虚拟dom对比")
		[2\. 执行patchChildren进行子元素dom对比的流程 3. patchKeyChildren进行新旧虚拟dom的子元素都为多个的复杂情况 头部不相同后，从尾部开始对比，尾部对比如果相同e1--、e2--。前后虚拟dom不同或i大于e1或e2后结束尾部对比 4. 新老虚…](https://juejin.cn/post/6940190146394488869)
	- [
		前端底层小码农
		](https://juejin.cn/user/430664288571213)
	- 430
	- 1
	- 评论
- [一文搞定虚拟列表【定高】与【不定高】](https://juejin.cn/post/7490973048242454578 "一文搞定虚拟列表【定高】与【不定高】")
		[前言 加载的列表数据过多，会导致页面卡住甚至崩溃，这时候就需要用到虚拟列表技术 实现核心 核心思想是：仅渲染可视区域内（及其附近）的列表项，即不管列表有多少条数据，只取指定数量的项渲染，比如说15条。](https://juejin.cn/post/7490973048242454578)
	- [
		简言856
		](https://juejin.cn/user/3076897747007828)
	- 295
	- 6
	- 评论
	![一文搞定虚拟列表【定高】与【不定高】](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/005722e3aa534333ba9668131291b60d~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg566A6KiAODU2:q75.awebp?rk3s=f64ab15b&x-expires=1747321347&x-signature=0bNaTo05UYGAOetmFM%2Fekgyd1u8%3D)
- [Vue3 Dom Diff算法核心逻辑解析](https://juejin.cn/post/7416659736986566675 "Vue3 Dom Diff算法核心逻辑解析")
		[一、什么是虚拟DOM 虚拟 DOM (Virtual DOM，简称 VDOM) 是一种编程概念，意为将目标所需的 UI 通过数据结构“虚拟”地表示出来，保存在内存中，然后将真实的 DOM 与之保持同步](https://juejin.cn/post/7416659736986566675)
	- [
		季礼祥
		](https://juejin.cn/user/581008310927918)
	- 150
	- 3
	- 评论
	![Vue3 Dom Diff算法核心逻辑解析](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/8f2e1da577d340e089071c65b8a1dea1~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg5a2j56S856Wl:q75.awebp?rk3s=f64ab15b&x-expires=1747321347&x-signature=A5NpS8OXn7r3Tca%2FTTBjRLfBQdo%3D)
- [疏通源码详解React DOM-diff](https://juejin.cn/post/7204285137046782012 "疏通源码详解React DOM-diff")
		[前言 这篇文章帮助大家梳理一下React中的dom-diff。在React中，根据新的虚拟DOM的不同，分为单节点（指的是同层级只有一个子节点），和多节点（指的是同层级有多个子节点），分别是在reco](https://juejin.cn/post/7204285137046782012)
	- [
		朔望
		](https://juejin.cn/user/1341853105918551)
	- 1.2k
	- 4
	- 2
	![疏通源码详解React DOM-diff](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/447dbc87d61447368d48b0a97ad7b163~tplv-k3u1fbpfcp-jj:135:90:0:0:q75.avis)
- [React/虚拟DOM及Diff算法](https://juejin.cn/post/7159017546031562766 "React/虚拟DOM及Diff算法")
		[Virtual DOM 是一种编程概念。在这个概念里， UI 以一种理想化的，或者说“虚拟的”表现形式被保存于内存中，并通过如 ReactDOM 等类库使之与“真实的” DOM 同步。](https://juejin.cn/post/7159017546031562766)
	- [
		二把刀前端团队
		](https://juejin.cn/user/1099167359318759)
	- 1.3k
	- 4
	- 1
	![React/虚拟DOM及Diff算法](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ca73e765ddcf44fda69537337f3060c1~tplv-k3u1fbpfcp-jj:135:90:0:0:q75.avis)
- [Vue diff算法源码浅析](https://juejin.cn/post/6989603389546397709 "Vue diff算法源码浅析")
		[在 vue 中会维护一个和 DOM 节点对应的 vnode 对象 vnode 的 children 数组中对应子节点的 vnode 对象，所以在 vue 中通过 vnode 和真实的 DOM 树进行映](https://juejin.cn/post/6989603389546397709)
	- [
		ade想吃油麦菜
		](https://juejin.cn/user/1091131683906254)
	- 924
	- 3
	- 评论
- [《Vue.js设计与实现》第9章-简单Diff算法](https://juejin.cn/post/7354021064030978084 "《Vue.js设计与实现》第9章-简单Diff算法")
		[9-1 减少DOM操作的性能开销 按照之前的做法，当更新子节点时，需要执行6次DOM操作： 卸载所有旧节点，需要3次DOM删除操作； 挂载所有新节点，需要3次DOM添加操作。 所以更理想的更新方式呢？](https://juejin.cn/post/7354021064030978084)
	- [
		小蜗牛de代码实验室
		](https://juejin.cn/user/817692382332951)
	- 1.0k
	- 点赞
	- 评论
- [Vue中的key起什么作用？](https://juejin.cn/post/7222210580320010299 "Vue中的key起什么作用？")
		[在Vue中，我们通常会使用\`v-for\`指令来渲染列表。当数据源发生改变时，Vue会尽可能高效地更新DOM元素以反映这些变化。然而，在某些情况下，Vue无法跟踪并处理到底哪些元素发生了变化。](https://juejin.cn/post/7222210580320010299)
	- [
		舟鱼
		](https://juejin.cn/user/1799217731347255)
	- 1.0k
	- 1
	- 评论
- [如何通过虚拟DOM更新页面？](https://juejin.cn/post/7139812321177632776 "如何通过虚拟DOM更新页面？")
		[会被解析成: createVNode创建虚拟DOM, 通过type props children 等属性描述整个节点: 更新: 更新以后就不是首次渲染了, 如何判断? 组件的子元素是由HTML标签和组](https://juejin.cn/post/7139812321177632776)
	- [
		水门溪风
		](https://juejin.cn/user/729731452636263)
	- 798
	- 点赞
	- 评论
- [Vue-基础入门(上)--Part.2(5-7)](https://juejin.cn/post/7026750282050584612 "Vue-基础入门(上)--Part.2(5-7)")
		[样式绑定语法、条件渲染、列表循环渲染(>ω･\* )ﾉ(づ｡◕ᴗᴗ◕｡)づφ(>ω<\*)(｀・ω・´)](https://juejin.cn/post/7026750282050584612)
	- [
		不准吃饭
		](https://juejin.cn/user/1003235289345703)
	- 166
	- 点赞
	- 评论
	![Vue-基础入门(上)--Part.2(5-7)](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1588cd17fd354dfa89762bcdfd9520f9~tplv-k3u1fbpfcp-jj:135:90:0:0:q75.avis)

APP内打开

![yoyo](https://lf-web-assets.juejin.cn/obj/juejin-web/xitu_juejin_web/img/yoyo.3df25da.png)

## 登录掘金领取礼包

更多登录后权益等你解锁