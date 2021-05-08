---
title: vue知识点笔记
date: 2020-05-27 14:20:33
tags:
---
声明：本篇文章纯属笔记性文章，非整体原创，是对vue知识的整理，对自己有很大帮助才分享出来！覆盖了vue3.0的一些知识点。



### 1. 说说你对MVVM的理解

* Model-View-ViewModel的缩写，Model代表数据模型，View代表UI组件,ViewModel将Model和View关联起来
* 数据会绑定到viewModel层并自动将数据渲染到页面中，视图变化的时候会通知viewModel层更新数据



### 2. Vue2.x响应式数据/双向绑定原理

* Vue 数据双向绑定主要是指：数据变化更新视图，视图变化更新数据。其中，View变化更新Data，可以通过事件监听的方式来实现，所以 Vue数据双向绑定的工作主要是如何根据Data变化更新View。
* 简述：
 1.当你把一个普通的 JavaScript 对象传入 Vue 实例作为 data 选项，Vue 将遍历此对象所有的 property，并使用 Object.defineProperty 把这些 property 全部转为 getter/setter。
 2.这些 getter/setter 对用户来说是不可见的，但是在内部它们让 Vue 能够追踪依赖，在 property 被访问和修改时通知变更。
 3.每个组件实例都对应一个 watcher 实例，它会在组件渲染的过程中把“接触”过的数据 property 记录为依赖。之后当依赖项的 setter 触发时，会通知 watcher，从而使它关联的组件重新渲染。
* 深入理解：
 1.监听器 Observer：对数据对象进行遍历，包括子属性对象的属性，利用 Object.defineProperty() 对属性都加上 setter 和 getter。这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到了数据变化。
 2.解析器 Compile：解析 Vue 模板指令，将模板中的变量都替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，调用更新函数进行数据更新。
 3.订阅者 Watcher：Watcher 订阅者是 Observer 和 Compile 之间通信的桥梁 ，主要的任务是订阅 Observer 中的属性值变化的消息，当收到属性值变化的消息时，触发解析器 Compile 中对应的更新函数。每个组件实例都有相应的 watcher 实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 setter 被调用时，会通知 watcher 重新计算，从而致使它关联的组件得以更新——这是一个典型的观察者模式
 4.订阅器 Dep：订阅器采用 发布-订阅 设计模式，用来收集订阅者 Watcher，对监听器 Observer 和 订阅者 Watcher 进行统一管理。



### 3. 你知道Vue3.x响应式数据原理吗？

* Vue3.x改用Proxy替代Object.defineProperty。
* 因为Proxy可以直接监听对象和数组的变化，并且有多达13种拦截方法。并且作为新标准将受到浏览器厂商重点持续的性能优化。
* Proxy只会代理对象的第一层，Vue3是怎样处理这个问题的呢？
    1.判断当前Reflect.get的返回值是否为Object，如果是则再通过reactive方法做代理， 这样就实现了深度观测。
    2.监测数组的时候可能触发多次get/set，那么如何防止触发多次呢？我们可以判断key是否为当前被代理对象target自身属性，也可以判断旧值与新值是否相等，只有满足以上两个条件之一时，才有可能执行trigger。



### 4. Proxy 与 Object.defineProperty 优劣对比    

* Proxy 的优势如下:
    Proxy 可以直接监听对象而非属性；
* Proxy 可以直接监听数组的变化；
    1.Proxy 有多达 13 种拦截方法,不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的；
    2.Proxy 返回的是一个新对象,我们可以只操作新的对象达到目的,而 Object.defineProperty 只能遍历对象属性直接修改；
    3.Proxy 作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利；
* Object.defineProperty 的优势如下:     
    1.兼容性好，支持 IE9，而 Proxy 的存在浏览器兼容性问题,而且无法用 polyfill 磨平，因此 Vue 的作者才声明需要等到下个大版本( 3.0 )才能用 Proxy 重写。


Vuex 篇

### 5. Vuex 是什么？ 

* Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。每一个 Vuex 应用的核心就是 store（仓库）。“store” 基本上就是一个容器，它包含着你的应用中大部分的状态 ( state )。
* 运用到了js设计模式中的单例模式，单例模式想要做到的是，不管我们尝试去创建多少次，它都只给你返回第一次所创建的那唯一的一个实例。
    1.Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
    2.改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化。
* 运用到了js设计模式中的单例模式，单例模式想要做到的是，不管我们尝试去创建多少次，它都只给你返回第一次所创建的那唯一的一个实例。
* Vuex 使用单一状态树，用一个对象就包含了全部的应用层级状态。至此它便作为一个“唯一数据源 (SSOT)”而存在。这也意味着，每个应用将仅仅包含一个 store 实例。单一状态树让我们能够直接地定位任一特定的状态片段，在调试的过程中也能轻易地取得整个当前应用状态的快照。——Vuex官方文档
* 主要包括以下几个模块：
    1.State：定义了应用状态的数据结构，可以在这里设置默认的初始状态。
    2.Getter：允许组件从 Store 中获取数据，mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性。
    3.Mutation：是唯一更改 store 中状态的方法，且必须是同步函数。
    4.Action：用于提交 mutation，而不是直接变更状态，可以包含任意异步操作。
    5.Module：允许将单一的 Store 拆分为多个 store 且同时保存在单一的状态树中。



### 6. 什么情况下使用 Vuex？

* 如果应用够简单，最好不要使用 Vuex，一个简单的 store 模式即可
* 需要构建一个中大型单页应用时，使用Vuex能更好地在组件外部管理状态



### 7. Vuex和单纯的全局对象有什么区别？

* Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
* 不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。



### 8. 为什么 Vuex 的 mutation 中不能做异步操作？

* Vuex中所有的状态更新的唯一途径都是mutation，异步操作通过 Action 来提交 mutation实现，这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。
* 每个mutation执行完成后都会对应到一个新的状态变更，这样devtools就可以打个快照存下来，然后就可以实现 time-travel 了。如果mutation支持异步操作，就没有办法知道状态是何时更新的，无法很好的进行状态的追踪，给调试带来困难。



### 9. 新增：vuex的action有返回值吗？返回的是什么？

* store.dispatch 可以处理被触发的 action 的处理函数返回的 Promise，并且 store.dispatch 仍旧返回 Promise
* Action 通常是异步的，要知道 action 什么时候结束或者组合多个 action以处理更加复杂的异步流程，可以通过定义action时返回一个promise对象，就可以在派发action的时候就可以通过处理返回的 Promise处理异步流程
* 一个 store.dispatch 在不同模块中可以触发多个 action 函数。在这种情况下，只有当所有触发函数完成后，返回的 Promise 才会执行。



### 10. 新增：为什么不直接分发mutation,而要通过分发action之后提交 mutation变更状态

* mutation 必须同步执行，我们可以在 action 内部执行异步操作
* 可以进行一系列的异步操作，并且通过提交 mutation 来记录 action 产生的副作用（即状态变更）



常规篇

### 11. computed 和 watch 的区别和运用的场景？ 

* computed：是计算属性，依赖其它属性值，并且 computed 的值有缓存，只有它依赖的属性值发生改变，下一次获取 computed 的值时才会重新计算 computed 的值；
* watch：没有缓存性，更多的是「观察」的作用，类似于某些数据的监听回调 ，每当监听的数据变化时都会执行回调进行后续操作；当我们需要深度监听对象中的属性时，可以打开deep：true选项，这样便会对对象中的每一项进行监听
* 运用场景：
    1.当我们需要进行数值计算，并且依赖于其它数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算；
    2.当我们需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用watch选项允许我们执行异步操作 ( 访问一个 API )，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。



### 12. Vue2.x组件通信有哪些方式？

* 父子组件通信
    1.事件机制(**父->子props,子->父 $on、$emit)
    2.获取父子组件实例 $parent、$children
    3.Ref 获取实例的方式调用组件的属性或者方法
    4.Provide、inject (不推荐使用，组件库时很常用)     
* 兄弟组件通信    
    1.Vuex
    2.eventBus 这种方法通过一个空的 Vue实例作为中央事件总线（事件中心），用它来触发事件和监听事件，从而实现任何组件间的通信，包括父子、隔代、兄弟组件
* 跨级组件通信
    1.Vuex
    2.$attrs、$listeners    
    3.Provide、inject



### 13. 说一下v-if和v-show的区别

* 当条件不成立时，v-if不会渲染DOM元素，v-show操作的是样式(display)，切换当前DOM的显示和隐藏。
* v-if 适用于在运行时很少改变条件，不需要频繁切换条件的场景；
* v-if 是真正的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建；也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
* v-show 则适用于需要非常频繁切换条件的场景
* v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 的 “display” 属性进行切换。



### 14. 为什么 v-for 和 v-if 不建议用在一起

* 当 v-for 和 v-if 处于同一个节点时，v-for 的优先级比 v-if 更高，这意味着 v-if 将分别重复运行于每个 v-for 循环中。如果要遍历的数组很大，而真正要展示的数据很少时，这将造成很大的性能浪费 
* 这种场景建议使用 computed，先对数据进行过滤



### 15. 组件中的data为什么是一个函数？

* 一个组件被复用多次的话，也就会创建多个实例。本质上，这些实例用的都是同一个构造函数。
* 如果data是对象的话，对象属于引用类型，会影响到所有的实例。所以为了保证组件不同的实例之间data不冲突，data必须是一个函数。



### 16. 子组件为什么不可以修改父组件传递的Prop？/怎么理解vue的单向数据流？

* Vue提倡单向数据流,即父级props的更新会流向子组件,但是反过来则不行。
* 这是为了防止意外的改变父组件状态，使得应用的数据流变得难以理解。
* 如果破坏了单向数据流，当应用复杂时，debug 的成本会非常高。



### 17. v-model是如何实现双向绑定的？

* v-model是用来在表单控件或者组件上创建双向绑定的
* 他的本质是v-bind和v-on的语法糖
* 在一个组件上使用v-model，默认会为组件绑定名为value的prop和名为input的事件



### 18. nextTick的实现原理是什么？

* 在下次 DOM 更新循环结束之后执行延迟回调，在修改数据之后立即使用 nextTick 来获取更新后的 DOM。
* nextTick主要使用了宏任务和微任务。
* 根据执行环境分别尝试采用Promise、MutationObserver、setImmediate，如果以上都不行则采用setTimeout定义了一个异步方法，多次调用nextTick会将方法存入队列中，通过这个异步方法清空当前队列。



### 19.Vue不能检测数组的哪些变动？Vue 怎么用 vm.$set() 解决对象新增属性不能响应的问题 ？    

* Vue 不能检测以下数组的变动：
    1.第一类问题
    // 法一：Vue.set Vue.set(vm.items, indexOfItem, newValue) 
    // 法二：Array.prototype.splice vm.items.splice(indexOfItem, 1, newValue)
    2.第二类问题，可使用 splice：
    vm.items.splice(newLength)     
    3.当你利用索引直接接收一个数组项时，例如：vm.items[indexOfItem] = newValue    
    4.当你修改数组的长度时，例如：vm.items.length = newLength
* vm.$set 的实现原理是：     
    1.如果目标是数组，直接使用数组的 splice 方法触发响应式；
    2.如果目标是对象，会先判读属性是否存在、对象是否是响应式，最终如果要对属性进行响应式处理，则是通过调用 defineReactive 方法进行响应式处理（ defineReactive 方法就是 Vue 在初始化对象时，给对象属性采用 Object.defineProperty 动态添加 getter 和 setter 的功能所调用的方法）



### 20.Vue事件绑定原理是什么？

* 原生事件绑定是通过addEventListener绑定给真实元素的，组件事件绑定是通过Vue自定义的$on实现的。



### 21.说一下虚拟Dom以及key属性的作用

* 由于在浏览器中操作DOM是很昂贵的。频繁的操作DOM，会产生一定的性能问题。这就是虚拟Dom的产生原因。
* Virtual DOM本质就是用一个原生的JS对象去描述一个DOM节点。是对真实DOM的一层抽象。(也就是源码中的VNode类，它定义在src/core/vdom/vnode.js中。)
* 虚拟 DOM 的实现原理主要包括以下 3 部分：
    1.用 JavaScript 对象模拟真实 DOM 树，对真实 DOM 进行抽象；
    2.diff 算法 — 比较两棵虚拟 DOM 树的差异；
    3.pach 算法 — 将两个虚拟 DOM 对象的差异应用到真正的 DOM 树。
* key 是为 Vue 中 vnode 的唯一标记，通过这个 key，我们的 diff 操作可以更准确、更快速
    1.更准确：因为带 key 就不是就地复用了，在 sameNode 函数a.key === b.key对比中可以避免就地复用的情况。所以会更加准确。
    2.更快速：利用 key 的唯一性生成 map 对象来获取对应节点，比遍历方式更快    



### 22.为什么不建议用index作为key?

* 不建议 用index 作为 key，和没写基本上没区别，因为不管你数组的顺序怎么颠倒，index 都是 0, 1, 2 这样排列，导致 Vue 会复用错误的旧子节点，做很多额外的工作



生命周期篇



### 23.说一下你对Vue的生命周期的理解

* beforeCreate、created、beforeMount、mounted、beforeUpdate、updated、beforeDestroy、destroyed。
* keep-alive 有自己独立的钩子函数 activated 和 deactivated。



### 24.Vue中组件生命周期调用顺序是什么样的？

* 组件的调用顺序都是先父后子,渲染完成的顺序是先子后父。
* 组件的销毁操作是先父后子，销毁完成的顺序是先子后父。



### 25.在什么阶段才能访问操作DOM？

* 在钩子函数 mounted 被调用前，Vue 已经将编译好的模板挂载到页面上，所以在 mounted 中可以访问操作 DOM。



### 26.你的接口请求一般放在哪个生命周期中？

* 可以在钩子函数 created、beforeMount、mounted 中进行调用，因为在这三个钩子函数中，data 已经创建，可以将服务端端返回的数据进行赋值。
* 但是推荐在 created 钩子函数中调用异步请求，因为在 created 钩子函数中调用异步请求有以下优点：
    1.能更快获取到服务端数据，减少页面loading 时间；
    2.ssr不支持 beforeMount 、mounted 钩子函数，所以放在 created 中有助于一致性；



路由篇



### 27.vue路由hash模式和history模式实现原理分别是什么，他们的区别是什么？ 

* hash 模式：
    1.后面 hash 值的变化，不会导致浏览器向服务器发出请求，浏览器不发出请求，就不会刷新页面
    2.通过监听 hashchange 事件可以知道 hash 发生了哪些变化，然后根据 hash 变化来实现更新页面部分内容的操作。
* history 模式：
    1.history 模式的实现，主要是 HTML5 标准发布的两个 API，pushState 和 replaceState，这两个 API 可以在改变 url，但是不会发送请求。这样就可以监听 url 变化来实现更新页面部分内容的操作
* 区别：
    1.url 展示上，hash 模式有“#”，history 模式没有
    2.刷新页面时，hash 模式可以正常加载到 hash 值对应的页面，而 history 没有处理的话，会返回 404，一般需要后端将所有页面都配置重定向到首页路由
    3.兼容性，hash 可以支持低版本浏览器和 IE。



### 28.路由懒加载是什么意思？如何实现路由懒加载？

* 路由懒加载的含义：把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件
* 实现：结合 Vue 的异步组件和 Webpack 的代码分割功能 (可以将异步组件定义为返回一个 Promise 的工厂函数 \(该函数返回的 Promise)
* 结合这两者，这就是如何定义一个能够被 Webpack 自动代码分割的异步组件  const Foo = () => import('./Foo.vue') const router = new VueRouter({ routes: [ { path: '/foo', component: Foo } ]})
* 使用命名 chunk，和webpack中的魔法注释就可以把某个路由下的所有组件都打包在同个异步块 (chunk) 中 chunkconst Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue') 



### 29.Vue-router 导航守卫有哪些 

* 全局前置/钩子：beforeEach、beforeResolve、afterEach
* 路由独享的守卫：beforeEnter
* 组件内的守卫：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave



进阶篇



### 30.说说vue和react的异同

* 相同点
    1.使用 Virtual DOM
    2.提供了响应式 (Reactive) 和组件化 (Composable) 的视图组件。
    3.将注意力集中保持在核心库，而将其他功能如路由和全局状态管理交给相关的库。
* 不同点
    1.在 React 应用中，当某个组件的状态发生变化时，它会以该组件为根，重新渲染整个组件子树（除非使用PureComponent/shouldComponentUpdate），在 Vue 应用中，组件的依赖是在渲染过程中自动追踪的，所以系统能精确知晓哪个组件确实需要被重渲染    
    2.在 React 中，一切都是 JavaScript。不仅仅是 HTML 可以用 JSX 来表达，现在的潮流也越来越多地将 CSS 也纳入到 JavaScript 中来处理
    3.Vue 的路由库和状态管理库都是由官方维护支持且与核心库同步更新的。React 则是选择把这些问题交给社区维护，因此创建了一个更分散的生态系统，所以有更丰富的生态系统
    4.Vue 提供了CLI 脚手架，能让你通过交互式的脚手架引导非常容易地构建项目。你甚至可以使用它快速开发组件的原型。React 在这方面也提供了create-react-app，但是现在还存在一些局限性
    5.React Native 能使你用相同的组件模型编写有本地渲染能力的 APP，Vue 和Weex会进行官方合作，Weex 是阿里巴巴发起的跨平台用户界面开发框架，同时也正在 Apache 基金会进行项目孵化，另一个选择是NativeScript-Vue，一个用 Vue.js 构建完全原生应用的NativeScript插件



### 31.什么是 mixin ？

* Mixin 使我们能够为 Vue 组件编写可插拔和可重用的功能。    
* 如果你希望再多个组件之间重用一组组件选项，例如生命周期 hook、 方法等，则可以将其编写为 mixin，并在组件中简单的引用它。
* 然后将 mixin 的内容合并到组件中。如果你要在 mixin 中定义生命周期 hook，那么它在执行时将优化于组件自已的 hook。



### 32.在 Vue 实例中编写生命周期 hook 或其他 option/properties 时，为什么不使用箭头函数 ？

* 箭头函数自已没有定义 this 上下文中。
* 当你在 Vue 程序中使用箭头函数 ( => ) 时，this 关键字病不会绑定到 Vue 实例，因此会引发错误。所以强烈建议改用标准函数声明。



### 33.Vue模版编译原理知道吗，能简单说一下吗？

简单说，Vue的编译过程就是将template转化为render函数的过程。会经历以下阶段（生成AST树/优化/codegen）：
* 首先解析模版，生成AST语法树(一种用JavaScript对象的形式来描述整个模板)。使用大量的正则表达式对模板进行解析，遇到标签、文本的时候都会执行对应的钩子进行相关处理。
* Vue的数据是响应式的，但其实模板中并不是所有的数据都是响应式的。有一些数据首次渲染后就不会再变化，对应的DOM也不会变化。那么优化过程就是深度遍历AST树，按照相关条件对树节点进行标记。这些被标记的节点(静态节点)我们就可以跳过对它们的比对，对运行时的模板起到很大的优化作用。
* 编译的最后一步是将优化后的AST树转换为可执行的代码。



### 34.diff算法说一下

* 同级比较，再比较子节点
* 先判断一方有子节点一方没有子节点的情况(如果新的children没有子节点，将旧的子节点移除)
* 比较都有子节点的情况(核心diff)
* 递归比较子节点



### 35.说说你对keep-alive组件的了解

* keep-alive 是 Vue 内置的一个组件，可以使被包含的组件保留状态，避免重新渲染 ，其有以下特性：
    1.一般结合路由和动态组件一起使用，用于缓存组件；
    2.提供 include 和 exclude 属性，两者都支持字符串或正则表达式， include 表示只有名称匹配的组件会被缓存，exclude 表示任何名称匹配的组件都不会被缓存 ，其中 exclude 的优先级比 include 高；
    3.对应两个钩子函数 activated 和 deactivated ，当组件被激活时，触发钩子函数 activated，当组件被移除时，触发钩子函数 deactivated。

```js
<keep-alive>
  <component>
    <!-- 该组件将被缓存！ -->
  </component>
</keep-alive>
如果只想 router-view 里面某个组件被缓存


export default [
  {
    path: '/',
    name: 'home',
    component: Home,
    meta: {
      keepAlive: true // 需要被缓存   
    }
  }, {
    path: '/:id',
    name: 'edit',
    component: Edit,
    meta: {
      keepAlive: false // 不需要被缓存
    }
  }
]
<keep-alive>
    <router-view v-if="$route.meta.keepAlive">
        <!-- 这里是会被缓存的视图组件，比如 Home！ -->
    </router-view>
</keep-alive>
 
<router-view v-if="!$route.meta.keepAlive">
    <!-- 这里是不被缓存的视图组件，比如 Edit！ -->
</router-view>
```





### 36.说说你对SSR的了解
* SSR也就是服务端渲染，也就是将Vue在客户端把标签渲染成HTML的工作放在服务端完成，然后再把html直接返回给客户端
* SSR的优势
    1.更好的SEO
    2.首屏加载速度更快
* SSR的缺点
    1.开发条件会受到限制，服务器端渲染只支持beforeCreate和created两个钩子
    2.当我们需要一些外部扩展库时需要特殊处理，服务端渲染应用程序也需要处于Node.js的运行环境
    3.更多的服务端负载    



### 37.你都做过哪些Vue的性能优化？    

* 编码阶段
    1.尽量减少data中的数据，data中的数据都会增加getter和setter，会收集对应的watcher
    2.v-if和v-for不能连用
    3.如果需要使用v-for给每项元素绑定事件时使用事件代理
    4.SPA 页面采用keep-alive缓存组件
    5.在更多的情况下，使用v-if替代v-show
    6.key保证唯一
    7.使用路由懒加载、异步组件
    8.防抖、节流
    9.第三方模块按需导入
    10.长列表滚动到可视区域动态加载
    11.图片懒加载
* SEO优化
    1.预渲染
    2.服务端渲染SSR    
* 打包优化
    1.压缩代码
    2.Tree Shaking/Scope Hoisting
    3.使用cdn加载第三方模块
    4.多线程打包happypack
    5.splitChunks抽离公共文件
    6.sourceMap优化
* 用户体验
    8.骨架屏
    9.PWA
    10.还可以使用缓存(客户端缓存、服务端缓存)优化、服务端开启gzip压缩等。



### 38.vue2.x中如何监测数组变化？

* 使用了函数劫持的方式，重写了数组的方法，Vue将data中的数组进行了原型链重写，指向了自己定义的数组原型方法，当调用数组api时，可以通知依赖更新。
* 如果数组中包含着引用类型，会对数组中的引用类型再次递归遍历进行监控。这样就实现了监测数组变化。



### 39.说说你对 SPA 单页面的理解，它的优缺点分别是什么？

* SPA（ single-page application ）仅在 Web 页面初始化时加载相应的 HTML、JavaScript 和 CSS。一旦页面加载完成，SPA 不会因为用户的操作而进行页面的重新加载或跳转；取而代之的是利用路由机制实现 HTML 内容的变换，UI 与用户的交互，避免页面的重新加载。
* 优点：
    1.用户体验好、快，内容的改变不需要重新加载整个页面，避免了不必要的跳转和重复渲染；
    2.基于上面一点，SPA 相对对服务器压力小；
    3.前后端职责分离，架构清晰，前端进行交互逻辑，后端负责数据处理；
* 缺点：
    1.初次加载耗时多：为实现单页 Web 应用功能及显示效果，需要在加载页面的时候将 JavaScript、CSS 统一加载，部分页面按需加载；
    2.前进后退路由管理：由于单页应用在一个页面中显示所有的内容，所以不能使用浏览器的前进后退功能，所有的页面切换需要自己建立堆栈管理；
    3.SEO 难度较大：由于所有的内容都在一个页面中动态替换显示，所以在 SEO 上其有着天然的弱势。

### 40.对于即将到来的 vue3.0 特性你有什么了解的吗？

* 监测机制的改变
    1.vue3.0将带来基于代理 Proxy的 observer 实现，提供全语言覆盖的反应性跟踪。
    2.消除了 Vue 2 当中基于 Object.defineProperty 的实现所存在的很多限制：
* 只能监测属性，不能监测对象
    1.检测属性的添加和删除；
    2.检测数组索引和长度的变更；
    3.支持 Map、Set、WeakMap 和 WeakSet。
* 模板
    1.模板方面没有大的变更，只改了作用域插槽，2.x 的机制导致作用域插槽变了，父组件会重新渲染，而 3.0 把作用域插槽改成了函数的方式，这样只会影响子组件的重新渲染，提升了渲染的性能。
    2.同时，对于 render 函数的方面，vue3.0 也会进行一系列更改来方便习惯直接使用 api 来生成 vdom 。
* 对象式的组件声明方式
    1.vue2.x 中的组件是通过声明的方式传入一系列 option，和 TypeScript 的结合需要通过一些装饰器的方式来做，虽然能实现功能，但是比较麻烦。
    2.vue3.0 修改了组件的声明方式，改成了类式的写法，这样使得和 TypeScript 的结合变得很容易
* 其它方面的更改
    1.支持自定义渲染器，从而使得 weex 可以通过自定义渲染器的方式来扩展，而不是直接 fork 源码来改的方式。
    2.支持 Fragment（多个根节点）和 Protal（在 dom 其他部分渲染组建内容）组件，针对一些特殊的场景做了处理。
    3.基于 tree shaking 优化，提供了更多的内置功能。




