---
title: React知识点笔记
date: 2021-03-10 16:02:30
tags:
---

声明：本篇文章纯属笔记性文章，非整体原创，是对React知识的整理，对自己有很大帮助才分享出来！


### 1、setState 是异步还是同步？

1.合成事件中是异步

2.钩子函数中的是异步

3.原生事件中是同步

4.setTimeout中是同步



### 2、聊聊 react@16.4 + 的生命周期

相关连接：[React 生命周期 我对 React v16.4 生命周期的理解](https://zhuanlan.zhihu.com/p/150929928)

废弃三个旧的生命周期函数
React 在 V16.3 版本中，为下面三个生命周期函数加上了 UNSAFE：

UNSAFE_componentWillMount
UNSAFE_componentWillReceiveProps
UNSAFE_componentWillUpdate

标题中的废弃不是指真的废弃，只是不建议继续使用，并表示在 V17.0 版本中正式删除。先来说说 React 为什么要这么做。

主要是这些生命周期方法经常被误用和滥用。并且在 React V16.0 之前，React 是同步渲染的，而在 V16.0 之后 React 更新了其渲染机制，
是通过异步的方式进行渲染的，在 render 函数之前的所有函数都有可能被执行多次。

长期以来，原有的生命周期函数总是会诱惑开发者在 render 之前的生命周期函数中做一些动作，现在这些动作还放在这些函数中的话，有可能会被调用多次，这肯定不是我们想要的结果。



### 3、useEffect(fn, []) 和 componentDidMount 有什么差异？

useEffect 会捕获 props 和 state。所以即便在回调函数里，你拿到的还是初始的 props 和 state。如果想得到“最新”的值，可以使用ref。



### 4、hooks 为什么不能放在条件判断里？

以 setState 为例，在 react 内部，每个组件(Fiber)的 hooks 都是以链表的形式存在 memoizeState 属性中
update 阶段，每次调用 setState，链表就会执行 next 向后移动一步。
如果将 setState 写在条件判断中，假设条件判断不成立，没有执行里面的 setState 方法，会导致接下来所有的 setState 的取值出现偏移，从而导致异常发生。



### 5、fiber 是什么？

React Fiber 是一种基于浏览器的单线程调度算法。

React Fiber 用类似 requestIdleCallback 的机制来做异步 diff。
但是之前数据结构不支持这样的实现异步 diff，于是 React 实现了一个类似链表的数据结构，将原来的 递归diff 变成了现在的 遍历diff，这样就能做到异步可更新了。



### 7、调用 setState 之后发生了什么？

在 setState 的时候，React 会为当前节点创建一个 updateQueue 的更新列队。

然后会触发 reconciliation 过程，在这个过程中，会使用名为 Fiber 的调度算法，开始生成新的 Fiber 树， Fiber 算法的最大特点是可以做到异步可中断的执行。

然后 React Scheduler 会根据优先级高低，先执行优先级高的节点，具体是执行 doWork 方法。

在 doWork 方法中，React 会执行一遍 updateQueue 中的方法，以获得新的节点。然后对比新旧节点，为老节点打上 更新、插入、替换 等 Tag。

当前节点 doWork 完成后，会执行 performUnitOfWork 方法获得新节点，然后再重复上面的过程。

当所有节点都 doWork 完成后，会触发 commitRoot 方法，React 进入 commit 阶段。

在 commit 阶段中，React 会根据前面为各个节点打的 Tag，一次性更新整个 dom 元素。




### 8、为什么虚拟dom 会提高性能?

虚拟dom 相当于在 JS 和真实 dom 中间加了一个缓存，利用 diff 算法避免了没有必要的 dom 操作，从而提高性能。



### 9、错误边界是什么？它有什么用？

在 React 中，如果任何一个组件发生错误，它将破坏整个组件树，导致整页白屏。

这时候我们可以用错误边界优雅地降级处理这些错误。

例如下面封装的组件：

```jsx

class ErrorBoundary extends React.Component<IProps, IState> {
  constructor(props: IProps) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError() {
    // 更新 state 使下一次渲染能够显示降级后的 UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 可以将错误日志上报给服务器
    console.log('组件奔溃 Error', error);
    console.log('组件奔溃 Info', errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 你可以自定义降级后的 UI 并渲染
      return this.props.content;
    }
    return this.props.children;
  }
}


```



### 10、什么是 Portals？

Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案。

```jsx

ReactDOM.createPortal(child, container)

```



### 11、React 组件间有那些通信方式? 


父组件向子组件通信

1、 通过 props 传递


子组件向父组件通信

1、 主动调用通过 props 传过来的方法，并将想要传递的信息，作为参数，传递到父组件的作用域中


跨层级通信

1、 使用 react 自带的 Context 进行通信，createContext 创建上下文， useContext 使用上下文。


参考下面代码：

```jsx 

import React, { createContext, useContext } from 'react';

const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

const ThemeContext = createContext(themes.light);

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}

export default App;



```

2、使用 Redux 或者 Mobx 等状态管理库

3、使用订阅发布模式



### 12、React 父组件如何调用子组件中的方法？

1、如果是在函数组件中调用子组件（>= react@16.8），可以使用 useRef 和 useImperativeHandle:

2、如果是在类组件中调用子组件（>= react@16.4），可以使用 createRef:


函数组件:

```jsx
const { forwardRef, useRef, useImperativeHandle } = React;

const Child = forwardRef((props, ref) => {
  useImperativeHandle(ref, () => ({
    getAlert() {
      alert("getAlert from Child");
    }
  }));
  return <h1>Hi</h1>;
});

const Parent = () => {
  const childRef = useRef();
  return (
    <div>
      <Child ref={childRef} />
      <button onClick={() => childRef.current.getAlert()}>Click</button>
    </div>
  );
};

```

类组件:

```jsx

const { Component } = React;

class Parent extends Component {
  constructor(props) {
    super(props);
    this.child = React.createRef();
  }

  onClick = () => {
    this.child.current.getAlert();
  };

  render() {
    return (
      <div>
        <Child ref={this.child} />
        <button onClick={this.onClick}>Click</button>
      </div>
    );
  }
}

class Child extends Component {
  getAlert() {
    alert('getAlert from Child');
  }

  render() {
    return <h1>Hello</h1>;
  }
}


```



### 13、React有哪些优化性能的手段?

类组件中的优化手段

1、使用纯组件 PureComponent 作为基类。

2、使用 React.memo 高阶函数包装组件。

3、使用 shouldComponentUpdate 生命周期函数来自定义渲染逻辑。


函数组件中的优化手段

1、使用 useMemo。

2、使用 useCallBack。



### 14、为什么 React 元素有一个 $$typeof 属性？ 

目的是为了防止 XSS 攻击。因为 Synbol 无法被序列化，所以 React 可以通过有没有 $$typeof 属性来断出当前的 element 对象是从数据库来的还是自己生成的。

如果没有 $$typeof 这个属性，react 会拒绝处理该元素。

在 React 的古老版本中，下面的写法会出现 XSS 攻击：

```jsx
// 服务端允许用户存储 JSON
let expectedTextButGotJSON = {
  type: 'div',
  props: {
    dangerouslySetInnerHTML: {
      __html: '/* 把你想的搁着 */'
    },
  },
  // ...
};
let message = { text: expectedTextButGotJSON };

// React 0.13 中有风险
<p>
  {message.text}
</p>
```



### 15、React 如何区分 Class组件 和 Function组件？

一般的方式是借助 typeof 和 Function.prototype.toString 来判断当前是不是 class，如下：

```jsx
    function isClass(func) {
      return typeof func === 'function'
        && /^class\s/.test(Function.prototype.toString.call(func));
    }

```

但是这个方式有它的局限性，因为如果用了 babel 等转换工具，将 class 写法全部转为 function 写法，上面的判断就会失效。

React 区分 Class组件 和 Function组件的方式很巧妙，由于所有的类组件都要继承 React.Component，所以只要判断原型链上是否有 

```jsx

AComponent.prototype instanceof React.Component

```



### 16、HTML 和 React 事件处理有什么区别?

在 HTML 中事件名必须小写：

```jsx

<button onclick='activateLasers()'>

```

而在 React 中需要遵循驼峰写法：

```jsx

<button onClick={activateLasers}>

```

在 HTML 中可以返回 false 以阻止默认的行为：

```jsx

<a href='#' onclick='console.log("The link was clicked."); return false;' />

```

而在 React 中必须地明确地调用 preventDefault()：

```jsx

function handleClick(event) {
  event.preventDefault()
  console.log('The link was clicked.')
}

```



### 17、什么是 suspense 组件?

Suspense 让组件“等待”某个异步操作，直到该异步操作结束即可渲染。在下面例子中，两个组件都会等待异步 API 的返回值：

```jsx

const resource = fetchProfileData();

function ProfilePage() {
  return (
    <Suspense fallback={<h1>Loading profile...</h1>}>
      <ProfileDetails />
      <Suspense fallback={<h1>Loading posts...</h1>}>
        <ProfileTimeline />
      </Suspense>
    </Suspense>
  );
}

function ProfileDetails() {
  // 尝试读取用户信息，尽管该数据可能尚未加载
  const user = resource.user.read();
  return <h1>{user.name}</h1>;
}

function ProfileTimeline() {
  // 尝试读取博文信息，尽管该部分数据可能尚未加载
  const posts = resource.posts.read();
  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.text}</li>
      ))}
    </ul>
  );
}

```

Suspense 也可以用于懒加载，参考下面的代码：

```jsx

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}

```



### 18、为什么 JSX 中的组件名要以大写字母开头？

因为 React 要知道当前渲染的是组件还是 HTML 元素。



### 19、redux 是什么？ 

Redux 是一个为 JavaScript 应用设计的，可预测的状态容器。

它解决了如下问题：

跨层级组件之间的数据传递变得很容易
所有对状态的改变都需要 dispatch，使得整个数据的改变可追踪，方便排查问题。


但是它也有缺点：

概念偏多，理解起来不容易
样板代码太多



### 20、react-redux 的实现原理？  

通过 redux 和 react context 配合使用，并借助高阶函数，实现了 react-redux。



### 21、reudx 和 mobx 的区别？

得益于 Mobx 的 observable，使用 mobx 可以做到精准更新；

对应的 Redux 是用 dispath 进行广播，通过Provider 和 connect 来比对前后差别控制更新粒度；



### 22、redux 异步中间件有什么什么作用?

假如有这样一个需求：请求数据前要向 Store dispatch 一个 loading 状态，并带上一些信息；

请求结束后再向Store dispatch 一个 loaded 状态


一些同学可能会这样做：

```jsx

function App() {
  const onClick = () => {
    dispatch({ type: 'LOADING', message: 'data is loading' })
    fetch('dataurl').then(() => {
      dispatch({ type: 'LOADED' })
    });
  }

  return (<div>
    <button onClick={onClick}>click</button>
  </div>);
}

```

但是如果有非常多的地方用到这块逻辑，那应该怎么办？

聪明的同学会想到可以将 onClick 里的逻辑抽象出来复用，如下：

```jsx

function fetchData(message: string) {
  return (dispatch) => {
    dispatch({ type: 'LOADING', message })
    setTimeout(() => {
      dispatch({ type: 'LOADED' })
    }, 1000)
  }
}

function App() {
  const onClick = () => {
    fetchData('data is loading')(dispatch)
  }

  return (<div>
    <button onClick={onClick}>click</button>
  </div>);
}

```

很好，但是 fetchData('data is loading')(dispatch) 这种写法有点奇怪，会增加开发者的心智负担。

于是可以借助 rudux 相关的异步中间件，以 rudux-chunk 为例，将写法改为如下：

```jsx

function fetchData(message: string) {
  return (dispatch) => {
    dispatch({ type: 'LOADING', message })
    setTimeout(() => {
      dispatch({ type: 'LOADED' })
    }, 1000)
  }
}

function App() {
  const onClick = () => {
-   fetchData('data is loading')(dispatch)
+   dispatch(fetchData('data is loading'))
  }

  return (<div>
    <button onClick={onClick}>click</button>
  </div>);
}


```

这样就更符合认知一些了，redux 异步中间件没有什么奥秘，主要做的就是这样的事情。



### 23、redux 有哪些异步中间件？

1、redux-thunk

源代码简短优雅，上手简单

2、redux-saga

借助 JS 的 generator 来处理异步，避免了回调的问题

3、redux-observable

借助了 RxJS 流的思想以及其各种强大的操作符，来处理异步问题
