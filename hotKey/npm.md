# npm 参考手册

## 索引
- 权限
    - [t/team 组织成员管理](#team)
    - access 包访问控制
    - adduser/login 用户登录
    - logout 注销
    - owner 所有者管理
    - whoami 查看用户信息
- 包仓储
    - s/se/search 仓储查找包
    - publish 发布
    - unpublish 取消发布
    - deprecate 弃用
    - stars 我喜欢的包
    - star 喜欢
    - unstar 取消喜欢
- 包本地
    - init 初始化package.json
    - i/install 安装
    - un/uninstall 删除
    - dedupe/ddp 清除重复包
    - dist-tags 标签管理
    - version 更新包的版本信息
    - it/install-test 运行npm install && npm test
    - ln/link 安装链接
    - ls/list 列出包
    - update/up 更新并安装遗漏的包
    - outdated 检测过期
    - pack 打包tarball文件
    - prune 清理外来包
    - shrinkwrap 锁定依赖包版本
    - cache 缓存管理
- 脚本
    - run/run-script 运行脚本
    - start 运行start脚本
    - stop 运行stop脚本
    - tst/test 运行test脚本
    - rb/rebuild 重新编译本地包
    - restart 顺序执行重启相关的一系列脚本
- 配置
    - c/config 配置管理
    - get 列出配置
    - set 设置配置
- 查看
    - root 包根目录
    - prefix 打印prefix配置
    - v/view 查看仓储信息
    - bin 查看bin目录
    - bugs/issue 浏览器查看bugs
    - docs/home 浏览器查看帮助文档
    - repo 浏览器查看仓储
    - help 查看帮助
    - help-search 帮助中搜索关键字
- 其他
    - completion shell插补
    - doctor 环境检测
    - edit 进入包目录并启动编辑器
    - explore 进入包目录并运行命令
    - ping 检查仓储是否可用

## 权限
npm允许通过scope组织私有包，通过team细化权限控制.

npm官方仓储有两种类型的包，普通包和scope包

普通包特征: - 只能公有，谁都可以下载使用 - 仅可以通过所有者(owner)进行权限控制，如果要允许某个用户修改或发布包，必须将该用户添加到包的所有者列表。添加到包所有者列表的用户具备所有的权限.

scope包特征: 
- 包名有两部组成，@scope/name, @后的为scope名,/后的才是具体的包名 - 可以控制公有和私有 - 细化的权限控制，比如可以创建团队,并赋予团队对包只读/修改的权限

## owner
```
npm owner add <user> [<@scope>/]<pkg> # 将用户添加到包的所有者列表  
npm owner rm <user> [<@scope>/]<pkg> # 从包的所有这列表中删除用户  
npm owner ls [<@scope>/]<pkg> # 列出包的所有者  
```
成为包的所有者的用户，将能够修改元数据(如标记弃用)，发布新版本,添加其他用户到包的所有者列表

## <span id="team">t/team</span>
npm team create <scope:team> # 创建团队  
npm team destroy <scope:team> # 删除团队

npm team add <scope:team> <user> # 添加用户到团队  
npm team rm <scope:team> <user> # 从团队中移除用户 

npm team ls <scope>|<scope:team> 列出团队/成员

npm team edit <scope:team>  用编辑器编辑团队信息  
access
npm access public [<package>]  # 设置包开放  
npm access restricted [<package>] # 设置包私有

npm access grant <read-only|read-write> <scope:team> [<package>] # 设置团队对包可以只读/允许修改  
npm access revoke <scope:team> [<package>] # 从团队中收回包权限

npm access ls-packages [<user>|<scope>|<scope:team>]  # 列出用户/域/团队能够访问的包  
npm access ls-collaborators [<package> [<user>]] # 列出包的权限信息  
npm access edit [<package>] # 用编辑器编辑包权限  
adduser/login
npm adduser [--registry=url] [--scope=@orgname] [--always-auth]  
提示输入username, password, email，进行登录校验，返回token保存到.npmrc

logout
npm logout [--registry=<url>] [--scope=<@scope>]  
请求仓储服务将当前token失效

whoami
npm whoami [--registry <registry>]  
列出用户在npmjs.org上的用户名

包仓储
s/se/search
npm search [-l|--long] [--json] [--parseable] [--no-description] [search terms ...]  
-l|--long: 展示出全部的DESCRIPTION栏信息
--no-description: 不显示DESCRIPTION栏信息
publish
npm publish [<tarball>|<folder>] [--tag <tag>] [--access <public|restricted>]  
--tag: 带上tag信息发布,之后包可以通过npm install <name>@<tag>安装
--access: 仅适用于scope包,默认为restricted
unpublish
npm unpublish [<@scope>/]<pkg>[@<version>]  
从仓储中删除包,该操作会破坏依赖，不推荐适用，如果是为了鼓励用户适用新版本，可以使用deprecate命令

deprecate
npm deprecate <pkg>[@<version>] <message>  
标记包弃用，用户在安装时npm会有警告

stars
npm stars [<user>]  
查看用户喜欢的包

star/unstart
npm star [<pkg>...]  
npm unstar [<pkg>...]  
标记喜欢/取消喜欢标记

包本地
init
npm init [-f|--force|-y|--yes]  
初始化package.json, 默认会有很多输入提示，可以通过-f|--force|-y|--yes选项创建默认配置的package.json 已经存在package.json后再次运行npm init不会破坏已有配置,只会变更你真正改动的部分

i/install
npm install (with no args, in package dir) # 读取package.json安装  
npm install [<@scope>/]<name> # 默认安装标签为latest  
npm install [<@scope>/]<name>@<tag> # 指定标签  
npm install [<@scope>/]<name>@<version> # 指定版本  
npm install [<@scope>/]<name>@<version range> # 指定版本范围  
npm install <tarball file>  # 通过tarball文件安装  
npm install <tarball url> # 通过tarball文件url链接安装  
npm install <git remote url> # 通过git安装包, url格式为<protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish>]  
npm install <folder> 通过包所在的文件夹安装  
--registry: 从指定仓储中下载安装包
-S/--save: 安装并保存包信息到package.json的dependencies区
-D/--save-dev: 安装并保存包信息到package.json的devDependencies区
--tag: 优先根据标签而不是版本安装包
--dry-run: 报告安装状况而不真的安装
-f/--force: 安装时跳过缓存直接从远程下载
-g/--global: 安装到全局
--link: 链接全局安装的包的本地
--no-shrinkwrap: 安装时忽略shrinkwrap
un/uninstall
npm uninstall [<@scope>/]<pkg>[@<version>]... [-S|--save|-D|--save-dev]  
-S/--save: 删除包并移除包在package.json的dependencies区的信息
-D/--save-dev: 删除包并移除包在package.json的devDependencies区的信息
ddp/dedupe
npm dedupe  
npm检查包依赖树并清除不要的包

dist-tags
npm dist-tag add <pkg>@<version> [<tag>] # 添加标签  
npm dist-tag rm <pkg> <tag> # 移除标签  
npm dist-tag ls [<pkg>] # 列出包所包含的标签  
常见标签有latest, next, lts等

可以在发布和下载包是带上标签

npm publish # 默认标签latest  
npm publish --tag next  # 发布next标签  
npm install npm # 默认标签latest  
npm install npm@next  
npm install --tag next  
--registry: 发布包到指定仓储
v/version
npm version [<newversion> | major | minor | patch | premajor | preminor | prepatch | prerelease | from-git]  
该命令执行步骤 1. 检查git工作目录 
2. 运行preversion脚本, 可以写些触发测试的脚本 
3. 结合当前包当前版本信息和patch, minor, major等，生成新版本号，更新package.json中version字段 
- patch 1.0.0 => 1.0.1 - prepatch 1.0.0 => 1.0.1-0 - minor 1.0.0 => 1.1.0 - preminor 1.0.0 => 1.1.0-0 - major 1.0.0 => 2.0.0 - premajor 1.0.0 => 2.0.0-0 - prerelease 1.0.0-0 => 1.0.0-1 - from-git 从git获取版本信息 4. 运行version脚本 
5. git提交并打标签 
6. 运行postversion脚本

it/install-test
npm it  
npm install-test  
相当于运行npm install && npm test

ln/link
npm link  # 在全局node_modules下创建当前文件夹的超链接  
npm link [<@scope>/]<pkg>[@<version>] # 将全局安装的包链接到本地node_modules中  
ls/list
npm ls [[<@scope>/]<pkg> ...]  
打印依赖树

--json: 已json格式输出
--long: 展示更多信息
--parseable: 显示展平的目录而不是依赖树
--global: 显示全局安装的包的依赖树
--depth: 树层级,从0开始
--prod/production: 仅显示package.json里dependencies包的依赖
--dev: 仅显示package.json里devDependencies包的依赖
up/update
npm update [-g] [<pkg>...]  
更新包到包的semver所允许的最新版本, 并安装遗漏的包

--save: 更新并保存更新到package.json
--dev: 同时更新devDependencies中的包
--depth: 默认情况下仅更新顶层(--depth=0)为0的包,如果想更新所有包，可以指定--depth=9999
outdated
npm outdated [[<@scope>/]<pkg> ...]  
.e.g

Package        Current  Wanted  Latest  Location  
ajv              4.8.2  4.11.8   5.0.1  example  
async            2.1.2   2.4.0   2.4.0  example  
body-parser     1.15.2  1.17.1  1.17.1  example  
列表栏

Current: 当前版本
Wanted: smever允许的最高版本
Latest: 仓储中最新版本
Location: 依赖树中的位置
命令选项

--json: 已json格式输出
--long: 展示更多信息
--parseable: 平铺展示
--global: 显示全局安装的包的依赖树
--depth: 树层级,默认0
pack
npm pack [[<@scope>/]<pkg>...]  
从包生成名为<name>-<version>.tgz的tarball,并缓存

prune
npm prune [[<@scope>/]<pkg>...] [--production]  
清理不在package.json生成的依赖树中的包

--production: 移除devDependencies中的包
shrinkwrap
npm shrinkwrap  
shrinkwrap用来锁定依赖包的版本

包A的package.json

{
    "name": "A",
    "version": "0.1.0",
    "dependencies": {
        "B": "<0.1.0"
    }
}
包A的依赖树

 A@0.1.0
    `-- B@0.0.1
        `-- C@0.0.1
当B有新版本0.0.2发布, B@0.0.2满足<0.1.0, 所以npm install A安装成功后依赖树

 A@0.1.0
    `-- B@0.0.2
        `-- C@0.0.1
我们希望包A依赖的B版本保持在B@0.0.1, 可以运行

npm shrinkwrap  
该命令会生成npm-shrinkwrap.json, 其内容如下

{
  "name": "A",
  "version": "0.1.0",
  "dependencies": {
    "B": {
      "version": "0.0.1",
      "from": "B@^0.0.1",
      "resolved": "https://registry.npmjs.org/B/-/B-0.0.1.tgz",
      "dependencies": {
        "C": {
          "version": "0.0.1",
          "from": "org/C#v0.0.1",
          "resolved": "git://github.com/org/C.git#5c380ae319fc4efe9e7f2d9c78b0faa588fd99b4"
        }
      }
    }
  }
}
运行npm install时如果存在npm-shrinkwrap.json, npm在安装包时会根据shrinkwrap.json锁定依赖包的版本

cache
npm cache add <tarball file> # 添加到缓存  
npm cache add <folder>  
npm cache add <tarball url>  
npm cache add <name>@<version> 

npm cache ls [<path>]  # 缓存明细

npm cache clean [<path>] # 清除缓存  
缓存路径可以通过npm config get cache获取

脚本
package.json的scripts区可以用来定义自定义脚本

run/run-script
npm run <command> [-- <args>...]  
运行package.json的scripts中定义的命令

npm run会自动将node_modules/.bin添加到环境变量PATH中。如果本地安装过mocha, 可以这样编写"scripts": {"test": "mocha test/\*.js"}而不需要"scripts": {"test": "node_modules/.bin/tap test/\*.js"}

start
npm start [-- <args>]  
等同与npm run start [-- <args>]

stop
npm stop [-- <args>]  
等同与npm run stop [-- <args>]

tst/test
npm test [-- <args>]  
等同与npm run test [-- <args>]

rb/rebuild
npm rebuild [[<@scope>/<name>]...]  
运行指定包中的build脚本,适用于更新node版本后，重新编译C++包

restart
npm restart [-- <args>]  
循序执行1. prerestart 2. prestop 3. stop 4. poststop 5. restart 6. prestart 7. start 8. poststart 9. postrestart

配置
c/config
npm config set <key> <value> [-g|--global] # 添加或更新  
npm config get <key> # 获取  
npm config delete <key> # 删除  
npm config list #  配置明细  
npm config edit # 编辑器编辑  
--global: 全局配置
get
npm get <key> # 同npm config get  
set
npm set <key> <value> [-g|--global] #同npm config set  
查看
root
npm root # 打印本地node_modules目录  
npm root -g # 打印全局node_modules目录  
prefix
npm prefix # 打印包含package.json最近父目录  
npm prefix -g # 打印全局配置prefix的值  
view
npm view [<@scope>/]<name>[@<version>] [<field>[.<subfield>]...]  
查看仓储信息

npm view compact


#  打印
{ name: 'compact',
  description: 'A JavaScript compacting middleware for express',
  'dist-tags': { latest: '0.1.2' },
  maintainers: [ 'serby <paul@serby.net>' ],
  time: 
   { modified: '2017-03-28T12:49:48.000Z',
     created: '2012-02-06T01:39:50.261Z',
     '0.1.2': '2012-09-04T11:19:17.618Z',
     '0.1.1': '2012-08-29T08:18:12.345Z',
     '0.1.0': '2012-07-09T14:40:56.751Z',
     '0.0.7': '2012-07-04T17:14:01.593Z',
     '0.0.6': '2012-06-29T14:29:04.847Z',
     '0.0.5': '2012-05-23T10:10:15.010Z',
     '0.0.4': '2012-03-31T09:05:40.450Z',
     '0.0.3': '2012-03-23T15:25:18.289Z',
     '0.0.2': '2012-03-21T18:15:24.718Z',
     '0.0.1': '2012-02-06T01:39:50.261Z' },
  users: { serby: true },
  author: 'Paul Serby <paul@serby.net>',
  repository: { type: 'git', url: 'git://github.com/serby/compact.git' },
  versions: 
   [ '0.0.1',
     '0.0.2',
     '0.0.3',
     '0.0.4',
     '0.0.5',
     '0.0.6',
     '0.0.7',
     '0.1.0',
     '0.1.1',
     '0.1.2' ],
  version: '0.1.2',
  main: './lib/compact.js',
  scripts: { test: 'mocha -r should -R spec' },
  engines: { node: '>=0.8' },
  dependencies: 
   { lodash: '~0.3',
     async: '~0.1',
     'uglify-js': '~1.3',
     mkdirp: '~0.3' },
  devDependencies: { mocha: '*', should: '~1.1', async: '~0.1', asyncjs: '~0.0' },
  optionalDependencies: {},
  dist: 
   { shasum: '66361e17108185bf261d42aff6a91b925e473139',
     size: 7603,
     noattachment: false,
     tarball: 'http://registry.npm.taobao.org/compact/download/compact-0.1.2.tgz' },
  directories: {},
  publish_time: 1346757557618 }
npm view compact@0.1.2 dependencies

# 打印
{ lodash: '~0.3',
  async: '~0.1',
  'uglify-js': '~1.3',
  mkdirp: '~0.3' }
bin
npm bin # 打印包含npm bin目录, 通常为node_modules/.bin/  
npm prefix -g # 打印全局npm bin目录  
bugs/issue
npm bugs [<packagename>]  
打开包bug追踪url

npm bugs npm # 浏览器打开https://github.com/npm/npm/issues  
docs/home
npm docs [<pkgname> [<pkgname> ...]]  
npm docs .  
npm home [<pkgname> [<pkgname> ...]]  
npm home .  
打开文档url

npm docs npm #浏览器打开https://docs.npmjs.com/  
repo
npm repo [<pkg>]  
打开git url

npm repo npm #浏览器打开https://github.com/npm/npm  
help
npm help <term> [<terms..>]  
打印特定术语或命令的帮助

help-search
npm help-search <text>  
从npm官方markdown文档中搜索词条

其他
completion
npm completion >> ~/.bashrc  
npm命令插补脚本

doctor
npm doctor  
环境检测 - npm能调用node和git命令 - registry能够访问 - 本地和全局node_modules可写 - 缓存存在且tarball文件健全

edit
npm edit <pkg>[@<version>]  
进入包目录并启动编辑器

explore
npm explore <pkg> [-- <cmd>]  
进入包目录并运行命令

npm explore connect -- ls

# 打印
HISTORY.md  index.js  LICENSE  node_modules  package.json  README.md  
ping
npm ping [--registry <registry>]  
npm ping --registry https://registry.npmjs.org  
检查仓储是否可用