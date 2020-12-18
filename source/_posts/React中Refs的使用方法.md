---
title: React中Refs的使用方法
date: 2020-11-26 18:00:03
tags:
---


欢迎访问主页，有更多文章内容
转载请注明原出处
原文链接地址:[React中Refs的使用方法](https://www.wekic.com/article/10)


### 什么是Refs

Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。
Ref转发是一项将ref自动通过组件传递到子组件的技巧。 通常用来获取DOM节点或者React元素实例的工具。在React中Refs提供了一种方式，允许用户访问dom节点或者在render方法中创建的React元素。


### Refs转发

Ref 转发是一个可选特性，其允许某些组件接收 ref，并将其向下传递（换句话说，“转发”它）给子组件。


### 背景

在React单向数据流中，props是父子组件交互的唯一方式。要修改一个子组件，需要通过新的props来重新渲染。在有些情况下，需要在数据流之外强行修改子组件(组件或者Dom元素),那么可以通过Refs来进行修改子组件。


### 组件类型

### 受控组件

在 HTML 中，表单元素 如input、 textarea 和 select 之类的表单元素通常自己维护 state，并根据用户输入进行更新。而在 React 中，可变状态（mutable state）通常保存在组件的 state 属性中，并且只能通过使用 setState()来更新。我们可以把两者结合起来，使 React 的 state 成为“唯一数据源”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。


### 非受控组件

不被React组件控制的组件。在受控制组件中，表单数据由 React组件自行处理。其中表单数据由DOM本身处理。文件输入标签就是一个典型的不受控制组件，它的值只能由用户设置，通过DOM自身提供的一些特性来获取。


### 受控组件与非受控组件的区别

受控组件和不受控组件最大的区别就是前者自身维护的状态值变化，可以配合自身的change事件，很容易进行修改或者校验用户的输入。在React中 因为 Refs的出现使得 不受控制组件自身状态值的维护变得容易了许多。


### 使用场景

对Dom元素的焦点控制、内容选择、控制
对Dom元素的内容设置及媒体播放
对Dom元素的操作和对组件实例的操作
集成第三方 DOM 库

避免使用 refs 来做任何可以通过声明式实现来完成的事情。

举个例子，避免在 Dialog 组件里暴露 open() 和 close() 方法，最好传递 isOpen 属性。


### 访问 Refs

当 ref 被传递给 render 中的元素时，对该节点的引用可以在 ref 的 current 属性中被访问。

```javascript

    const node = this.myRef.current;
```


### ref 的值根据节点的类型而有所不同：

当 ref 属性用于 HTML 元素时，构造函数中使用 React.createRef() 创建的 ref 接收底层 DOM 元素作为其 current 属性。
当 ref 属性用于自定义 class 组件时，ref 对象接收组件的挂载实例作为其 current 属性。
不能再函数组件上使用Ref属性，因为函数组件没有实例。


### DOM为元素添加ref

用 ref 去存储 DOM 节点的引用：

```tsx
class CustomTextInput extends React.Component {
 constructor(props) {
   super(props);
   // 创建一个 ref 来存储 textInput 的 DOM 元素
   this.textInput = React.createRef();
   this.focusTextInput = this.focusTextInput.bind(this);
 }

 focusTextInput() {
   // 直接使用原生 API 使 text 输入框获得焦点
   // 注意：我们通过 "current" 来访问 DOM 节点
   this.textInput.current.focus();
 }

 render() {
   // 告诉 React 我们想把 <input> ref 关联到
   // 构造器里创建的 `textInput` 上
   return (
     <div>
       <input
         type="text"
         ref={this.textInput} />
       <input
         type="button"
         value="Focus the text input"
         onClick={this.focusTextInput}
       />
     </div>
   );
 }
}

```

组件会在挂在是给current属性传入DOM元素，并在组件卸载时传入null。ref会在componentDidMount或者componentDidUpdate生命周期触发前更新。

### class组件添加ref

包装上面的 CustomTextInput，来模拟它挂载之后立即被点击的操作，我们可以使用 ref 来获取这个自定义的 input 组件并手动调用它的 focusTextInput 方法：

```tsx
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focusTextInput();
  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    );
  }
}
```

### 函数组件添加refs

默认情况下，不能再函数组件上使用ref属性，因为函数组件没有实例。如果要在函数组件上使用ref，需要使用useRef和useImperativeHandle结合使用。

useRef 返回一个可变的 ref 对象，其 current 属性被初始化为传入的参数（initialValue）

useRef 返回的 ref 对象在组件的整个生命周期内保持不变，也就是说每次重新渲染函数组件时，返回的ref 对象都是同一个（使用 React.createRef ，每次重新渲染组件都会重新创建 ref）


```tsx
import React, { useState, useEffect, useRef } from 'react';
import ReactDOM from 'react-dom';
function Parent() {
    let [number, setNumber] = useState(0);
    return (
        <>
            <Child />
            <button onClick={() => setNumber({ number: number + 1 })}>+</button>
        </>
    )
}
let input;
function Child() {
    const inputRef = useRef();
    console.log('input===inputRef', input === inputRef);
    input = inputRef;
    function getFocus() {
        inputRef.current.focus();
    }
    return (
        <>
            <input type="text" ref={inputRef} />
            <button onClick={getFocus}>获得焦点</button>
        </>
    )
}

```

forwardRef.

因为函数组件没有实例，所以函数组件无法像类组件一样可以接收 ref 属性
forwardRef 可以在父组件中操作子组件的 ref 对象
forwardRef 可以将父组件中的 ref 对象转发到子组件中的 dom 元素上
子组件接受 props 和 ref 作为参数

```tsx
function Child(props,ref){
  return (
    <input type="text" ref={ref}/>
  )
}
Child = React.forwardRef(Child);
function Parent(){
  let [number,setNumber] = useState(0); 
  // 在使用类组件的时候，创建 ref 返回一个对象，该对象的 current 属性值为空
  // 只有当它被赋给某个元素的 ref 属性时，才会有值
  // 所以父组件（类组件）创建一个 ref 对象，然后传递给子组件（类组件），子组件内部有元素使用了
  // 那么父组件就可以操作子组件中的某个元素
  // 但是函数组件无法接收 ref 属性 <Child ref={xxx} /> 这样是不行的
  // 所以就需要用到 forwardRef 进行转发
  const inputRef = useRef();//{current:''}
  function getFocus(){
    inputRef.current.value = 'focus';
    inputRef.current.focus();
  }
  return (
      <>
        <Child ref={inputRef}/>
        <button onClick={()=>setNumber({number:number+1})}>+</button>
        <button onClick={getFocus}>获得焦点</button>
      </>
  )
}

```





useImperativeHandle

useImperativeHandle可以让你在使用 ref 时，自定义暴露给父组件的实例值
在大多数情况下，应当避免使用 ref 这样的命令式代码。useImperativeHandle 应当与 forwardRef 一起使用
父组件可以使用操作子组件中的多个 ref

```tsx 

import React,{useState,useEffect,createRef,useRef,forwardRef,useImperativeHandle} from 'react';

function Child(props,parentRef){
    // 子组件内部自己创建 ref 
    let focusRef = useRef();
    let inputRef = useRef();
    useImperativeHandle(parentRef,()=>(
      // 这个函数会返回一个对象
      // 该对象会作为父组件 current 属性的值
      // 通过这种方式，父组件可以使用操作子组件中的多个 ref
        return {
            focusRef,
            inputRef,
            name:'计数器',
            focus(){
                focusRef.current.focus();
            },
            changeText(text){
                inputRef.current.value = text;
            }
        }
    });
    return (
        <>
            <input ref={focusRef}/>
            <input ref={inputRef}/>
        </>
    )

}
Child = forwardRef(Child);
function Parent(){
  const parentRef = useRef();//{current:''}
  function getFocus(){
    parentRef.current.focus();
    // 因为子组件中没有定义这个属性，实现了保护，所以这里的代码无效
    parentRef.current.addNumber(666);
    parentRef.current.changeText('<script>alert(1)</script>');
    console.log(parentRef.current.name);
  }
  return (
      <>
        <ForwardChild ref={parentRef}/>
        <button onClick={getFocus}>获得焦点</button>
      </>
  )
}
```
