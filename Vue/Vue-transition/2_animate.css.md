## 使用 animate.css

🔗 官方网站：<https://daneden.github.io/animate.css/>

# 基础

**引入：**
```
npm install animate.css --save
```

> ./main.js
``` javascript
import animate from ‘animate.css’     
```

**使用：**
``` html
<transition
    enter-active-class="animated tada"
    leave-active-class="animated bounceOutRight"
>
```

🦊 其他的不变，只需要将 animate.css 官方提供的实例的动画名称替换即可


# 自定义过渡类名

- enter-class
- enter-active-class
- leave-class
- leave-active-class

> 自定义动画在开始和离开时应用的类名。\
他们的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 Animate.css 结合使用十分有用。