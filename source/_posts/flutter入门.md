---
title: flutter 简单了解
date: 2019-3-27 10:47:21
tags: 
  - Flutter
  - Dart 
categories: 杂记
---
### What is flutter ?

- [Flutter](https://github.com/flutter/flutter)是Google开发的一套全新的 mobile app SDK
- 开源UI框架
- 一套代码同时运行在Android和iOS系统
- 媲美原生应用的性能
- 基于Dart编程语言
- 目前已经发布了120个版本，最新1.4.1(2019/3/27)

### Why flutter ?

在Flutter之前，就已经有许多跨平台UI框架：

- 基于WebView的cordova、AppCan
- 将web渲染成原生控件的weex、ReactNative

基于WebView框架的优点很明显，几乎可以完全继承现代Web开发的所有成果，Web开发人员，不需要太多的学习和迁移成本就可以开发一个App

但也有很关键的缺点：WebView的渲染效率和JavaScript执行性能太差；Android各个系统版本和设备厂商的定制。

为了解决WebView性能差的问题，以React Native为代表的一类框架将最终渲染工作交还给了系统；但是随着系统版本变化和API的变化，这些框架本身需要处理大量平台相关的逻辑，开发者也需处理不同平台的差异；有些特性只能在部分平台。

为了解决这些问题，flutter从头到尾重写一套跨平台的UI框架，包括UI控件、渲染逻辑甚至开发语言，只调用原生的图形渲染接口。

### How ?

我们试着写个简单的demo。

1. 开发环境

   安装Android Studio，flutter， VsCode插件，模拟器

2. 在Android Studio里创建flutter app：

   ```dart
   import 'package:flutter/material.dart';
   
   void main() => runApp(new MyApp());
   
   class MyApp extends StatelessWidget { 
     @override
     Widget build(BuildContext context) {
       return new MaterialApp( 
         title: 'Welcome to Flutter',
         home: new Scaffold(
           appBar: new AppBar(
             title: new Text('Welcome to Flutter'),
           ),
           body: new Center(
             child: new RandomWords()
           ),
         ),
       );
     }
   }
   ```

   ![1](https://haitao.nos.netease.com/4d776dfe-9076-4aaf-8db8-a46a49f570df_1390_820.png)

   这里MyApp继承了`StatelessWidget` ，这是一个无状态组件， 无状态组件是不可变的, 这意味着它们的属性不能改变 - 所有的值都是最终的。相对的，还有State*ful* widgets（有状态组件）。

    在Flutter中，大多数东西都是widget，包括对齐(alignment)、填充(padding)和布局(layout)，

   Scaffold 是 Material library 中提供的一个widget, 它提供了默认的导航栏、标题和包含主屏幕widget树的body属性。widget树可以很复杂。

3. 添加一个 Stateful 组件

   实现一个 stateful widget 至少需要两个类:

   1. 一个 StatefulWidget类。
   2. 一个 State类。 StatefulWidget类本身是不变的，但是 State类在widget生命周期中始终存在.

   ```dart
   import 'package:english_words/english_words.dart';
   
   class RandomWords extends StatefulWidget {
     @override
     createState() => new RandomWordsState();
   }
   
   class RandomWordsState  extends State<RandomWords> {
     @override
     Widget build(BuildContext context) {
       final wordPair = new WordPair.random();
       return new Text(wordPair.asPascalCase);
     }
   }
   ```

   ```dart
   child: new Text('Hello World') => child: new RandomWords()
   ```

   ![2](https://haitao.nos.netease.com/56a1a839-87e1-4abe-bed4-ee1df060ecc0_1239_859.png)

4. 添加一个无限滚动的list

   ```dart
   class RandomWordsState  extends State<RandomWords> {
     final _suggestions = <WordPair>[]; // _下划线开头，强制私有化
   
     @override
     Widget build(BuildContext context) {
       return new Scaffold (
         appBar: new AppBar(
           title: new Text('Startup Name Generator'),
         ),
         body: _buildSuggestions(),
       );
     }
     
     Widget _buildSuggestions() {
       return new ListView.builder(
         padding: const EdgeInsets.all(16.0),
         itemBuilder: (context, i) {
           if(i.isOdd) return new Divider();
   
           final index = i ~/ 2;
           if(index >= _suggestions.length) {
             _suggestions.addAll(generateWordPairs().take(10));
           }
           return _buildRow(_suggestions[index]);
         }
       );
     }
     Widget _buildRow(WordPair pair) {
       return new ListTile(
         title: new Text(
           pair.asPascalCase,
           style: const TextStyle(fontSize: 18.0),
         )
       );
     }
   }
   ```

   ```dart
   class MyApp extends StatelessWidget {
     @override
     Widget build(BuildContext context) {
       return new MaterialApp(
         title: 'Startup Name Generator',
         home: new RandomWords(),
       );
     }
   }
   ```

   

   ![3](https://haitao.nos.netease.com/645a534f-dd3e-40ea-9ba6-ab43b57a4811_1369_918.png)

5. 添加交互

   ```dart
   class RandomWordsState  extends State<RandomWords> {
       ...
   	final _saved = new Set<WordPair>();
       ...
       
       Widget _buildRow(WordPair pair, String index) {
           final isSaved = _saved.contains(pair);
           return new ListTile(
             title: new Text(
               pair.asPascalCase + '：兴爷第' + index + '个小弟',
               style: _biggerFont,
             ),
             trailing: new Icon(
               isSaved ? Icons.favorite : Icons.favorite_border,
               color: isSaved ? Colors.red : null,
             ),
             onTap: () {
               setState(() {
                 if (isSaved) {
                   _saved.remove(pair);
                 } else {
                   _saved.add(pair);
                 }
               });
             },
           );
         }
   }
   ```

   ![gif](https://haitao.nos.netease.com/860a39bd-3ccf-4eb5-a7a4-ace0c65fe4b8_1072_820.gif)