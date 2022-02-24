# Class与Style绑定

操作元素的class列表和内联样式是数据绑定的一个常见需求。因为它们都是属性，所以可以用`v-bind`来处理它们：通过表达式计算出字符串结果即可。由于，字符串拼接麻烦易错，将`v-bind`用于`class`和`style`时，Vue.js作了专门的增强：表达式结果的类型除了可以是字符串，还可以是对象或者数组。

## 1、绑定HTML class

### 1.1、对象语法

传递给`v-bind:class`一个对象：

```html
<div v-bind:class="{ active: isActive }"></div>
```

这个语法表示`active`这个class存在与否取决于数据属性`isActive`的_truthiness_。

还可以在对象中传入更多属性来动态切换多个class。此外，`v-bind:class`指令还可以与普通的class属性共存。例如：

```html
<div id="class-example" class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
<script>
new Vue({
  el: '#class-example',
  data: {
    isActive: true,
    hasError: false
  }
})
</script>
```

结果会渲染为：

```html
<div class="static active"></div>
```

当`isActive`或`hasError`变化时，class列表会相应的更新。

绑定的数据对象不必内联定义在模板里，比如：

```html
<div id="class-example"
  v-bind:class="classObject"
></div>
<script>
new Vue({
  el: '#class-example',
  data: {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
})
</script>
```

也可以绑定一个返回对象的计算属性：

```html
<div id="class-example" v-bind:class="classObject"></div>
<script>
new Vue({
  el: '#class-example',
  data: {
    isActive: true,
    error: null
  },
  computed: {
    classObject: function () {
      return {
        active: this.isActive && !this.error,
        'text-danger': this.error && this.error.type === 'fatal'
      }
    }
  }
})
</script>
```

### 1.2、数组语法

可以把数组传给`v-bind:class`，以应用一个class列表：

```html
<div id="class-example"
  v-bind:class="[activeClass, errorClass]"
></div>
<script>
new Vue({
  el: '#class-example',
  data: {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
})
</script>
```

结果会渲染为：

```html
<div class="active text-danger"></div>
```

也可以根据条件切换列表中的class，采用三元表达式：

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

这样就会始终添加`errorClass`，只有在`isActive`是_truthy_（真值，即除 `false`、`0`、`""`、`null`、`undefined` 和 `NaN` 以外皆为真值）时才添加`activeClass`。

另外，在数组语法中也可以使用对象语法：

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

### 1.3、用在组件上

当在自定义组件上使用`class`属性时，这些class将被添加到该组件的根元素上，根元素上已经存在的class不会被覆盖。比如，自定义组件：

```js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

为组件添加class：

```html
<my-component class="baz boo"></my-component>
```

渲染的结果为：

```html
<p class="foo bar baz boo">Hi</p>
```

对动态数据绑定cass也同样适用：

```html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

当`isActvie`为真值时，渲染结果为：

```html
<p class="foo bar active">Hi</p>
```

## 2、绑定内联样式

### 2.1、对象语法

`v-bind:style`的对象语法十分直观——非常像CSS，但实际上是JavaScript对象。CSS属性名可以用驼峰式（camelCase）或者中间线分隔（kebab-case，但是要用单引号括起来）来命名：

```html
<div id="style-example" v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
<script>
new Vue({
  el: '#style-example',
  data: {
      activeColor: 'red',
      fontSize: 30
  }
})
</script>
```

绑定一个style对象，更加清晰明了：

```html
<div id="style-example" v-bind:style="styleObject"></div>
<script>
new Vue({
  el: '#style-example',
  data: {
      styleObject: {
        color: 'red',
        fontSize: '13px'
      }
  }
})
</script>
```

当然，也可以结合返回对象的计算属性使用。

### 2.2、数组语法

`v-bind:style`的数组语法可以将多个样式对象应用到同一个元素上：

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

### 2.3、自动添加前缀

当`v-bind:style`使用需要添加浏览器引擎前缀的CSS属性时，比如`transform`，Vue会自动侦测并添加相应的前缀。

### 2.4、多重值

从Vue 2.3.0版本开始可以为`style`绑定中的属性提供一个包含多个值的数组，常用于提供多个带前缀的值，比如：

```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

这样写只会渲染数组中最后一个被浏览器支持的值。如果，浏览器支持不带前缀的flexbox，那么渲染结果是`display:flex`。

