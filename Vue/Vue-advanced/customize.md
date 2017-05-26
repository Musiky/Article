# 自定义指令

- [简介](#简介)
- [钩子函数](#钩子函数)
- [钩子函数参数](#钩子函数参数)

## 简介

除了默认设置的核心指令( ``v-model`` 和 ``v-show`` ),Vue 也允许注册自定义指令。

注意，在 Vue2.0 里面，代码复用的主要形式和抽象是组件——然而，有的情况下,你仍然需要对纯 DOM 元素进行底层操作,这时候就会用到自定义指令。

下面这个例子将聚焦一个 input 元素:

1.全局注册

*./main.js*
``` javascript
Vue.directive('focus', {
    // 生命钩子，当组件插入到 Dom 中时
    inserted (el) {
        el.focus()
    }
})
```

*./App.vue*
``` html
<template>
    <input type="text" v-focus />
</template>
```

<br/>


2.局部注册

*./App.vue*
``` html
<template>
    <input type="text" v-focus />
</template>

<script>
export default {
    directives: {
        focus: {
            inserted (el) {
                el.focus()
            }
        }
    }
}
</script>
```

<br/>


## 钩子函数

指令定义函数提供了几个钩子函数（可选）：

- ``bind``: 只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化动作。 

- ``inserted``: 被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于 document 中）。 

- ``update``: 被绑定元素所在的模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新（详细的钩子函数参数见下）。 

- ``componentUpdated``: 被绑定元素所在模板完成一次更新周期时调用。 

- ``unbind``: 只调用一次， 指令与元素解绑时调用。 

*接下来我们来看一下钩子函数的参数 (包括 ``el``，``binding``，``vnode``，``oldVnode``) 。*

<br/>

## 钩子函数参数

钩子函数被赋予了以下参数：

- el: 指令所绑定的元素，可以用来直接操作 DOM 。

- binding: 一个对象，包含以下属性：
	- name: 指令名，不包括 ``v-`` 前缀。
	- value: 指令的绑定值， 例如： ``v-my-directive="1 + 1"``, value 的值是 ``2``。
	- oldValue: 指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
	- expression: 绑定值的字符串形式。 例如 ``v-my-directive="1 + 1"`` ， expression 的值是 ``"1 + 1"``。
	- arg: 传给指令的参数。例如 ``v-my-directive:foo``， arg 的值是 ``"foo"``。
	- modifiers: 一个包含修饰符的对象。 例如： ``v-my-directive.foo.bar``, 修饰符对象 modifiers 的值是 ``{ foo: true, bar: true }``。
	
- vnode: Vue 编译生成的虚拟节点，查阅 VNode API 了解更多详情。

- oldVnode: 上一个虚拟节点，仅在 ``update`` 和 ``componentUpdated`` 钩子中可用。

> ⚠️ 除了 ``el`` 之外，其它参数都应该是只读的，尽量不要修改他们。如果需要在钩子之间共享数据，建议通过元素的 ``dataset`` 来进行。

<br/>

**实例：**

``` html
<template>
    <div id="app">
        <div v-demo="'hello world'"></div>
    </div>
</template>

<script>
export default {
    directives: {
        demo: {
            bind (el, binding) {
                el.innerHTML = binding.value
            }
        }
    }
}
</script>
```

<br/>

## 函数简写

大多数情况下，我们可能想在 bind 和 update 钩子上做重复动作，并且不想关心其它的钩子函数。可以这样写:

1.全局简写

*./main.js*

``` javascript
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

2.局部简写

*./App.vue*

``` javascript
export default {
  directives: {
    color-swatch (el, binding) {
      el.style.backgroundColor = binding.value    }
  }
}
```

<br/>


## 对象字面量

如果指令需要多个值，可以传入一个 JavaScript 对象字面量。记住，指令函数能够接受所有合法类型的 JavaScript 表达式。

``` html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

``` javascript
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```



