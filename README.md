# VUE

## 1、简介

* MVVM 框架
* 数据和视图分离
* 双向绑定：视图和数据双向绑定，互相影响
* 以数据驱动视图，只关心数据变化
* web 组件化

## 2、MVVM框架

### 2.1、MVC

Model——数据

View——视图

Controller——控制器

### 2.2、MVVM

#### 2.2.1、Model——数据

对应 Vue 的 `data`

#### 2.2.2、View——视图

对应 Vue 的 `template`

#### 2.2.3、ViewModel——视图模型

对应 Vue 的 `new Vue({...})`

#### 2.2.4、三者的关系

View 通过事件绑定的方式响应 Model，Model 通过数据绑定（获取中的数据？？）影响 View，ViewModel 是 Model 和 View 的连接

## 3、顺带提及webpack

webpack 主要用途是通过 CommonJS 的语法把需要发布的所有静态资源作相应的准备，比如合并和打包。

例如，脚本文件 _app.js_，依赖了另一个脚本 _module.js_，通过命令：

```shell
webpack app.js bundle.js
```

可以把 _app.js_ 和 _module.js_ 合并到一起，即合并为 _bundle.js_

webpack 提供了 loader 机制和 plugin 机制，loader 机制支持载入各种静态资源（包括js、html、css、images等）；plugin 机制可以对整个 webpack 流程进行一定的控制。

背后原理是将所有的非 js 资源都转换为 js（比如，将 css 文件转换为 `style` 代码片段，图片则转换为一个图片地址的 js 变量或 base64 编码等），然后用 CommonJS 的机制管理起来。

同样，Vue 文件也可以转换为 webpack 包。（vue+webpack+vue-loader）

## 4、其它

- **ECMAScript**：ECMA International 指定了的语言规范。

- **JavaScript**：跨平台的脚本语言，遵循了 ECMAScript 的标准，即是 ECMAScript 语言规范的一种方言。JavaScript 不仅是 ECMAScript，它包含了三部分：

  - 语言核心功能基于 ECMAScript（ES 5 / ES 6 语法）规范
  - DOM——支持对 DOM 的维护，通过 document、element 对象实现（不属于 ECMAScript）
  - BOM——支持对 BOM 的维护，通过 window 对象实现（不属于 ECMAScript）

  js 代码由 JavaScript 引擎解析。

- **Node.js**：按照 ECMAScript 标准实现的，由 Chrome V8 引擎解析，但是没有 DOM 和 BOM 操作，但是增加了非阻塞 IO 模型。

- **Core.js**：JavaScript 标准库 polyfill（补丁）。将新的 ES API 转换为大部分浏览器都可以支持运行的 API 的补丁包的集合。

- **TypeScript**：微软开发和维护的开源的面向对象的编程语言。是 JavaScript 的超集，包含了 JavaScript 的所有元素，扩展了 JavaScript 语法，增加了静态类型、类、模块、接口和类型注解。可以载入 JavaScript 代码运行。