# 列表渲染`v-for`

## 1、数组`v-for`

`v-for`指令可以基于一个数组来渲染一个列表。`v-for`指令需要使用`item in items`形式的特殊语法，其中`items`是源数据数组，而`item`则是数组元素别名。

```html
ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
<script>
new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
</script>
```

渲染结果：

![](/assets/1583373804408.png)

可以用`of`替换迭代语法中的`in`：

```html
<div v-for="item of items"></div>
```

在`v-for`块中，可以访问所有的父作用域属性。`v-for`迭代数组的语法中还有一个可选的参数——索引：

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
<script>
new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
</script>
```

渲染结果：

![](/assets/1583374093936.png)

## 2、对象`v-for`

`v-for`也可以用来遍历一个对象的属性：

``` html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
<script>
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
</script>
```

渲染结果：

![](/assets/1583374929961.png)

`v-for`遍历对象属性有两个可选参数——属性名（键名）、索引。

```html
<div id="v-for-object" class="demo">
  <div v-for="(value,name,index) in object">
    {{index}}.{{name}}:{{ value }}
  </div>
</div>
<script>
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
</script>
```

渲染结果：

![](/assets/1583374991993.png)

遍历对象时，`v-for`按照`Object.keys()`的结果遍历，但是不能保证不同JavaScript引擎下遍历顺序一致。

## 3、维护状态

当正在更新`v-for`的目标（数组、对象）时，Vue默认使用“就地更新”策略。如果目标顺序改变，Vue不会移动dom元素位置来匹配数据的顺序，而是更新每个元素，以确保每个位置正确渲染。这种模式高效，但是，只适用于不依赖子组件状态或临时dom状态（比如，表单输入）的列表渲染。为了给每个元素一个身份，从而重用和重排现有元素，可以给每个元素提供一个唯一的`key`属性：

```html
<div v-for="item in items" v-bind:key="item.id">
  <!-- 内容 -->
</div>
```

建议使用`v-for`时提供`key`属性，除非输出dom内容非常简单，或者需要依赖默认策略的高效。

应当使用字符串或者数值类型的值作为`key`的值。

## 4、数组变更检测

### 4.1、变异方法（mutation method）

Vue将`v-for`的目标数组的变异方法进行了包裹，使用这些变异方法会触发视图的更新。这些变异方法包括：

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

### 4.2、替换数组

相对于变异方法，也有**非变异方法**，例如：`filter()`、`concat()`、`slice()`，这些方法不会改变原始的数组，而是返回一个新的数组。使用非变异方法时，可以用新数组替换旧数组：

``` JavaScript
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

即使是替换数组，Vue也不会丢弃现有的dom重新渲染整个列表，而是采用智能的启发式的方法最大范围的重用dom元素，所以用一个含有相同元素的数组去替换原来的数组是非常高效的。

### 4.3、注意事项

Vue不能检测到以下数组变动：

1. 利用索引设置数组元素，比如：`vm.items[indexOfItem] = newValue`；
2. 修改数组长度，比如：`vm.items.length = newLength`。

例子：

```javascript
new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // 不是响应性的
vm.items.length = 2 // 不是响应性的
```

但是，针对这些情况Vue有替代方案，比如：

```javascript
/*对于第一类问题*/
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
// 使用vm.$set，Vue.set方法的别名
vm.$set(vm.items, indexOfItem, newValue)
/*对于第二类问题*/
// 使用splice
vm.items.splice(newLength)
```

## 5、对象变更检测

还是由于JavaScript的限制，Vue不能检测到对象属性的添加或删除：

```javascript
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` 现在是响应式的

vm.b = 2
// `vm.b` 不是响应式的
```

对于已经创建的实例，Vue不允许动态添加根级别的响应式属性。但是，可以使用`Vue.set(object, propertyName, value)`方法向嵌套对象添加响应式属性。例如：

```javascript
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
// 添加一个新的属性age到嵌套对象userProfile
Vue.set(vm.userProfile, 'age', 27)
// 也可以使用Vue.set的别名——vm.$set
vm.$set(vm.userProfile, 'age', 27)
```

如果使用`Object.assign()`或`_.extend()`为已有对象添加多个新属性，那么应该为两个新属性创建一个新的对象，具体实现，如下：

```javascript
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

而**不是**：

```javascript
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

## 6、显示过滤/排序后的结果

如果要显示一个数组经过过滤或排序后的结果，而不改变或重置原始数据。可以创建一个计算属性：

```html
<ul id="v-for-ary">
  <li v-for="n in evenNumbers">{{ n }}</li>
</ul>
<script>
new Vue({
  	el: '#v-for-ary',
	data: {
	  numbers: [ 1, 2, 3, 4, 5 ]
	},
	computed: {
	  evenNumbers: function () {
		return this.numbers.filter(function (number) {
		  return number % 2 === 0
		})
	  }
	}
})
</script>
```

计算属性不适用的情况下（比如，在嵌套`v-for`循环中），可以使用方法：

```html
<ul id="v-for-ary">
  <li v-for="n in even(numbers)">{{ n }}</li>
</ul>
<script>
new Vue({
  	el: '#v-for-ary',
    data: {
      numbers: [ 1, 2, 3, 4, 5 ]
    },
    methods: {
      even: function (numbers) {
        return numbers.filter(function (number) {
          return number % 2 === 0
        })
      }
    }
})
</script>
```

## 7、`v-for`中使用值范围

`v-for`可以接收一个整数*n*，并把模板重复*n*次：

```html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

结果：

![](/assets/1583460943362.png)

## 8、在`<template>`中使用`v-for`

可以用带有`v-for`的`<template>`渲染一组元素：

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## 9、`v-for`与`v-if`

`v-for`比`v-if`的优先级高，如果同时应用于同一个元素，`v-if`将分别重复运行于每次`v-for`循环。如果只渲染循环中的部分内容，这种优先级机制十分有用：

```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

这段代码将只渲染`isComplete`属性为非真值的todo。

**但是**，`v-for`和`v-if`同时使用是Vue不推荐。

## 10、组件上使用`v-for`

在自定义组件上使用`v-for`与普通元素上使用类似：

```html
<my-component v-for="item in items" :key="item.id"></my-component>
```

在Vue 2.2.0以上的版本，在组件上使用`v-for`时，`key`是**必须**的。

但是，任何数据都不能传递到组件里，组件有自己独立的作用域。要使用prop把数据传递到组件里：

```html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
></my-component>
```

如下是一个简单的todo列表完整例子：

```html
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat"
    >
    <button>Add</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
<script>
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Remove</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Do the dishes',
      },
      {
        id: 2,
        title: 'Take out the trash',
      },
      {
        id: 3,
        title: 'Mow the lawn'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
</script>
```

其中`is="todo-item"`十分必要，因为在`<ul>`元素内只有`<li>`元素会被看作有效内容，这样效果与`todo-item`相同，同时也避免了潜在的浏览器解析错误。

