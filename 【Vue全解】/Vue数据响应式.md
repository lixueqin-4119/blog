# 数据响应式
> 主要原理:[深入响应式原理](https://cn.vuejs.org/v2/guide/reactivity.html)
> 
> 内容:深入理解options.data


# Vue对data做了什么？

```js
const myData = {
  n: 0
};
console.log(myData); 

new Vue({
  data: myData,
  template: `
    <div>{{n}}</div>
  `
}).$mount("#app");

setTimeout(() => {
  myData.n += 10;
  console.log(myData); 
}, 3000);

```
3s后n变成10，这次没有在vm里面加，而是在外面，说明在外面也可以变更myData。

**一般我们是在vm内变更myData的**
```js
new Vue({
  template:`
  <div>
    {{n}}
    <button @click="add"> +1 </button>
  </div>
  `,
  methods:{
    add(){
    //this.n +=10
      myData.n +=10
    }
  }
}).$mount("#app")
```
平常我们都用`this.n`，今天试试`myData.n`。

分别打印出myData刚声明和3s后的结果，如果第1次是n:0,那第2次就应该是n:10，
看下结果:

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2771ec25574140009b4344b3316927ed~tplv-k3u1fbpfcp-zoom-1.image)

**第2次并不是n:10，那这个`n:(...)`是什么呢？**

我们需要先学习[ES6](https://fangyinghang.com/es-6-tutorials/)的[getter、setter](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Object_initializer#ECMAScript_6%E6%96%B0%E6%A0%87%E8%AE%B0)

示例:要想得到姓名就要调用函数`obj1.姓名()`所以括号不能省，但是ES6的get语法可以实现`obj1.姓名`删掉括号。
```js
// 需求一，姓名不要括号也能得出值
let obj1 = {
  姓: "高",
  名: "圆圆",
  get 姓名() { //以函数的形式定义的属性
    return this.姓 + this.名; 
  }
};
console.log("需求一：" + obj1.姓名);
// 总结:getter就是不加括号的函数而已。

// 需求二：姓名可以被写
let obj2 = {
  姓: "高",
  名: "圆圆",
  get 姓名() {
    return this.姓 + this.名;
  },
  set 姓名(xxx){
    this.姓 = xxx[0]
    this.名 = xxx.substring(1)
  }
};
obj2.姓名 = '高媛媛'
console.log(`需求二：姓 ${obj2.姓}，名 ${obj2.名}`)
// 总结:setter用= xxx触发set函数
```
**把obj2打出来**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99788346ebb6434e89e2ac422f7e478b~tplv-k3u1fbpfcp-zoom-1.image)

浏览器说你确实可以对姓名进行读和写，但是并不存在一个叫姓名的属性。但是你可以通过get和set设置它。

**推断1:** 由此推断之前得到的n也是一个`getter、setter`
`get 姓名、set 姓名`说明`姓名:(...)`不是一个真实的属性

**推断2:**`n:(...)`并不存在属性n,而是有个`get n、set n`,它们来模拟对n的读写操作。

**那为什么要把n变成`get n、set n`呢？**

需要再学下[Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

之前在使用`getter、setter`时，是在定义这个对象时直接使用的。在定义完一个对象之后，要想再添加新的get set时只能用`Object.defineProperty()`
```js
Object.defineProperty(obj2,'xxx',{ 
//给obj2添加虚拟属性xxx，注意xxx是不存在的。
  var _xxx=0 //_xxx是用来存放set值的
  get(){
    return _xxx
  },
  set(value){
    _xxx=value
  }
})
```
```js
let data0 = { n: 0 }
// 需求一:用Object.defineProperty定义n
let data1 = {}
Object.defineProperty(data1, 'n', { //给data1添加虚拟属性n,n=0
  value: 0
})
console.log(`需求一：${data1.n}`) //0
// 这语法把事情搞复杂了？非也，继续看。

// 需求二：n不能小于0
let data2 = {}
data2._n = 0 //用_n存储n的值
Object.defineProperty(data2, 'n', {
  get(){
    return this._n
  },
  set(value){ 
  //set可以添加判断:小于0直接return,否则将n值置为最新value
    if(value < 0) return 
    this._n = value 
  }
})
console.log(`需求二：${data2.n}`)
data2.n = -1
console.log(`需求二：${data2.n} 设置为 -1 失败`)
data2.n = 1
console.log(`需求二：${data2.n} 设置为 1 成功`)
// 那如果对方直接用data2._n呢？_n可以直接设置为-1呀

// 需求三：使用代理obj
//括号里是匿名对象,直接改为没有名字的对象{n:0}，data可有可无
let data3 = proxy({ data:{n:0} }) 
function proxy({data}){ //接收data属性
  const obj = {}
  //理论应该遍历data的所有key，这里做了简化
  Object.defineProperty(obj, 'n', { 
    get(){
      return data.n //当你取obj.n,就返回data.n
    },
    set(value){
      if(value<0)return
      data.n = value //当你设置obj.n,就设置data.n
    }
  })
  return obj
}
// data3 就是 obj
console.log(`需求三：${data3.n}`)
data3.n = -1
console.log(`需求三：${data3.n}，设置为 -1 失败`)
data3.n = 1
console.log(`需求三：${data3.n}，设置为 1 成功`)
//杠精说,你看下面代码

// 需求四：绕过代理，通过引用
let myData = {n:0}
let data4 = proxy({ data:myData })//括号里是匿名对象，无法访问
console.log(`杠精：${data4.n}`)
myData.n = -1
console.log(`杠精：${data4.n}，设置为 -1 失败了吗！？`)

// 需求五：就算用户擅自修改myData，也要拦截他
let myData5 = {n:0}
let data5 = proxy2({ data:myData5 }) //data5就是myData5的代理对象
//监听data
function proxy2({data}){ 
  let value = data.n //拿到n,记录下来
  Object.defineProperty(data, 'n', { 
    get(){ return value },
    set(newValue){
      if(newValue<0)return
      value = newValue
    }
  })
//代理的逻辑
  const obj = {}
  Object.defineProperty(obj, 'n', {
    get(){ return data.n },
    set(value){
      if(value<0)return
      data.n = value //通过替身value就不能直接修改data.n啦
    }
  })
  return obj
}
console.log(`需求五：${data5.n}`)
myData5.n = -1
console.log(`需求五：${data5.n}，设置为 -1 失败了`)
myData5.n = 1
console.log(`需求五：${data5.n}，设置为 1 成功了`)

// 这代码看着眼熟吗？
let data5 = proxy2({ data:myData5 }) 
let vm = new Vue({data: myData})
现在我们可以说说 new Vue 做了什么了
```
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e91bb7b65b9447db8c3396bfd0b0b004~tplv-k3u1fbpfcp-zoom-1.image)


### 小结
**4.Object.defineProperty**

可以给对象添加属性value

可以给对象添加getter/setter

getter/setter用于对属性的读写进行监控

**啥是代理(设计模式)**

对myData对象的属性读写，全权由另一个对象vm负责

那么vm就是myData的代理

比如myData.n不用，偏要用vm.n来操作myData.n

**vm = new Vue({data: myData})**

**一.会让vm成为myData的代理(proxy)**

**二.会对myData的所有属性进行监控**

为什么要监控，为了防止myData的属性变了，vm不知道

vm知道了又如何？

知道属性变了可以render(data)刷新呀

UI=render(data)

**注意:** 全程这个对象`n:0`都没有被我扔掉过，一直在改这个对象里面的东西，不是搞出了一个新对象。我是把这个对象的n给覆盖掉了，变成get n、set n,我没有把这个对象删掉，因为如果我把这个对象删掉生成新的对象，那关联就断开了，不是同一个对象了。

我全程都是在这个对象上面修改，修改后得到一个被修改的对象。然后在被修改的基础上，生成了一个新的代理。

**明白2件事**

1.Vue会对data后面的`{n:0}`进行窜改，给它加监听。

2.会新生成一个对象，这个对象会代理篡改后的对象。

**Object.defineProperty**

可以给对象添加属性value

可以给对象添加getter/setter

getter/setter用于对属性的读写进行监控

**数据响应式**
> 若一个物体能对外界的刺激做出反应，它就是响应式的

**Vue的data是响应式**

`const vm=new Vue({data:{n:0}})`

我如果修改vm.n,那么UI中的n就会响应我

Vue 2通过`Object.defineProperty`来实现数据响应式

**响应式网页是啥？**

如果我改变窗口大小，网页内容会做出响应，那就是响应式网页。

### Vue的data的bug
> 目前你已经知道了数据响应式，但面试不会考常态一般考变态。

**`Object.defineProperty`的问题**

`Object.defineProperty(obj,'n',{...})`

必须要有一个'n'，才能监听 & 代理obj.n对吧

如果前端开发者比较水，没有给n怎么办

**示例1.** Vue会给出一个警告
```js
new Vue({
  data: {},
  template: `
    <div>{{n}}</div>
  `
}).$mount("#app");
```
**示例2.** Vue只会检查第一层属性
```js
new Vue({
  data: {
    obj: {
      a: 0 //obj.a会被Vue监听 & 代理
    //b:undefined  
    }
  },
  template: `
    <div>
      {{obj.b}}
      <button @click="setB">set b</button>
    </div>
  `,
  methods: {
    setB() {
      this.obj.b = 1; //页面中不会显示1
    //Vue.set(this.obj,'b'，1)
    //this.$set(this.obj,'b'，1) 
    }
  }
}).$mount("#app");
```


**解决办法**

**1.把k都声明好，后面不再加属性** 比如`b:undefined` 

**2.使用Vue.set或者this.$set**  

`$`是为了防止重名

比如`Vue.set(this.obj,'b')`或者`this.$set(this.obj,'b'，1)`

**Vue.set和this.$set作用:**

新增key

自动创建代理和监听(如果没有创建过)

触发UI更新(但不会立刻更新)

### 数组的变异方法
**data中有数组，没法提前声明所有key怎么办？**

示例
```js
new Vue({
  data: {
    array: ["a", "b", "c"]
  },
  template: `
    <div>
      {{array}}
      <button @click="setD">set d</button>
    </div>
  `,
  methods: {
    setD() {
    //this.array[3] = "d"; 页面中不会显示'd'
    //this.$set(this.array,3,'d') 增加下标的方式实现添加
    this.array.push('d')//尤雨溪的做法
    console.log(this.array)
    }
  }
}).$mount("#app");
```

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0705b3c1e3874c09b5d10c9ee15dc054~tplv-k3u1fbpfcp-zoom-1.image)


**尤雨溪的做法:**[篡改数组的API](https://cn.vuejs.org/v2/guide/list.html#s-authing)

Vue在这个对象，你以为这个是数组对象，你传给Vue之后，Vue就会篡改这个数组。它会**在中间加一层原型**。这个原型有7个方法，这7个方法跟以前是同名的但是代码被尤雨溪改了,会帮你set(监听,每次push都会通知Vue)。也就是说push会做2件事情:调以前的push,调完后通知Vue添加监听和代理。

**这7个API都会被Vue篡改，调用后会更新UI**

**总结**

**1.对象中新增的key**

Vue无法事先监听和代理

要使用set来新增key,创建监听和代理,更新UI

最好提前把属性都写出来，不要新增key

但数组做不到「不新增key」

**2.数组中新增的key**

也可用set来新增key,更新UI

不过尤雨溪篡改了7个API方便你对数组进行增删

这7个API会**自动处理监听和代理**，并更新UI

结论:数组新增key最后通过7个API


![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/747dd91fdf354b1eb0055a0d2905105c~tplv-k3u1fbpfcp-zoom-1.image)


**面试题**

**说说你对Vue数据响应式的理解**

Vue数据响应式,使得数据更新时得到及时的渲染。vue通过`Object.defineProperty()`给数据对象添加value属性，设置getter和setter监控属性的读写，并使用vm对象负责数据对象的代理，当属性更新时，调用rander()更新。