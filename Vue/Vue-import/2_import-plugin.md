# 插件安装
## 索引
- [axios](#axios)
- [vue-video-player](#vue-video-player)
- [vuex](#vuex)
- [lodash](#lodash)
- [better-scroll](#better-scroll)
- [jQuery](#jquery)
- [marked](#marked)
- [Sass](#sass)

## Axios
### 安装 axios
##### [回到索引](#索引)
```
$ npm install axios --save
```

### 全局引用 axios
> ./main.js
``` javascript
import Vue from ‘vue'
import axios from ‘axios’

// 为 Vue 添加一个原型方法
Vue.prototype.$http = axios
```

**调用：**
``` javascript
this.$http.post( ... )
```

## vue-video-player
##### [回到索引](#索引)
> 安装
```
npm install vue-video-player --save
```

> 在 main.js 中全局引入
``` javascript
// import
import Vue from 'vue'
import VueVideoPlayer from 'vue-video-player'
// mount with global
Vue.use(VueVideoPlayer)
```

## vuex
##### [回到索引](#索引)
### 安装
```
npm i vuex --save
```

### 引入 ./main.js
``` javascript
import Vuex from ‘vuex’
Vue.use(Vuex)
```

## lodash
##### [回到索引](#索引)
> lodash 会随 vue-cli 安装。

安装完毕后，需要在 main.js 文件中引入
``` javascript
import _ from ‘lodash'
```
**使用**
``` javascript
export default {
    created() {
        console.log( _.add( 6, 4 ) )
    }
}
```

## better-scroll
##### [回到索引](#索引)
### 安装 better-scroll
```
npm install better-scroll --save
```

⚠️⚠️⚠️\
**这个方法一定要注意初始化的时机！**\
因为 vue 加载 DOM 是异步的，该方法必须要等到 DOM 节点加载完成后，才能计算出滚动区域的高度。\
如果是通过请求数据改变 DOM 结构，一定要将初始化的操作放在请求体中，否则两个方法异步加载，滚动高度就无法正确计算！
``` javascript
created() {
    this.$http
        .get('/api/goods')
        .then((res) => {
            if (res.status == 200) {
                this.goods = res.data.data;
            }
        });

    this.$nextTick(() => {
        this.initScroll()
    })
}
```
⚠️ 这种方式，是无法拿到滚动高度的！要写在请求体中！

### Demo
``` html
<template>
    <div class="scroll-wrapper">
        <div class="scroll" ref="scroll">
            <ul>
                <li>mock data</li>
                <li>mock data</li>
                <li>mock data</li>
                <li>mock data</li>
                <li>mock data</li>
                <li>mock data</li>
                <li>mock data</li>
                <li>mock data</li>
                <li>mock data</li>
                <li>mock data</li>
            </ul>
        </div>
    </div>
</template>

<script>
// 1⃣️ 引入插件
// =========
import BScroll from 'better-scroll'

export default {
    // 3⃣️ 初始化滚动事件
    // 这里必须用到 $nextTick，
    // 将回调延迟到下次 DOM 更新循环之后执行。在修改数据之后立即使用它，然后等待 DOM 更新，
    // 如果不使用 $nextTick，better-scroll 就无法定义容器的高度，因为 vue 的 DOM 加载是异步的。
    created() {
        this.$nextTick(() => {
            this.initScroll()
        })
    },
    methods: {
        // 2⃣️ 初始化 scroll
        // # 利用 vue 的 ref 属性，查找需要滚动的 DOM 节点
        // ===========================================
        initScroll() {
            this.scroll = new BScroll(this.$refs.scroll, {})
        }
    }
}
</script>

<style>
* {
    padding: 0;
    margin: 0;
}

.scroll-wrapper {
    height: 500px;
    overflow: hidden
}

.scroll {
    height: 100%
}
</style>
```

## 🚩总结
a) 注意 initScroll 的时机，要在页面 dom 加载完毕后 init，否则无法计算出滚动的距离

b) 更新 dom 后，要 this.scroll.refresh()，来强制刷新滚动控件，重新计算滚动距离

c) 注意滚动控件的 dom 结构，.scroll-wrapper > .scroll > ul > item…

d) 如果列表包含图片的话，一定要给图片预设高度，因为 better scroll 会在 dom 加载时决定滚动高度，但是列表加载时图片不一定已经加载完成，如果不给图片预设高度，那么图片的高度为0，等图片加载出来，就会超出已计算的滚动距离

e) 一个列表的计算，在真机上通常会少一截高度，那是因为真机浏览器环境通常是有导航栏的，这个导航栏的高度遮盖住了页面的高。在列表下面放置一个空的具有高度的盒子即可解决。

## jQuery
##### [回到索引](#索引)
- 首先在 **package.json** 里的 `dependencies` 加入 `"jquery" : "^2.2.3”`，然后 `npm install`

- 在**webpack.base.conf.js**里加入
``` javascript
var webpack = require("webpack")
```

- 在**module.exports**的最后加入
``` javascript
plugins: [
    new webpack.optimize.CommonsChunkPlugin('common.js'),
    new webpack.ProvidePlugin({
    jQuery: "jquery",
        $: "jquery"
    })
]
```

- 在main.js 引入
``` javascript
import $ from ‘jquery'
```
```
npm run dev
```

## Marked
##### [回到索引](#索引)

### 安装 marked 和 highlight.js
```
npm install -S marked
npm install -S highlight.js
```

### vue中注册自定义指令
> main.js:
``` javascript
import Vue from 'vue'
import Marked from './common/directive/marked.js'
Vue.use(Marked);
```

ps: `Vue.use()`会自动调用参数文件里的`install()`

> marked.js:
``` javascript
import marked from 'marked';
import 'highlight.js/styles/monokai-sublime.css';//这个样式有多种类型可选择
marked.setOptions({
  renderer: new marked.Renderer(),
  gfm: true,
  tables: true,
  breaks: false,
  pedantic: false,
  sanitize: false,
  smartLists: true,
  smartypants: false,
  highlight: function (code) {
    return require('highlight.js').highlightAuto(code).value;
  }
});
let install = function(Vue){
    /* istanbul ignore if */
    if (install.installed) return;
    Vue.directive('marked',{
      //注意，这儿得使用bind钩子函数，因为我们使用此指令主要是为了写文档，
      //文档里不会有变量且一次性生成,而update在自定义指令所在模板变化时就会重新执行，
      //会影响渲染文档的方法，所以不能使用update钩子，也不能使用函数简写
      bind:function(el,binding,vnode){
        el.innerHTML = marked(el.innerText);
      }
    })
}
export default install;
```

### 使用自定义指令: **v-marked**

里面的缩进和空格也是markdown语法的一部分\
🦊 也就是说不能格式化，必需贴在最左侧
``` html
<div v-marked>
### title
[百度](https://www.baidu.com)
</div>
```
**ps:**\
代码语法高亮的样式可以有多种选择:\
我们可以在 highlight.js 文件中找到 styles 文件夹，并运用里面的样式\
reset.css 可能会与之冲突

### vue自定义指令相关知识

**钩子函数**

指令定义函数提供了几个钩子函数（可选）：

- bind: 只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化动作。
- inserted: 被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于 document 中）。
- update: 被绑定元素所在的模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新（详细的钩子函数参数见下）。
- componentUpdated: 被绑定元素所在模板完成一次更新周期时调用。
- unbind: 只调用一次， 指令与元素解绑时调用。

**钩子函数的参数**

- el: 指令所绑定的元素，可以用来直接操作 DOM 。
- binding: 一个对象，包含以下属性：
- name: 指令名，不包括 v- 前缀。
- value: 指令的绑定值， 例如： v-my-directive="1 + 1", value 的值是 2。
- oldValue: 指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
- expression: 绑定值的字符串形式。 例如 v-my-directive="1 + 1" ， expression 的值是 "1 + 1"。
- arg: 传给指令的参数。例如 v-my-directive:foo， arg 的值是 "foo"。
- modifiers: 一个包含修饰符的对象。 例如： v-my-directive.foo.bar, 修饰符对象 modifiers 的值是 { foo: true, bar: true }。
- vnode: Vue 编译生成的虚拟节点，查阅 VNode API 了解更多详情。
- oldVnode: 上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。

**函数简写**

大多数情况下，我们可能想在 bind 和 update 钩子上做重复动作，并且不想关心其它的钩子函数。可以这样写:

``` javascript
//首先bind的时候执行一次，再多次执行update
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```


## Sass
##### [回到索引](#索引)
### 安装 node-sass + sass-loader
在项目地址下安装这两个插件\
使用 --save 会在 package.json 中自动添加配置
```
npm install node-sass --save
npm install sass-loader --save
```

### 使用时需要加上声明
``` css
<style rel="stylesheet/scss" lang="scss">
    .container {
        // ...
    }
</style>
```


