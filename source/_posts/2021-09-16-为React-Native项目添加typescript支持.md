---
title: 为ReactNative项目添加typescript支持
date: 2021-09-16 16:51:40
tags:
- ts
categories: 
- ReactNative 
comments: true
---

### Ts和Js的区别
>1. ts是静态类语言，可以做到声明即文档，js是动态类语言相对更灵活。
2. 如用ts写一个button组件可以清晰的知道，ButtonProps如是否必传，可选，style是什么类型，disabled是什么类型，较js，ts更易于维护和拓展，可以做到代码即注释，避免一个月不见
3. 代码自己都忘记自己写了什么的尴尬，
4. ts对比js基础类型上，增加了 void/never/any/元组/枚举/以及一些高级类型
5. js没有重载概念，ts有可以重载
6. vscode/ide对ts有很友好的提示
7. ts更利于重构

TypeScript可以编译生成JS文件，然后供给ReactNative调用
话不多说，直接开始吧

##### 对项目添加TypeScript支持
```
npm install typescript -g  #全局安装
```
```
yarn add -D typescript  #项目根目录安装
```
```
yarn add -g typings  #项目添加TypeScript类型支持
```
##### 根目录添加配置tsconfig.json文件
```
tsc --init
```
项目根目录下将会生成tsconfig.json文件
按自身需求修改配置内容
```json
{
  "compilerOptions": {
      "target": "es6",
      "allowJs": true,
      "jsx": "react", // 这里必须改为react，否则无法识别tsx
      "sourceMap": false,
      "noImplicitAny": false
  },
  "exclude": [
      "node_modules"
  ],
  "paths": {
      "@/*":["src/*"] // 这里是目录别名 @代表src目录
    }, 
}
```
##### 添加相应的React库到TypeScript中，不然tsx代码中将不能使用相关模块
```
yarn add -D typescript
```
```
yarn add -D @types/react @types/react-native @types/react-navigation
```
至此，这个项目已经可以配置使用TypeScript进行ReactNative开发，感觉TypeScript比Javascript使用起来更爽，开发更便捷

文件：Body.tsx
```ts
import React from 'react';
import { View, SafeAreaView } from 'react-native';
import DStatusBar from "@/components/DStatusBar"
export default ({ children, style={} }) => {
    return (
        <View style={[{
            flex: 1,
            minHeight: global.SCREEN_HEIGHT,
            width: global.SCREEN_WIDTH,
            position: 'relative',
            backgroundColor: global.gThemeFun.themeGetColor('main_bg_color')
        }, style]}>
            <DStatusBar />
            {children}
            <SafeAreaView></SafeAreaView>
        </View>
    )
}
```

附上我遇到的一个问题，项目中以前的全局变量在ts中都无法使用，ts的全局变量需要重新声明
声明方法如下：
```ts
// ts全局变量声明
declare global {
    namespace NodeJS {
        interface Global {
            gApi: any;  // 全局api调用方法集
            gHttp: any; // 全局axios请求
            // global.$api = apis()
            gStyle: any;  // 全局通用样式（不包过多主题）
            gImg: any;  // 全局图片路径字典
            gOs: String;  // 当前系统类型（android || ios）
            gThemeFun: any; // 全局多主题方法集
            $TRACK_KEY: String; // token的AES加密key
            $TRACK: String; // tonken密匙
            i18n: any;  // 全局多语言处理对象
            gFSize: Function; // 全局文字自适应手机屏幕大小函数
            GWSize: Function; // 全局宽度自适应手机屏幕大小函数
            gHSize: Function; // 全局高度自适应手机屏幕大小函数
            SCREEN_HEIGHT: number;  // 当前手机屏幕高度
            SCREEN_WIDTH: number;   // 当前手机屏幕宽度
        }
    }
}
// 全局对象调用/赋值/修改：global.xxx
import { Platform } from 'react-native';
// 分辨率适配
import api from "@/http/apis"
import http from '@/http/http';
// import apis from '@/http/apis/index';

// 全局样式
import gStyle from '@/assets/style/common';

// 图片管理
import imgManager from "@/assets/img"

// 多主题设置方法
import themeFun from '@/theme/themeFun';
// 多语言
import { i18n } from '@/utils/locales/index';
// 定义全局变量
global.gApi = api
global.gHttp = http
global.gStyle = gStyle
global.gImg = imgManager
global.gOs = Platform.OS
global.gThemeFun = themeFun

```