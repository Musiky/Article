# 插件

## 开发插件

插件通常会为Vue添加全局功能。

插件的范围没有限制——一般有下面几种：


1. 添加全局资源：指令/过滤器/过渡等，如 ``vue-touch``

2. 通过全局 mixin方法添加一些组件选项，如: ``vuex``

3. 添加 Vue 实例方法，通过把它们添加到 Vue.prototype 上实现。

4. 添加全局方法或者属性，如: ``vue-element``

5. 一个库，提供自己的 API，同时提供上面提到的一个或多个功能，如 ``vue-router``


Vue.js 的插件应当有一个公开方法 ``install`` 。这个方法的第一个参数是 ``Vue`` 构造器 , 第二个参数是一个可选的选项对象:

*结构*
``` bash
| src
| ---| plugin
| ---| ---| components
| ---| ---| ---| appbar
| ---| ---| ---| ---| appbar.vue
| ---| App.vue
| ---| main.js
```

*./plugin/index.js*

``` javascript
// 引入组件
import appbar from './components/appbar/appbar.vue'
// import ...

// 导出
export default {
    /**
     * Vue 为插件开发提供一个公开的 install 方法，
     * 通过全局方法 Vue.use() 就可以使用插件。
     * @param {Vue}     [Object] Vue实例构造器
     * @param {options} [Object] 可选的选项对象
     */
    install (Vue, options) {
        // 1⃣️ 添加全局资源
        Vue.directive('focus', {
            bind (el, binding, vnode, oldVnode) {
                // ...
            },
            inserted (el, binding, vnode, oldVnode) {
                el.focus()
            }
        })

        // 2⃣️ 通过 mixin 注入组件
        Vue.mixin({
            components: {
                appbar
            }
        })

        // 3⃣️ 添加实例方法
        Vue.prototype.$userinfo = {
            name: '木子七',
            age: 24,
            say () {
                alert('我叫' + this.name + ', 今年' + this.age + '岁。')
            }
        }
    }
}
```

<br/>

## 使用插件

通过全局方法 ``Vue.use()`` 使用插件:

*./main.js*

``` javascript
import Vue from 'vue'
import plugin from './plugin/index'
// 传入选项参数
// Vue.use 会自动阻止注册相同插件多次，届时只会注册一次该插件
Vue.use(plugin, { someOption: true })
```

*./App.vue*

``` html
<template>
    <div id="app">
        <!--1⃣️-->
        <input type="text" v-focus>

        <!--2⃣️-->
        <appbar></appbar>           
    </div>
</template>

<script>
export default {
    created () {
        // 3⃣️
        this.$userinfo.say()
    }
}
</script>
```