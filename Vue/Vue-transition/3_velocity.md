**引入：**

1⃣️ 安装

```
npm install velocity-animate --save
```
2⃣️ 引入
``` javascript
import Velocity from ‘velocity-animate’
Vue.prototype.$Velocity = Velocity
```

**使用：**
``` html
<template>
<div class="container">
    <transition @before-enter=“beforeEnter"
                      @enter="enter” 
                      @leave="leave” 
                      :css="false">
        <div v-if="is_show">content</div>
    </transition>
    <button id="button" @click="is_show = !is_show">Toggle</button>
</div>
</template>
```
``` javascript
<script>
export default {
data() {
    return {
        is_show: false
    }
},
methods: {
    beforeEnter(el) {
        el.style.opacity = 0;
        el.style.transformOrigin = 'left';
    },
    enter(el, done) {
        this.$Velocity(el,
            {
                opacity: 1
            },
            {
                complete: done
            }
         )
    },
    leave(el, done) {
        this.$Velocity(el, {rotateZ: ['80deg', 'ease-in'], translateX: '10px'}, {duration: 500});
        this.$Velocity(el, {rotateZ: '60deg'}, {loop: 2});
        this.$Velocity(el,
            {
                translateY: '50px',
                translateX: '200px',
                opacity: 0
            },
            {
                complete: done
            })
        }
    }
}
</script>
```
