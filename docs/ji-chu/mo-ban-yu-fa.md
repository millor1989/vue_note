# 模板语法

## 1、插值

### 1.1、文本

mustache语法（双大括号）文本插值：

```html
<span>Message: { { msg } }</span>
```

mustache标签会被替换为数据对象的`msg`属性的值。`msg`发生变化时，插值处的内容会随着更新。

使用`v-once`指令，可以执行一次性插值，数据改变时，插值处的内容不会更新。但是，这会影响到该节点上的其它数据绑定：

```html
<span v-once>这个将不会改变: { { msg } }</span>
```

### 1.2、原始HTML

mustache语法会将数据解释为普通文本，而非HTML代码。使用`v-html`指令可以输出原始的HTML：

```html
<p>Using mustaches: { { rawHtml } }</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

执行结果：

![](/assets/1583219589091.png)

需要注意的是，`v-html`会直接使用_rawHtml_的值，不会解析_rawHtml_中的数据绑定。另外，动态渲染**任意**的HTML是有风险的，会导致**XSS攻击**。

### 1.3、HTML属性

mustache语法不能作用在HTML的属性（attribute）上，此时要使用`v-bind`指令：

```html
<div v-bind:id="dynamicId"></div>
```

对于布尔属性（值存在就意味着值为_true_）`v-bind`作用起来有些不同：

```html
<button v-bind:disabled="isButtonDisabled">Button</button>
```

如果isButtonDisabled的值为_null_，_undefined_或_false_，那么`distributed`属性甚至都不会被包含在渲染出来的button标签元素中。

### 1.4、JavaScript表达式

对于所有的数据绑定，Vue都提供了完全的JavaScript表达式支持：

```html
{ { number + 1 } }

{ { ok?'YES':'NO' } }

{ { message.split('').reverse().join('') } }

<div v-bind:id="'list-' + id"></div>
```

这些表达式都会在所属Vue实例的数据作用域下作为JavaScript被解析。但是，每个绑定只能包含**单个表达式**，如下表达式，是不会生效的：

```html
<!-- 这是语句，不是表达式 -->
{ { var a = 1 } }

<!-- 流控制也不会生效，请使用三元表达式 -->
{ { if (ok) { return message } } }
```

## 2、指令（Directives）

指令是指带有`v-`前缀的特殊属性（attribute）。指令的预期值是**单个JavaScript表达式**（`v-for`例外）。指令的作用是，表达式的值改变时，将值改变的影响响应式的作用于dom。例如：

```html
<p v-if="seen">现在你看到我了</p>
```

`v-if`指令会根据表达式`seen`的值的真假来插入或溢出`p`元素。

### 2.1、参数

某些指令可以接收一个“参数”，在指令后加冒号表示。例如，`v-bind`指令可以响应式的更新HTML属性：

```html
<a v-bind:href="url">...</a>
```

其中`herf`是参数，`v-bind`指令将该元素的`herf`属性与表达式`url`的值绑定。另一个例子，`v-on`指令，可以用于监听事件：

```html
<a v-on:click="doSomething">...</a>
```

其中参数是监听的事件名称。

### 2.2、动态参数（于2.6.0版本新增）

动态参数，即把方括号包括的JavaScript表达式作为指令的参数：

```html
<a v-bind:[attributeName]="url"> ... </a>
```

其中`attributeName`会被作为一个JavaScript表达式，表达式的值作为参数。同样，监听事件`v-on`可以使用动态参数：

```html
<a v-on:[eventName]="doSomething"> ... </a>
```

#### 2.2.1、动态参数的值的约束

动态参数的预期值是字符串类型，例外的值为_null_，_null_值可以被显式地用来移除绑定。非字符串类型的值会触发警告。

#### 2.2.2、动态参数表达式的约束

动态参数表达式有些语法约束，某些字符，如空格和引号，放在HTML的属性名中是无效的。如：

```html
<!-- 这会触发一个编译警告 -->
<a v-bind:['foo' + bar]="value"> ... </a>
```

解决方法是使用没有空格或引号的表达式，或这用计算属性替代这种复杂的表达式。

在dom中使用模板时（直接在一个HTML中撰写模板），还需要避免使用大写来命名键名，因为浏览器会把属性名全部转换为小写：

```html
<!--
在 DOM 中使用模板时这段代码会被转换为 `v-bind:[someattr]`。
除非在实例中有一个名为“someattr”的 property，否则代码不会工作。
-->
<a v-bind:[someAttr]="value"> ... </a>
```

### 2.3、修饰符

修饰符（modifier）是以半角句号`.`指明的特殊后缀，用于指出指令应该以特殊方式绑定。例如`.prevent`修饰符告诉`v-on`指令对于触发的事件调用`event.preventDefault()`：

```html
<form v-on:submit.prevent="onSubmit">...</form>
```

## 3、缩写

### 3.1、`v-bind`缩写

```html
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>

<!-- 动态参数的缩写 (2.6.0+) -->
<a :[key]="url"> ... </a>
```

### 3.2、`v-on`缩写

```html
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>

<!-- 动态参数的缩写 (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```

`:`和`@`不会出现在最终渲染的标记中。

