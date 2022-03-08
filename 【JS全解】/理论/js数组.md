# JS 数组
> object可以分很多类，其中最著名的2个类是数组和函数。
> 
> 数组对象是一种特殊的对象，所以对象能做的事情，数组都能做。
> 
> 2021年12月24日js其实没有真正的数组，只是用对象模拟数组。

其它

1.第1层__proto__是共有属性，第2层是构造该Object的prorotype

2.let divList=document.querySelectorAll('div') 获取所有div

3.一个对象提供的函数就叫做API

4.${} 意思是可以插入一个变量

**5.for循环里的i是字符串下标的值**

**6.数组中reduce和splice功能特别强大,最重要**

7.n % 2 === 1? sum += n : false

8.x%2只有2个值0或1,0偶数1奇数

9.[js中各种类型的toString方法](https://blog.csdn.net/caosiyuan1991/article/details/115313960)

js中定义了7种数据类型，在这7种数据类型中，除了undefined和null类型之外，都有toString方法。


### JS的数组不是典型数组

**典型的数组**

元素的数据类型相同

使用连续的内存存储

通过数字下标获取元素

![](https://pic1.zhimg.com/v2-da2f31966ea6c4f0d24ede75e8879e9c_b.jpg)

**但JS的数组不这样**

元素的数据类型可以不同

内存不一定是连续的(对象是随机存储的)

**不能通过数字下标,而是通过字符串下标**

这意味着数组可以有任何key

比如

let arr=[1,2,3]

arr['xxx']=1 //'xxx'是字符串下标哦

![](https://pic4.zhimg.com/v2-83b0a880654cbccc632b6f1b4d41ef2f_b.jpg)

![](https://pic4.zhimg.com/v2-aeca05111fab8f527e6d708fea7421d3_b.jpg)

js的所有数组并不是数组，假的，它实际上还是个对象。

注意：下标是字符串，没有数字

# 创建一个数组

**方式一：**

let arr=[1,2,3]

let arr=new Array(1,2,3) //参数只有1个就是长度，多个就是元素

let arr=new Array[3] //长度为3

**方式二：通过字符串创建数组**

转化

let arr='1,2,3'.split(',')

let arr='123'.split('')

Array.from('123')

要满足条件才能够转化：有"0123下标"和"length属性"

js会自动将数字.toString()成字符串

**变态**

1.如果下标与length不一致呢？

```
Array.from({0:'a',1:'b',2:'c',3:'d',length:2})
输出结果：['a', 'b']
```

2.伪数组

伪数组的原型链中并没有数组的原型

第1层__proto__是共有属性，第2层是构造该Object的prorotype。

因为它的原型直接指到了Object原型，少了一层原型链中的共有属性。没有数组共用属性的「数组」，那么它就是伪数组。

例子

let divList=document.querySelectorAll('div')

divList.push(4)//失败，不能push,伪数组不能使用共有属性

![](https://pic2.zhimg.com/v2-48b3ac576ae7f28a78c337baafc7dfad_b.jpg)

**怎么解决？**

Array.from可以构造数组

```
let divList=document.querySelectorAll('div')
let divArray=Array.from(divList)
console.dir(divArray)
```

伪数组有什么用？

没啥用，遇到伪数组第一件事把它转化成数组就行了。

**方式三：合并 & 截取**

不改变原来的数据

合并得到一个"新的"数组 //变长

arr1.concat(arr2)

截取一个数组的一部分 //变短

arr1.slice(1) //从第2个元素开始切

arr1.slice(0) //全部截取，**经常用来复制一个数组！**

**注意，JS只提供浅拷贝!**
```
let arr=[1,2,3,4,5,6,7,8,9]
arr.slice(3)
输出结果:[4, 5, 6, 7, 8, 9]
arr
输出结果:[1, 2, 3, 4, 5, 6, 7, 8, 9]

let arr2=[123456789]
arr.slice(0)
输出结果：[1, 2, 3, 4, 5, 6, 7, 8, 9]

let arr3=arr.slice(0) //复制一个数组,js不提供单独的复制方法
输出结果：[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

# 数组元素的增删改查API 

### 一.删元素

**1.删除第1个的元素**

arr.shift() //arr被修改，并返回被删元素

![](https://pic2.zhimg.com/v2-754970027551245c3a4d0fe894d8ef5d_b.jpg)

**2.删除最后1个的元素**

arr.pop() //arr被修改，并返回被删元素

![](https://pic2.zhimg.com/v2-8c2adaac5c30b82bb7c7766b94389291_b.jpg)

**3.删除中间的元素**

splice强大的数组API

arr.splice(index,1) //删除index的一个元素

arr.splice(index,1,'x') //并在删除位置添加'x'

arr.splice(index,1,'x','y') //并在删除位置添加'x',y'

例子

```
let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9]
arr.splice(1, 1, 'x', 'y')
console.log(arr)
输出结果: Array(10)
        0: 1
        1: "x"
        2: "y"
        3: 3
        4: 4
        5: 5
        6: 6
        7: 7
        8: 8
        9: 9
        length: 10
```

下面2种也能删,但不推荐!

(1)跟对象一样
```
let arr=['a','b','c']
delete arr['0']
arr 
输出结果：[empty,'b','c'] //数组长度并没变length:3，删掉之后位置还会留着
```
这种没有下标只有长度的叫**稀疏数组**,稀疏数组没任何好处只有bug，奇葩记住就好。

(2)直接改length
```
let arr=[1,2,3,4,5]
arr.length=1
arr
输出结果：[1]   //居然可以，JS真神奇！
          0: 1
     length: 1
```
**注意：不要随便改length！**

# 查看所有元素

遍历数组

用对象的方式查，可能得到的不是你满意的答案。**所以一般用for循环和forEach这两种方式**

**一.对象的方式(不推荐)**

1.查看所有属性名

访问对象

1' Object.keys(arr)

2' for(let key in arr){console.log(`${key}:${arr[key]}`)}

for in是访问对象的,不要用Object.key它会获取到你不想要的东西。
```
let arr=[1,2,3,4,5];
arr.x='xxx'
1' Object.keys(arr)  //遍历**属性名**
   输出结果：(6) ['0', '1', '2', '3', '4', 'x']

   Object.values(arr)  //遍历**属性值**
   输出结果：(6) [1, 2, 3, 4, 5, 'xxx']

2' for(let y in arr){console.log(y)}
   输出结果：0
           1
           2
           3
           4
           x
```
上面2种方法只适合对象并不适合数组，因为我们并不希望访问数组时看到x。

**二.一般用for循环和forEach**

**2.查看数字(字符串)属性名和值**

访问数组

**1' for循环**

for(let i = 0; i < arr.length; i++){

  console.log(`${i}:${arr[i]}`)

 }

 要自己让i从0增长到length-1

 例子
```
for(let i = 0; i < arr.length; i++){
  //console.log(i)
   console.log(`${i}:${arr[i]}`)
}
输出结果:0:1
        1:2
        2:3
        3:4
        4:5
```

**2' 也可以用forEach/map等原型上的函数**

forEach回调访问数组。

forEach原理就是遍历这个数组，然后每一次都调研这个函数。

第一个参数item是当前的元素，第二个参数index是下标。名字无所谓，顺序对就行。

arr.forEach(function(item,index){

  console.log(`${index}:${item}`)

})

例子

```
arr.forEach(function(xxx){
  console.log(xxx)
})
输出结果:1
        2
        3
        4
        5
        
arr.forEach(function(xxx,yyy){ //回调
  console.log(`${yyy}:${xxx}`)
})
输出结果:0:1
        1:2
        2:3
        3:4
        4:5
```
帮助理解

等价于下面的写法
```
第1个参数
function forEach(array,fn){ //forEach接收数组(第1个参数item)和函数
  for(let i = 0; i < array.length; i++){
    fn(array[i]) //获取array里的每一项，把每一项作为参数传给fn。
  }
}

//forEach(['a','b','c'],function(){console.log('执行了一次')})
forEach(['a','b','c'],function(x){console.log(x)})
输出结果：a
        b
        c
```
第2个参数  
```      
function forEach(array,fn){ 
  for(let i = 0; i < array.length; i++){
    fn(array[i],i) 
  }
}

forEach(['a','b','c'],function(x,y){console.log(x,y)})
输出结果：a 0
        b 1
        c 2      
```			        
第3个参数,很少用到       
```
function forEach(array,fn){ 
  for(let i = 0; i < array.length; i++){
    fn(array[i],i,array) 
  }
}

forEach(['a','b','c'],function(x,y,z){console.log(x,y,z)})
输出结果：a 0  (3) ['a', 'b', 'c']
        b 1  (3) ['a', 'b', 'c']
        c 2  (3) ['a', 'b', 'c']   
```
forEach用for访问每一项。

forEach原理就是遍历这个数组，然后每一次都调研这个函数。

**面试题**

**第1种for循环和第2种forEach的区别是什么？**

1.for循环是关键字，里面有break和continue，可以break和continue。功能更强大点。

  但forEach只是普通函数不支持，只要开始就只能走到结束不能中断。

2.for循环是关键字不是函数，所以这里没有函数作用域只有块级作用域。

  foreach是函数所以这里是函数作用域。

  一个是块，一个是函数。

# 查看单个属性

**1.跟对象一样**

let arr=[111,222,333]

arr[0] //都会变成字符串，js没有数字下标一说

要注意越界的问题。不能让下标是负数，也不能让下标等于length！

**2.索引越界(重要)**

给的下标索引不存在就是越界

arr[arr.length]===undefined

arr[-1]===undefined

例子
```
let arr=[1,2,3,4,5]
for(let i = 0; i <= arr.length; i++){
   console.log(arr[i].toString)
}

报错：Uncaught TypeError: Cannot read properties of undefined (reading 'toString')
//不能读取undefined的toString!
```
length=5,i是字符串下标的值。arr[5]不存在，所以索引越界。

undefined不是个对象，所以没有toString。

7种数据类型中，除了undefined和null类型之外，都有toString方法。

```
数组越界
Uncaught TypeError: Cannot read properties of undefined (reading 'toString')
```

### 3.查找某个元素是否在数组里

(1)arr.indexOf(item) //存在返回索引，否则返回-1

例子
```
arr.indexOf(5)
返回结果：4
arr.indexOf(6)
返回结果：-1
```
(2)也可以使用for循环，for循环可以解决数组的任何问题。不推荐
```
let arr=[22,55,3,77,45,108]
for(let i=0;i<arr.length;i++){
  if(arr[i]===3){
    console.log('yes')
  }
}
```
(3)使用条件查找元素

arr.find(item => item%2===0) //找第1个偶数

等同于

arr.find(function(x){

  return x%2 === 0

})

(4)使用条件查找元素的索引

.find永远只会返回找到的第1个结果，.findIndex会返回找到的第1个结果的**下标**

```
arr.findIndex(item => item%2===0)

返回结果：1  //第1个结果的下标
```

# 增加数组中的元素

不要用arr[100]=100添加,容易变成稀疏数组。

如下图所示:

![](https://pic2.zhimg.com/v2-f3961545fda1418f4b4de7d905bb3c2d_b.jpg)

**1.在尾部添加元素**

arr.push(newItem) //修改arr,返回新长度

arr.push(item1,item2,item3) //修改arr,返回新长度

返回新长度
```
arr.push(102,103,104)
```
**2.在头部添加元素**

arr.unshift(newItem)//修改arr,返回新长度

arr.unshift(item1,item2,item3)//修改arr,返回新长度

返回新长度

**3.在中间添加元素**

**0表示不删，除0外其它数表示删除**

arr.splice(index,0,'x')    //在index处插入'x',index是字符串数组下标

arr.splice(index,0,'x','y')

例子
```
arr.splice(3,0,'x','y')
arr
输出结果：[-1, -1, 0, 'x', 'y', 1, 2, 3,4，5]

arr.splice(3,2)
输出结果：[-1, -1, 0, 1, 2, 3,4，5]
```
还可以用做修改
```
arr.splice(0,1,-2) //修改，等同于arr[0]=-2
arr[-2, -1, 0, 1, 2, 3,4，5]
```

# 修改数组中的元素

**1.反转顺序**

arr.reverse() //修改原数组

**面试题**

**如何反转一个"字符串"**

字符串没有.reverse()
```
var s='abcde'
s.split('').reverse().join('')
```

s.split('')

输出结果：['a', 'b', 'c', 'd', 'e']

s.split('').reverse()

输出结果：['e', 'd', 'c', 'b', 'a']

s.split('').reverse().join('')

输出结果：'edcba'

**2.自定义顺序**

arr.sort((a,b)=>a-b)

需要详细讲解

arr.sort()  //默认从小到大排序

从大到小排序怎么排？

sort()接收一个函数，这个函数有两个参数

arr.sort(function(a,b){})

function有3个返回值1、0、-1，1表示前面的大，0表示一样大，-1表示后面的大。

例子
```
var arr=[2,6,3,1,5]
arr.sort(function(a,b){
  if(a>b){
    return 1
  }else if(a===b){
    return 0
  }else{
    return -1
  }
})
输出结果： [1, 2, 3, 5, 6]
```
 把接收的值1和-1对调，就是从大到小排了。
```
var arr=[2,6,3,1,5]
arr.sort(function(a,b){
  if(a>b){
    return -1
  }else if(a===b){
    return 0
  }else{
    return 1
  }
})
输出结果：[6, 5, 3, 2, 1]
```
js默认小的在前面，大的在后面，但是到底谁小它不知道。

例子
```
let arr=[
{name:'小白',score:99},{name:'小丽',score:69},{name:'小李',score:88},
]
arr.sort(function(a,b){
 if(a.score>b.score){return 1
  }else if(a.score===b.score){return 0
  }else{return -1}
})
输出结果：0: {name: '小丽', score: 69}
        1: {name: '小李', score: 88}
        2: {name: '小白', score: 99}
        length: 3
```
可以简写成
```
arr.sort((a,b)=> a.score-b.score) //从小到大
arr.sort((a,b)=> b.score-a.score) //从大到小
```

解析:a.score-b.score 若结果>0，就返回正数，否则返回负数。

# 数组变换

都不会改变原数组

reduce功能非常强大,可以代替map和filter，尽量都使用reduce。

1.map     //n变n

2.filter  //n变少

**3.reduce  //n变1**

reduce是把所有东西合成一个东西。

**但是合成的东西有时候不一定是一个东西，还可能是多个东西。**

**reduce初始值为0，每次返回的是修改后的新值。**

**补充：reduce第2个参数可以是0、[]、{}。用来初始化值的**

图形助解

![](https://pic3.zhimg.com/v2-d0dfc1c3d6f02e0b13a68c26c528c8ce_b.jpg)

**例1 map n变n**

```
把数组每项变成平方
let arr=[1,2,3,4,5]
for(let i=0; i < arr.length;i++){
  arr[i]= arr[i] * arr[i]
}
//[1, 4, 9, 16, 25]

等同于
let arr=[1,2,3,4,5]
arr.map(item => item * item)
//[1, 4, 9, 16, 25]
```
例2 filter n变少 过滤操作 
```
求偶
arr.filter(item => item%2 ===0 ? true :false)
//简写 arr.filter(item => item%2 ===0)
输出结果：[2, 4]
```
例3 reduce n变1
```
求和
let arr=[1,2,3,4,5]
let sum=0
for(let i=0; i < arr.length;i++){
 sum += arr[i]
}
//15

等同于

arr.reduce((sum,item)=>{return sum+item},0) //0:sum初始值为0
```
**reduce初始值为0，每次返回的是修改后的新值。**

reduce是把所有东西合成一个东西。

**但是注意合成的东西有时候不一定是一个东西，还可能是多个东西。**

例4

reduce替代map，把数组每项变成平方

![](https://pic3.zhimg.com/v2-56fa36d1c9165751afec7b5cffaa6a5e_b.jpg)
``` 
let arr=[1,2,3,4,5]

arr.reduce((result,item)=>{return result.concat(item*item)},[])

//item指当前数据,将item和result 连成一个新的数组

输出结果：[1, 4, 9, 16, 25]
```
例5

reduce替代filter，求偶

```
let arr=[1,2,3,4,5]
arr.reduce((result,item)=>{
  if(item%2===1){
    return result
  }else{
    return result.concat(item)//concat可以接收一个元素/数组
  }
},[])
输出结果：[2, 4]

可以简写
arr.reduce((result,item)=>{
  item%2===1 ?
    result
  :
    result.concat(item)
  }
},[])

还可以简写
let arr=[1,2,3,4,5]
arr.reduce((result,item)=>
  result.concat(item % 2 === 1 ? [] : item) 
  //如果是想要的值就把它放进去，否则啥也不放(把[]空的放进去)
,[])
输出结果：[2, 4]
```

**面试题**

**数据变换**

let arr=[

{名称:'动物',id:1,parent:null},

{名称:'狗',id:2,parent:1},

{名称:'猫',id:3,parent:1}

]

将数组变成对象

{

  id:1,名称:'动物',children:[

  {id:2,名称:'狗',children:null},

  {id:3,名称:'猫',children:null}, 

  ]
}


```
arr.reduce((result,item)=>{
  if(item.parent === null){
    result.id=item.id
    result['名称']=item['名称']
  }else{
     result.children.push(item)
     delete item.parent
     item.children=null
   } 
   return result
},{id:null,children:[]})  //初始化id和children
```

**课后习题**

1.用 arr.map 把0变成'周日'，把1变成'周一'，以此类推
```
let arr = [0,1,2,2,3,3,3,4,4,4,4,6]
let arr2 = arr.map((i)=>{
  const week = {0:'周日',1:'周一',2:'周二',3:'周三',4:'周四',5:'周五',6:'周六'}
  return week[i]
})

console.log(arr2)
['周日', '周一', '周二', '周二', '周三', '周三', '周三', '周四', '周四', '周四', '周四', '周六']
```

2.找出所有大于60分的成绩
```
let scores = [95,91,59,55,42,82,72,85,67,66,55,91]
let scores2 = scores.filter(x => x > 60 )

console.log(scores2)
[95,91,82,72,85,67,66, 91]
```

3.算出所有奇数之和
```
let scores = [95,91,59,55,42,82,72,85,67,66,55,91]
let sum = scores.reduce((sum, n)=>{
 n % 2 === 1? sum += n : false
return sum
},0)

console.log(sum) 
奇数之和：598 
```