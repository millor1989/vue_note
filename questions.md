给 input 赋值之后，无法修改 input

https://www.cnblogs.com/huanhuan55/p/12302333.html

```js
<el-input  v-model="student.name"></el-input>

export default {
  data () {
    return {
      student:{}
    }
  },
  methods: {
      update () {
         this.student.name='莉莉丝'
    }
  }
}
```

**原因**：vue实列创建的时候 student 的 name 属性名并未声明，因此 vue 就无法给属性添加 `getter/setter`，从而导致 student 并不是响应式的

**解决办法**：

- 给 student 赋初始值  `sutdent:{name:""}`
- `this.$set(this.student,'name','莉莉丝')`