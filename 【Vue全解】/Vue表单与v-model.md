# Vue表单与v-model
> [Ant Design Vue 文档](https://1x.antdv.com/docs/vue/getting-started-cn/#%E5%BC%95%E5%85%A5-ant-design-vue)

**表单基本用法**

input/textarea/checkbox/radio/select/from

**修饰符**

.lazy/.number/.trim

**运行环境**

新建文件vue-demo,用**Vue的脚手架**给它初始化下。
```js
vue create .
选择在当前目录创建,使用默认的vue2
yarn serve
```
### 表单基本用法
**1.input 文本**

**2.textarea 多行文本**
```js
<template>
  <div id="app">
    <input v-model="message" placeholder="edit me">
    <textarea v-model="message" placeholder="add multiple lines"></textarea>

<p>Message is: {{ message }}</p>
    <p>
      <button @click="message='fuck'">change message</button>
    </p>
  </div> 
</template>

<script>
export default {
  name: 'App',
  data(){ //需要声明message，每个input是需要对应有个变量的。
  return { 
    message:'hi' 
  }
  },
  components: {}
}
</script>
```
改message，UI会自动变化。用户改input，message会自动变化。

**双向绑定：改内存**页面会自动变化，**改页面**内存也会自动变化，这就叫双向绑定。

**3.复选框 checkbox**
```js
单个复选框，绑定到布尔值
<template>
  <div id="app">
    <label>
      <input type="checkbox" v-model="x">
      <span>x:{{x}}</span>
    </label>
  </div> 
</template>
<script>
export default {
  name: 'App',
  data(){ 
    return { x:true }
  }
}
</script>
```
需要声明x，每个checkbox是需要对应有个变量的。
```js
多个复选框，绑定到同一个数组：
<template>
  <div id="app">
    爱好:{{x}}
    <label>
      <input type="checkbox" v-model="x" :value="1">
      <span>抽烟</span>
    </label>
   <label>
      <input type="checkbox" v-model="x" :value="2">
      <span>喝酒</span>
    </label>
    <label>
      <input type="checkbox" v-model="x" :value="3">
      <span>烫头</span>
    </label>
  </div> 
</template>
<script>
export default {
  name: 'App',
  data(){ 
    return {  x:[] }
  }
}
</script>
```

**4.单选按钮 radio**

`name=""`当name值相同时就表示是一组的。
```js
<template>
  <div id="app">
     你想要:{{x}}
    <label>
      <input name="want" type="radio" v-model="x" :value="1">
      <span>抽烟</span>
    </label>
   <label>
      <input name="want" type="radio" v-model="x" :value="2">
      <span>喝酒</span>
    </label>
    <label>
      <input name="want" type="radio" v-model="x" :value="3">
      <span>烫头</span>
    </label>
  </div> 
</template>
<script>
export default {
  name: 'App',
  data(){ 
    return { x:'' }
  }
}
</script>
```
**5.选择框 select**
```js
单选时：
<template>
  <div id="app">
     你想要:{{x}}
     <hr>
    <select multiple v-model="x">
    <option  value="">-</option>
    <option v-for="item in array" :value="item.value" :key="item.value">{{item.text}}</option>
  </select>
  </div> 
</template>
<script>
export default {
  name: 'App',
  data(){ 
    return { 
      array:[
        {text:"抽烟",value:1},
        {text:"喝酒",value:2},
        {text:"烫头",value:3}
      ],
      x:'' //x:[]
    }
  }
}
</script>

多选时：
1.添加`multiple`
2.x:'' 改成 x:[]
补充:尽量少用这种形式的multiple，一般会单独做个有复选框的select
```
**6.from**
**用from提交元素**
```js
<template>
  <div id="app">
    登录
    <form @submit.prevent="onSubmit">
      <label>
        <span> 用户名 </span>
        <input type="text" v-model="user.username" />
      </label>
      <label>
        <span> 密码 </span>
        <input type="password" v-model="user.password" />
      </label>
      <button type="submit">登录</button>
    </form>
  </div> 
</template>
<script>
export default {
  name: 'App',
  data(){ 
    return { 
     user:{
       username:'',
       password:''
     }
    }
  },
  methods:{
    onSubmit(){
      console.log(this.user)
    }
  }
}
</script>
```
### 修饰符
`.lazy`延迟触发,比如，input时等用户输完了再触发

`.number`当你只想要数字时

注意:需要设置初始值为0，且只能截取首段数字.

`.trim`(**常用**)自动把前面和后面的空格去掉
```js
lazy使用场景:适用于富文本编辑器
<input type="text" v-model.lazy="user.username" />
number使用场景:当你只想要数字时
<input type="text" v-model.number="user.username" />
data(){ 
    return { 
      user:{ username:0 }, //注意:需要将初始值为0，且只能截取首段数字
    }
trim使用场景:
```
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7eb9e8dc29bf459ea0b4424b1351642c~tplv-k3u1fbpfcp-zoom-1.image)

**知识点**

v-model默认会监听input事件

input事件:键盘、鼠标、任何输入设备的输入

change事件:只在input失去焦点时触发

### v-model
v-model的等价写法
```js
<input type="text" v-model="user.username" />
//等价于
<input type="text" :value="user.username" @input="user.username=$event.target.value"/>
//绑定value等于一个东西，@input=让这个东西等于$event.target.value
```
**为了了解v-model: 自己封装个input**

新建组件MyInput.vue，代码如下：
```js
MyInput.vue
<template>
    <div class="red wrapper">
        <input :value="value" @input="onInput"/>
    </div>
</template>
<script>
export default {
    name:'MyInput',
    props:{
        value:{
            type:String 
        }
    },
    methods:{
        onInput(e){
            const value=e.target.value
            this.$emit("input",value)
        }
    }
}
</script>
<style scoped>
.red{background: red;}
.wrapper{display: inline-block;}
</style>

App.vue 使用MyInput
<template>
  <div id="app">
    {{user}}
    <form @submit.prevent="onSubmit">
      <label>
        <span> 用户名 </span>
        <MyInput :value="user.username" @input="user.username = $event"/>
      </label>
      ...
    </form>
  </div> 
</template>
<script>
import MyInput from './MyInput.vue'
export default {
  components: {MyInput},
  name: 'App',
  data(){ 
    return { 
      user:{
        username:'',
        password:''
     }
    }
  },
  methods:{
    onSubmit(){
      console.log(this.user)
    }
  }
}
</script>
```

**面试题**

**请问，Vue的双向绑定是如何实现的？**

**1.解释v-model的作用**

v-model可以实现绑定一个变量:

在变量变化的时候UI也会变化,用户改变UI时数据也会变化，这就是双向绑定。

**2.深入**

v-model是v-bind:value 和 v-on:input的语法糖

**v-on:input="???"** v-model对应的input到底是什么呢？(2种情况)

1' 如果是原生的input，input就是`xxx=$event.target.value`

2' 如果是自定义的组件，input就是`xxx=$event`

### 使用Ant Design of Vue
> **Ant Design** 是一个UI设计语言，是一套提炼和应用于企业级后台产品的交互语言和视觉体系。
> 
> **UI** 用户界面User interface:用户研究、界面设计、交互设计(用户与界面间的交互关系)

[Ant Design Vue文档](https://1x.antdv.com/docs/vue/getting-started-cn/#%E5%BC%95%E5%85%A5-ant-design-vue)

**步骤**

**一.安装脚手架工具**
```js
yarn add ant-design-vue@1.5.1
```
注意:ant-design-vue版本与Vue版本要保持一致。

**如果你安装的是Vue3,请参照[Ant Design Vue 3](https://www.antdv.com/docs/vue/getting-started-cn)**

**二.引入Antd**

**完整引入**:把Antd所有组件都导入代码

```js
main.js
import Antd from 'ant-design-vue';
import 'ant-design-vue/dist/antd.css';
Vue.use(Antd);
```
**快速修复不识别单词**
![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b2f675dcfd5743aa94d65c91267b54e3~tplv-k3u1fbpfcp-zoom-1.image)

[三.展示UI](https://1x.antdv.com/components/form-cn/)

**根据要展示的内容，查看对应的文档。**

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7703054abd1249aaa83f82549cbb92e5~tplv-k3u1fbpfcp-zoom-1.image)

比如，如果要展示form就要看form文档，选好要展示的样式，然后选择粘贴代码。
```js
App.vue
<template>
  <div id="app">
    <a-form id="components-form-demo-normal-login" :form="form" class="login-form" @submit.prevent="onSubmit" >
    <a-form-item>
      <a-input v-decorator="['username',{ rules: [{ required: true, message: '你丫没写用户名' }] }]"
        placeholder="用户名">
        <a-icon slot="prefix" type="user" style="color: rgba(0,0,0,.25)" />
      </a-input>
    </a-form-item>
    <a-form-item>
      <a-input v-decorator="['password',{ rules: [ //校验规则
            { required: true, message: '你丫没填密码' },
            { min: 8, message: '密码最少8个字符' },
            { pattern: /[a-zA-Z]/, message: '且必须包含至少一个字母' },] },]"
        type="password" placeholder="密码" >
        <a-icon slot="prefix" type="lock" style="color: rgba(0,0,0,.25)" />
      </a-input>
    </a-form-item>
    <a-button type="primary" html-type="submit" class="login-form-button">
      登录
    </a-button>
  </a-form>
  </div>
</template>
<script>
export default {
  name: 'App',
  beforeCreate() { 
    this.form = this.$form.createForm(this, { name: 'normal_login' });
  },
methods:{
  onSubmit(e){
      e.preventDefault();
      this.form.validateFields((error, values) => {
        if (!error) {
          console.log('Received values of form: ', values);
        }else{
          console.log("error",error)
        }
      });
  } }
}
</script>
<style></style>
```