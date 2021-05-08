---
title: React开发中遇到的问题
date: 2021-03-03 15:08:31
tags:
---

## 下面是为大家分享 react 开发中遇到的问题

1. react.js 函数组件解决内存泄漏警告

在React开发中，我们可能经常会遇到这个一个警告：

报错内容：Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function.
in PopularRegisterDetailInfoComp (created by ConnectFunction)
in ConnectFunction (at adminEdit.tsx:237)

我们不能在组件销毁后设置state，防止出现内存泄漏的情况

 
关于react中切换路由时报以上错误，实际的原因是因为在组件挂载（mounted）之后进行了异步操作，比如ajax请求或者设置了定时器等，而你在callback中进行了setState操作。当你切换路由时，组件已经被卸载（unmounted）了，此时异步操作中callback还在执行，因此setState没有得到值。

将解决方案：


报错前

```jsx

  const [currentStatus, setCurrentStatus] = useState<number>();

  useEffect(() => {
    dispatch({
      type: 'popular/fetchCurrent',
      payload: { id },
    });
  }, []);

  useEffect(() => {
    if (currentPopular) {
      setCurrentStatus(1);
    }
  }, [currentPopular]);
```

报错原因：因为我在useEffect里面使用useState来接收异步请求的值导致
解决方法：

```jsx 

  const [currentPopular, setCurrentPopular] = useState<any>({});
  const [currentStatus, setCurrentStatus] = useState<number>();

  const fetchData = async () => {
    const res = await fetchDetail({
      id: id,
    });

    if (res.code === 0) {
      setCurrentPopular(res.result);
      setCurrentStatus(1);
      // console.log('res', res);
    }
  };

  useEffect(() => {
    fetchData();
  }, []);

```


### 拓展 

如果是React 的类组件的话 


一、在卸载的时候对所有的操作进行清除（例如：abort你的ajax请求或者清除定时器）


```jsx

componentDidMount = () => {
    //1.ajax请求
    $.ajax('你的请求',{})
        .then(res => {
            this.setState({
                aa:true
            })
        })
        .catch(err => {})
        
    //2.定时器
    timer = setTimeout(() => {
        //dosomething
    },1000)
}

componentWillUnMount = () => {
    //1.ajax请求
    $.ajax.abort()
    //2.定时器
    clearTimeout(timer)
}

```


二、设置一个flag，当unmount的时候重置这个flag

```jsx

componentDidMount = () => {
    this._isMounted = true;
    $.ajax('你的请求',{})
        .then(res => {
            if(this._isMounted){
                this.setState({
                    aa:true
                })
            }
        })
        .catch(err => {})
}

componentWillUnMount = () => {
    this._isMounted = false;
}

```


三、最简单的方式

```jsx 

componentDidMount = () => {
    $.ajax('你的请求',{})
    .then(res => {
        this.setState({
            data: datas,
        });
    })
    .catch(error => {
 
     });
}
componentWillUnmount = () => {
    this.setState = (state,callback)=>{
      return;
    };
}

```