# 条件渲染

## 1、`v-if`

`v-if`指令条件性地渲染一块内容，这块内容只会在指令的表达式返回*truthy*（真值）时被渲染。

```html
<h1 v-if="awesome">Vue is awesome!</h1>
```

可以搭配`v-else`使用：

```html
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

### 1.1、在`<template>`元素上使用`v-if`条件渲染分组

`v-if`指令必须加在元素上，如果要用它同时控制多个元素，可以使用`<template>`元素包括多个元素，并在`<template>`元素上使用`v-if`。

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### 1.2、`v-else`

配合`v-if`使用，否则不会被识别

```html
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```

### 1.3、`v-else-if`

配合`v-if`使用，可以连续使用

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

### 1.4、`key`管理可重复的元素

为了高效渲染元素，Vue通常会复用已有元素而不是从头开始渲染。例如，切换登录方式：

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

其中切换`loginType`不会清除用户已经输入的内容。因为两个模板使用了相同的元素，`<input>`不会被替换掉，替换的是它的`palceholder`。但是，这样不总是符合需求，所以Vue提供了`key`属性，不同`key`属性的元素完全独立，不能重用：

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

其中，`<label>`元素仍然会复用。

## 2、`v-show`

```html
<h1 v-show="ok">Hello!</h1>
```

`v-show`控制的元素始终被保留在dom中，`v-show`只是简单地切换元素的CSS属性`display`。

`<template>`元素不支持`v-show`

## 3、`v-show` VS `v-if`

`v-if`是“真正”的条件渲染，它会确保在切换过程中条件块内的事件监听器和子组件适当的销毁和重建。`v-if`是惰性的：如果初始条件为假，则什么也不做——直到条件第一次为真值时才开始渲染条件块。

`v-show`元素总是会被渲染，并且只是简单地修改CSS属性。

一般来说，`v-if`切换开销高，`v-show`初始渲染开销高。要根据实际场景选择。

## 4、`v-if`与`v-for`

`v-if`与`v-for`同时使用时，`v-for`比`v-if`优先级高。但是，不推荐同时使用二者。