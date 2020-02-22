

## ES6相关 

### es6的新增的一些东西

### 一. let const的增加

#### 1.let

ES6中新增了用于声明变量的关键字

##### a.具有块级作用域

```javascript
 if (true) { 
     let a = 10;
 }
console.log(a) // a is not defined
```

**注意：**使用let关键字声明的变量才具有块级作用域，使用var声明的变量不具备块级作用域特性。

##### b.不存在变量提升

```javascript
console.log(a); // a is not defined 
let a = 20;
```

##### c.暂时性死区

利用let声明的变量会绑定在这个块级作用域，不会受外界的影响

```javascript
  var tmp = 123;
  if (true) {
    let tmp;
    tmp = 'abc';
    console.log(tmp); //abc

  }
  console.log(tmp); //123
```

##### 经典面试题

```javascript
 var arr = [];
 for (var i = 0; i < 2; i++) {
     arr[i] = function () {
         console.log(i); 
     }
 }
 arr[0]();
 arr[1]();

```

![1582384470998](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1582384470998.png)

**经典面试题图解：**此题的关键点在于变量i是全局的，函数执行时输出的都是全局作用域下的i值。

```javascript
 let arr = [];
 for (let i = 0; i < 2; i++) {
     arr[i] = function () {
         console.log(i); 
     }
 }
 arr[0]();
 arr[1]();

```

![1582384500724](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1582384500724.png)

**经典面试题图解：**此题的关键点在于每次循环都会产生一个块级作用域，每个块级作用域中的变量都是不同的，函数执行时输出的是自己上一级（循环产生的块级作用域）作用域下的i值.

#### 2.const

声明常量，常量就是值（内存地址）不能变化的量

##### a.具有块级作用域

```javascript
 if (true) { 
     const a = 10;
 }
console.log(a) // a is not defined
```

##### a.声明常量时必须赋值

```javascript
const PI; // Missing initializer in const declaration
```

##### c.赋值后，值就不能修改

```javascript
const PI = 3.14;
PI = 100; // Assignment to constant variable.

const ary = [100, 200];
ary[0] = 'a';
ary[1] = 'b';
console.log(ary); // ['a', 'b']; 
ary = ['a', 'b']; // Assignment to constant variable.
```

#### 小结

- const声明的变量是一个常量

- 既然是常量不能重新进行赋值，如果是基本数据类型，不能更改值，如果是复杂数据类型，不能更改地址值(里面的值可以修改,但整个对象或数组不能重新赋值).

- 声明 const时候必须要给定值.


#### let、const、var 的区别

- 使用 var 声明的变量，其作用域为该语句所在的函数内，且存在变量提升现象

- 使用 let 声明的变量，其作用域为该语句所在的代码块内，不存在变量提升

- 使用 const 声明的是常量，在后面出现的代码中不能再修改该常量的值

  ![1582384980805](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1582384980805.png)
### 二.字符串的扩展

####  1.模板字符串

模板字符串可以解析变量,换行,调用函数

```js
var str = `abc
def
gh`;
console.log(str);
let name = "小明";
function a() {
    return "ming";
}
console.log(`我的名字叫做${name}，年龄${17+2}岁，性别${'男'}，游戏ID：${a()}`);
```

#### 2.字符串startsWith() 和 endsWith()方法

是否在原字符的头部/尾部,返回布尔值

```js
let str = 'Hello world!';
str.startsWith('Hello') // true 
str.endsWith('!')       // true
```

#### 3.字符串repeat()方法

repeat()改变原字符串重复n次，返回一个新字符串

```js
'x'.repeat(3)      // "xxx" 
'hello'.repeat(2)  // "hellohello"
```

### 三. 展开运算符(...运算符)

1. 将字符串转成数组
```js
var str="abcd";
console.log([...str]) // ["a", "b", "c", "d"]
```
2. 两个数组的合并
```js
var a1=[1,2,3];
var a2=[4,5,6];
console.log([...a1,...a2]); //[1, 2, 3, 4, 5, 6]
```
3.对象的合并

```js
let obj = { a: 1, b: 2 };
let obj2 = { ...obj, c: 3 }; // { a:1, b:2, c:3 }
let obj3 = { ...obj, a: 3 }; // { a:3, b:2 }//如果重复的会覆盖
```
4. 将集合转成数组
```js
var sets=new Set([1,2,3,4,5]) //解释Set对象是值的集合,它是js的内置对象
console.log([...sets]) // [1, 2, 3, 4, 5]
```
5. 在函数的参数中使用，用来代替arguments参数
```js
function func(...args){
    console.log(args);//[1, 2, 3, 4]
    console.log(arguments)//Arguments[1, 2, 3, 4]
}
func(1, 2, 3, 4);
 
function f(x, ...y) {
    console.log(x);
    console.log(y);
}
f('a', 'b', 'c');     //a 和 ["b","c"]
f('a')                //a 和 []
f()                   //undefined 
```

### 四. 函数的扩展

#### 箭头函数(重要)

1. 可以省略function关键字 
2. 可以不写函数名
3. 如果只有一个表达式可以不需要写{} 和 返回值,直接加一个()即可

```js

 //普通写法
var getFullName = (firstName, lastName) => {
    return firstName + lastName;
}
//es6写法
var getFullName = (firstName, lastName) => (firstName + lastName);

```

4.箭头函数中不绑定this，可以简单理解成，定义箭头函数中的作用域的this指向谁，它就指向谁.

```js
const obj = { name: '张三'} 
 function fn () { 
     console.log(this);//this 指向 是obj对象
     return () => { 
         console.log(this);//this 指向 的是箭头函数定义的位置，那么这个箭头函数定义在fn里面，而这个fn指向是的obj对象，所以这个this也指向是obj对象
     } 
 } 
 const resFn = fn.call(obj); 
 resFn();
```

箭头函数面试题

```javascript
  var age = 100;
  var obj = {
    age: 20,
    say: () => {
      console.log(this) //window,对象是没有作用域的,所以this指向最近的函数上下文对象,最近的是window
    },
    say2() {
      console.log(this); //obj
    }
  }
  obj.say();
  obj.say2();
```

#### 函数参数给默认值

```js
function A(a,b=1){
    console.log(a+b);
}
A(1);    //2
A(2,3);  //5
```

#### 

#### 函数的三个点参数

##### 函数剩余参数语法
允许我们将一个不定数量的参数表示为一个数组，不定参数定义方式，这种方式很方便的去声明不知道参数情况下的一个函数

```javascript
function sum (first, ...args) {
     console.log(first); // 10
     console.log(args); // [20, 30] 
 }
 sum(10, 20, 30)

```

三个点也可以用在数组解构里

```javascript
let students = ['wangwu', 'zhangsan', 'lisi'];
let [s1, ...s2] = students; 
console.log(s1);  // 'wangwu' 
console.log(s2);  // ['zhangsan', 'lisi']

```

### 五.数组和对象的解构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）

#### 数组的解构赋值

数组的元素是按次序排列的，变量的取值由它的位置决定, 如果有对应不上的就是undefined

```js
var [name, pwd, sex]=["小周", "123456", "男"];
console.log(name) //小周
console.log(pwd)//123456
console.log(sex)//男
```

#### 对象的解构赋值

对象的解构赋值多了一项可以起别名.

```js
var obj={name:"小周", pwd:"123456", sex:"男"}
var {name, pwd, sex}=obj;
console.log(name) //小周
console.log(pwd)//123456
console.log(sex)//男

//别名
let { foo: foz, bar: baz } = { foo: "aaa", bar: "bbb" };
console.log(foz) // "aaa"
console.log(foo) // error: foo is not defined
```



### 六.数组的扩展

#### 展开语法

```js
 let ary = [1, 2, 3];
 ...ary  // 1, 2, 3
 console.log(...ary);    // 1 2 3,相当于下面的代码
 console.log(1,2,3);//1,2,3
```

#### 展开语法应用
2.1快速合并数组

```js
// 合并方法一:直接用扩展运算符组成 
 let ary1 = [1, 2, 3];
 let ary2 = [3, 4, 5];
 let newArr = [...ary1, ...ary2];//newArr是[1,2,3,3,4,5]
 // 合并方法二: 用扩展运算符push
 ary1.push(...ary2);//ary1是[1,2,3,3,4,5],push方法返回的是新数组的长度
```
 2.2将HTML集合转换数组
```JS
    let oDivs = document.getElementsByTagName('div'); 
    oDivs = [...oDivs];
```

![1582388064280](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1582388064280.png)

#### 静态方法:Array.from()

可以将伪数组对象转换为真正的数组;

```
//定义一个集合
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
}; 
//转成数组
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```

改方法还可以接受第二个参数(是一个函数)，作用类似于数组的map方法，用来对每个元素进行处理，将处理后的值放入返回的数组.

```
 let arrayLike = { 
     "0": 1,
     "1": 2,
     "length": 2
 }
 let newAry = Array.from(arrayLike, item => item *2)//[2,4]
```

#### 实例方法：find()

用于找出第一个符合条件的数组成员，如果没有找到返回undefined

```javascript
let ary = [{
     id: 1,
     name: '张三'
 }, { 
     id: 2,
     name: '李四'
 }]; 
 let target = ary.find((item, index) => item.id == 2);//找数组里面符合条件的值，当数组中元素id等于2的查找出来，注意，只会匹配第一个

```

#### 实例方法：findIndex()

用于找出第一个符合条件的数组成员的位置，如果没有找到返回-1

```javascript
let ary = [1, 5, 10, 15];
let index = ary.findIndex((value, index) => value > 9); 
console.log(index); // 2
```

#### 实例方法：includes()

判断某个数组是否包含给定的值，返回布尔值。

```javascript
[1, 2, 3].includes(2) // true 
[1, 2, 3].includes(4) // false

```


### 七. 对象的扩展详解

#### 属性和方法简写。
属性简写
```js
const foo = 'bar';
const baz = {foo};//简写
const baz = {foo: foo};// 等同于这个
```
##### 方法简写

```js

var o = {
  method() {
    return "Hello!";
  }
};
 
// 等同于
var o = {
  method: function() {
    return "Hello!";
  }
};
```
#####  #### Object.keys()方法
获取对象的所有属性名或方法名（不包括原形的内容），返回一个数组

```js
//直接传入对象,返回属性名数组
var obj={
    name: "john", 
    age: "21", 
    getName: function () { 
        alert(this.name)
    	}
	};
console.log(Object.keys(obj));    // ["name", "age", "getName"]
console.log(Object.keys(obj).length);    //3
 //如果传入的是数组,则返回数组下标数组,如果传入的是字符串,则返回字符下标数组
console.log(Object.keys(["aa", "bb", "cc"]));    //["0", "1", "2"]
console.log(Object.keys("abcdef"));    //["0", "1", "2", "3", "4", "5"]

```

#####  #### Object.assign ()
Object.assign()方法将多个原对象的属性和方法都合并到了第一个目标对象上面。可以接收多个参数，第一个参数是目标对象，后面的都是源对象

```js

var target  = {}; //目标对象
var source1 = {name : 'ming', age: '19'}; //源对象1
var source2 = {sex : '女'}; //源对象2
var source3 = {sex : '男'}; //源对象3，和source2中的对象有同名属性sex
Object.assign(target,source1,source2,source3);
 
console.log(target);    //{name : 'ming', age: '19', sex: '男'}
```

### 八. import和export模块化引包和导出详解

1. import导入模块;
2. export用于导出模块;
3. import和export命令只能在模块的顶部，不能在代码块之中;
4. 用as起别名,*全导和大括号部分导入;

```js
//导入部分:
//全部导入
import Person from './example'
//将整个模块全部导入,并用as起别名
import * as example from "./example.js"
console.log(example.name)
console.log(example.getName())
//部分导入
import { name } from './example'
 
//导出部分:
// 导出默认
export default App
// 部分导出
export class User extends Component{};
```

### 八.Set 数据结构（★★）

ES6 提供了新的数据结构  Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

Set本身是一个构造函数，用来生成  Set  数据结构

```javascript
const s = new Set();
```

Set函数可以接受一个数组作为参数，可以快速去重。

```javascript
const set = new Set([1, 2, 3, 4, 4]);//{1, 2, 3, 4}

```

#### 实例方法

- add(value)：添加某个值，返回 Set 结构本身
- delete(value)：删除某个值，返回一个布尔值，表示删除是否成功
- has(value)：返回一个布尔值，表示该值是否为 Set 的成员
- clear()：清除所有成员，没有返回值

```javascript
 const s = new Set();
 s.add(1).add(2).add(3); // 向 set 结构中添加值 
 s.delete(2)             // 删除 set 结构中的2值   
 s.has(1)                // 表示 set 结构中是否有1这个值 返回布尔值 
 s.clear()               // 清除 set 结构中的所有值
 //注意：删除的是元素的值，不是代表的索引
```

#### 遍历

Set 结构的实例与数组一样，也拥有forEach方法，用于对每个成员执行某种操作，没有返回值。

```javascript
s.forEach(value => console.log(value))

```



### 十. Promise对象异步对象

1. Promise的概念和作用： Promise是异步编程的一种解决方案，将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数

2. Promise的状态： 它有三种状态，分别是pending进行中、resolved已完成、rejected已失败

3. Promise用法：用new关键字创建Promise对象,参数是一个匿名函数,里面有两个参数:resolve和reject,这两个参数均为方法。resolve方法用于处理异步操作成功后业务,reject方法用于操作异步操作失败后的业务.
4. then和catch方法:Promise实例化后的对象也是一个Promise对象,它有一个then和catch方法,then是是成功后会调用,then可以有多个,用来发不同的请求,,catch写一个,是前面出现失败后就会调用.

语法：
```js

let pro =new Promise(function(resolve,reject){
    let condition =true;//假设condition的值为true
    if(condition){
        resolve('操作成功');
    }else{
        reject('操纵失败');
    }
})
pro.then(function(res){
    console.log(res);//最终打印:操作成功
    
}).catch(function(error){
    console.log(error);
    
})
```
多个异步调用过程:

![1574498506011](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1574498506011.png)

5.Promise.all([promise对象1,promise对象2])方法:

![1574498923845](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1574498923845.png)

6.Promise.rase([p对象1,p对象2])方法

![1574499141298](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1574499141298.png)



##### 什么是Promise？
下面的回答很像在背概念，但是很精辟

所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理，让开发者不用再关注于时序和底层的结果。Promise的状态具有不受外界影响和不可逆两个特点。

##### Promise是同步的还是异步的？
是同步.下面代码说明
```js
  console.log(1)
  let a = new Promise((res, rej) => {
    console.log(2);
  });
  console.log(3);
  let b = new Promise((res, rej) => {
    console.log(4);
  });
  console.log(5);
  //上面依次输出12345
```

##### 传统的回调式异步操作有什么缺点？（Promise是如何解决异步操作）
传统的回调有五大信任问题：

调用回调过早
调用回调过晚（或没有被调用）
调用回调次数过少或过多
未能传递所需的环境和参数
吞掉可能出现的错误和异常

Promise的解决办法：

1.调用回调过早

对于Promise来说，即使是立即完成的Promise也无法被同步观察到，也就是说一个Promise调用then()的时候，即使这个Promise已经决议了，提供给then的回调也总会被异步调用。

2.调用回调过晚（或没有被调用）

对于一个Promise对象注册的每一个观察回调都是相对独立、互不干预的。而Promise对象调用resolve()和reject()时，每个注册的观察回调也都会被自动调度。所以这些观察回调的任意一个都无法影响或延误对其他回调的调用。

此外，关于回调未调用。正常情况下，没有任何东西可以阻止Promise向你通知它的决议，即使你的JavaScript代码报错了，一会通过异常回调来捕获到。如果Promise永远不被决议的话，Promise本身已提供了竞态的抽象机制来作为解决方案。

3.调用回调次数过少或过多

Promise的定义方式使得它只能被决议一次。即使代码中出现多次决议，这个Promise也会接受第一次决议，并会忽略掉其他任何后续调用。所以任何通过then()注册的回调只会被调用一次。

4.未能传递所需的环境和参数

凡是被决议的值都会传递到观察回调中，如果没有显示的决议值也会传递一个undefined给观察回调。需要注意的是，Promise只允许传一个决议值，其他值将会被默默忽略掉。

5.吞掉可能出现的错误和异常

如果在创建Promise时，存在JavaScript代码错误，会直接导致该Promise的拒绝决议，那么你可以通过reject()来捕获异常，代码中的任何异常都不会吞掉。

以上的回答十分的啰嗦，但是如果上面的五点你都能记住的话，你会了解很多关于Promise的细节问题，也会应对一些面试官的追问，如Promise的then()会不会被重复调用 等。



##### Promise中的异步模式有哪些？有什么区别？
好吧，这个问题可能会把面试者问懵……可以考虑另一种问法，或者直接进入下一个问题，说一说Promise.all()和Promise.race()的区别。因为ES6中的Promise中只有这两个模式all和race，其他的如first、any、last等都是其他Promise库提供的。

回到问题本身，Promise.all()和Promise.race()的区别

all会将传入的数组中的所有promise全部决议以后，将决议值以数组的形式传入到观察回调中，任何一个promise决议为拒绝，那么就会调用拒绝回调。

race会将传入的数组中的所有promise中第一个决议的决议值传递给观察回调，即使决议结果是拒绝。

如果向Promise.all()和Promise.race()传递空数组，运行结果会有什么不同？
all会立即决议，决议结果是fullfilled，值是undefined

race会永远都不决议，程序卡死……

如何确保一个变量是可信任的Promise（Promise.resolve方法传入不同值的不同处理有哪些）
可以通过Promise.resolve()方法对不确定的值进行Promise化，返回一个Promise对象。

如果是一个立即值，如一个普通变量，那么该Promise会立即决议为成功。

如果是一个Promise值，那么会将该Promise直接返回赋值给这个Promise，不会有额外开销。

如果是一个类Promise值， 比如其中含有名称为then的成员变量，那么会将then展开形成一个新的Promise对象。



##### Promise是如何捕获异常的？与传统的try/catch相比有什么优势？
传统的try/catch捕获异常方式是无法捕获异步的异常的，代码如下：

try {
​	setTimeout(function(){
​		undefined();		//undefined不是一个方法，会抛出异常
​	}, 500)
} catch(err){
​	//这里并不能捕获异常
​	console.log(err);
}
而对于Promise对象来说，构造Promise实例时的代码如果出错，则会被认为是一个拒绝的决议，并会向观察回调中传递异常信息。所以即使是一个异步的请求，Promise也是可以捕获异常的。此外，Promise还可以通过catch回调来捕获回调中的异常。






