# 事件处理

## 1、监听事件

使用`v-on`指令可以监听dom事件，并在事件触发时运行一些JavaScript代码：

```html
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
<script>
new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
</script>
```

## 2、事件处理方法

对于比较复杂的处理，`v-on`指令可以通过接收一个需要调用的**方法名称**来实现：

```html
<div id="example-2">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>
<script>
new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // `this` 在方法里指向当前 Vue 实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
</script>
```

## 3、内联处理器中的方法

除了直接绑定到一个方法，也可以在内联JavaScript语句中调用方法：

```html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
<script>
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
</script>
```

如果需要在内联语句处理器中访问原始的dom事件，可以用特殊变量`$event`将其传入：

```
<div id="example-4">
	<form>
		<input name="input"/>
		<button type="submit" v-on:click="warn('Form cannot be submitted yet.', $event)">
  		Submit
		</button>
	</form>
</div>
<script>
new Vue({
  el: '#example-4',
  methods: {
	  warn: function (message, event) {
		// 现在我们可以访问原生事件对象
		if (event) {
		  // 阻止默认的提交行为
		  event.preventDefault()
		}
		alert(message)
	  }
  }
})
</script>
```

## 4、事件修饰符

在事件处理时调用`event.preventDefault()`或者`event.stopPropagation()`是非常常见的需求。尽管方法中可以实现这种需求，但是应该尽量使方法成为纯粹的数据逻辑，而不用方法处理dom事件细节。因此，Vue提供了`v-on`指令**事件修饰符**，修饰符是由`.`开头的指令后缀表示：

- `.stop`
- `.prevent`
- `.capture`
- `.self`
- `.once`：2.1.4新增，不同于其它只能对原生dom事件起作用的修饰符，`.once`修饰符可以被用到**自定义的组件事件**上。
- `.passive`：2.3.0新增，对应`addEventListener`中的`passive`，尤其能够提升移动端性能

```html
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>

<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
<!-- 而不会等待 `onScroll` 完成  -->
<!-- 这其中包含 `event.preventDefault()` 的情况 -->
<div v-on:scroll.passive="onScroll">...</div>
```

注意，使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。比如，`v-on:click.prevent.self`会阻止所有的点击，而`v-on:click.self.prevent`只会阻止对元素自身的点击。不要把`.passive`和`.prevent`一起使用，`.prevent`将会被忽略，同时浏览器可能会发出警告。`.passive`会告诉浏览器，你**不想**阻止事件的默认行为。

## 5、按键修饰符

在监听键盘事件时，经常需要检查详细的按键。Vue允许为`v-on`在监听键盘事件时添加按键修饰符：

```html
<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input v-on:keyup.enter="submit">
```

可以直接将`KeyboardEvent.key`暴露的任意有效按键名转换为kebab-case来作为修饰符：

```
<!-- 只有在 `key` 是 `PageDown` 时调用处理函数 -->
<input v-on:keyup.page-down="onPageDown">
```

### 5.1、按键码

`keyCode`的事件用法已经被废弃，并且可能不会被新的浏览器版本支持。

使用`keyCode` attribute也是允许的：

```html
<input v-on:keyup.13="submit">
```

## 6、系统修饰键

2.1.0版本新增，可以用如下修饰符实现仅在按下相应的按键时才触发鼠标或键盘事件的监听器：

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

比如：

```html
<!-- Alt + C -->
<input @keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

注意，系统修饰键与常规按键不同，在和`keyup`事件一起使用时，事件触发时修饰键必须处于按下的状态。比如，只有在按下`ctrl`时按其它按键才能触发`keyup.ctrl`，而单单释放`ctrl`不会触发事件。

### 6.1、`.exact`修饰符

2.5.0新增，`.exact`修饰符可以用来精确控制系统修饰符组合触发的事件：

```html
<!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
<button @click.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- 没有任何系统修饰符被按下的时候才触发 -->
<button @click.exact="onClick">A</button>
```

### 6.2、鼠标按钮修饰符

2.2.0新增，限制处理函数仅响应特定的鼠标按钮：

- `.left`
- `.right`
- `.middle`

