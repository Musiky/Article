# .sync
> v2.3.0 +

**使用方法**

*../components/child.vue*

``` html
<template>
  <div class="child">
      <button @click="$emit('update:msg', 'change')">{{msg}}</button>
  </div>
</template>

<script>
export default {
    props: {
      msg: {
        type: String
      }
    }
}
</script>
```

*./App.vue*

``` html
<template>
  <div class="container" id="app">  
      <child :msg.sync="msg"></child> 
  </div>
</template>

<script>
import child from './components/child'
export default {
    data () {
        return {
            msg: 'origin'
        }
    },
    components: {
        child
    }
}
</script>
```

**原理:**

在一些情况下，我们可能会需要对一个 prop 进行『双向绑定』。

事实上，这正是 Vue 1.x 中的 ``.sync`` 修饰符所提供的功能。当一个子组件改变了一个 prop 的值时，这个变化也会同步到父组件中所绑定的值。

这很方便，但也会导致问题，因为它破坏了『单向数据流』的假设。由于子组件改变 prop 的代码和普通的状态改动代码毫无区别，当光看子组件的代码时，你完全不知道它何时悄悄地改变了父组件的状态。

这在 debug 复杂结构的应用时会带来很高的维护成本。

上面所说的正是我们在 2.0 中移除 ``.sync`` 的理由。但是在 2.0 发布之后的实际应用中，我们发现 ``.sync`` 还是有其适用之处，比如在开发可复用的组件库时。我们需要做的只是让子组件改变父组件状态的代码更容易被区分。

在 2.3 我们重新引入了 ``.sync`` 修饰符，但是这次它只是**作为一个编译时的语法糖**存在。它会被扩展为一个自动更新父组件属性的 v-on 侦听器。

如下代码:

``` html
<comp :foo.sync="bar"></comp>
```

它是以下代码的语法糖:
``` html
<comp :foo="bar" @update:foo="val => bar = val"></comp>
```

当子组件需要更新 foo 的值时，我们需要显示地触发一个更新事件:
``` html
this.$emit('update:foo', newValue)
```