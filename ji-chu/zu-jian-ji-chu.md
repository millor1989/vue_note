# 组件基础

## 1、基本示例

```javascript
// 定义一个名为 button-counter 的新组件
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```

组件是可复用的Vue实例，且有名字，本例组件名字是`<button-counter>`。在通过`new Vue`创建的Vue根实例中，可以把这个组件作为自定义元素来使用：

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
<script>
    new Vue({ el: '#components-demo' })
</script>
```

组件是可复用的Vue实例，所以，它们和`new Vue`有同样的选项，比如：`data`，`computed`，`watch`，`methods`以及生命周期hook等。不同的是Vue根实例特有的`el`选项。

## 2、组件的复用

可以将组将复用任意次：

```html
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

其中，每个组件都会拥有独立的`data`等，互不影响。每使用一次组件，就会有一个新的实例被创建。

### 2.1、组件的`data`必须是一个函数

定义组件`<button-counter>`组件时，`data`不是提供一个对象：

```javascript
data: {
  count: 0
}
```

而是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝：

```javascript
data: function () {
  return {
    count: 0
  }
}
```

## 3、组件的组织

通常一个应用会以一棵嵌套的组件树的形式来组织：

![](/assets/1584069012860.png)

如图，有页头、侧边栏、内容区等组件，每个组件又包含了其它像导航链接、博文、广告位之类的组件。

为了能在模板中使用，必须注册组件以便Vue能够识别。组件注册有分为**全局注册**和[**局部注册**](/zu-jian-zhu-ce.md)。通过`Vue.component`是进行全局注册：

```html
Vue.component('my-component-name', {
  // ... options ...
})
```

全局注册的组件可以用在其被注册之后的任何（通过`new Vue`）新创建的Vue根实例，也包括其组件树中的所有子组件的模板中。

## 4、通过Prop向子组件传递数据

Prop是可以在组件上注册的一些自定义的属性（attribute）。当一个值传递给一个prop属性时，它就成为了那个组件实例的一个属性。比如，为了给博文组件传递一个标题，可以使用`props`选项将其包含在该组件可以接受的prop列表中：

```javascript
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
```

一个组件默认可以拥有任意数量的prop，任何值都可以传递给任何prop。在组件中访问prop和访问`data`中的值一样。prop被注册之后，可以把它像自定义属性一样来使用：

```html
<blog-post title="My journey with Vue"></blog-post>
<blog-post title="Blogging with Vue"></blog-post>
<blog-post title="Why Vue is so fun"></blog-post>
```

在典型的应用中，可能在`data`中有一个博文数组：

```javascript
new Vue({
  el: '#blog-post-demo',
  data: {
    posts: [
      { id: 1, title: 'My journey with Vue' },
      { id: 2, title: 'Blogging with Vue' },
      { id: 3, title: 'Why Vue is so fun' }
    ]
  }
})
```

可以使用`v-bind`指令来动态传递prop：

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
></blog-post>
```

## 5、单个根元素

每个组件必须只有一个根元素。即，构建组件时，组件的结构应该是：

```html
<div class="blog-post">
  <h3>{{ title }}</h3>
  <div v-html="content"></div>
</div>
```

而不能是：

```
<h3>{{ title }}</h3>
<div v-html="content"></div>
```

当组件变得复杂，为每个信息定义一个prop会很麻烦：

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
  v-bind:content="post.content"
  v-bind:publishedAt="post.publishedAt"
  v-bind:comments="post.comments"
></blog-post>
```

可以将其重构为接收单一`post` prop的组件：

```javascript
Vue.component('blog-post', {
  props: ['post'],
  // 此处使用了`模板字符串`，让多行模板易读，但是IE不支持，可以用`折行转义字符`替代
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <div v-html="post.content"></div>
    </div>
  `
})
```

不论何时为`post`对象添加一个新的属性，它都会自动地在`<blog-post>`内可用。

## 6、监听子组件事件

如下为一个通过子组件按钮点击事件改变博文字号的例子：

```html
<div id="app">
    <div :style="{fontSize: blogFontSize + 'em'}">
        <blog-item
        v-for="item in blogs"
        v-bind:key="item.id"
        v-bind:blog="item" v-on:enlarge-text="blogFontSize += 0.1"></blog-item>
    </div>
</div>

<script>
Vue.component('blog-item', {
  props: ['blog'],
  template: `
      <div>
          <h3>{{ blog.title }}</h3>
              <button v-on:click="$emit('enlarge-text')">
                Enlarge text
              </button>
          <div v-html="blog.content"></div>
      </div>
  `
})
/* 模板部分使用`折行转义字符`表示
Vue.component('blog-item', {
  props: ['blog'],
  template: '<div>\
      <h3>{{ blog.title }}</h3>\
          <button v-on:click="$emit(\'enlarge-text\')">\
            Enlarge text\
          </button>\
      <div v-html="blog.content"></div>\
  </div>'
})
*/
new Vue({
  el: '#app',
  data: {
    blogs: [
      { id:1,title: 'Runoob',content:'RunoobRunoob' },
      { id:2,title: 'Google',content:'GoogleGoogleGoogleGoogle' },
      { id:3,title: 'Taobao',content:'TaobaoTaobaoTaobao' }
    ],
    blogFontSize: 0.1
  }
})
</script>
```

Vue实例提供了一个自定义事件系统，父级组件可以像处理native dom事件一样通过`v-on`监听子组件实例的任意事件（自定义事件）。子组件通过调用内建的`$emit`方法来触发一个事件，在本例中为`v-on:click="$emit('enlarge-text')"`。使用`v-on:enlarge-text="blogFontSize += 0.1"`监听器，父级组件就能响应`enlarge-text`事件并更新`blogFontSize`的值。

### 6.1、使用事件抛出一个值

在组件中使用一个事件来抛出一个特定的值非常有用。对于上例，可以让`blog-item`组件提供它希望放大文本的量。可以使用`$emit`方法的第二个参数提供这个量：

```html
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
```

在父级组件监听这个事件时，通过`$event`访问被抛出的这个值：

```html
<blog-item
  <!--...-->
  v-on:enlarge-text="blogFontSize += $event"
></blog-item>
```

如果处理这个事件的是一个方法：

```html
<blog-item
  <!--...-->
  v-on:enlarge-text="onEnlargeText"
></blog-item>
```

那么，随事件抛出的值会作为第一个参数传入这个方法：

```javascript
methods: {
  onEnlargeText: function (enlargeAmount) {
    this.blogFontSize += enlargeAmount
  }
}
```

### 6.2、在组件上使用`v-model`

自定义事件也可以用于创建支持`v-model`的自定义输入组件。

其实：

```html
<input v-model="searchText">
```

等价于：

```html
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value">
```

在组件上使用`v-model`时：

```html
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event"
></custom-input>
```

为了让它正常工作，组件内的`<input>`必须满足以下条件：

* 将其`value`属性绑定到一个名为`value`的prop上；
* 在其`input`事件被触发时，将新的值通过自定义的`input`事件抛出。

即，将组件如下定义：

```javascript
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

然后，就可以在这个组件上使用`v-model`了：

```html
<custom-input v-model="searchText"></custom-input>
```

## 7、通过插槽分发内容

和HTML元素一样，可能需要向组件元素传递内容，比如：

```html
<alert-box>
  Something bad happened.
</alert-box>
```

但是，这样可能达不到效果，标签之间的内容不会展示。

使用Vue自定义的`<slot>`元素使这变得非常简单：

```html
Vue.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```

只用在需要的地方加入`<slot>`就行了。

## 8、动态组件

在不同组件之间进行动态切换非常有用，可以通过Vue的`<component>`元素加一个特殊的`is`属性来实现：

```html
<!-- 组件会在 `currentTabComponent` 改变时改变 -->
<component v-bind:is="currentTabComponent"></component>
```

在这个示例中，`currentTabComponent`可以包括：

* 已注册组件的名字
* 一个组件的选项对象

`is`属性也可以用于常规的HTML元素，但这些元素将被视为组件，这意味着所有的属性都会被作为dom属性被绑定。对于像`value`这样的属性，如果想要让它如预期般的工作，需要使用`.prop`修饰器。

## 9、解析DOM模板时的注意事项

对于某些HTML元素，例如`<ul>`、`<ol>`、`<table>`、`<select>`，对可以出现在其内部的元素都是有严格限制的；而某些元素，比如`<li>`、`<tr>`和`<option>`只能出现在某些特定的元素内部。这会导致使用时遇到一些问题，比如：

```html
<table>
  <blog-post-row></blog-post-row>
</table>
```

这个自定义组件`<blog-post-row>`会被作为无效的内容提升到外部，并导致最终渲染结果出错。`is`属性提供了解决方案：

```html
<table>
  <tr is="blog-post-row"></tr>
</table>
```

但是，如果从以下来源使用模板的话，这些限制是**不存在**的：

* 字符串（例如：`template:'...'`）
* 单文件组件（`.vue`）
* `<script type="text/x-template"/>`



