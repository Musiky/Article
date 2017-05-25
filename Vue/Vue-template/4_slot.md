# slot 内容分发

## 作用域

## 单个 slot
> 为子组件指定一个**插入位置**，使用 ``slot`` 标签来表示；\
如果在这种模式下插入多个 ``slot``，就会在该标签相应的位置，重复显示父组件分发的元素；\
``slot`` 标签内包裹的元素是备用元素，也可以理解成默认元素。如果父组件中没有分发内容，就会选择备用元素显示

*../components/child*
``` html
<template>
  <div class="child">
      <header>header</header>
      <main>
        <slot>没有分发内容时显示。</slot>
      </main>
      <footer>footer</footer>
  </div>
</template>
```

*./App.vue*
``` html
<template>
    <div class="container"
         id="app">
        <child>
            content
        </child>
    </div>
</template>

<script>
import child from './components/child'
export default {
    components: {
        child
    }
}
</script>
```

## 具名 slot

``<slot>`` 元素可以用一个特殊的属性 ``name`` 来配置如何分发内容。

多个 ``slot`` 可以有不同的名字。具名 ``slot`` 将匹配内容片段中有对应 ``slot`` 特性的元素。

仍然可以有一个匿名 ``slot`` ，它是默认 ``slot`` ，作为找不到匹配的内容片段的备用插槽。

如果没有默认的 slot ，这些找不到匹配的内容片段将被抛弃。

> 这里我们模拟一个 ``appbar`` 组件，来演示具名 ``slot``

*../component/appbar.vue*
``` html
<template>
  <header class="appbar"
          :style="{background: backgroundColor}">
    <!--Left-->
    <div class="left">
      <slot name="left"></slot>
    </div>
    <!--left-->
  
    <!--Right-->
    <div class="right">
      <slot name="right"></slot>
    </div>
    <!--right-->
  
    <!--Label-->
    <h1 class="label"
        :style="{color}">{{label}}</h1>
    <!--label-->
  </header>
</template>

<script>
export default {
  props: {
    label: {
      type: String,
      default: 'title'
    },
    backgroundColor: {
      type: String,
      default: '#41b883'
    },
    color: {
      type: String,
      default: '#fff'
    }
  }
}
</script>

<style lang="scss">
.appbar {
  position: relative;
  width: 100%;
  height: .88rem;
  line-height: .88rem;
  text-align: center;
  .label {
    font-size: .48rem;
  }
  .left, .right {
    position: absolute;
    top: 50%;
    transform: translate3d(0, -50%, 0);
    width: .66rem;
    height: .66rem;
    line-height: .66rem;
    text-align: center;
  }
  .left {
    left: .12rem;
  }
  .right {
    right: .12rem;
  }
}
</style>

```

*./App.vue*
``` html
<template>
    <div class="container"
         id="app">
        <appbar>
            <i slot="left"
               style="color: #fff">菜单</i>
        </appbar>
    </div>
</template>

<script>
import appbar from './components/appbar'
export default {
    components: {
        appbar
    }
}
</script>

<style lang="scss">
* {
    margin: 0;
    padding: 0;
}
</style>
```

## 作用域插槽
> 作用域插槽实际上就是将 **子组件插槽** 定义的参数，传递给 **父组件分发** 使用

简单示例：

> 这个实例在子组件中定义 ``text`` 参数，并传给父组件使用。

*../components/child.vue*
``` html
<template>
  <div class="child">
    <slot text="hello from child"></slot>
  </div>
</template>
```

*./App.vue*
``` html
<template>
    <div class="container"
         id="app">
        <appbar>
            <template scope="props">
                <div>{{props.text}}</div>
            </template>
        </appbar>
    </div>
</template>
```