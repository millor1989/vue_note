# VUE

## 1、简介

* MVVM框架
* 数据和视图分离
* 双向绑定：视图和数据双向绑定，互相影响
* 以数据驱动视图，只关心数据变化
* web组件化

## 2、MVVM框架

### 2.1、MVC

Model——数据

View——视图

Controller——控制器

### 2.2、MVVM

#### 2.2.1、Model——数据

对应Vue的`data`

#### 2.2.2、View——视图

对应Vue的`template`

#### 2.2.3、ViewModel——视图模型

对应Vue的`new Vue({...})`

#### 2.2.4、三者的关系

View通过事件绑定的方式响应Model，Model通过数据绑定（获取中的数据？？）影响View，ViewModel是Model和View的连接

## 3、顺带提及webpack

webpack主要用途是通过CommonJS的语法把需要发布的所有静态资源作相应的准备，比如合并和打包。

例如，脚本文件_app.js_，依赖了另一个脚本_module.js_，通过命令：

```shell
webpack app.js bundle.js
```

可以把_app.js_和_module.js_合并到一起，即合并为_bundle.js_

webpack提供了loader机制和plugin机制，loader机制支持载入各种静态资源（包括js、html、css、images等）；plugin机制可以对整个webpack流程进行一定的控制。

背后原理是将所有的非js资源都转换为js（比如，将css文件转换为`style`代码片段，图片则转换为一个图片地址的js变量或base64编码等），然后用CommonJS的机制管理起来。

同样，Vue文件也可以转换为webpack包。（vue+webpack+vue-loader）

