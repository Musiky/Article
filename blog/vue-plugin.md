# 手把手教你写 Vue UI 组件库

最近在研究 `muse-ui` 的实现，发现网上很少有关于 vue 插件具体实现的文章，官方的文档也只是一笔带过，对于新手来说并不算友好。

笔者结合官方文档，与自己的摸索总结，以最简单的 `FlexBox` 组件为例子，带大家入门 `vue` 的插件编写，如果您是大牛，不喜勿喷～

<br>

**项目结构**

``` bash
| src
| ---| plugin
| ---| ---| flexbox                # 组件文件夹
| ---| ---| ---| flexbox.vue       # flex 布局的父组件
| ---| ---| ---| flexboxItem.vue   # flex 布局的子组件
| ---| ---| ---| flexbox.scss      # 样式文件，我使用的是 sass
| ---| ---| ---| index.js          # 组件的出口
| ---| ---| styles                 # 公用的 css 样式文件
| ---| ---| index.js               # 插件的出口
| ---| App.vue
| ---| main.js
```

<br>

## <一> 让项目装载插件
首先，我们不去理会组件的具体实现，先让我们的项目能够正常装载一个我们`自定义的插件`。

现在，我们的目标，是让项目能够正常显示这两个组件，能显示文本 `flexbox demo` 就可以啦！

*./src/plugin/flexbox/`flexbox.vue`*

``` html
<template>
  <div>flexbox demo</div>
</template>

<script>
export default {
    // 这是该组件的自定义名称，
    // 之后引用组件时就会用到这个名称。
    name: 'my-flexbox'
}
</script>
```

<br>

*./src/plugin/flexbox/`flexboxItem.vue`*

``` html
<template>
    <div>flexboxItem demo</div>
</template>

<script>
export default {
    name: 'my-flexbox-item'
}
</script>
```

<br>

*./src/plugin/flexbox/`index.js`*

这是整个 `flexbox` 组件的出口文件。\
因为这个组件有两个子组件，所以我们将引入的 `default` 改名为该组件的名称。``{ default as flexbox }``

``` javascript
// 引用 scss 文件
import './flexbox.scss'  

// 引用组件
export { default as flexbox } from './flexbox.vue'
export { default as flexboxItem } from './flexboxItem.vue'
```

<br>

*./src/plugin/`index.js`*

现在，我们来到插件的出口文件。

``` javascript
// ----- 1
import * as flexbox from './flexbox'

// ----- 2
const components = {
    ...flexbox
}

// ----- 3
const install = function (Vue, Option) {
    // ----- 4
     Object.keys(components).forEach((key) => {
        Vue.component(components[key].name, components[key])
    })
}

// ----- 5
export default {
    install
}
```

1. 引入组件

2. 定义 `components` 变量 

3. **这里是重点**，vue 为编写插件提供了一个 `install(Vue, Option)` 方法，该方法为 vue 添加全局功能；<br>
该方法有两个参数，第一个是 `Vue`构造器，第二个是可选的参数；

4. 使用 vue 的全局方法 `Vue.component(Name, Object)` 定义组件，第一个参数是组件名，第二参数是组件对象；

5. 最后将组件默认导出。

<br>

*./src/`main.js`*

我们来到 `main.js`，在这里，我们将进行插件的最后配置啦。

使用 vue 的全局方法 `Vue.use(PluginName, Options)`，第一个参数是插件的名字，第二个是可选的参数。

``` javascript
import plugin from './plugin'
Vue.use(plugin)
```

<br>

*./src/`App.vue`*

终于，我们可以在项目中引用我们定义的组件啦！

``` html
<template>
    <my-flexbox></my-flexbox>
    <my-flexbox-item></my-flexbox-item>
</template>
```

<br>

## <二> `Flexbox` 组件的实现

组件的具体实现，就和平时自己写组件的方法是一样的了。

这里先贴出代码，我会将具体原理写在代码注释里面。

*./src/plugin/flexbox/`flexbox.vue`*

``` html
<template>
    <!-- 为组件绑定一个类，这个类的值通过计算属性来得出 -->
    <div class="my-flexbox"
         :class="classObj">
        <!-- slot 用来装载子组件，my-flexbox-item -->
        <slot></slot>
    </div>
</template>

<script>
export default {
    name: 'my-flexbox',
    props: {
        // 子组件 my-flexbox-item 之间是否存在间隙，
        // 默认，8px 的间隙。
        gutter: {
            type: Number,
            default: 8
        },
        // 子组件的排列方式，水平，或垂直排列。
        orient: {
            type: String,
            default: 'horizontal'
        },
        justify: {
            type: String
        },
        align: {
            type: String
        },
        wrap: {
            type: String,
            default: 'nowrap'
        }
    },
    computed: {
        // 我们通过父级传递过来的参数，
        // 来判断该组件需要应用哪些样式
        // 如：<my-flexbox orient="vertical" justify="flex-start"></my-flexbox>
        classObj () {
            let classObj = {};

            // orient
            if (this.orient === 'vertical') classObj['flex-vertical'] = true;

            // wrap
            if (this.wrap === 'wrap') {
                classObj['flex-wrap'] = true
            } else {
                classObj['flex-nowrap'] = true
            }

            // justify
            switch (this.justify) {
                case 'flex-start':
                    classObj['justify-start'] = true;
                    break;
                case 'flex-end':
                    classObj['justify-end'] = true;
                    break;
                case 'center':
                    classObj['justify-center'] = true;
                    break;
                case 'space-between':
                    classObj['justify-space-between'] = true;
                    break;
                case 'space-around':
                    classObj['justify-space-around'] = true;
                    break
            };

            // align
            switch (this.align) {
                case 'flex-start':
                    classObj['align-start'] = true;
                    break;
                case 'flex-end':
                    classObj['align-end'] = true;
                    break;
                case 'center':
                    classObj['align-center'] = true;
                    break;
                case 'baseline':
                    classObj['align-baseline'] = true;
                    break;
                case 'stretch':
                    classObj['align-stretch'] = true;
                    break;
            };

            return classObj;
        }
    }
}
</script>
```

<br>

*./src/plugin/flexbox/`flexbox.scss`*

`scss` 中定义需要使用到的样式

``` css
.my-flexbox {
    width: 100%;
    display: flex;
}
.flex-vertical {
    flex-direction: column;
}
.flex-wrap {
    flex-wrap: wrap;
}
.flex-nowrap {
    flex-wrap: nowrap;
}

/* justify */
.justify-start {
    justify-content: flex-start
}
.justify-end {
    justify-content: flex-end
}
.justify-center {
    justify-content: center
}
.justify-space-between {
    justify-content: space-between
}
.justify-space-around {
    justify-content: space-around
}

/* align */
.align-start {
    align-items: flex-start
}
.align-end {
    align-items: flex-end
}
.align-center {
    align-items: center
}
.align-baseline {
    align-items: baseline
}
.align-stretch {
    align-items: stretch
}
```

<br>

*./src/`App.vue`*

好了！我们可以在项目中用到这个组件了！

``` html
<template>
    <div id="app">
        <my-flexbox>
            <p>demo</p>
            <p>demo</p>
        </my-flexbox>
    </div>
</template>
```

你也可以让他们垂直排列！

``` html
<template>
    <div id="app">
        <my-flexbox orient="vertical">
            <p>demo</p>
            <p>demo</p>
        </my-flexbox>
    </div>
</template>
```

<br>

## <三> `FlexboxItem` 组件的实现

`flexbox-item` 组件和 `flexbox` 组件实现原理大同小异，直接贴代码了！

*./src/plugin/flexbox/`flexbox.vue`*

``` html
<template>
    <div class="my-flexbox-item"
         :style="styleObj">
        <slot></slot>
    </div>
</template>

<script>
export default {
    name: 'my-flexbox-item',
    props: {
        grow: {
            type: [String, Number],
            default: 0
        },
        shrink: {
            type: [String, Number],
            default: 1
        },
        basis: {
            type: [String, Number],
            default: 'auto'
        },
        order: {
            type: [String, Number],
            default: 0
        }
    },
    computed: {
        styleObj () {
            let styleObj = {};

            // gutter
            let gutter = this.$parent.gutter,
                orient = this.$parent.orient;
            
            let marginName = orient === 'horizontal'?'marginLeft':'marginTop';
            styleObj[marginName] = gutter + 'px';

            // grow
            styleObj['flex-grow'] = this.grow;

            // shrink
            styleObj['flex-shrink'] = this.shrink;

            // basis
            styleObj['flex-basis'] = this.basis;

            // order
            styleObj['order'] = this.order;

            return styleObj;
        }
    }
}
</script>
```

<br>

*./src/`App.vue`*

``` html
<template>
    <div id="app">
        <my-flexbox>
            <my-flexbox-item grow="1">
                demo
            </my-flexbox-item>
            <my-flexbox-item>
                demo
            </my-flexbox-item>
        </my-flexbox>
    </div>
</template>
```

<br>

## 总结

这只是 vue 中编写插件的其中一个方法，还有更多的，例如：

1. 使用 ``Vue.directive(Name, [Define])``，自定义指令，添加全局资源，如 `vue-touch`。[可以看我总结的这篇文章](https://github.com/Musiky/Article/blob/master/Vue/Vue-advanced/customize.md)

2. 添加 Vue 实例方法，通过把它们添加到 Vue.prototype 上实现。

3. 添加全局方法或者属性，如: `vue-element`。

4. 一个库，提供自己的 API，同时提供上面提到的一个或多个功能，如 `vue-router`。

本文的案例，只讲解了最简单的如何配置插件，意在帮助大家尽快上手。

*觉得有帮助，就打赏打赏吧。*

![img](./img/IMG_2759.jpg)
