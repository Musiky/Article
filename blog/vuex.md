# 手把手教你怎么拆分 vuex@2.x

> 这里假设读者已阅读过官方的文档，对 vuex 的基本概念有所了解。如果您还不知道 vuex 是啥，建议先阅读 [文档](https://vuex.vuejs.org/zh-cn/intro.html)。

## `Vuex` 是啥？

**引自官方的解释：**

Vuex 是一个专为 Vue.js 应用程序开发的 **状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

很绕口～反正我是看不懂～

简单粗暴的理解，你可以将一个组件的状态（如：显示，隐藏）挂载到一个全局变量上，然后通过某个方法，可以随时随地改变它的状态。

如果不使用 `vuex` 做状态管理，那么你就必须使用 `$emit` 将子组件内部发生的事情，告知父组件，来管理该组件的状态。这种方式在大型应用，或嵌套很深的应用中，会相当麻烦，所以我身先士卒，毫无底线的拥抱了 `vuex`～

[这是我最近使用 vuex 重构的一个社区论坛](https://github.com/Musiky/vue-community)

<br>

## 思路

我们今天要利用 `vuex` 完成一个简单的 请求数据 demo。

- 通过 `action` 完成数据请求，
- 通过 `mutations` 提交请求数据，并改变 `loading` 状态。

<br>

**废话不多说，我们先拆了这货**

> 如果您对 ES6 的 Module 相关知识不太了解的话，建议阅读[相关文档](http://es6.ruanyifeng.com/#docs/module)

**项目结构**

``` bash
| src
| --| store
| --| --| constants   
| --| --| --| types.js          # ----- 1
| --| --| mutations             # ----- 2
| --| --| --| topcis.js
| --| --| actions               # ----- 3
| --| --| --| topcis.js
| --| --| getters               # ----- 4
| --| --| --| topcis.js
| --| --| modules               # ----- 5
| --| --| --| topcis.js
| --| --| --| index.js          # ----- 6
| --| --| index.js              # ----- 7
| --| App.js
| --| main.js
```
文件解释：
1. 为了保证 mutations 的方法名称唯一，我们需要在`types.js`中定义它们键名；

2. 所有 mutations 文件放在这里，`mutations` 负责提交修改对应的 modules 中 state 的值；在这里我们使用[vuejs中文论坛](https://www.vue-js.com/api/)提供的公开 api，去获取 `主题首页` 的内容；

3. 所有 actions 文件放在这里，通常使用 `actions` 异步请求数据，再使用 mutations 将请求到的数据提交给 state；

4. 所有的 getters 全部放在这个文件夹里；

5. 将零散的 `mutations, actions, getters` 文件，统一在这里组装成一个完整的模块；

6. 模块的出口；

7. 将所有模块合并，导出 store 对象。

<br>

## <一> 定义 `types.js`

在开始之前，我们需要梳理这个小项目需要用到哪些方法：

1. 发送一个请求，并将获取到的数据提交给对应的 `state`;

2. 发送请求时，显示 正在加载中，请求结束，关闭 正在加载中。

<br>

*./src/store/constants/`types.js`*

> 这里为区分，`mutations` 使用的方法名都使用大写英文，最好语义明确便于理解。比如 `FETCH_TOPICS_SUC` 意为，请求接口成功。

``` javascript
export const FETCH_TOPICS_SUC = 'FETCH_TOPICS_SUC'     // ----- 1
export const FETCH_TOPICS_REQ = 'FETCH_TOPICS_REQ'     // 请求中
export const FETCH_TOPICS_ERR = 'FETCH_TOPICS_ERR'     // 请求失败
```

代码解释：
1. 请求数据成功后 执行的方法，通过这个方法，我们将请求到的数据提交给 `state`，并关闭 加载状态；

2. 请求中 执行的方法，通过这个方法，显示 加载中状态；

3. 请求失败后 执行的方法，通过这个方法，将失败错误信息提交并打印出来，同时官兵 加载状态。

<br>

## <二> 定义 `mutations`

刚刚，我们先预设了 `mutations` 需要使用到方法的名称，现在我们就来写 `mutations` 的具体实现：

*./src/store/mutations/`topics.js`*

``` javascript
// ----- 1
import * as types from '../constants/types'

export const topics_mutations = {
    // ----- 2
    [types.FETCH_TOPICS_SUC] (state, action) {
        state.isFetching = false; // ----- 3
        state.data = action.data; // ----- 4
    },
    [types.FETCH_TOPICS_REQ] (state) {
        state.isFetching = true
    },
    [types.FETCH_TOPICS_ERR] (state, action) {
        state.isFetching = false;
        state.error = action.error
    }
}
```

代码解释：
1. `*` 代表将 `types.js` 中所有暴露的接口引入，并将它们命名为 `types`，之后就可以通过 `types.XXX` 去访问这些接口；<br>
当然，你也可以通过大括号的方式引入它们：`import { XXX, XXX } from '../constants/types'`；

2. 定义一个 `commit` 函数方法，<br>
第一个参数 `state` 是该模块局部的 `state` 实例，<br>
第二个参数 `action` 是调用该 `mutation` 时传递过来的参数。

3. '正在加载中' 显示的状态。请求成功，为 `false`；请求中，为 `true`；请求失败，为 `false`；

4. 向该模块的局部 `state` 提交数据，这个数据来自 `actions`，稍后我们会讲解。

<br>

## <三> 定义 `actions`

`actions` 主要是做异步处理的，所以我们将 `ajax` 请求放在 `actions` 里面处理：

*./src/store/actions/`topics.js`*

``` javascript
import * as types from '../constants/types'
import axios from 'axios'

export const topics_action = {
    // ----- 1
    fetchTopics({ commit, state }, params) {

        // ----- 2
        commit(types.FETCH_TOPICS_REQ);

        // ----- 3
        axios({
            method: 'get',
            url: 'https://www.vue-js.com/api/v1/topics',
            params: {
                page: params.page,
                tab: params.tab,
                limit: params.limit
            }
        }).then((res) => {
            let data = res.data.data;

            // ----- 4
            commit(types.FETCH_TOPICS_SUC, {
                data
            })
        }).catch((error) => {
            // ----- 5
            commit(types.FETCH_TOPICS_ERR, {
                error
            });
            console.log(error)
        })
    }
}
```

代码解释：
1. 定义一个 `actions` 方法，<br>
该方法的第一个参数是 `context`，它是一个对象，里面包括：<br>
`commit, dispatch, getters, rootGetters, rootState, state` 6个对象和方法；<br>
commit 用做提交一个 `mutations`；<br>
dispatch 用作执行另一个 `actions` 方法；<br>
getters, rootGetters, rootState, state 只是普通的对象。

2. 任务到请求体之前，执行 `FETCH_TOPICS_REQ` 方法，将 '正在加载' 状态显示出来；

3. 请求体，这里用的 `axios` 作 `ajax` 请求；

4. 请求成功，将请求到的数据传递给 `state`；

5. 请求失败，将失败错误信息传递给 `state`，并打印。

<br>

## <四> 组装模块

我们已经定义好了 `mutations`， `actions` 方法，现在我们要将这些方法组装到模块中去：

*./src/store/modules/`topics.js`*

``` javascript
// ----- 1
import { topics_action } from '../actions/topics'
import { topics_mutations } from '../mutations/topics'
// -----

// ----- 2
const topics = {
    state: {
        data: {},
        isFetching: false,
        error: ''
    },
    actions: topics_action,
    mutations: topics_mutations
}
// ----- 

export default topics
```

代码解释：
1. 引入定义好的 `mutations`，`actions` 方法；

2. 定义模块的局部 `state` 对象的属性值，并为它的 `actions`，`mutations` 赋值。

<br>

因为实际项目开发过程中，模块肯定不止一个，所以我们需要创建一个 `index.js` 文件，来导出这些模块

*./src/store/modules/`index.js`*

``` javascript
export { default as topics } from './topics'
// export { default as info } from './info'
// export { default as user } from './user'
// ...
```

<br>

## <五> 根节点，导出 `Store`

终于到最后一步了～

我们来到根节点，创建一个 `Store` 实例，将定义的所有模块引入该实例，并默认导出它，然后我们就可以在熟悉的 `main.js` 文件中引入它们啦！

*./src/store/`index.js`*

``` javascript
// ----- 1
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
// -----

// ----- 2
import * as modules from './modules'

// ----- 3
const store = new Vuex.Store({
    modules: {
        topics: modules.topics
    }
})
// -----

export default store
```

代码解释：
1. 引入 `vuex` 插件；

2. 引入定义的所有模块；

3. 为 `store` 对象的 `modules` 属性赋值。

### 大功告成！

## <六> 使用它！

首先，我们需要在 `main.js` 中引入，具体代码如下：

``` javascript
// ...
import store from './store'

new Vue({
  // ...  
  store,
  // ...
})
```

<br>

最后的最后～我们在 `App.vue` 中使用它：

``` html
<template>
    <div id="app">
        <!-- 3 -->
        <button @click="fetchTopicsData">{{topics.isFetching?'正在加载...':'点击请求'}}</button>
        <!---->

        <ul>
            <li v-for="list in topics.data">
                {{list.title}}
            </li>
        </ul>
    </div>
</template>

<script>
// ----- 1
import { mapState } from 'vuex'
// -----

export default {
    // ----- 1
    computed: {
        ...mapState([
            'topics'
        ])
    },
    // -----
    methods: {
        // ----- 2
        fetchTopicsData () {
            this.$store.dispatch('fetchTopics', {
                page: 0,
                tab: 'share',
                limit: 20
            })
        }
        // -----
    }
}
</script>
```

代码解释：
1. 引入并读取 `topics` 模块的 `state`；

2. 定义点击事件，执行 `fetchTopics` actions 方法；

3. 我们第一步已经获取到了 `topics` 的 `state` 对象，在这里，我们通过判断 `topics.isFetching` 的真假，来显示请求加载状态。

如果您每一个步骤都没出错的话，你应该看到：
- 点击按钮，按钮的文字会显示 `正在加载...`；
- 数据加载成功后，页面会渲染 20 条条目信息，并且按钮的文字会还原。