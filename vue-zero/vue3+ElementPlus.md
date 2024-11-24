### 在浏览器存储存储东西

```js
localStorage.setItem("user-token", data.data);//存储
localStorage.getItem("user-token");//获取
```

-------------------





### 版本查看

```shell
npm list 包或库的名称
npm list vue
```

-------





### 清除依赖并重新下载

```shell
#清理缓存
npm cache clean --force
#删除依赖
rm -rf node_modules
#下载依赖
npm install
```

-------







###  创建 Vue 3 环境和新项目

####   Vue CLI 构建

```shell
vue create 项目名称
vue create vue3-app
```

选择 自定义按下面的选择，然后 `cd` 到 vue3-app 项目目录，接下里的操作都在这个目录里完成。

![](D:\note\vue-zero\vue3+ElementPlus.assets\1694657258058.png)

生成后文件目录说明

```shell
|-- public //无需打包的静态资源文件
|-- src //项目源代码目录
    |-- assets //需要打包的静态资源
    |-- components //公共组件
    |-- main.ts //项目入口文件
    |-- shims-vue.d.ts //声明文件
|-- .browserslistrc //兼容目标浏览器范围的配置文件
|-- .eslintrc.js //eslint配置文件
|-- babel.config.js //babel配置文件
|-- tsconfig.json //ts配置文件
```

#### Vite 构建(推荐)

----------------

```shell
npm create vite@latest 项目名称
npm create vite@latest test-vue3-vite-ts
npm create vite@latest vue3-vite-ts
```

![1697095941932](D:\note\vue-zero\vue3+ElementPlus.assets\1697095941932.png)

| 说明                                                         | 选择                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 选择框架                                                     | √ Select a framework: » Vue                                  |
| 使用 create-vue 进行定制                                     | √ Select a variant: » Customize with create-vue ↗            |
| 是否添加 TS                                                  | √ Add TypeScript? ... No / Yes                               |
| 是否添加 JSX  [详细说明](https://juejin.cn/post/7018742119082754062) | √ Add JSX Support? ... No / Yes                              |
| 是否添加 Vue Router(路由)                                    | √ Add Vue Router for Single Page Application development? ... No / Yes |
| 用于组件通信                                                 | √ Add Pinia for state management? ... No / Yes               |
| 测试框架                                                     | √ Add Vitest for Unit Testing? ... No / Yes                  |
| 端到端测试                                                   | √ Add an End-to-End Testing Solution? » No                   |
| 代码检查器                                                   | √ Add ESLint for code quality? ... No / Yes                  |
| 代码风格检查(格式化)                                         | √ Add Prettier for code formatting? ... No / Yes             |



-------------------





### 引入Element Plus 

 使用 `vue add` 自动帮你选择适配的  Element Plus  版本

```shell
vue add element-plus

//"element-plus": "^1.0.2-beta.28",
```

 选择完整引入，完成 Element-plus 引入。 

选择后将会提示，询问是否要重写 SCSS，一般选择 N(用于自定义主题色，之类的配置);

接着语言选择中文

---------------------



### 安装按需引入插件

 安装按需引入插件 `babel-plugin-import` 我们可以只引入需要的组件，以便打包时减小体积。 

```shell
npm install babel-plugin-import -D
```

如果出现错误使用下面命令安装

```shell
npm install babel-plugin-import  --legacy-peer-deps
```

-------





### 使用全局事件总线

#### 导入依赖

```js
//main.ts/main.js
import { createApp } from 'vue'
import App from './App.vue'
import mitt from 'mitt'//如果没有需要先npm下载

const app = createApp(App)
//在全局上挂载
app.config.globalProperties.bus = mitt()
app.mount('#app')
```

#### 注册函数

```js

import { getCurrentInstance} from "vue";
//获取全局共用属性
const globalProperties = getCurrentInstance()?.appContext.config.globalProperties

//参数一:函数名,参数二:函数执行内容
globalProperties?.bus.on("toggleDialog", (value: any) => {
  dialogVisible.value = value;
})

```

#### 触发函数

```js
import { getCurrentInstance} from "vue";
//获取全局共用属性
const globalProperties = getCurrentInstance()?.appContext.config.globalProperties

const closeDialog = (islogin: boolean = false) => {
    //参数一:函数名,参数二:函数需要的参数
  globalProperties?.bus.emit('toggleDialog', islogin)
};
```

-------







































































