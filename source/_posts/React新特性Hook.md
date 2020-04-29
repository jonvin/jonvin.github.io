---
title: React新特性Hook
date: 2020-04-20 17:06:38
tags:
---

前言：上一篇文章讲到了基于 React 的 Ant Design Pro, 在使用这个后台解决方案的过程中,尝试了使用 React 的一个强大的新增特性 Hook, Hook 是 React 16.8 的新增特性。Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数。Hook 不能在 class 组件中使用 —— 这使得你不使用 class 也能使用 React。（我们不推荐把你已有的组件全部重写，但是你可以在新组件里开始使用 Hook。）

举一个简单的例子

```tsx
import React, { useState } from "react";

const TableList: React.FC<{}> = () => {
  const [Count, setCount] = useState<Number>(0);
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
};
```

在上面例子中 useState 就是我们要学习的第一个“Hook”，那为什么要使用 Hook 呢? Hook 解决了我们五年来编写和维护成千上万的组件时遇到的各种各样看起来不相关的问题。无论你正在学习 React，或每天使用，或者更愿尝试另一个和 React 有相似组件模型的框架，你都可能对这些问题似曾相识。

在组件之间复用状态逻辑很难

React 没有提供将可复用性行为“附加”到组件的途径（例如，把组件连接到 store）。如果你使用过 React 一段时间，你也许会熟悉一些解决此类问题的方案，比如 render props 和 高阶组件。但是这类方案需要重新组织你的组件结构，这可能会很麻烦，使你的代码难以理解。如果你在 React DevTools 中观察过 React 应用，你会发现由 providers，consumers，高阶组件，render props 等其他抽象层组成的组件会形成“嵌套地狱”。尽管我们可以在 DevTools 过滤掉它们，但这说明了一个更深层次的问题：React 需要为共享状态逻辑提供更好的原生途径。

你可以使用 Hook 从组件中提取状态逻辑，使得这些逻辑可以单独测试并复用。Hook 使你在无需修改组件结构的情况下复用状态逻辑。 这使得在组件间或社区内共享 Hook 变得更便捷。

----以上文字来自于 React 官网

### 使用 State Hook

    如果熟悉 React  那么一定对class 的写法特别熟悉, 接下来我们将Hook 与Class 的写法进行对比

Hook 写法：

```tsx
import React, { useState } from "react";

const Example: React.FC<{}> = () => {
  const [Count, setCount] = useState<Number>(0);
  //你可以在这里使用 state Hook
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
};
```

等价的 class 示例：

```tsx
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

声明 State 变量

    Hook写法：

```tsx
const TableList: React.FC<{}> = () => {
  const [previewstate, setValues] = useState({
    previewVisible: false,
    previewImage: "",
  });
};
```

class 的写法：

```tsx
class TableList extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
        previewVisible: false,
        previewImage: '',
    };
  }

```

Effect Hook
之前可能已经在 React 组件中执行过数据获取、订阅或者手动修改过 DOM。我们统一把这些操作称为“副作用”，或者简称为“作用”。useEffect 就是一个 Effect Hook，给函数组件增加了操作副作用的能力。它跟 class 组件中的 componentDidMount、componentDidUpdate 和 componentWillUnmount 具有相同的用途，只不过被合并成了一个 API。（我们会在使用 Effect Hook 里展示对比 useEffect 和这些方法的例子。）

```jsx
import React, { useState, useEffect } from "react";

function Example() {
  const [count, setCount] = useState(0);

  // 相当于 componentDidMount 和 componentDidUpdate:
  useEffect(() => {
    // 使用浏览器的 API 更新页面标题
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```
