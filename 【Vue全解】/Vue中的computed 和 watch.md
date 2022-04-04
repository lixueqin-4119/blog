# computed 和 watch
> [Vue2 options文档](https://cn.vuejs.org/v2/api/#%E9%80%89%E9%A1%B9-%E6%95%B0%E6%8D%AE)

**响应式原理options.data**

1.data会被Vue监听

2.会被**Vue实例代理**,vm就是data的代理

3.每次对data的**读写都会被**Vue监控。不管你是读写它的本身还是读写代理都会被监控，data对象会被篡改,本来的n会变成`get n`和`set n`。

4.Vue会在data变化时更新UI

**data变化时除了更新UI，还能做些啥？**

## 一.Computed 计算属性

> **1.用途:被计算出来的属性就是计算属性**
> 
> **2.缓存：** 如果依赖的属性没有变化，就不会重新计算
> 
>    getter/setter默认不会做缓存，Vue做了特殊处理

**例1:用户名展示**
```js
new Vue({
  data: {
    user: {
      email: "1231231231@qq.com",
      nickname: "小白",
      phone: "13812345678"//手机号用string存，因为有时候会出现+86
    }
  },
  computed: { //计算属性
    displayName: {
      get() {
        const user = this.user;
        return user.nickname || user.email || user.phone;
      },
      set(value) {
        console.log(value);
        this.user.nickname = value;
      }
    }
  },
  // Don't repeat yourself
  // 用 computed 来计算 displayName
  template: `
    <div>
      {{displayName}}
 <!-- {{user.nickname || user.email || user.phone}} -->
      <div>
        {{displayName}}
 <!-- {{user.nickname || user.email || user.phone}} -->
        <button @click="add">set</button>
      </div>
    </div>
  `,
  methods: {
    add() {
      console.log("add");
      this.displayName = "小红";
    }
  }
}).$mount("#app");
```
**什么时候用computed？**

如果我想在页面的100处展示`{{user.nickname || user.email || user.phone}}`，那就需要把这行代码复制100次。假如需求变了:调整顺序，那就需要一个个修改。为了解决这个问题可以用`computed`计算属性。

**计算属性的好处：**

让一些“根据其它属性计算而来的属性”变成一个属性，比如displayName。

**怎么设置修改一个属性？**[文档](https://cn.vuejs.org/v2/api/#computed) 

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e2a1e3aaf8c7474bba2f11883441be2d~tplv-k3u1fbpfcp-zoom-1.image)

> **1' key(属性名):函数**
> 
> **2' `key:{get:Function,set:Function}`**

```js
computed: {
    displayName: { //key:{get:Function,set:Function}
      get() {
        const user = this.user;
        return user.nickname || user.email || user.phone;
      },
      set(value) {
        this.user.nickname = value;
      }
    }
  },
```
**例2:[列表展示](https://codesandbox.io/s/bold-matsumoto-efn0i)**
```js
let id = 0;
const createUser = (name, gender) => { //DRY原则：1.封装createUser
  id += 1;
  return { id, name, gender };
};
new Vue({
  data() {
    return {
      users: [
        createUser("小百", "男"),//直接调用已封装的createUser
        createUser("圆圆", "女"),
        createUser("小新", "男"),
        createUser("小白", "女")
      ],
    displayUsers: []
    };
  },
  created() {
    this.displayUsers = this.users
  },
  methods: {
    showMale() {
    //把性别男的filter出来，将得到的新数组的地址赋值给displayUsers
      this.displayUsers = this.users.filter(u => u.gender === "男")
    },
    showFemale() {
      this.displayUsers = this.users.filter(u => u.gender === "女")
    },
    showAll() {
      this.displayUsers = this.users
    }
  },
    // 如何给三个按钮加事件处理函数
    // 思路一：点击之后改users
    // 思路二：使用computed
  template: `
  <div>
    <div>
      <button @click="showAll">全部</button>
      <button @click="showMale">男</button>
      <button @click="showFemale">女</button>
    </div>
    <ul>
      <li v-for="(u,index) in displayUsers" :key="index">
        {{u.name}} - {{u.gender}}
      </li>
   </ul>
  </div>
  `
}).$mount("#app");
```
**使用computed**
```js
let id = 0;
const createUser = (name, gender) => {
  id += 1;
  return { id, name, gender };
};
new Vue({
  data() {
    return {
      users: [
        createUser("方方", "男"),
        createUser("圆圆", "女"),
        createUser("小新", "男"),
        createUser("小葵", "女")
      ],
      //displayUsers: []
      gender: ""
    };
  },
  computed: {
    //使用computed思路
    displayUsers() {
      const hash = {
        male: "男",
        female: "女"
      };
      const { users, gender } = this; //析构赋值

      if (gender === "") {
        return users;
      } else if (typeof gender === "string") {
        return users.filter((u) => u.gender === hash[gender]);
      } else {
        throw new Error("gender 的值是意外的值");
      }
    }
  },
  methods: {
    setGender(string) {
      this.gender = string;
    }
  },
  // 如何给三个按钮加事件处理函数
  // 思路一：点击之后改users
  // 思路二：使用computed
  template: `
    <div>
      <div>
        <button @click="setGender('')">全部</button>
        <button @click="setGender('male')">男</button>
        <button @click="setGender('female')">女</button>
        </div>
      <ul>
        <li v-for="(u,index) in displayUsers" :key="index">
          {{u.name}} - {{u.gender}}
        </li>
      </ul>
    </div>
  `
}).$mount("#app");
```
computed让逻辑更清晰

**知识点**

**1.Vue循环 v-for**

**注意:`v-for`后面必须接key**

`v-for="u in users"`把`users`里面的每一项取名为`u`,
后面就可以直接使用u，代表我们遍历到的那个东西。
```js
<li v-for="(u,index) in users" :key="index">{{u.name}} - {{u.gender}}</li>
//<li v-for="u in users" :key="u.id">{{u.name}} - {{u.gender}}</li>
```

## 二.Watch 监听/侦听

> **用途：** 当数据变化时，执行一个函数


[例1](https://codesandbox.io/s/lucid-shamir-cpcw3) 

**watch默认不监听第1次的值。watch是“完美的实现历史功能”的一个方法。**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f3dc019de22427696dc6e57701eb5ff~tplv-k3u1fbpfcp-zoom-1.image)


#### 撤销功能
思路：就是把某项弹出来，再用from值覆盖。
```js
[ { "from": 0, "to": 1 }, { "from": 1, "to": 2 } ]
比如撤销第2项:就是把最后项pop，提取from值:const old = last.from,
再用from值覆盖掉当前n:this.n = old
```
虽然第2项被删掉了，但是又增加了一条`from 2 to 1`这是因为撤销操作也被watch了，如何解决？vuejs官方[github](https://github.com/vuejs/vue)
> **解决思路**:**watch就是监听它的变化的**。你要做的是:自己控制，用变量控制。例如` inUndoMode: false`是否处在撤销模式
> 
> 默认不在撤销模式，当点击**撤销**时才处于**撤销模式**。
> 
> watch判断下:如果当前不在撤销模式就push,如果在撤销模式就直接return
> 
> 设置处于撤销模式`this.inUndoMode = true`watch就不会记录n的变化了。

**注意:当前代码中watch是异步**,所以watch没有发现它在撤销模式。
```js
new Vue({
  data: {
    n: 0,
    history: [],
    inUndoMode: false //默认不在撤销模式
  },
  watch: {
    n(newValue, oldValue) {
      console.log(`在不在撤销模式：${this.inUndoMode ? '在' : '不在'}`)
      if (!this.inUndoMode) { //如果当前不在撤销模式就push
        this.history.push({ from: oldValue, to: newValue })
      }
    }
  },
  template: `
  <div>
    {{n}}
    <hr />
    <button @click="add1">+1</button>
    <button @click="add2">+2</button>
    <button @click="minus1">-1</button>
    <button @click="minus2">-2</button>
    <hr/>
    <button @click="undo">撤销</button>
    <hr/>

    {{history}}
  </div>
  `,
  methods: {
    add1() {
      this.n += 1;
    },
    add2() {
      this.n += 2;
    },
    minus1() {
      this.n -= 1;
    },
    minus2() {
      this.n -= 2;
    },
    undo() {
      const last = this.history.pop() //拿到最后项
      console.log(last)
      const old = last.from //取出from值
      this.inUndoMode = true
      this.n = old //watch n的函数会异步调用，不会立即得结果
      this.$nextTick(() => { //setTimeout
        this.inUndoMode = false
      }, 0)
    }
  }
}).$mount("#app");
```
异步结果要想先得到值，可以将下一步也改成异步状态,比如`setTimeout`
watch用的**异步接口**是Vue自己封装的叫`this.$nextTick`

**例2 模拟computed**
```js
new Vue({
  data: {
    user: {
      email: "fangfang@qq.com",
      nickname: "方方",
      phone: "13812312312"
    },
    displayName: ""
  },
  watch: {
    "user.email": {
      handler: "changed",
      immediate: true //第一次渲染时也触发watch
    },
    "user.nickname": {
      handler: "changed",
      immediate: true //第一次渲染时也触发watch
    },
    "user.phone": {
      handler: "changed",
      immediate: true //第一次渲染时也触发watch
    }
  },
  template: `
    <div>
       {{displayName}}
       <button @click="user.nickname=undefined">remove nickname</button>
    </div>
  `,
  methods: {
    changed() {
      console.log(arguments);
      const user = this.user;
      this.displayName = user.nickname || user.email || user.phone;
    }
  }
}).$mount("#app");
```
**watch默认不监听第1次的值。**

所以页面中的{{displayName}}不显示，当点击按钮后才会重新触发计算displayName

**如何展示第1次计算出来的displayName？**

先把它变成**对象**，然后添加`handler(){...},immediate:true`

**优先使用computed，computed不满足就使用watch.**

它们都是在数据变化时执行一个函数，只不过computed着重于依赖之间的变化以及缓存，watch着重于变化时去执行什么东西而不是得出一个结果。

**对watch来说或者对Vue的所有监听器来说，什么叫做变化？**

1.当改变 **简单的数据类型** 的值时，值变了就是变了。

2.如果改变 **对象里面的简单类型**，就只做值的对比。值变了才是变了，值没变就是没变。

比如`obj: { a: "a" }`改成`a: "ahi"`，obj没变，因为地址没变，**只是改了里面的值**。obj.a变了。

3.如果你把一个 **对象的地址** 变了，Vue才会认为这个对象变了。
里面的依然是比较值。

比如`obj: { a: "a" }`改成`obj: { a: "a" }`，obj变了，因为地址变了，**obj赋给了一个新的对象。**

但是**obj.a**没变，因为值没变。

简单类型看值，复杂类型(对象)看地址，其实就是===的规则。

4.**增加需求:** 只要对象里任何一个属性变了就都算obj变了。

Vue提供了一个选项`deep:true` **方法**:添加`handler(){}、deep:true`


#### watch语法
[推荐文档](https://cn.vuejs.org/v2/api/#watch)

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0f0b01f7a3c844aebdb62cd8d9dc392a~tplv-k3u1fbpfcp-zoom-1.image)

watch接收一个对象，对象的key必须是string，value可以是`string`或`函数`或`对象`或`数组`。

**语法一**
```js
watch: { //不要使用箭头函数来定义watch函数
  a: function (val, oldVal) { ... }, //这两个参数是Vue传给你的
  b(){ ... },
  c:[f1,f2],//当c变化时依次执行f1、f2
  d:'methodName', //当d变化时会去methods里面找对应名字的函数,方法名
  e:{handle:fn,deep:true,immediate:true}, //immediate立即调用
   //handler: function (val, oldVal) { /* ... */ },
  'object.a':function(){ ... } //在object.a变化时执行
  //这里的function最好不要省，主要看webpack版本
```

**不要使用箭头函数来定义watch函数**

![
](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7721b524779e43cf9c8279eb557bc1fe~tplv-k3u1fbpfcp-zoom-1.image)

箭头函数是没有this的，这个this是它外面函数的this。

new Vue()不是函数，是**函数调用**。所以箭头函数的外层函数是全局作用域，全局作用域的this是全局对象`window或global`

**语法二**

`vm.$watch('xxx',fn,{deep:...,immediate:..})`加$防止冲突

其中`'xxx'`可以改为一个返回字符串的函数

也可以移到里面
```js
//是否与DOM有关

//如果与DOM无关就放到created，当实例被创建到内存时监听n,一旦n变了就执行函数...
created(){
  this.$watch('n',fn,{deep:...,immediate:..})
}
```
**computed VS watch**

watch当数据变化时，执行一个函数

computed如果一个数据变了，我就计算出一个新的属性。

**面试题(常考)**

**computed 与 watch有什么区别？**

computed是计算属性的意思，watch是监听的意思。

**computed:**

1.computed是用来计算出一个值的，这个值在调用的时候不需要加括号，可以当属性样用。

2.根据依赖自动缓存，如果依赖不变computed值就不会重新计算。

**watch:**

1.watch是用来监听的，它有2个选项immediate和deep。

2.immediate表示是否在第一次渲染的时候要执行这个函数。

  deep如果我们监听一个对象那么是否要看里面的属性的变化。
  
定义:如果某个属性变化了我就去执行这个函数。

这就是computed 和 watch的区别。






