# 入门

### 1、声明式渲染

Vue.js的核心是可以采用简洁的模板语法来声明式地将数据渲染进视图的dom。

```html
<div id="app">
  <p>{{ message }}</p>
</div>

<script>
new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!'
  }
})
</script>
```

![](/assets/1582880212868.png)

也可以用如下方式绑定元素的attribute：

```html
<div id="app">
  <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
</div>
```

`v-bind`:attribute被称为**指令**。dom的属性attribute会和数据进行绑定。

### 2、条件与循环

通过seen的值可以控制标签p是否显示

```html
<div id="app-3">
  <p v-if="seen">现在你看到我了</p>
</div>
<script>
new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
</script>
```

所以，通过数据不仅能控制dom的文本和属性，还可以控制dom的结构。此外，Vue提供了强大的过渡效果系统，在Vue改变dom结构时自动应用过渡效果。

类似的还有`v-for`、`v-else-if`、`v-else`。

### 3、事件响应和数据绑定

使用`v-on`可以监听事件，对事件进行响应处理：

```html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">反转消息</button>
</div>

<script>
new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
</script>
```

使用`v-model`指令，可以将表单输入和数据绑定：

```html
<div id="app-5">
  <p>{{ message }}</p>
  <input v-model="message">
</div>

<script>
new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
```

改变输入框（`input`）的值，则标签`p`的内容也会随着改变。

### 4、组件化

在Vue中定义组件很简单：

```js
// 定义名为 todo-item 的新组件
Vue.component('todo-item', {
  template: '<li>这是个待办项</li>'
})

var app = new Vue(...)
```

然后，就可以使用它：

```html
<ol>
  <!-- 创建一个 todo-item 组件的实例 -->
  <todo-item></todo-item>
</ol>
```

还可以为组件定义属性：

```js
Vue.component('todo-item', {
  // todo-item 组件现在接受一个
  // "prop"，类似于一个自定义 attribute。
  // 这个 prop 名为 todo。
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

使用如下：

```html
<div id="app-7">
  <ol>
    <!--
      现在我们为每个 todo-item 提供 todo 对象
      todo 对象是变量，即其内容可以是动态的。
      我们也需要为每个组件提供一个“key”，稍后再
      作详细解释。
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id"
    ></todo-item>
  </ol>
</div>
<script>
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: '蔬菜' },
      { id: 1, text: '奶酪' },
      { id: 2, text: '猕猴桃' }
    ]
  }
})
</script>
```

效果如下：

![](/assets/1582882105830.png)

