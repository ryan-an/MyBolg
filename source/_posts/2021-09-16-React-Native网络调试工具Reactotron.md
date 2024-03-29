---
title: ReactNative网络调试工具Reactotron
date: 2021-09-16 19:33:46
tags:
- ReactNative
- 开发工具
categories: 
- ReactNative 
comment: true
---

>Reactotron是为React Native应用提供网络调试的工具，其功能与浏览器的F12一样
- [下载相应reactotron](https://github.com/infinitered/reactotron/releases)
- 安装reacttotron在自己的RN项目中的dev依赖上
```
npm i --save-dev reactotron-react-native
```

- 在项目目录下创建ReactotronConfig.js 文件，并且将以下代码粘贴进去
```js
import Reactotron from 'reactotron-react-native'
Reactotron
 .configure() // controls connection & communication settings
 .useReactNative() // add all built-in react native plugins
 .connect() // let's connect!
```
- 最后在App.js或者index.js中引入ReactotronConfig.js，添加以下代码：
```js
//注意此处文件路径改为自己项目中的相对路径
if(__DEV__) {
  import('./ReactotronConfig').then(() => console.log('Reactotron Configured'))
}
```
- 打开reactotron工具，启动项目
- 现在便可以进行相关网络请求的调试；注意：其默认监听的端口为9090，确保其没有被占用的情况下使用；并且如果是用真机调试，请开启9090连接
```
adb reverse tcp:9090 tcp:9090
```