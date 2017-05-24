# vue 发布

### 执行：
```
npm run build
```

### 运行
**a) 服务器环境运行**

>将生成的 dist 文件放到服务器上，就可以运行了\
如： gulp-connect 方法

**b) 浏览器环境运行**
> 要让编译出来的文件在浏览器中运行，需要改变一下 webpack 的编译配置

*./config/index.js*

`build: {assetsPublicPath: '/'}` => `build: {assetsPublicPath: './'}` 

