### loader VS plugin
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/730a46d493fc4b1abdfcfc85331424b3~tplv-k3u1fbpfcp-zoom-1.image)

**用webpack生成JS**

我们给它个js文件，这个js文件通过webpack得到了一个新的js文件。
内置了`babel-loader`(库),通过这个loader把js load到webpack里面，然后webpack就输出了另外一个js。

**用webpack生成CSS**

引入css(js import './x.css'),通过`'style-loader'和'css-loader'`把css变成`<style>`标签
给我个css,通过插件`MiniCssExtractPlugin`,把css变成文件。把n个css变成1个文件。

**加载器与插件的区别？**

loader是加载器，plugin是插件。
loader永远是1对1，plugin是n对1。loader功能比较单一就是用来加载文件的，plugin功能丰富。

**用webpack生成html**

html是通过`HtmlWebpackPlugin`生成新的html，该html会自动引入css、js。

**面试题(必考)**

**webpack loader与plugin的区别是什么？**

1.webpack loader是用来加载文件的，webpack plugin是用来扩展webpack的功能的。

2.loader主要用来加载一个个的文件。比如说，它可以加载js文件把js文件转译成低版本浏览器可以支持的js。它也可以加载css文件，`'style-loader'和'css-loader'`把css变成页面上的`<style>`标签或其它的一些处理。它也可以加载图片文件，对图片进行一些优化。

3.plugin是用来加强webpack功能的。

比如说`HtmlWebpackPlugin`,它是用来生成html文件的，`MiniCssExtractPlugin`是用来抽取css代码把它变成一个css文件的。

### 目标五. webapck引入SCSS
**要点**

node-sass已经过时,请使用dart-sass。

dart-sass已被重命名为sass

**步骤** 🔍[webpack scss loader](https://github.com/webpack-contrib/sass-loader#getting-started)
```js
1.yarn add sass-loader sass --dev
2.新建文件x.scss,并写好样式。
//scss语法,还是最原始的css
3.x.js
  import "./x.scss";
  import "./y.scss";  
4.webpack.config.base.js添加
module: {
  rules: [{
      test: /\.scss$/i,
      use: [
        "style-loader",
        "css-loader",
        "sass-loader",
      ],},
     ],
    },
webpack.config.js添加
rules: [
  ...base.module.rules,//先抄过来再覆盖
  {
    test: /\.css$/i,
    use: ['style-loader', 'css-loader']
  }
]
webpack.config.prod.js添加
 rules: [
  ...base.module.rules, 
  {
    test: /\.css$/i,
    use: [MiniCssExtractPlugin.loader, "css-loader"],
  }
]
5.yarn build
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/38ffcbd4ab1646a598af6eeb4f1c9e80~tplv-k3u1fbpfcp-zoom-1.image)
**遇到警告"[3步走](https://github.com/lixueqin-4119/webpack-demo/tree/master#readme)"**
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a0af79a57a19483f8110f5fb5260cb20~tplv-k3u1fbpfcp-zoom-1.image)


**报错:** 找不到node-sass，说明依然在用node-sass 🔍[dart-sass](https://github.com/webpack-contrib/sass-loader/issues/435#issuecomment-375787934)
```js
解决:webpack.config.base.js添加
  
  use: [
    "style-loader",
    "css-loader",
    {
      loader: "sass-loader",
      options: { implementation: require('sass')
     }
```

### 目标六.用webapck 引入 LESS 和 Stylus
**经验**

CSS有SASS、LESS、Stylus3种变种语言

**引入less的步骤** [🔍webpack less loader](https://www.webpackjs.com/loaders/less-loader/#%E5%AE%89%E8%A3%85)
```js
1.yarn add less-loader less --dev
2.新建文件y.less,并写好样式。
//sass,less都兼容css,所以直接修改后缀即可
//less语法,使用变量
 @color :red; 
 body {
   background: @color;
   color: @color;
 }
3.x.js
  import "./y.less";
4.webpack.config.base.js添加
rules: [
  {
    test: /\.less$/,
    use: [{
      loader: "style-loader" //把js字符串变成<style>标签
    }, {
      loader: "css-loader" //把css语法转为js字符串
    }, {
      loader: "less-loader" //把less语法转为css语法
    }]
  },
  ...
]
5.yarn build
6.yarn start //预览
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e6b94f0fbe74c23be1197a91765fcc5~tplv-k3u1fbpfcp-zoom-1.image)

**遇到警告"[3步走](https://github.com/lixueqin-4119/webpack-demo/tree/master#readme)"**
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5fd1f0b1dd104a819f224eb378063662~tplv-k3u1fbpfcp-zoom-1.image)


### 引入Stylus的步骤
 [stylus语法](https://stylus-lang.com/docs/variables.html)  
  [🔍webpack Stylus loader](https://github.com/webpack-contrib/stylus-loader#normal-usage)

```js
1.yarn add stylus-loader stylus --dev
2.新建文件z.styl,并写好样式。
//stylus语法
 c=rgb(255, 0, 191); 
 body{
   background: c;
 }
3.x.js
  import './z.styl'
4.webpack.config.base.js添加
 rules: [
   {
     test: /\.styl$/,
     use: [
       {loader: "style-loader", },
       {loader: "css-loader",},
       {loader: "stylus-loader", },
     ],
   },
 ],
5.yarn build
6.yarn start //预览
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8a0ee587509b4c2da46a4b7b0a0c4245~tplv-k3u1fbpfcp-zoom-1.image)

**遇到警告"[3步走](https://github.com/lixueqin-4119/webpack-demo/tree/master#readme)"**


### 目标七.用webpack引入图片
> 用file-loader引入图片，所以东西都能用webpack引入。
**要点**

万物皆JS

方法1'把图片上传公司的CDN，`src="图片的网络地址"` 。

**方法2'用相对路径。** 但图片无法加载,因为server不是当前目录，而是在dist目录下。

**步骤**🔍[webpack image loader](https://www.webpackjs.com/loaders/file-loader/#%E5%AE%89%E8%A3%85)
```js
1.yarn add file-loader --dev
2.本地导入图片:将图片放到assets目录下。
3.index.js
 import png from './assets/1.png'
 //console.log(png)
 const div=document.getElementById('app')
 div.innerHTML=`
   <img src="${png}">
 ` 
4.webpack.config.base.js添加
 rules: [
   {//上传图片
     test: /\.(png|svg|jpg|jpeg|gif)$/i,
     use: ["file-loader"],//把文件变成文件路径
   },
   ...
 ]
5.yarn start
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b57756d6ac414197963e70cb851e9e55~tplv-k3u1fbpfcp-zoom-1.image)

每张图片都要引一次，loader一对一。

### 目标八.webpack import() 懒加载
**使用懒加载**

**为什么要懒加载，为什么不一开始就加载？**

因为体积大。如果有100个模块那用户就要等好久才能出现。不如当用户开启某个模块时再用。

**步骤**
```js
1.新建文件lazy.js
 export default function lazy(){
   console.log('我是一个懒加载的模块')
 }
2.index.js
 const button = document.createElement('button')
 button.innerText = '懒加载'
 button.onclick = () => {
    const promise = import('./lazy') //异步的promise
    promise.then((module) => {
      const fn = module.default()
      fn()
    }, () => { })
 }
div.appendChild(button)
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f06194cd37e44458ba0dec71e58a4b2a~tplv-k3u1fbpfcp-zoom-1.image)

打印出了一个模块，默认是lazy函数。

**那怎么用呢？**
```js
promise.then((module)=>{
  const fn=module.default
  fn()
},()=>{})
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/73f59e624d4b465f9cce8668a3c891a2~tplv-k3u1fbpfcp-zoom-1.image)

[动态import](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import#%E5%8A%A8%E6%80%81import)

关键字import可以像调用函数一样来动态的导入模块。以这种方式调用，将返回一个 promise。

**面试题(高频)**
**请问如何实现懒加载？**
```js
const promise = import('./lazy') //异步的promise
  promise.then((module) => {
    const fn = module.default()
    fn()
  }, () => { console.log("模块加载错误")})
```
用import()加载这个文件,会得到一个promise。promise.then(()=>{},()=>{})前面写成功后的操作，后面写失败后的操作。

### 目标九.[部署到github](https://github.com/lixueqin-4119/webpack-demo)
1' git commit后重新build生成dist目录，理论上来说dist目录里面的文件就是独立的前端网站。

先在本地预览测试下，没问题就可以上传github了。
```js
//已提交过一次
yarn build
cd dist/
hs -c-1
control+c中断
cd ..
git push //提交新代码
```
如果你已经添加过想换一个新的地址，将add改为set即可。
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7de9b5d6c77140f69101f9bc376f5421~tplv-k3u1fbpfcp-zoom-1.image)


**2' 使用分支**

1.先把本地dist目录删掉再重新build,新建分支并进入。

2.把dist里面的东西全弄出来:除dist外全部删掉，把`node_modules`和`.gitignore`也留下。

删多了可以通过重置`git reset --hart HEAD`挽回。

3.把dist里面的东西都拷出来
```git
rm -rf dist
git add .
git commit -m 'remove dist'
yarn build
git push
git branch gh-pages //新建分支
git checkout gh-pages //进入分支
//git reset --hart HEAD 重置
重新提交
mv dist/* ./
rm -rf dist
再次提交
```
**最终效果图**
![请添加图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/271ca1a1dfa7444998fcdf518327a996~tplv-k3u1fbpfcp-zoom-1.image)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ad738720aa84d9ab588ae3427d4a94e~tplv-k3u1fbpfcp-zoom-1.image)

浏览器逆时针转表示**正在请求**，顺时针转表示**在下载**。

pending挂起中。
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/848ae00ca7484f97a825a60982fdba6e~tplv-k3u1fbpfcp-zoom-1.image)

vue-cli就是配置好 webpack 的 vue，有时候不想使用默认的webpack就必须自己配置webpack。





