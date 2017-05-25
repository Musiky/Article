# 基础

![img](https://cn.vuejs.org/images/props-events.png)

## 字面量语法VS动态语法
##### [回到索引](#索引)
初学者常犯的一个错误是使用字面量语法传递数值：
``` html
<!-- 传递了一个字符串 "1" -->
<comp some-prop="1"></comp>
```

因为它是一个字面 prop ，它的值是字符串 ``"1"`` 而不是number。如果想传递一个实际的number，需要使用 ``v-bind`` ，从而让它的值被当作 JavaScript 表达式计算：
``` html
<!-- 传递实际的 number -->
<comp v-bind:some-prop="1"></comp>
```
> 对于 布尔值 也是如此。``<comp v-bind:some-prop="true"></comp>``

---

## 单向数据流
##### [回到索引](#索引)

prop 是单向绑定的：当父组件的属性变化时，将传导给子组件，但是不会反过来。这是为了防止子组件无意修改了父组件的状态——这会让应用的数据流难以理解。

另外，每次父组件更新时，子组件的所有 prop 都会更新为最新值。这意味着你不应该在子组件内部改变 prop 。如果你这么做了，Vue 会在控制台给出警告。

为什么我们会有修改prop中数据的冲动呢？通常是这两种原因：
- prop 作为初始值传入后，子组件想把它当作局部数据来用；
- prop 作为初始值传入，由子组件处理成其它数据输出。

对这两种原因，正确的应对方式是：

1.定义一个局部变量，并用 prop 的值初始化它：
``` javascript
props: ['initialCounter'],
data: function () {
  return { counter: this.initialCounter }
}
```

2.定义一个计算属性，处理 prop 的值并返回。
``` javascript
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

> ⚠️ 注意在 JavaScript 中对象和数组是引用类型，指向同一个内存空间，如果 prop 是一个对象或数组，在子组件内部改变它会影响父组件的状态。