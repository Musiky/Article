# vue 发布

### 执行：
```
npm run build
```

### 运行
## 一、服务器环境运行

>将生成的 dist 文件放到服务器上，就可以运行了\
如： gulp-connect 方法

## 二、浏览器环境运行
> 要让编译出来的文件在浏览器中运行，需要改变一下 webpack 的编译配置

*./config/index.js*

`build: {assetsPublicPath: '/'}` => `build: {assetsPublicPath: './'}` 

> 编译后，css 中的 background-image 路径会出错。解决方法如下

1. 在 vue 的 data() 函数中引入图片
``` javascript
export default {
    data () {
        return {
            img_url: require('../assets/images/img.jpg')
        }
    }
}
```

2. 将路径绑定在标签上
``` html
<div :style="{backgroundImage: `url(${img_url})`}"></div>
```

