## 过渡模式 - 多个元素之间替换

``` html
<transition 
    enter-active-class="animated slideInLeft"
    leave-active-class="animated slideOutLeft"
    mode="in-out">
    <div v-if="is_show" key="on">on</div>
    <div v-else key="off">off</div>
</transition>
```

🦊

当有**相同标签名**的元素切换时，需要通过 key 特性设置唯一的值来标记以让 Vue 区分它们，否则 Vue 为了效率只会替换相同标签内部的内容。\
若没有设置 key，切换的动画会丢失。

🦊 

同时生效的进入和离开的过渡不能满足所有要求，所以 Vue 提供了 过渡模式

* `in-out`: 新元素先进行过渡，完成之后当前元素过渡离开。
* `out-in`: 当前元素先进行过渡，完成之后新元素过渡进入。