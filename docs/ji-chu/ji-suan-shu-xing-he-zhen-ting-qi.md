# 计算属性和侦听器

## 1、计算属性

模板内的表达式很方便：

```html
<div id="example">
  { { message.split('').reverse().join('') } }
</div>
```

但是，在模板中放入太多的逻辑会让模板过重且难以维护。所以，对于复杂的逻辑，应该使用**计算属性**。

### 1.1、例

对于上面的例子，可以使用计算属性，如下：

```html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{ { reversedMessage } }"</p>
</div>
<script>
new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
```

运行结果：

![](/assets/1583292158898.png)

此处，声明了一个计算属性`reversedMessage`。提供的函数会被用作属性`vm.reversedMessage`的getter函数，`vm.reversedMessage`的值取决于`vm.message`的值：

```js
console.log(vm.reversedMessage) // => 'olleH'
vm.message = 'Goodbye'
console.log(vm.reversedMessage) // => 'eybdooG'
```

可以像绑定普通属性一样在模板中绑定计算属性。计算属性的getter函数没有副作用，更易于测试和理解。

### 1.2、计算属性缓存 VS 方法

其实，通过在表达式中调用**方法**也可以达到与**计算属性**同样的结果：

```html
<div id="example">
  <p>Original message: "{ { message } }"</p>
  <p>Computed reversed message: "{ { reversedMessage() } }"</p>
</div>
<script>
new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  methods: {
    reversedMessage: function () {
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
```

不同的是，**计算属性是基于它们的响应式依赖进行缓存**的，相关的响应式依赖发生改变时才会重新求值。只要`message`不变，多次访问计算属性`reversedMessage`会返回之前缓存的结果，而不会再次执行getter函数。所以，如下例子中的计算属性不会更新，因为`Date.now()`不是响应式依赖：

```js
computed: {
  now: function () {
    return Date.now()
  }
}
```

然而，调用方法总是会再次执行函数。

所以，使用计算属性还是方法要依据应用场景而定。

### 1.3、计算属性 VS 侦听属性

侦听属性（`watch`）用来观察和响应Vue实例的数据变动。通常较好的做法是使用计算属性而不是命令式的侦听属性回调。比如，使用侦听属性：

```html
<div id="demo">{ { fullName } }</div>
<script>
new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

代码有些冗余，相比之下使用计算属性要简洁很多：

```html
<div id="demo">{ { fullName } }</div>
<script>
new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

### 1.4、计算属性的setter

计算属性默认只有getter，但是在需要时也可以提供一个setter：

```js
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

这样，在运行`vm.fullName='Kobe Braint'`时，setter会被调用，`vm.firstName`和`vm.lastName`会被更新。

## 2、侦听器

使用侦听器可以提供响应数据变化的方法。需要在数据变化时执行异步或者开销比较大的操作时，可以使用侦听器。比如：

```html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{ { answer } }</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // 如果 `question` 发生改变，这个函数就会运行
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // `_.debounce` 是一个通过 Lodash 限制操作频率的函数。
    // 在这个例子中，我们希望限制访问 yesno.wtf/api 的频率
    // AJAX 请求直到用户输入完毕才会发出。想要了解更多关于
    // `_.debounce` 函数 (及其近亲 `_.throttle`) 的知识，
    // 请参考：https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
```

本例中，使用`watch`选项进行了异步操作（访问一个API），限制执行操作的的频率，并在获取最终结果之前设置中间状态。这些都是计算属性无法做到的。所以，对于两个相关的数据，一般如果有比较简单直接联系的情况下使用计算属性，如果关系比较复杂，应该使用侦听器。

除了`watch`选项，还可以使用`vm.$watch` API。

