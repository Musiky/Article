# 混合

- [基础](#基础)
- [选项合并](#选项合并)

## 基础

混合是一种灵活的分布式复用 Vue 组件的方式。

混合对象可以包含任意组件选项。

以组件使用混合对象时，所有混合对象的选项将被混入该组件本身的选项。

**例子：**

*./asset/js/mixins.js*
``` javascript
export const demoMixin = {
	created () {
		console.log('from mixin')
	},
	methods: {
		demoMethod () {
			console.log('demoMethod')
		}
	}
}
```

*./App.vue*
``` html
<template>
	<div id="app">
		<button @click="demoMethod">click me</button>
	</div>
</template>

<script>
	import { demoMixin } from './asset/js/mixins.js'
	export default {
		mixins: [
			mixin
		]
	}
</script>
```

<br/>

## 选项合并

当组件和混合对象含有 **同名选项** 时，这些选项将以恰当的方式混合。

比如，同名钩子函数将混合为一个数组，因此都将被调用。

另外，混合对象的 钩子将在组件自身钩子 **之前** 调用 ：

``` javascript
var mixin = {
  created: function () {
    console.log('混合对象的钩子被调用')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('组件钩子被调用')
  }
})

// -> "混合对象的钩子被调用"
// -> "组件钩子被调用"
```

值为对象的选项，例如 ``methods``, ``components`` 和 ``directives``，将被混合为同一个对象。 

两个对象键名冲突时，取组件对象的键值对。

``` javascript
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo()         // -> "foo"
vm.bar()         // -> "bar"
vm.conflicting() // -> "from self"
```

<br/>

## 全局混合
*./main.js*
``` javascript
Vue.mixin({
	created () {
		console.log('全局')
	}	
})
```

> 谨慎使用全局混合对象，因为会影响到每个单独创建的 Vue 实例（包括第三方模板）。\
> 大多数情况下，只应当应用于自定义选项，就像上面示例一样。\
> 也可以将其用作 Plugins 以避免产生重复应用

