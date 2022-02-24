# Vue实例

### 1、创建Vue实例

每个Vue应用都是通过使用Vue函数创建新的Vue实例开始的：

```js
var vm = new Vue({
  // 选项
})
```

代码和文档经常会使用`vm`（ViewModel的缩写）表示Vue实例。

### 2、数据与方法

当Vue实例被创建时，它将`data`对象中的所有属性加入到Vue的响应式系统中。当这些属性改变时，视图就会产生响应。

```js
// 我们的数据对象
var data = { a: 1 }

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 获得这个实例上的属性
// 返回源数据中对应的字段
vm.a == data.a // => true

// 设置属性也会影响到原始数据
vm.a = 2
data.a // => 2

// ……反之亦然
data.a = 3
vm.a // => 3
```

只有当实例被创建时，存在于`data`中的属性才是响应式的。

`Object.freeze(Object obj)`会阻止修改对象_obj_的属性：

```html
<div id="app">
  <p>{{ foo }}</p>
  <!-- 这里的 `foo` 不会更新！ -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>

<script>
var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
</script>
```

点击按钮，则`p`标签的内容**不会**变为_baz_。

除了数据属性，Vue实例还暴露了一些有用的实例属性和方法，它们都有前缀`$`，以便与用户定义的属性区分，例如：

```javascript
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch 是一个实例方法
vm.$watch('a', function (newValue, oldValue) {
  // 这个回调将在 `vm.a` 改变后调用
})
```

### 3、实例生命周期hook

每个Vue实例在被创建时都要经历一系列的初始化过程，比如：数据监听、编译模板、实例挂载到dom、数据变化时更新dom等。在这些过程中，会运行一些叫做**生命周期hook**的函数。比如，使用`created` hook函数，可以在实例被创建后执行代码：

```javascript
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

类似的hook函数还有`mounted`、`updated`、`destroyed`。hook函数的`this`上下文指向调用它的Vue实例。

**注意**，不要在选项属性或回调上使用**箭头函数表达式**（简洁，但是没有`this`，`arguments`，`super`或`new.target`），没有`this`，`this`会作为变量一直向上级词法作用域查找，直到找到为止，经常会导致`Uncaught TypeError: Cannot read property of undefined`或`Uncaught TypeError: this.myMethod is not a function`之类的错误。

### 4、Vue实例生命周期

![](/assets/vueinstancelifecycle.png)

