# [package.json](https://docs.npmjs.com/files/package.json)

旨在让同学们更了解`package.json`内的各字段的意义, package.json必须是json，不是js的object

什么是一个`package`
- a) 一个文件夹包含的执行程序，内部有个`package.json`描述文件
- b) 一个a的压缩包
- c) 一个返回b的链接地址
- d) 一个`<name>@<version>`，其在`npm`上注册发布过的包并且包含c
- e) 一个`<name>@<tag>`，其指向d
- f) 一个`<name>`，其有一个最新的tag满足e
- g) 一个`<git remote url>`，其返回一个a
- ...

## Required 字段

### name 
项目名称，`必须是小写`，`小于或等于214个字符`，并且只能用连字符或下划线,且不能以下划线开头, 最好不要在名称内加`js`或`node`等字段


### version 
项目版本，必须是`x.x.x`，遵循标准的语义化版本规则，npm本身是以[semver](https://docs.npmjs.com/misc/semver)库去判断版本号的合法性

## not Required 字段

### scripts 
指定了运行脚本命令的`npm`命令行缩写，比如`start`指定了运行`npm run start`时，所要执行的命令。[详情](https://docs.npmjs.com/misc/scripts)

```json
"scripts": {
  "start": "node index.js",
  "hi": "echo hello world !",
  "build": "webpack ./webpack.config.js"
}
```

### config
项目script运行时所需要的参数，[详情](https://docs.npmjs.com/misc/config),比如上文的script

```json
"config": {
	"port": "8080"
}
```
然后在执行`start`命令的时候，就可以通过`process.env.npm_package_config_port`获取到参数

### dependencies
指定项目运行所依赖的模块，开发，生产都会依赖这里的模块,列举一些合法的包的dependencies方式，要分清楚，哪些模块放在`dependencies`，哪些放在`devDependencies`中

```json
{ "dependencies" :
  { "foo" : "1.0.0 - 2.9999.9999"
  , "bar" : ">=1.0.2 <2.1.2"
  , "baz" : ">1.0.2 <=2.3.4"
  , "boo" : "2.0.1"
  , "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0"
  , "asd" : "http://asdf.com/asdf.tar.gz"
  , "til" : "~1.2"
  , "elf" : "~1.2.3"
  , "two" : "2.x"
  , "thr" : "3.3.x"
  , "lat" : "latest"
  , "dyl" : "file:../dyl"
  , "bzz" : "^1.0.0"
  }
}
```
贴出常用版本的含义：

~比较好理解，就是最后一位版本号模糊：
-	~1.2.3 :  >=1.2.3 <1.3.0
-	~1.2   :  >=1.2.0 <2.0.0

^比较蛋疼，看下面三个例子或许你就理解了：
-	^1.2.3 :  >=1.2.3 <2.0.0
-	^0.2.3 :  >=0.2.3 <0.3.0
-	^0.0.3 :  >=0.0.3 <0.0.4

总之，就是小于非0最高位的那位数+1

还有几种方式：
- URLs as Dependencies 指定压缩包的链接地址和版本号范围
- Git URLs as Dependencies 格式如：`<protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish> | #semver:<semver>]`
Examples：
  ```
  git+ssh://git@github.com:npm/cli.git#v1.0.27
  git+ssh://git@github.com:npm/cli#semver:^5.0
  git+https://isaacs@github.com/npm/cli.git
  git://github.com/npm/cli.git#v1.0.27
  ```
- Local Paths
Examples：
  ```
  {
    "name": "baz",
    "dependencies": {
      "bar": "file:../foo/bar"
    }
  }
  ```
这种对本地开发库，测试改库比较有用，不用发布等

### devDependencies 
指定项目开发所需要的模块，生产中用不到的模块
别人使用你的库，肯定不想下载一些不需要的的模块，所有开发者自己要将一些测试，文档工具，或者帮助开发的工具放在`devDependencies`中

```json
{
  "devDependencies": {
    "react": "16.2.0"
  }
}
```

### peerDependencies
标示插件与它们的宿主之间的依赖，好比说 “我是宿主 1.2.x 的扩展，如果你安装我，请确定安装兼容的宿主。” 暂时称这种关系为同伴依赖，`npm 3+` 不再自动安装同伴依赖,会给出`warning`
比如antd 3.x版本的

```json
"peerDependencies": {
	"react": ">=16.0.0",
	"react-dom": ">=16.0.0"
}
```

### bundledDependencies
依赖包名称的数组，在`npm pack`发布打包的时候，会将这些依赖一起打入包内，生成`tgz`

### optionalDependencies
可选依赖，可选依赖会覆盖`dependencies`里同名称的依赖，比如下面这种逻辑

```js
try {
  var foo = require('foo')
  var fooVersion = require('foo/package.json').version
} catch (er) {
  foo = null
}
if ( notGoodFooVersion(fooVersion) ) {
  foo = null
}

// .. then later in your program ..

if (foo) {
  foo.doFooThings()
}
```

### description
是个字符串，顾名思义，描述，能够帮助别人发现你的包，会在 `npm search`中展现出来

### keywords
是个字符串的数组，关键词的数组，能够帮助别人发现你的包，会在 `npm search`中展现出来

### homepage
项目首页或者文档首页

```json
"homepage": "https://github.com/owner/project#readme"
```

### bugs
项目的`issue`的地址或者是你的邮箱地址，可以帮助使用者遇到问题时，通过改路径想你反馈问题
可以是个对象

```json
{ 
	"url" : "https://github.com/owner/project/issues", 
	"email" : "project@hostname.com"
}
```
或者是个字符串

```json
"bugs": "https://github.com/owner/project/issues"
```

### repository
代码托管的位置，对于想要贡献代码的用户很有用，如果代码是托管在`github`上，通过`npm docs`能找到你

```json
"repository": {
  "type" : "git",
  "url" : "https://github.com/npm/cli.git"
}

"repository": {
  "type" : "svn",
  "url" : "https://v8.googlecode.com/svn/trunk/"
}
```
*** 注意：地址并不是浏览器页面的地址，是项目地址，并且是公开的地址 ***

### author
包的作者

### contributors
贡献者，贡献者是一群人，是个数组

### files
文件名或者文件夹名的数组，标示了，使用者在安装你的包作为依赖的时候，都包含哪些文件或者文件夹，语法更`.gitignore`类似。
files里包含的文件`优先级最高`，如果没有该字段，会找到根目录下的`.npmignore`或者`.gitignore`去排除一些文件，如果都没有，那就会把当前

某些文件总是被包含在内的，不论规则是否匹配到它们：

- package.json
- README
- CHANGES / CHANGELOG / HISTORY
- LICENSE / LICENCE
- NOTICE
- The file in the “main” field 即main字段指定的文件(默认被包含进去的只能根目录下的一个文件，不能是xxx/xxx.js，这种不会被默认打包进去)

某些文件总是被忽略：

- .git
- CVS
- .svn
- .hg
- .lock-wscript
- .wafpickle-N
- .*.swp
- .DS_Store
- ._*
- npm-debug.log
- .npmrc
- node_modules
- config.gypi
- *.orig
- package-lock.json

可以通过在根目录下创建`.npmignore`文件忽略一些文件，语法和`gitignore`一样

### main
包的主入口文件。即如果别人`require`你的包，然后返回的是主入口文件`exports`出的模块

### browser
指定该模板供浏览器使用的版本。

### bin
指定各个内部命令对应的可执行文件的位置。比如：

```json
"bin": {
  "someTool": "./someTool.js"
}
```
上面代码指定了，someTool命令对应的可执行文件为根目录下的someTool.js，当用户安装这个包后，`npm`会寻找这个文件，在`node_modules/.bin/`目录下建立对应名称的`符号链接(软链接)`，
由于`node_modules/.bin/`目录会在运行时加入系统的`PATH`变量，因此在运行`npm`时，就可以不带路径，直接通过命令来调用这些脚本。

```json
"scripts": {  
  "start": "./node_modules/someTool/someTool.js"
}

// 简写为

"scripts": {  
  "start": "someTool"
}
```
`bin`可以是个路径的字符串，eg："./someTool.js"，那么默认`bin`的命令即是包的`name`字段。或者是是个对象

```json
{ 
  "name": "my-program", 
  "version": "1.2.5", 
  "bin": "./path/to/program" 
}
```
等同于

```json
{
  "name": "my-program", 
  "version": "1.2.5", 
  "bin" : { "my-program" : "./path/to/program" }
}
```

#### 注意⚠️：`bin`里包含的文件，文件开头要加`#!/usr/bin/env node`，否则不会在`node`环境下执行 


### license
包的许可证，决定了使用者有哪些使用权限和使用限制

```json
{
	"license": "MIT"
}
```

如果不希望别人在任何情况下使用，可以这样写

```json
{ "license": "UNLICENSED" }
```
同时设置`"private": true`防止意外发布


### engines
开发者，可以指定自己的包在指定版本的node上运行，或者指定`*`或者不指定，那就是任务版本都可以

```json
{ "engines" : { "node" : ">=0.10.3 <0.12" } }
```
还可以指定什么版本的`npm`可以安装
```json
{ "engines" : { "npm" : "~1.0.20" } }
```



