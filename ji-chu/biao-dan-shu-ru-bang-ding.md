# 表单输入绑定

## 1、基础用法

使用`v-model`指令可以在表单`<input>`、`<textarea>`和`<select>`元素上创建双向数据绑定。它会根据控件类型自动选择正确的方法来更新元素。`v-model`本质上是语法糖，它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。

`v-model`会忽略所有的表单元素属性（`value`、`checked`、`selected`）的初始值，而总是将Vue实例的数据作为数据源。所以，应该通过JavaScript，在组件的`data`选项中声明相应的初始值。

`v-model`在内部为不同的输入元素使用不同的属性并抛出不同的事件：

- text和textarea是：`value`属性和`input`事件；
- checkbox和radio是：`checked`属性和`change`事件；
- select是：`value`作为prop，`change`作为事件。

对于使用输入法输入的语言（比如，中文、日文等），`v-model`不会在输入发组合文字的过程中更新。如果要处理这个过程，要使用`input`事件。

### 1.1、文本

```html
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

### 1.2、多行文本

```html
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

对于`<textarea>`使用插值（`<textarea>{{text}}</textarea>`）不会生效，应该使用`v-model`来代替。

### 1.3、复选框

单个复选框，绑定布尔值：

```html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```

多个复选框，绑定到同一个数组：

```html
<div id='example-3'>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>
</div>
<script>
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
</script>
```

### 1.4、单选按钮

```html
<div id="example-4">
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">One</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Two</label>
  <br>
  <span>Picked: {{ picked }}</span>
</div>
<script>
new Vue({
  el: '#example-4',
  data: {
    picked: ''
  }
})
</script>
```

### 1.5、选择框

单选时：

```html
<div id="example-5">
	<label for="select">Select:</label>
    <select id="select" v-model="selected">
      <option disabled value="">请选择</option>
      <option>A</option>
      <option>B</option>
      <option>C</option>
    </select>
	<br/>
    <span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-5',
  data: {
    selected: ''
  }
})
</script>
```

如果`v-model`表达式的初始值没有匹配任何选项，`<selected>`元素将被渲染为“为选中”状态。在iOS中，这会使用户无法选择第一个选项，因为，此时，iOS不会触发change事件；所以，推荐提供一个空值禁用（`disabled`）选项。

多选时，绑定到一个数组：

```html
<div id="example-6">
	<label for="select">Select:</label>
    <select id="select" multiple style="width:50px;" v-model="selected">
      <option>A</option>
      <option>B</option>
      <option>C</option>
    </select>
	<br/>
    <span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-6',
  data: {
    selected: []
  }
})
</script>
```

使用`v-for`渲染动态选项：

```html
<div id="example-6">
    <select v-model="selected">
      <option v-for="option in options" v-bind:value="option.value">
        {{ option.text }}
      </option>
    </select>
	<span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-6',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
</script>
```

## 2、值绑定

对于单选按钮、复选框以及选择框的选项，`v-model`绑定的值通常是静态字符串（对于复选框也可以是布尔值）：

```html
<!-- 当选中时，`picked` 为字符串 "a" -->
<input type="radio" v-model="picked" value="a">

<!-- `toggle` 为 true 或 false -->
<input type="checkbox" v-model="toggle">

<!-- 当选中第一个选项时，`selected` 为字符串 "abc" -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

使用`v-bind`可以把值绑定到Vue实例的动态属性上，并且属性的值可以不是字符串。

### 2.1、复选框

```html
<!--
// 当选中时
vm.toggle === 'yes'
// 当没有选中时
vm.toggle === 'no'
-->
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no"
>
```

这里`true-value`和`false-value`属性不会影响输入控件的`value`属性，因为浏览器在提交表单时不会包含未被选中的复选框。如果要确保表单中这两个值一个被提交，那么应该使用单选按钮。

### 2.2、单选按钮

```html
<!--
// 当选中时
vm.pick === vm.a
-->
<input type="radio" v-model="pick" v-bind:value="a">
```

### 2.3、选择框的选项

```html
<!--
// 当选中时
typeof vm.selected // => 'object'
vm.selected.number // => 123
-->
<select v-model="selected">
    <!-- 内联对象字面量 -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
```

## 3、修饰符

### 3.1、`.lazy`

默认情况下，`v-model`在每次`input`事件触发后将输入框的值与数据进行同步（除了之前提到的输入法组合文字时）。可以添加`.lazy`修饰符，从而改为使用`change`事件进行同步：

```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" >
```

### 3.2、`.number`

如果要自动将用户的输入值转为数值类型，可以给`v-model`添加`.number`修饰符：

```html
<input v-model.number="age" type="number">
```

这个修饰符很有用，因为即使在`type="number"`时，HTML输入元素的值也总会返回字符串。当值无法被`parseFloat`解析时，返回原始的值。

### 3.3、`.trim`

`.trim`修饰符可以自动过滤用户输入的首位空白字符：

```html
<input v-model.trim="msg">
```

