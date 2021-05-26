---
title: flutter实战爬坑笔记
date: 2019-08-09 09:15:24
tags:
---

前言：之前在做 flutter 实战操作的时候做了一大堆笔记，今天就把这些笔记来整理一下吧！

### 基础组件

● Text 组件
用于装文字的组件

```bash
Text(
    'Dolore ut aute quis amet.Aliqua eiusmod nulla amet quis et.Et elit eu dolore aliqua.Do amet id aute sit.',
    maxLines: 2,                             //文字不超过·2行
    overflow:TextOverflow.ellipsis,         // 超过2行部分 隐藏 并显示 "...."
    style: TextStyle(
        color: Colors.black,
        fontSize:18.0
    ),
), 

● Container 组件
Container 组件类似于 Html 中的 div.

```bash  
    Container(
        color:Color.(0xff0094ff),   //Container的背景颜色
        margin: EdgeInsets.all(10.0),
        padding: EdgeInsets.all(10.0);
        width:300,    //宽100% ，可以设置为   double.infinity
        height:300,    //高100% ， 可以设置属性值为 double.infinity
        child: Text(
            '这是Container组件',
            style:TextStyle( color:Colors.white );
        ),
        decoration: BoxDecoration(    //设置边宽 border
              color: Color.fromRGBO(255, 255, 255, .3),        //设置背景的颜色
              borderRadius: BorderRadius.all(new Radius.circular(4.0)),    //设置圆角             
              border: Border.all(width: 1.0, color: Color(0xffcccccc))          // 设置边框的颜色和宽度
        ),
    ),

```

● Column 组件
垂直布局组件,类似于 flex 布局的 垂直布局

```bash
    return Container(
        width: double.infinity,
        child:Column(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,  //垂直居中展开
            mainAxisAlignment: MainAxisAlignment.spaceAround,  //垂直居中展开
            mainAxisAlignment: MainAxisAlignment.center,        //所有元素居中
            mainAxisAlignment: MainAxisAlignment.spaceBetween,  //上下 靠边展开
            children: <Widget>[
            Icon(Icons.settings, size: 40.0),
            Icon(Icons.settings, size: 40.0),
            Icon(Icons.satellite, size: 40.0),
            Icon(Icons.group, size:40.0),
            ],
        )

    )

```

● row 组件
横排布局组件, 类似于 flex 布局的左右展开布局

```bash
    return Container(
      width: double.infinity,
      child:Row(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,  //左右居中展开
        mainAxisAlignment: MainAxisAlignment.spaceAround,  //左右居中展开
        mainAxisAlignment: MainAxisAlignment.center,        //所有元素居中
        mainAxisAlignment: MainAxisAlignment.spaceBetween,  //左右 靠边展开
        children: <Widget>[
          Icon(Icons.settings, size: 40.0),
          Icon(Icons.settings, size: 40.0),
          Icon(Icons.satellite, size: 40.0),
          Icon(Icons.group, size:40.0),
        ],
      )

    );

```

● Flexible 和 Expanded 组件
Flexible 是一个控制 Row、Column、Flex 等子组件如何布局的组件。
Flexible 组件可以使 Row、Column、Flex 等子组件在主轴方向有填充可用空间的能力(例如，Row 在水平方向，Column 在垂直方向)，但是它与 Expanded 组件不同，它不强制子组件填充可用空间。
Row、Column、Flex 会被 Expanded 撑开，充满主轴可用空间，用发基本相同。

Expanded

```bash
        Row(
            children:<Widget>[
                new Expanded(
                flex: 1,
                child: new RaisedButton(
                    onPressed: () {
                    print('点击黄色按钮事件');
                    },
                    color: const Color(0xfff1c232),
                    child: new Text('黄色按钮'),
                ),
                ),
                Container(
                    width:100.0;
                ),
            ]

        )



```

Flexible

```bash
        Row(
            children:<Widget>[
                new Flexible(
                flex: 1,
                child: new RaisedButton(
                    onPressed: () {
                    print('点击黄色按钮事件');
                    },
                    color: const Color(0xfff1c232),
                    child: new Text('黄色按钮'),
                ),
                ),
                Container(
                    width:100.0;
                ),
            ]

        )

```

● RichText 组件
实现在一个段落中 Text 应用不同的样式的需求。

```bash
    return RichText(
      text: TextSpan(
        text: 'This is ',
        style: TextStyle(color: Colors.black, fontSize: 18.0),      //整体文字的样式
        children: <TextSpan>[
          TextSpan(
            text: '样式一  加粗的样式 ',
            style: TextStyle(fontWeight: FontWeight.bold,)
          ),
          TextSpan(
              text: 'text. '
          ),
          TextSpan(
              text: 'This is ',
          ),
          TextSpan(
              text: '样式二  字体增大的样式 ',
              style:
              TextStyle(fontSize: 22.0)
          ),
          TextSpan(
              text: 'font size.',
          ),
          TextSpan(
            text: 'This is ',
          ),
          TextSpan(
              text: '样式三  颜色不同的得样式',
              style:
              TextStyle(color: Colors.red)
          ),
          TextSpan(
            text: 'color.',
          ),
        ],
      ),
    );
```

● TabBar 组件 和 DefaultTabController
DefaultTabController 要简单很多，由于应用在无状态控件中，使用 DefaultTabController 包裹需要用到 Tab 的页面即可：

```bash
        return DefaultTabController(
            length: 3,      //必填项  一共有几块内容供 tab栏切换
            child: Scaffold(
                    appBar: AppBar(
                        title:Text('tab 栏切换'),
                        elevation:0.0,
                        leading: Icon(Icons.menu),
                        actions: <Widget>[
                        // Icon(Icons.search),
                        // Icon(Icons.send),
                        // Icon(Icons.search)
                        ],
                        bottom: TabBar(
                            labelColor:Colors.white,     //被选中时候的颜色
                            labelStyle: TextStyle(         // 被选中时候的字体大小
                            fontSize: 20.0
                            ),
                            unselectedLabelColor: Colors.black,    //没有被选中时候的 字体颜色
                            unselectedLabelStyle: TextStyle(        // 没有别选中时候的字体大小
                                fontSize: 12.0
                            ),
                            indicatorColor: Colors.white,     //被选中下划线的 颜色
                            indicatorWeight: 2,                 // 被选中下划线的粗细
                            indicatorSize: TabBarIndicatorSize.label,  //被选中下划线的长短 ( label：是跟文字一样长  tab: 是整个tab 盒子的长度  )
                            tabs: <Widget>[
                            Tab(
                                text:'首页',
                            ),
                            Tab(
                                text:'视频',
                            ),
                            Tab(
                                text:'文章',   //模板语法  '${article.name}'
                            ),
                            ],
                        ),
                    ),
                    // body:Hello(),
                    body:TabBarView(                            //此处填写对应的内容
                        children: <Widget>[
                        Icon(Icons.satellite, size: 90,),
                        Icon(Icons.satellite, size: 90,),
                        Icon(Icons.save, size: 90,)
                        ],
                    ),
                    drawer:DrawerList()
            ),
        )
```

● Stack 组件  
层叠堆放组件 Stack 类似于 position:absolute;

```bash
    Stack(
    children: <Widget>[
        Container(
        color: Color(0xfffcccccc),
        width: double.infinity,
        height: 200.0,
        ),
        Positioned(
        top: 22.0,
        right: 10.0,
        child: Icon(Icons.ac_unit, color:Colors.white),
        ),
    ],
    ),
```

● AspectRatio 组件
AspectRatio 的作用是调整 child 到设置的宽高比，这种控件在其他移动端平台上一般都不会提供，Flutter 之所以提供，我想最大的原因，可能就是自定义起来特别麻烦吧。

```bash
  AspectRatio(
    aspectRatio: 16/9,            //必须设置的 属性  宽高比
    child: Container(
        color: Color(0xfffff7899),
        child:Text('固定宽高的组件'),
    )
  )

```

● 列表分割线 ListTitle 组件 和 Divider 组件
如同 html 中的 li 标签 和 1 像素的线

```bash
    children: <Widget>[
            ListTitle(    //列表的内容
            title:Text('我的收藏'),
            subtitle: Text('个人中心'),
            leading: Icon(Icons.arrow_forward_ios),
            onTap: (){
                    print('你已经点到我了')
            }
            )
            Divider( color: Color(0xfffcccccc))   //列表分割线
            ListTitle(
            title:Text('我的收藏'),
            subtitle: Text('个人中心'),
            leading: Icon(Icons.arrow_forward_ios),
            onTap: (){
                    print('你已经点到我了')
            }
            )
            Divider( color: Color(0xfffcccccc))  // 列表分割线
            ListTitle(
            title:Text('我的关注'),
            subtitle: Text('个人中心'),
            leading: Icon(Icons.arrow_forward_ios),
            onTap: (){
                    print('你已经点到我了')
            }
            )
            Divider( color: Color(0xfffcccccc))
            ListTitle(
            title:Text('我的收藏'),
            subtitle: Text('个人中心'),
            leading: Icon(Icons.arrow_forward_ios),
            onTap: (){
                    print('你已经点到我了')
            }
            )
            Divider( color: Color(0xfffcccccc))
    ]
```

● 按钮 FlatButton (文字按钮) , FlatButton.icon(带图标的文字按钮) ,RaisedButton(带背景颜色的按钮), FloatingActionButton(带浮动的 按钮)

```bash
    FlatButton(
          child: Text('文字按钮'),
          textColor: Colors.red,
          onPressed:(){
              print('点击');
          }
    ),

    FlatButton.icon(
              icon: Icon(Icons.arrow_back),
              label: Text('图标文字'),
              textColor: Colors.blue,
              onPressed: (){
                  print('点击按钮le');
              },
    )

    RaisedButton(
              onPressed:(){}
    )

    floatingActionButton: FloatingActionButton(
          child:Icon(Icons.arrow_back),
          elevation: 0.0,
          backgroundColor: Colors.red,
          onPressed: (){
            print('按下了浮动按钮');
          },
    ),
```

● 点击 改变text 的状态

``` dart

void main(){
    runApp(SampleApp() );     
}

class SampleApp extends StatelessWidget {

    @override 
    Widget build( BuildContext context ){
        return MaterialApp(
            title: 'Sample App',
            theme: ThemeData(
                primarySwatch:　Colors.blue, 
            ),
            home: SampleAppPage(), 
        )
    }
}


class SampleAppPage extends StatefulWidget{
    SampleAppPage({key key}) : super(key:key); 

    @override 
    _SampleAppPageState createState()=> _SampleAppPageState(); 

}   

class _SampleAppPageState extends State<SampleAppPage>{

    String textToShow = 'I Like Flutter'


    void _updataText(){
        setState((){
            textToShow = 'Flutter is Awesome!'
        })
    }

    @override 
    Widget build (BuildContext context ){
        return Scaffold (
            appBar: AppBar(
                title: Text('Sample App'),  
            )
            body: Center(child: Text(textToShow)), 
            floatingActionButton: FloatingActionButton(
                onPressed: _updateText, 
                tooltip: 'Updata Text',
                child:Icon(Icon.update), 
            ) 
        )
    }
}



```

● Map与JSON的转换

``` dart 

    import 'dart:convert';
    Map user = json.decode(jsonStr);
    String jsonStr = json.encode(user)

```

● 两个List拼接

``` dart

List.from(lista)..addAll(listb);

```


● List的增删改查



1.增加list.add();
2.删除指定元素list.remove(item);
3.删除指定下标元素list.removeAt(index);
4.修改list[N] = newValue;
5.查找list.indexOf(target);




● 类型转换



1.int/double to String target.toString();
2.String to int int.parse(target);
3.String to double double.parse(target);




● 按照列表中字典或者实例的某个元素排序

```dart 

1.类实例: list.sort((a, b) => b.target.compareTo(a.target));
2.字典: list.sort((a, b) => b[target].compareTo(a[target]));

```


● 定时器

```dart 

import 'dart:async';
//  启动定时器
Timer _timer = Timer.periodic(const Duration(seconds: 1), callback);
// 关闭定时器
_timer.cancel();

```


● 去除字符串中所有空格

```dart 

value.replaceAll(new RegExp(r"\\s+\\b|\\b\\s|\\s|\\b"), "").split('');

```


● 其他
• 删除字符串最后一个字符 String a = 'abcd'; print(a.substring(0, a.length - 1));
• 保留两位小数点 3.14159.toStringAsFixed(2);
• null 判断符 isNull ?? '';
• 判断List是否为空 List.isEmpty();
• 判断字符串是否包含字符 String.contains(Str);
• 判断Map是否包含某个key Map.containsKey();
• 判断Map是否包含某个value Map.containsValue();
• 判断Map是否为空 Map.isEmpty();
• 遍历Map Map.forEach((key, value) {...});
• Map删除操作
a. 删除某一项remove(key);
b. 删除满足条件的项removeWhere((key, value) => [bool]);
c. 删除全部Map.clear();
• Map浅克隆 Map.from();
• Map深克隆 import 'dart:convert'; // ... Map clonedUser = json.decode(json.encode(user));
• Map追加 Map.addAll(newMap);