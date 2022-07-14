---
title: vue3项目集成Prettier+ESlint+stylelint
date: 2022-03-27 00:34:32
tags:
---

## 创建vue3项目
```
# 输入创建命令
yarn create vite [项目名称]

```
> 在命令行选择vue项目框架
> 选择vue-ts
```
cd [项目名称]
# 下载项目依赖
yarn
# 启动
yarn dev
```
搭建成功

## 安装ESlint
> 负责 js/ts 文件代码检查、修复，文档详见 官网
```
yarn add --dev eslint
yarn eslint --init // 初始化eslint配置文件
```
按照需求选择选择对应的选项
```
您想如何使用ESLint?
? How would you like to use ESLint?
To check syntax, find problems, and enforce code style
 
您的项目使用什么类型的模块?
? What type of modules does your project use?
JavaScript modules (import/export)
 
您的项目使用哪个框架?
? Which framework does your project use?
None of these
 
你的代码在哪里运行?(按<space>选择，<a>切换所有，<i>反转选择)
? Where does your code run? (Press <space> to select, <a> to toggle all, <i> to invert selection)
Node
 
您想如何为您的项目定义一个样式?
? How would you like to define a style for your project?
Use a popular style guide
 
您想遵循哪种风格指南?
? Which style guide do you want to follow?
Airbnb (https://github.com/airbnb/javascript)
 
您希望配置文件的格式是什么?
? What format do you want your config file to be in?
JavaScript
 
Checking peerDependencies of eslint-config-airbnb-base@latest
您所选择的配置需要以下依赖项:
The config that you've selected requires the following dependencies:
eslint-config-airbnb-base@latest eslint@^4.19.1 || ^5.3.0 eslint-plugin-import@^2.14.0
您想现在用npm安装它们吗?
? Would you like to install them now with npm?
Yes
```

> 安装完成之后就可以手动执行yarn eslint lint命令对代码进行检查格式是否符合规范，同时可以加上--fix参数进行自动修复。
> 我们再加上vscode的插件可以实现实时编码时的格式检查/报错提示,以及保存代码时的自动格式修复。
在vscode插件仓库安装eslint插件。安装完之后我们编辑代码就会有对应的代码格式报错了，但还不能实现自动保存格式化代码。
需要在vscode的setting.json中配置如下
```json
# 可以通过下面配置做一些禁用操作。
{
  "editor.codeActionsOnSave": {
    "source.fixAll": false,  //默认true保存的时候会执行所有的lint,可能会有时间消耗
    "source.fixAll.eslint": true // 仅仅支持eslint
  }
}
# 也可以通过editor.codeActionsOnSaveTimeout可以配置一些自动保存时间，默认750ms。
```
需要注意的是，需要关闭一些tslint的遗留配置，可能会影响到eslint，或者是vetur的格式化配置，假如没有遇到这些问题可以忽略。
```json
{
  // 关闭js/ts的默认format,统一用eslint进行格式化
  "javascript.format.enable": false,
  "typescript.format.enable": false,
  // 关闭vetur的js/ts/html的formatter。html用eslint-plugin-vue格式化。
  // js/ts程序用eslint，防止vetur中的prettier与eslint格式化冲突
  "vetur.format.defaultFormatter.html": "none",
  "vetur.format.defaultFormatter.js": "none",
  "vetur.format.defaultFormatter.ts": "none",
}

```
配置之后vscode在`ctrl+s`保存时就能自动格式化代码了，无需再使用`ctrl+shift+f`手动格式化。
不过这样子的配置在多人合作开发中比较麻烦，不能保证项目组每个人都对vscode作了类似的配置以保证eslint的正常工作，
所以我们有一个方案，项目组都使用vscode时，在项目的根目录新建一个.vscode文件夹，里面放入`extensions.json` 、`launch.json` 和`settings.json` 三个文件.
还有其他的文件配置，可以查看[.vscode](https://code.visualstudio.com/docs/editor/extension-marketplace)

extensions.json配置
```json
{
  "recommendations": [
    "johnsoncodehk.volar",
    "dbaeumer.vscode-eslint",
  ]
}
```
launch.json：配置调试程序，主要用作debugger，具体配置可查看在 [VS Code 中调试 — Vue.js](https://cn.vuejs.org/v2/cookbook/debugging-in-vscode.html)
```json
{
  "version": "0.1.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "Launch Chrome",
      "url": "http://localhost:3000",
      "webRoot": "${workspaceFolder}/src",
      "sourceMaps": true
    }
  ]
}
```
settings.json: 覆盖vscode全局配置
``` json
{
  "typescript.tsdk": "./node_modules/typescript/lib",
  "npm.packageManager": "pnpm",
  "editor.tabSize": 2,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```
完成上面的配置之后，已较为完美的统一相关配置。

## Prettier
+ ESLint主要负责: 质量检查(例如使用了某个变量却忘记了定义)、风格检查
+ Prettier主要负责: 风格检查, 没有质量检查
> [prettier官网](https://prettier.io/)
需要注意一点，由于会出现引入多个风格检查的插件和配置的lint，会导致存在的部分与prettier的lint出现冲突。因此需要lint预设包`eslint-config-prettier`，
这个包的作用是先关闭这些与prettier冲突的lint规则，然后之后再启用prettier的规则，使得冲突的lint规则以prettier为准
`eslint-config-prettier`[配置文档](https://github.com/prettier/eslint-config-prettier)
配置时，需要把p`rettier`和`plugin:prettier/recommended`放到`extends`的最后

## 配置ESlint
eslint的规则我们可以参照官网文档定制适合自己团队的规则，也可以使用一些常见的开源规则标准，比如常见的像airbnb，standard这些，这些配置可以当做一个基础配置。
+ [eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb)
+ [eslint-config-standard](https://www.npmjs.com/package/eslint-config-standard)
+ [eslint-config-google](https://www.npmjs.com/package/eslint-config-google)
关于上面的标准规则库可以单独下载配置，以airbnb为例
`yarn add --dev eslint-config-airbnb`
```json
//.eslintrc.json
{
  "extends": ["eslint:recommended","airbnb"] // eslint:recommended: 加入eslint默认配置。
}
```
### 忽略不需要校验的文件
在项目根目录创建一个.eslintignore文件来配置，这个类似于git的.gitignore文件，eslint校验时会忽略它们
```
public/
```

## 让 Prettier 来管理代码
安装prettier依赖
`yarn add --dev prettier eslint-plugin-prettier eslint-config-prettier`
在项目根目录新建`prettier.config.js`文件，总共22个配置[Prettier配置参数说明](https://prettier.io/docs/en/options.html)
注意，配置文件是js格式时只能用`module.exports = {}`, 而不能用es6的`export default {}`。
```json
// prettier.config.json
{
  "singleQuote": true, // 使用单引号 
  "tabWidth": 2, // 一个 tab 键缩进相当于 2 个空格 
  "useTabs": false, // 行缩进使用 tab 键代替空格 
  "semi": false, // 语句的末尾加上分号 
  "trailingComma": "all", // 不用在多行的逗号分隔的句法结构的最后一行的末尾加上逗号 
  "printWidth": 120, // 单行代码超出 120 个字符自动换行 
  ...

}

```
可以通过`npx prettier --write .`来格式化代码

添加`.prettierignore`文件来忽略不需要检查的文件
```
/dist/*
.local
.output.js
/node_modules/**
**/*.svg
**/*.sh
/public/*
```
## prettier配合eslint
`eslint-config-prettier` 的作用是使用 Prettier 默认推荐配置，并且关闭 eslint 自身的格式化功能，防止 Prettier 和 ESLint 的自动格式化的冲突，主要解决配置冲突的，解决冲突的思路就是通过将这个包提供的扩展放到 extends 最后面引入，依据 rules 生效的优先级，所以它会覆盖前面起冲突的规则

```js
{
  parser: 'vue-eslint-parser',
  parserOptions: {
    parser: '@typescript-eslint/parser', 
    sourceType: 'module',
  },
  extends: [
    'plugin:vue/vue3-recommended', // vue3 基础配置
    'airbnb' // 可以加入airbnb的规则
    'plugin:@typescript-eslint/recommended', // ts配置
    'prettier', // prettier
    'plugin:prettier/recommended', // 合并eslint-plugin-prettier和eslint-config-prettier 配置
  ],
}
```
最后设置vscode的保存自动格式化功能，在vscode插件市场安装`prettier eslint`插件就可以了
> 若不能自动格式化，vscode的控制台 output输出一栏有eslint服务的日志，可在这里调试查看报错原因。一般出现一些依赖包缺失或者配置文件格式问题。

## 配置StyleLint
[StyleLint](https://stylelint.io/)是针对于类css文件的静态检查和格式化，避免样式文件的错误及代码风格。
安装配置
`yarn add --dev stylelint stylelint-config-recommended stylelint-order`
我这里使用了scss，安装scss依赖`yarn add --dev sass sass-loader stylelint-config-standard-scss`
这里用到的插件有
1. `stylelint`
2. `stylelint-config-standard`: stylelint官方共享的标准规则集成。里面包括了 [Google's CSS Style Guide](https://github.com/necolas/idiomatic-css)、[Airbnb's Styleguide](https://github.com/airbnb/css)、[The Idiomatic CSS Principles](https://github.com/necolas/idiomatic-css)以及[@mdo's Code Guide](https://codeguide.co/)标准规则库。
3. `stylelint-order`：用于规范样式属性写作顺序的插件.
4. `stylelint-config-prettier` : 禁用所有与格式相关的 Stylelint 规则，解决 prettier 与 stylelint 规则冲突，确保将其放在 extends 队列最后，这样它将覆盖其他配置。

在跟目录创建`.stylelintrc.json`配置文件,添加配置如下所示
```json
{
  "root": true,
  "plugins": ["stylelint-order"],
  "extends": ["stylelint-config-standard", "stylelint-config-prettier"],
  "rules": {
    "selector-class-pattern": null,
    "selector-pseudo-class-no-unknown": [
      true,
      {
        "ignorePseudoClasses": ["global"],
      },
    ],
    "selector-pseudo-element-no-unknown": [
      true,
      {
        "ignorePseudoElements": ["v-deep"],
      },
    ],
    "at-rule-no-unknown": [// 由于自己大量的样式都是用sass语言编写，里面用到的部分语法例如@extend会报错，因为不是css中的语法。
      true,
      {
        "ignoreAtRules": [
          "apply",
          "variants",
          "responsive",
          "screen",
          "function",
          "if",
          "each",
          "include",
          "mixin",
        ],
      },
    ],
  },

  "ignoreFiles": ["**/*.js", "**/*.jsx", "**/*.tsx", "**/*.ts"],
  "overrides": [ // 特殊文件的配置
    {
      "files": ["*.vue", "**/*.vue", "*.html", "**/*.html"],
      "extends": ["stylelint-config-recommended", "stylelint-config-html"],
      "rules": {
        "keyframes-name-pattern": null,
        "selector-pseudo-class-no-unknown": [ // 解决vue提供的 v-deep v-global语法的报错
          true,
          {
            "ignorePseudoClasses": ["deep", "global"],
          },
        ],
        "selector-pseudo-element-no-unknown": [
          true,
          {
            "ignorePseudoElements": ["v-deep", "v-global", "v-slotted"],
          },
        ],
      },
    },
  ],
}
```
安装vscode的stylelint插件
安装官方提供的`stylelint`插件，和`stylelint-stzhang`插件，此插件用来解决自动保存功能的还有错误提示。
`yarn add --dev stylelint-config-standard`
stylelint插件我安装0.87.6版本，最新版不会根据配置文件提示报错。
vscode的settings.json文件中配置
```json
{
  "stylelint.enable": true, // 默认 true
  "stylelint.autoFix": true,
}
```
完成以上安装配置就能够自动修复代码了。
<!-- >注意：安装的 stylelint 和 VSCode Stylelint 插件版本太新，stylelint v14 及以上版本不兼容 vue3。所以可能会出现配置之后不能格式化的问题，还有可能出现类似Unknown word (CssSyntaxError)Stylelint(CssSyntaxError) 这样的报错，这些问题都是因为14版本太新导致的，具体原因官网有说明：[github.com/stylelint/s…](https://github.com/stylelint/stylelint/blob/14.0.0/docs/migration-guide/to-14.md#syntax-option-and-automatic-inferral-of-syntax)
这里我使用`"stylelint": "^13.13.1"`版本, -->


一个编辑器里对某个类型的文件有多个Formatter（格式化执行者），是一定会打架的。所以需要一些配置。
Save时格式化：下面的json型设置需要写到setting.json里,详情可看[Visual Studio Code User and Workspace Settings](https://code.visualstudio.com/docs/getstarted/settings#_settings-file-locations)

```json
// 保存时使用VSCode 自身格式化程序格式化，触发自动保存机制。
"editor.formatOnSave": true, // *重要

// 开启eslint格式化
"eslint.format.enable": true,

// 显示eslint状态
"eslint.alwaysShowStatus": true,

// 默认格式化走prettier
"editor.defaultFormatter": "esbenp.prettier-vscode", // *重要

// 开启stylelint校验
"stylelint.enable": true,

// 自动保存触发stylelint
"stylelint.autoFix": true // *重要

// 各种文件格式化规则
"[javascriptreact]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},
"[typescript]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},
"[typescriptreact]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},
"[html]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},
"[css]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},
"[less]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},
"[scss]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},
"[markdown]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
},

"editor.codeActionsOnSave": {
  "source.fixAll.eslint": true
},
"[vue]": {
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": false
  }
},

// 两者会在格式化js时冲突，所以需要关闭默认js格式化程序 
"javascript.format.enable": false
```

另外vscode需要安装`Prettier-Code formatter` 插件，否则`esbenp.prettier-vscode`配置会报找不到插件的错误，导致配置不生效

## vscode 的格式化程序和插件
```json
// 代表默认格式js程序是否开启，默认开启。用eslint格式化js时需要设置为false
"javascript.format.enable": true 
```
### Vetur
用于在 vue 单文件中展示不同语言，有`格式化`、`展示`、`高亮`等能力，安装即默认开启格式化，`vetur` 还内置了`prettier`和 `eslint` 的能力。
```json
// 关闭vetur格式化程序 ，防止和eslint冲突
"vetur.format.defaultFormatter.js": "none",
"vetur.format.defaultFormatter.html": "none",
"vetur.format.defaultFormatter.ts": "none",
"vetur.validation.script": false,
"vetur.validation.template": false,
```
### Volar
vue的二代插件,支持vue3，除了集成了vetur的相关功能，如`高亮`、`语法提示`等。当然还很多其他强大的功能，可以参考官方文档。
```json
// 关闭volar格式化程序 ,加载过慢。
"editor.codeActionsOnSave": {
  "source.fixAll.volar": false
},
```

项目可以`package.json`添加命令用于stylelint的全局格式检查和修复
```json
"stylelint": "stylelint \"src/**/*.{css,scss,vue}\"",
"stylefix": "stylelint \"src/**/*.{css,scss,vue}\" --fix",
```
执行yarn stylelint 报错，需要安装解析scss相关依赖
`yarn add --dev stylelint-config-html postcss-html postcss-scss`

## 加入pre-commit钩子校验代码
以上设置了eslint自动修复代码错误以及prettier进行代码格式化，但在开发中难免遇到无法fix的错误代码，可能开发人员忘记修改，那这个时候把代码提交到远程仓库，那将是糟糕的代码。为解决这个问题，我们可以通过git hooks的pre-commit钩子来实现，主要的工具是husky和lint-staged这个两个包。

>husky 是一个为 git 客户端增加 hook 的工具。安装后，它会自动在仓库中的 .git/ 目录下增加相应的钩子；比如 pre-commit 钩子就会在你执行 git commit 的触发。
>lint-staged，一个仅仅过滤出 Git 代码暂存区文件(被 git add 的文件)的工具；这个很实用，因为我们如果对整个项目的代码做一个检查，可能耗时很长，如果是老项目，要对之前的代码做一个代码规范检查并修改的话，特别消耗时间，所以lint-staged对开发者来说比较友好。

#### 安装
`yarn add --dev husky lint-staged`
#### 配置
lint-staged的配置可以配置到package.json中也可以根目录创建配置文件.lintstagedrc
```json
// lintstagedrc.json
{
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "{!(package)*.json,*.code-snippets,.!(browserslist)*rc}": [
      "prettier --write--parser json"
    ],
    "package.json": [
      "prettier --write"
    ],
    "*.vue": [
      "eslint --fix",
      "prettier --write",
      "stylelint --fix"
    ],
    "*.{scss,less,styl,html}": [
      "stylelint --fix",
      "prettier --write"
    ],
    "*.md": [
      "prettier --write"
    ]
  }
```
```json
// package.json
"scripts": {
  "lint": "lint-staged"
}

```
#### 初始化husky
执行下列命令会在.husky下创建一个pre-commit文件
```
yarn husky install # 创建.husky文件
yarn husky add .husky/pre-commit
```
接下来在生成的.husky文件夹下的pre-commit 文件写入comiit之前的需要执行的校验代码格式的命令即可实现，校验不成功则不会执行commit
```
# pre-commit
echo "---代码格式检查开始---"
yarn lint
echo "---检查结束---"
```

至此大功告成
## 总结
>创建vue3+ts项目，使用prettier集成eslint代码检查、stylelint样式格式化，prettier作为中间件控制eslint和stylelint的格式标准，并已此标准检查报错和格式化代码。
关闭了vscode本身的代码格式化和Volar的格式化，使用各插件处理解决几种格式化工具之间的冲突。
使用husky和lint-staged检查并格式化git暂存区的代码。
避免团队成员提交不符合团队规范标准的代码到远程仓库，至此完成了多人合作的项目的代码规范标准化，极大的方便了后续代码维护修改，提高了项目代码的易读性，同时严格规范代码避免垃圾代码侧面的提升了代码性能。