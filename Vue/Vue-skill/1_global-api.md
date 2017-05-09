# 全局 API
## 索引
- [Vue.set](#vue-set)

### Vue-set
##### [回到索引](#索引)
开发过程中，经常会对**对象**进行操作。如设置对象的属性；\
我们通常使用以下方式设置**对象**的属性：
``` javascript
export default {
    data: {
        return {
            info: {
                name: '木子七'
            }
        }
    },
    methods: {
        editInfo () {
            this.info.name = '丫丫'
        }
    }
}
```
这种方式是响应式的，视图也会更新。\
但是如果我们为其设置一个不存在的值，虽然调试工具可以显示该值，但**视图不会更新**，如：
``` javascript
methods: {
    editInfo () {
        this.info.age = 24
    }
}
```
如果我们想添加一个不存在的值，并确保视图是响应式的更新，我们就需要使用到 **Vue.set** 方法：
``` javascript
/**
 * @param [Object]  目标对象
 * @param [key]     添加属性的 key
 * @param [value]   添加属性的 value
 */
Vue.set(Object, key, value)
```
``` javascript
export default {
    data: {
        return {
            info: {
                name: '木子七'
            }
        }
    },
    methods: {
        editInfo () {
            this.$set(info, 'age', 24)
        }
    }
}
```
