---
title: '[flutter的爬坑之路]'
date: 2019-07-18 10:52:48
tags:
---
前言：最近我们团队在用flutter开发app，也挺感谢公司给我们这一次实战爬flutter坑的机会,因为之前，爬过一次 React-Native 的坑，所以这次flutter环境搭的还是比较顺利的，更值得称赞的是 flutter doctor,在搭建环境的时候帮了不少忙！

### 系统配置要求
● 操作系统：Windows 7 SP1 或更高的版本（64 位操作系统）。

● 磁盘空间：除安装 IDE 和一些工具之外还应有至少 400 MB 的空间。

● 工具：要让 Flutter 在你的开发环境中正常使用，依赖于以下的工具：

        Windows PowerShell 5.0 或者更高的版本（Windows 10 中已经预装了）

        Git for Windows 2.x，并且勾选从 Windows 命令提示符使用 Git 选项。

        如果 Windows 版的 Git 已经安装过了，那么请确保能从命令提示符或者 PowerShell 中直接执行 git 命令。


### 所需工具

● Flutter SDK

● Android SDK

● Android Studio或其他支持Flutter的IDE

### 下载Flutter SDK

2.1下载地址：https://flutter.io/get-started/install/，选择操作系统对应的版本（本文使用windows版）。

2.2下载SDK压缩包，并解压至自定义路径。

###进行配置环境变量，目前在国内这一步必不可少

如果您在中国安装或使用Flutter，使用托管Flutter依赖关系的可靠本地镜像站点可能会有帮助。要指示Flutter工具使用备用存储位置，您需要设置两个环境变量，PUB_HOSTED_URL并且FLUTTER_STORAGE_BASE_URL在运行该flutter命令之前。

``` bash

 PUB_HOSTED_URL=https://pub.flutter-io.cn
 FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

```

### Android Studio 安装
Android Studio: 为Flutter提供完整的IDE体验

安装Android Studio
Android Studio, 3.0或更高版本.
或者，您也可以使用IntelliJ：

IntelliJ IDEA Community, version 2017.1或更高版本.
IntelliJ IDEA Ultimate, version 2017.1 或更高版本.
安装Flutter和Dart插件
需要安装两个插件:

Flutter插件： 支持Flutter开发工作流 (运行、调试、热重载等).
Dart插件： 提供代码分析 (输入代码时进行验证、代码补全等).

要安装这些:

1.启动Android Studio.
2.打开插件首选项 (Preferences>Plugins on macOS, File>Settings>Plugins on Windows & Linux).
3.选择 Browse repositories…, 选择 Flutter 插件并点击 install.
4.重启Android Studio后插件生效.

### Visual Studio Code (VS Code) 安装
VS Code: 轻量级编辑器，支持Flutter运行和调试.

安装 VS Code
VS Code, 安装1.20.1或更高版本.
安装Flutter插件
启动 VS Code
调用 View>Command Palette…
输入 ‘install’, 然后选择 Extensions: Install Extension action
在搜索框输入 flutter , 在搜索结果列表中选择 ‘Flutter’, 然后点击 Install
选择 ‘OK’ 重新启动 VS Code

通过Flutter Doctor验证您的设置

1.调用 View>Command Palette…
2.输入 ‘doctor’, 然后选择 ‘Flutter: Run Flutter Doctor’ action
3.查看“OUTPUT”窗口中的输出是否有问题

### 创建新的应用
1.启动 VS Code
2.调用 View>Command Palette…
3.输入 ‘flutter’, 然后选择 ‘Flutter: New Project’ action
4.输入 Project 名称 (如myapp), 然后按回车键
5.指定放置项目的位置，然后按蓝色的确定按钮
6.等待项目创建继续，并显示main.dart文件

上述命令创建一个Flutter项目，项目名为myapp，其中包含一个使用Material 组件的简单的演示应用程序。

在项目目录中，您的应用程序的代码位于 lib/main.dart.

运行应用程序
1.确保在VS Code的右下角选择了目标设备
2.按 F5 键或调用Debug>Start Debugging
3.等待应用程序启动
4.如果一切正常，在应用程序建成功后，您应该在您的设备或模拟器上看到应用程序:


