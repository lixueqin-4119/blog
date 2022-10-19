> [webpack文档](https://www.webpackjs.com/guides/getting-started/#%E5%9F%BA%E6%9C%AC%E5%AE%89%E8%A3%85)、[npm文档](https://docs.npmjs.com/cli/v8/configuring-npm/package-json)
> 
> 通过11个任务学会webpack。

**工具**\
**webpack@4 和 webpack-dev-server**本地预览\
本地预览httpserver没有webpack-dev-server功能强\
parcel也能本地预览但与webpack不配套,所以只能用webpack-dev-server。

**webpack这么多插件怎么学？**\
重要的"不是用什么"，而是"怎么知道用什么"，要学会搜+CRM法。\
🔍英文关键词+CRM法

### 框架

**Webpack功能**\
转译代码(ES6转为ES5，SCSS转为css)\
构建build\
代码压缩\
代码分析

**安装依赖**

```js
终端:yarn add webpack@4 webpack-cli@3 --dev //webpack-cli用于在命令行中运行webpack
```

### [目标一. 用webpack转译JS](https://www.webpackjs.com/guides/getting-started/#%E5%9F%BA%E6%9C%AC%E5%AE%89%E8%A3%85)

**1.调用webpack**\
**(1)新建文件webpack-demo并用vscode打开，新建终端：**

```js
npm init -y //创建package.json
yarn add webpack@4 webpack-cli@3 --dev //安装它的2个依赖
//会多个node_modules的目录。由于是本地安装，不是全局安装,文件在node_modules里的.bin目录下
```

**(2)新建目录src、新建index.js、新建x.js**

```js
import x from './x.js'
console.log(x)
x.js
export default 'fuck'
```

**(3)运行:**  由于是本地安装，运行时只能用绝对路径调用webpack

```js
npx webpack //自动但可能不稳定
//或者./node_modules/.bin/webpack --version // 手动调用本地webpack
```

调用后会默认把index.js转变为 main.js。\
会分析js代码，然后将代码变成IE或低版本浏览器可以用的js。


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8c59f6134c494f59b3218deecf741aaa~tplv-k3u1fbpfcp-zoom-1.image)

**2.初始化webpack.config.js**\
**去除警告：** [文档](https://webpack.js.org/concepts/configuration/)


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2507fcb159404eedb189cdb310bf779d~tplv-k3u1fbpfcp-zoom-1.image)

**(1)新建文件webpack.config.js，打开[文档](https://webpack.js.org/concepts/configuration/)复制粘贴**

```js
const path = require('path');

module.exports = {
    mode: 'development' //'production'
};
```

会设置main.js为开发者模式,方便自己阅读或者debug调试。如果是正在**开发中**可以设置为development，如果你正要**发布**可以设置为production用户模式，给用户看的，内容最少。\
**3.重新运行`npx webpack`**

### 目标二. 理解文件名中hash的用途

**附加知识**\
HTTP响应头中的Cache-Control

**1.webpack配置entry和output(入口和出口)**\
还是webpack.config.js文件，复制[文档](https://webpack.js.org/concepts/configuration/)

```js
const path = require('path');

module.exports = {
    mode: 'development',
    entry: './src/index.js',//你的入口文件
    output: {
        filename: 'main.js',//'index.js'
    },
};
```

entry是你的入口文件名。index.js转义后会默认生成main.js，output是转义后的文件名(默认是main.js)，可与入口名保持一致。\
**重新运行`npx webpack`**


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fdd6576635b24389b809aa681edae2fe~tplv-k3u1fbpfcp-zoom-1.image)

**2.更多配置**\
🔍[webpack filename hash](https://webpack.js.org/guides/caching/#output-filenames)

```js
filename: '[name].[contenthash].js',
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9b39a673b3104024b139fe28d2838329~tplv-k3u1fbpfcp-zoom-1.image)

这涉及到**HTTP缓存的意义**\
当你访问百度时，它会给你返回几个文件,当你第2次访问时它会重新再加载一遍。


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bb82c090f68043c79bff873a3c45b2e3~tplv-k3u1fbpfcp-zoom-1.image)

**设置缓存头的好处**\
比如在1.css、2.css、1.js、2.js**缓存头内**设置一年内只需下载1次缓存，且一年内不会修改。\
当你第2次访问baidu.com时就只需要下载index.html，然后它会从内存里得到1.css、2.css...


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/74b0d13b50594598b326514067034caa~tplv-k3u1fbpfcp-zoom-1.image)

每次修改后再次发布webpack，webpack就会重新生成个新的文件。浏览器一看请求(文件名)不一样就会立刻请求新的文件，放弃之前的缓存用新的缓存,从而实现更新。\
利用webpack，对文件内容做一个哈希 md5(在线解密加密)，webpack就会自动做这件事情。

**问题2：为什么首页不缓存呢？**\
因为首页需要通知浏览器给其它文件设置缓存的入口。

**问题3：**  每次修改都会得到新的，**那dist里的文件不是越来越多吗？**\
它会根据内容产生哈希，这样只要内容变了，就会得到新的文件。\
**方法:**  每次更新时删掉旧的，或者怕忘记可以添加到package.json里。\
npx webpack可简写成webpack。

```js
package.json
"scripts": {
    "build":"rm -rf dist && webpack",
    "test": "echo "Error: no test specified" && exit 1"
  },
```

**以后每次打包时直接运行**

```js
yarn build //npm run build
```

**总结\
hash的用途:便于你去添加缓存，这个缓存是http协议里规定的Cache-Control,浏览器自动支持**。

### 目标三. webpack插件自动生成HTML

**要点**\
如何自动改文件名

**1.安装html插件 html-webpack-plugin**\
🔍[webpack create html page](https://webpack.docschina.org/plugins/html-webpack-plugin/#installation)\
**步骤**

```js
(1)安装:yarn add html-webpack-plugin --dev
(2)使用:webpack.config.js添加plugins
   const HtmlWebpackPlugin = require('html-webpack-plugin');
   module.exports = {
     plugins: [new HtmlWebpackPlugin()],
   }
(3)运行:yarn build //npm run build
```

在安装HtmlWebpackPlugin后重启时报错：Cannot read property 'tap' of undefined。\
**原因:**  html-webpack-plugin与webpack版本不一致

```js
(1)先复制下面的package.json覆盖你的"devDependencies"。
"devDependencies": {
    "css-loader": "^3.2.0",
    "dart-sass": "^1.23.7",
    "file-loader": "^5.0.2",
    "html-webpack-plugin": "^3.2.0",
    "less": "^3.10.3",
    "less-loader": "^5.0.0",
    "mini-css-extract-plugin": "^0.8.0",
    "sass-loader": "^8.0.0",
    "style-loader": "^1.0.1",
    "stylus": "^0.54.7",
    "stylus-loader": "^3.0.2",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.10",
    "webpack-dev-server": "^3.9.0"
  }
(2)然后
yarn install
yarn build
```

dist目录下会生成一个空的index.html，并引入了你的main.xxxxxxxxxxxxx.js。


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bd4bba9287604823a41b22e0736d96da~tplv-k3u1fbpfcp-zoom-1.image)\




**js文件名可修改**

```js
webpack.config.js
filename: 'index.[contenthash].js'
//filename: '[name].[contenthash].js'

重新打包:yarn build
```

这就是网站首页生成的原理,自动化,全是自动生成的。

**2.如何自动改文件名**\
修改index.js的内容并重新打包yarn build,index.xxxxxxxx.js会自动更新地址的。\
index.html就是个空框架想加点东西都不行。dist目录下所有东西都是生成的，你加了之后下次打包就不见了。

**3.如何在index.html里加个div？[抄文档](https://github.com/jantimon/html-webpack-plugin#options)**\
**先试试加个title:**

```js
1.webpack.config.js
new HtmlWebpackPlugin({
  title: 'My App'
})
2.重新运行:yarn build
```

**再加个`<div>`** [抄文档](https://github.com/jantimon/html-webpack-plugin#generating-multiple-html-files)\
模仿文档:新建目录assets表示资源,再新建index.html

```js
1.webpack.config.js
plugins: [new HtmlWebpackPlugin({
  title: '小李子',
  template: 'src/assets/index.html'
})]
2.yarn build
```

**用我给你的模版来生成html。**\
它真的用了我们的模版。因为我们的模版是空的，所以只有一个`<script>`


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fbd412e9af4b446284e2aa58ffc5615e~tplv-k3u1fbpfcp-zoom-1.image)

**现在完善下我们自己的模版**

再看下生成的index.html

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/40a23c77280d496d8e824a16d2066e17~tplv-k3u1fbpfcp-zoom-1.image)

**4.怎么用配置webpack.config.js里的title？[抄文档](https://github.com/jantimon/html-webpack-plugin#writing-your-own-templates)**

```js
1.修改配置文件webpack.config.js,写好需要展示的内容.
  title: '前端框架 -webpack',
2.修改assets目录下的index.html
  <title><%= htmlWebpackPlugin.options.title %></title>
3.yarn build
```

dist目录下的index.html就会被设置好。\
**5.重置meta:vp**

```js
assets目录下的index.html
重置它的meta:vp
```

assets目录下的index.html随便设计，到时候生成就会按你这个写的。\
它只是会帮你插入js的入口文件而已。

### 目标四. webapck引入CSS

**要点**\
可以使用JS生成style。\
也可以把CSS抽成文件。

#### 1.用JS生成style

> html既然是自动插入的，我们就使用模块化的css

**步骤**

```js
第1步.新建x.css  body { background: red; }
第2步.在x.js里引入  import './x.css'
第3步.yarn build
```

**错误1** 它把css当成js来处理了。


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2d5c1816fb64490bbb99b9bbbb94971a~tplv-k3u1fbpfcp-zoom-1.image)

**解决**🔍[webpack css-loader](https://www.webpackjs.com/loaders/css-loader/#%E5%AE%89%E8%A3%85)

```js
(1)yarn add css-loader --dev //今天的所有依赖(loader)都是--dev。因为webpack的东西，用户是用不到的.
(2)webpack.config.js添加module。
//解析:如果你的文件名是以.css结尾就使用'css-loader'
module: {
  rules: [
    {
      test: /.css$/,
      use: ['style-loader', 'css-loader']
    }
  ]
}
(3)yarn add style-loader --dev
(4)yarn build
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b06fedc4784429f9943cb6f04b0436d~tplv-k3u1fbpfcp-zoom-1.image)

**错误2**:插件与webpack版本不搭


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8205ec6bf5bb4e9ebfa2ec22aad32b00~tplv-k3u1fbpfcp-zoom-1.image)

```js
plugin和loader部分遇到报错时(遇到警告3步走):
(1)请复制上面的package.json覆盖你的"devDependencies"。
(2)yarn install
(3)yarn build 
```

2个错误搞定！

**第4步.预览css**\
查看dist下的index是否成功引入css

```js
cd dist    
hs -c-1   //当前目录
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a6e49cfb51c1488cadeac91faeef5af2~tplv-k3u1fbpfcp-zoom-1.image)

原理:`style-loader`创建了`<style>`标签\
webpack配置:如果发现以.css结尾的文件就用`css-loader`先将文件读到js里。\
然后`style-loader`会将读到的内容，变成`<style>`标签放到head里。

每次**修改**都要中断http-server,退出dist目录,重新运行,太麻烦.

```js
快捷键:control+c
cd ..
yarn build
```

### 2.用webpack-dev-server代替hs，配置[devServer](https://v4.webpack.docschina.org/guides/development/#%E4%BD%BF%E7%94%A8-webpack-dev-server)

> 注意:webpack-dev-server不依赖dist目录文件，直接在内存中搞定。不会生成dist目录。\
> 会读你的index.js并转义成可运行的js,然后读到内存里。

```js
1.cd ..  //中断并退出dist目录 
2.yarn add webpack-dev-server --dev
//npm info webpack-dev-server
3.webpack.config.js添加
  devtool: 'inline-source-map',
  devServer: {
     contentBase: './dist'
   },
4.package.json添加
 "scripts": { 
     "start": "webpack-dev-server --open",
            //"webpack-dev-server", 是否帮你打开浏览器
   }
5.yarn start
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/76cc051f4f5647cdaa64f31d0d834be8~tplv-k3u1fbpfcp-zoom-1.image)

**遇到警告3步走...**

保存即刷新。

### 3.使用插件提取CSS文件

> 每次操作前记得提交代码防止挂掉

🔍[webpack css extract plugin](https://webpack.docschina.org/plugins/mini-css-extract-plugin/) 中文易过时

```js
1.安装:yarn add mini-css-extract-plugin --dev
2.webpack.config.js添加
const MiniCssExtractPlugin=require("mini-css-extract-plugin");
  
  plugins: [new MiniCssExtractPlugin()],
  module: {
    rules: [{
        test: /.css$/i,
        use: [MiniCssExtractPlugin.loader, "css-loader"]//抽成文件
      //use: ["style-loader", "css-loader"] //放到页面
      }],
  },
3.yarn build      
```

放到页面或抽成文件，只能2选1。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d6d6367f4d7b4b6bbd1a2ec95f48554b~tplv-k3u1fbpfcp-zoom-1.image)

**遇到警告3步走...**

dist目录下会多个main.css。\
html自动生成时，会自动帮你加该引入的东西。

[4.如何缓存css文件？](https://webpack.docschina.org/plugins/mini-css-extract-plugin/#publicpath)

```js
1.webpack.config.js添加
  new MiniCssExtractPlugin({
     filename: '[name].[contenthash].css',
     chunkFilename: '[id].[contenthash].css',
  }),
2.yarn build
3.预览:yarn start
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7a7572a058af47d4be05cecafea1ec8d~tplv-k3u1fbpfcp-zoom-1.image)

### 使用两个webpack config文件

### 第1部分.切换模式

> **切换模式:**  生产环境上线代码`yarn build`,开发`yarn start`。

```js
package.json
"script":{
  "start": "webpack serve",
  "build": "rm -rf dist; webpack",
}
```
**思路:** `build`和`start`对应不同的命令，可以设置它们用不同的config，实现2个插件的切换。\
**步骤**\
1.生产环境webpack.config.prod.js\
复制webpack.config.js重命名为webpack.config.prod.js。

```js
mode: 'production',
use: [MiniCssExtractPlugin.loader, "css-loader"]
```

2.开发环境webpack.config.js

```js
use: ['style-loader', 'css-loader'] //将<script>标签插到页面,速度更快
```

**3.通过命令选择要切换的文件**

```js
npx webpack --help //帮助文档
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/322b12c1a40f452eb957b09d4c7080f3~tplv-k3u1fbpfcp-zoom-1.image)

```js
package.json
"scripts": {
  "start": "webpack-dev-server --open",
  "build": "rm -rf dist && webpack --config webpack.config.prod.js",
 //当build时用生产配置
}
```
**4.测试**
```js
yarn start //开发 <style>标签
再开一个终端
yarn build //生产 <link/>标签,打开dist目录查看index.html
```

### 第2部分.优化

> 两个webpack config文件80%的内容都是相同的，用继承的思想简化

1.新建文件[webpack.config.base.js](https://github.com/lixueqin-4119/js-demo51/blob/master/webpack.config.base.js)并归纳共有属性。\
2.[webpack.config.js](https://github.com/lixueqin-4119/js-demo51/blob/master/webpack.config.js)继承base

```js
const base = require('./webpack.config.base.js')
module.exports = {
  ...base,//把base的所有属性抄到这来
  devtool: 'inline-source-map',
  devServer: {
    contentBase: './dist'
  },
  mode: 'development',
  module: {
    rules: [{
      test: /.css$/i,
      use: ['style-loader', 'css-loader']
    }]
  }
};
```

3.[webpack.config.prod.js](https://github.com/lixueqin-4119/js-demo51/blob/master/webpack.config.prod.js)继承base

```js
const base = require('./webpack.config.base.js')
module.exports = {
  ...base,
  mode: 'production',
  plugins: [
    ...base.plugins,//把base之前的抄过来
    new MiniCssExtractPlugin({
      filename: '[name].[contenthash].css',
      chunkFilename: '[id].[contenthash].css',
    }),
  ],
  module: { //直接覆盖
    rules: [{
      test: /.css$/i,
      use: [MiniCssExtractPlugin.loader, "css-loader"],
    }]
  }
};
```

**webpack的高级使用方法**\
🔍[webpack config merge](https://github.com/survivejs/webpack-merge) CRM法
