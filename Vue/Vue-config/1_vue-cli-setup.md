# vue-cli 安装
## 安装
1⃣️ 检测 node 版本，大于 4.0

2⃣️ 全局安装 vue-cli，在命令行输入  vue list 可以看到可供安装到模块
> 全局安装后，在任何目录都可以使用
```
sudo npm install -g vue-cli
```

3⃣️ 新建项目文件，在项目文件中安装 webpack 模块
```
vue init webpack projectName
```
```
? Project name projectName
? Project description A Vue.js project
? Author 木子七
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Setup unit tests with Karma + Mocha? No
? Setup e2e tests with Nightwatch? No
```
配置完毕后，会在当前目录建立一个文件夹，在终端中，会告诉你接下来需要怎么做：
```
 vue-cli ·
 Generated “projectName".

   To get started:
   
     cd projectName
     npm install
     npm run dev
```
执行之！

执行 `npm run dev` 后，会建立一个本地服务器，并自动在浏览器打开。

**安装成功！**
