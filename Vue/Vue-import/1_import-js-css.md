# 在项目中引入 css／js 文件
## javascript file 引入

### 局部引入：

> ./asset/js/api.js
``` javascript
const api = {
    hostname: ‘...'
}
export default api
```

>  ./App.vue
``` javascript
import api from './asset/js/api.js'
export default {
    created() {
        alert( api.hostname )
    }
}
```

### 全局引入：
1.将对象方法写在 main.js 中，对象作为 Vue 实例的原型
> ./main.js
``` javascript
Vue.prototype.$Object = {
    name: ‘木子七’,
    say: ( text ) => {
        alert( text )
    }
}
```

2.将对象方法写在外部 js 文件中，再在 main.js 中引入
> ./asset/js/common.js
``` javascript
exports.install = function (Vue, options) {
    Vue.prototype.$Object = {
        name: '木子七',
        say: (text) => {
            alert(text)
        }
    }
};
```

> ./main.js
``` javascript
import commonJS from ‘./asset/js/common.js’
Vue.use( commonJS )
```

**使用：**
> ./App.vue
``` javascript
export default {
    created() {
        this.$Object.say( this.$Object.name )
    }
}
```

## css file 引入
🦊 全局引入在 main.js 中，局部引入在 *.vue 文件中

> ./asset/css/common.css
``` css
.container {...}
```

> ./main.js
``` javascript
import test from './asset/css/common.css'
```

> ./App.vue
``` html
<template>
    <div class=“container”>...</div>
</template>
```
