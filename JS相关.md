#### 一.数据类型
在javascript当中数据类型总共分为两类:基本类型和引用类型;基本类型是有6种分别是:null,undefined,boolean,number,string和symbol(es6新增,表示独一无二的值,具体可以看阮一峰的介绍);引用类型统称为Object对象,主要包括对象,数组.

基本类型和引用类型的区别:

  主要是存储位置不一样,基本类型直接存储在栈当中,而引用类型只是把指针存储在栈中,对应的值是存储在堆中的.

数据类型检测方法:



typeof :除了null,其它都可以检查;

A(对象) instanceof B(构造函数):判断这个构造函数的prototype属性, 在不在这个对象的原型链上.。如果在则返回true,否则返回false。比如:console.log([] instanceof Array);//true(检测方法不准确,因为[] instanceof  Object)也是true;

万能检测数据类型:Object.prototype.toString.call(数据),比如:console.log(Object.prototype.toString.call(null)); //'[object Null]'

数组单独的检测方法:console.log(Array.isArray([]))//true

#### 二.预解析
##### 预解析的规律:
1.把变量的声明提升到当前作用域的最前面,只会提升变量的声明,不会提升赋值;
2.把函数的声明提升到当前作用域的最前面,只会提升函数的声明,不会提升调用;
3.在同一级作用域里存在变量声明和函数声明,优先会提升变量声明;

演示代码1:

```js
var a = 25;
function abc (){
  alert(a);//undefined
  var a = 10;
}
abc();
console.log(a);//25
function a() {
  console.log('aaaaa');//没有调用,不会执行
}
var a = 1;
console.log(a);//1
```

预解析会变为:

```js
var a;
function abc (){
  var a;
  alert(a);//undefined
  a = 10;
}
function a() {
  console.log('aaaaa');//没有调用,不会打印
}
a = 25;
abc();
console.log(a);//25
a = 1;
console.log(a);//1
```

演示代码2:(涉及多个赋值的拆解)

```js
f1();
console.log(c);
console.log(b);
console.log(a);
function f1() {
  var a = b = c = 9;
  console.log(a);
  console.log(b);
  console.log(c);
}
```

预解析后变为:

```js
function f1() {
  //注意多个赋值拆解规律:变量声明第一,最后赋值第二,依次后往前的规律赋值
  var a;
  c=9;
  b=c;
  a=b;

  console.log(a);//9
  console.log(b);//9
  console.log(c);//9
}
f1();
console.log(c);//9
console.log(b);//9
console.log(a);//只有局部f1里才有,全局里没有,直接报错
```

#### 三.对象

##### 创建对象的方法
引言:我平时项目中一般四种方法比较常用,但我看了<<javascript高级程序设计>>这本书之后发现有9种方法,分别是:

1.字面量方法:

```js
    var person = {
        name: "小明",
        age: 18,
        sayHi: function () {
            console.log("hi");
        }
    }
    console.log(person);
```
2.new Object()创建方法:

```js
  var person =new Object();
    person.name="小明";
    person.age=18;
    person.sayHi=function(){
        console.log("hi");
        
    }
    console.log(person);
```

3.工厂模式创建对象方法:(是new Object()创建的升级,实际是封装函数后将对象返回出来)

```js
 function createPerson(name,age,logData){
        var person =new Object();
        person.name=name;
        person.age=age;
        person.sayHi=function(){
            console.log(logData);  
        }
        return person;
    }
    var person=createPerson('小明',18,"hi")
    console.log(person);
```
4.构造函数方法:

```js
    function Person(name,age,logData){
        
        this.name=name;
        this.age=age;
        this.sayHi=function(){
            console.log(logData);  
        }
        console.log(this);//this是对象本身
    }
    var person=new Person('小明',18,"hi")
    console.log(person);
```

构造函数写法两个注意点:

a.函数首字母要大写;

b.要使用new关键字;



构造函数new关键字做的四件事:

提示:结合工厂函数和构造函数对比发现

a.new会在内存中用new Object()方法创建一个新的空对象;

b.new会让this指向这个心的空对象;

c.执行构造函数,给这个新的空对象添加属性和方法;

d.最后,new会将有值的新对象进行返回;

注意:

因为new关键字会自动帮我们返回他创建的对象,所以一般在构造函数中是不用写return返回值的, 那万一构造函数中就是写了return返回值,这样看返回值是简单类型还是引用类型,简单类型不影响,如果是复杂类型,那么这个返回值会覆盖原来new关键字自动返回的那个对象,这样使创建的对象直接变为复杂类型值.

5.原型链方法:也是使用了构造函数,但构造函数参数和函数体都是空,我们直接在函数点prototype点加要添加的属性名=值;

```js
    function Person() {}
    Person.prototype.name = "小明";
    Person.prototype.age = 18;
    Person.prototype.sayHi = function () {
        console.log("hi");

    }
    var person = new Person();
    console.log(person);//它会从原型里找到
```



6.构造函数和原型链组合方法:(实际就是一部分属性写在构造函数里,一部分属性写在原型里)

```js
 function Person(name) {
        this.name = name;
    }
 Person.prototype = {
        constructor: Person, //这里不写也不会受影响
        age: 18,
        sayHi: function () {
            console.log("hi");
        }
    }
    var person = new Person("小明")
    console.log(person);
```

7.构造函数动态原型方法:(实际构造函数里面加了一个方法判断,判断方法不存在的情况下才加方法)

```js
    function Person(name,age,fnData){
        this.name=name;
        this.age=age;
        if(typeof this.sayHi !='function'){
            Person.prototype.sayHi=function(){
                console.log(fnData);
            }
        }
    }
    var person=new Person('小明',18,"hi");
    console.log(person);
```
8.寄生构造函数方法:(书中说了跟工厂模式一模一样)
9.稳妥的构造函数方法:(实际就是没有使用new关键字调用,在函数里面用new Object创建对象,在函数里面将对象进行返回)

```js
    function Person(name,age,fnData) {
        var person =new Object()
        person.name=name;
        person.age=age;
        person.sayHi=function(){
            console.log(fnData);
        }
        return person
    }
    var person=Person("小明",18,"hi")
    console.log(person.sayHi());
```





 #### 四.函数内部this指向



首先我们要知道这几个点:

1.javascript是一种弱类型(可以随意赋值类型),动态类型检查(运行时才检查)的语言,这就导致函数内部this指向声明的话是不确定的,只有在调用时才可以确定;

2.所以我们可以总结一句很有用的话:不管函数或者方法是如何声明的,要看这个函数或者方法是最终谁最终点的,这个this就指向谁.

![1582351019208](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1582351019208.png)

案例:

```js
  //1.普通函数中的this:指向window
    function welcome(){
        console.log(this);
        
    }
    welcome();//window,相当于window.welcome();
    
    // ----------------------------------
    // 2.对象方法中的this:对象点出来,指向这个对象
    var obj={
        name:"小明",
        sayHi:function(){
            console.log(this);
            
        }
    }
    obj.sayHi();//指向obj对象
    // ----------------------------------
    // 对象里的对象,最终谁点出来就是谁
    var p1={
        dog:{
            name:'阿黄',
            sayHi:function(){
                console.log(this);
            }
        }
    }
    console.log(p1.dog.sayHi());//this是dog对象
    // ----------------------------------

    // 3.构造函数里的this,指向构造函数实例化的对象,这个对象值是已经添加对应的属性和值的对象
    function Student(name,age) {
        this.name=name;
        console.log(this);
        this.age=age;
    }
    var s=new Student('小明',18)//打印有小明的对象
       
        
    // ---------------------------
    // 构造函数忘记使用new的话,指向window
    function Student(name,age) {
        this.name=name;
        console.log(this);//没有写new关键字,相当于给window添加属性和方法
        this.age=age;
    }
    var s= Student('小明',18)//打印window
```


#### 五.函数的上下文调用模式(其实就是修改函数内部this指向)

我们可以使用Function.prototype里的call(),apply()和bind()方法,所以只要是函数,都可以使用这个方法.

```js
	var obj = {
		name: '我是要this要指向的对象'
	}
 	//1.call();
    // 语法:函数名.call(修改后的this指向,参数1,参数2,...)
    // 特点:会调用这个函数
    function getSum(a,b) {
        console.log(a+b);
        console.log(this);    
    }
    // getSum(1,2)//普通调用,this指向window
    getSum.call(obj,1,2)//this指向obj对象了

    // 2.apply();
    // 语法:函数名.apply(修改后的this指向,[参数1,参数2,...]),与call()不一样的是apply方法只能写两个参数,参数2是数组,里面可以写多个要传递的元素
    // 特点:会调用这个函数
	//两个总结:apply方法只能写两个参数,第二个参数是数组,数组里面可以写多个参数;而call方法里可以直接写要传递的多个参数.
     function getSum(a,b) {
        console.log(a+b);
        console.log(this);    
    }
    // getSum(1,2)//普通调用,this指向window
    getSum.apply(obj,[1,2])//this改为了obj对象

    // 3.bind();
    // 语法:函数名.bind(新的this指向对象,参数1,参数2...)
    // 特点:最大特点不会调用,只是返回一个修改了this后并携带参数的函数
    function getSum(a,b) {
        console.log(a+b);
        console.log(this); //打印obj   
    }
    // getSum.bind(obj)//不会打印,代表不会调用
    var fn=getSum.bind(obj,1,1);
    // console.log(fn);//这里还是添加了默认参数的getSum函数
    fn()//这里调用,打印this是obj对象    


```

修改上下文注意点:

![1574440603082](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1574440603082.png)

#### 六.原型链

答:javascript是面向对象的,每一个实例化的对象都有一个proto属性,这个属性指向它的原型对象,同时,这个实例化对象的构造函数有一个属性prototype,与对象的proto属性指向同一个原型,当一个对象在查找一个属性或方法时,它会第一时间看自己有没有,如果自己没有它会根据proto向它的原型进行查找,如果也没有,它会继续像原型的原型里继续查找,直到查到Object.prototype._proto为null,即原型链的终点null,这样就形成了一个完整的原型链.

我对原型图总结了自己的三条经验,记住这三条就很好理解

1.只要是构造函数,它就有prototype属性,同时构造函数也是对象,它就有proto属性;

2.一个实例化的对象对应的proto完整的路线有四条:实例化对象.proto第一条;构造函数.proto第二条;构造函数Function.proto第三条;构造函数Object.proto第四条;

3.Function.proto和Function.prototype指向同一个Function.prototype;



完整的原型链图:

![1574480479628](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1574480479628.png)

补充:记住规律对象proto的起始主线有四条:p1.proto;Person.proto;Function.proto,Object.proto;

内置对象(比如下面的Array,Date)的原型链:

![1574483334100](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1574483334100.png)

#### 七.继承的方式

1.混入式继承:实际就是遍历父元素的对象,在对象的循环体里将父元素的值赋值给子类即可(sonObj[key]=fatherObj[key]).

```js
    // 混入式
     var wangjianlin = {
       house:{
         price:10000000,
         adress:'洛杉矶'
       },
       car:{
         price:5000000,
         brand:'劳斯莱斯幻影'
       }
     };
     var wangsicong = {
       grilFriends:['豆得儿','雪梨','张馨予','林更新','001号']
     };
     //wangsicong这个对象想拥有wangjianlin这个对象的车和房.继承
     for(var key in wangjianlin){
       wangsicong[key] = wangjianlin[key];
     }
     console.log(wangsicong);//这样就有了父类和自己的属性和方法
```

2.替换原型式继承:实际就是将父类的值赋值给子类构造函数的原型(即Son.prototype=fatherObj).

```js
    //替换原型的方式.
   var wangjianlin = {
     house:{
       price:10000000,
       adress:'洛杉矶'
     },
     car:{
       price:5000000,
       brand:'劳斯莱斯幻影'
     }
   };

   //渣男构造构造
   function ZhaNan(gfs){
     this.gfs = gfs;
   }
   //每一个渣男都有一个骗女孩子的方法.
   ZhaNan.prototype.huaQian = function () {
     console.log("花钱请吃6块钱麻辣烫...");
   }

   //替换原型继承.(重点操作)
   ZhaNan.prototype = wangjianlin;

   //实例化一个渣男对象.
   var wangsicong = new ZhaNan(['豆得儿','雪梨','张馨予','林更新','001号']);//给自己添加了gfs属性,值是这个数组
   console.log(wangsicong);
   console.log(wangsicong.car);//可以得到得到父对象里的car属性值

```

3.混合式继承:
实际也是遍历父类对象,在循环体里用父类对象的值赋值给子类构造函数的原型里(即Son.prototype[key]=fatherObj[key]).

```js
    var wangjianlin = {
      house:{
        price:10000000,
        adress:'洛杉矶'
      },
      car:{
        price:5000000,
        brand:'劳斯莱斯幻影'
      }
    };

    //渣男构造构造
    function ZhaNan(gfs){
      this.gfs = gfs;
    }
    //每一个渣男都有一个骗女孩子的方法.
    ZhaNan.prototype.huaQian = function () {
      console.log("花钱请吃6块钱麻辣烫...");
    }

    //混合式继承.
    //这里并没有替换原型,而是给原型增加了属性和方法.
    for(var key in wangjianlin){
      ZhaNan.prototype[key] = wangjianlin[key];
    }

    //实例化一个渣男对象.
    var wangsicong = new ZhaNan(['豆得儿','雪梨','张馨予','林更新','001号']);
    console.log(wangsicong);
    console.log(wangsicong.car);//也可以得到得到父对象里的car属性值
```

4.class类的继承:
class类的继承的核心在于使用extends表明继承哪个类,并且在子类的构造函数中必须调用super()方法;

```js
    // 父类
    class Parent {
        constructor(props) {
            this.name = props;
            this.house = {
                price: 10000000,
                adress: '洛杉矶'
            };
            this.car = {
                price: 5000000,
                brand: '劳斯莱斯幻影'
            }
        }
        getValue() {
            console.log("我是父类的方法");
        }
    }
    //  子类
    class Child extends Parent {
        constructor(props) {
            console.log(props);//就是传递过来的参数
            super(props);
            this.name = props;
        }
    }
    let child = new Child("小明")
    console.log(child.getValue()); //可以调用父类方法
    console.log(child.car); //得到父亲的car
```




#### 八.arguments关键字

每一个函数都有一个单独的arguments对象,它只能写在函数里面,,它用来获取函数传递过来的所有的实参,如果在函数里面修改了参数,arguments也会变,它是伪数组,没有数组的方法,但有数组的长度属性,所以,作用:可以根据参数的数量来执行不同的业务逻辑.

![1574431043155](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1574431043155.png)

#### 九.闭包

定义:父函数内部有一个子函数,子函数调用的时候可以访问到父函数中的变量,而在全局里的其它人访问不到,那么子函数B就是闭包.

闭包是指有权访问另一个函数作用域中的变量的函数. ----javascript高级程序设计

```js
function A() {
    var a=1;
    window.B=function(){
        console.log(a);
    }
}
A();//先执行一下,因为里面要创建里面内容,创建完后一直存在,不会回收
B();//得到1
```

闭包的意义就是让我们可以间接的访问函数内部的变量;

经典面试题:

```js
 // 经典题目:下面会打印什么?
for(var i=0;i<=5;i++){
    setTimeout( ()=> {
       console.log(i);
    }, 100);
}
//这里会打印六个6(原因:setTimeout是异步,i一开始就走完变为6,var它没有暂时性死区,所以打印的都是全局的i=6,具体可以看es6中let介绍),改为0,1,2...5的方法:
// 方法1,改成自执行函数,并将i传递过去,里面用function接收,其余函数体就照常(利用了闭包)
for(var i=0;i<=5;i++){
    //这里可以访问到i
    (function(j){//自执行函数里接收传递过来的i
        setTimeout( ()=> {
       console.log(j);
    }, 100)
    })(i)
}
// 方法2:定时器添加第三个参数并接收即可
for(var i=0;i<=5;i++){
    setTimeout( (j)=> {
       console.log(j);
    }, 100,i);
}
// 方法3:直接用let
for(let i=0;i<=5;i++){
    setTimeout( ()=> {
       console.log(i);
    }, 100);
}
```
闭包有两个作用:
1.延长函数里面变量的生命周期;
2.提供有限的访问权限;

##### 闭包应用-计算打车价格 

```js
/*需求分析
打车起步价13(3公里内),  之后每多一公里增加 5块钱.  用户输入公里数就可以计算打车价格
如果有拥堵情况,总价格多收取10块钱拥堵费*/

 var car = (function() {
     var start = 13; // 起步价  局部变量
     var total = 0; // 总价  局部变量
     return {
       // 正常的总价
       price: function(n) {
         if (n <= 3) {
           total = start;
         } else {
           total = start + (n - 3) * 5
         }
         return total;
       },
       // 拥堵之后的费用
       yd: function(flag) {
         return flag ? total + 10 : total;
       }
	}
 })();
console.log(car.price(5)); // 23
console.log(car.yd(true)); // 33 ,里面的total数据还在,这就是闭包应用
```

#### 十.递归
 就是函数内部调用自己,同时要有结束条件.
 递归应用:

 ```js
     //1.求1-n之间的整数的累加和. n=5
    //1+2+3+4+5   getSum(n)
    //1+2+3+4  +5//第一个是5,前面4个继续调
    //1+2+3    +4
    //1+2      +3
    //1        +2

    //如果我们写了一个函数getSum就是用来求1-n之间的整数累加和的.
    //那在求1-n之间累加和的时候, 要先求1- (n-1)之间的累加和,再加上n本身.
    function getSum(n){
      if(n == 1){//到了临界条件让它返回1
        return 1;
      }else{
      return getSum(n-1) + n;//不到临界条件就让它一直调用
      }
    }
    console.log(getSum(5));//15
	//2.求斐波那契数列中第n位的数是多少.
    //1 1 2 3 5 8 13 21 34.....
    //如果我们写了一个函数getFB()就是用来求斐波那契数列中第n是多少的.
    //要求n位是多少,要先求n-1位和n-2位是多少.
  function getFB(n) {
    if (n == 1 || n == 2) {
      return 1
    } else {
      return getFB(n - 1) + getFB(n-2)
    }
  }
  console.log(getFB(10));//55
    3.  利用递归遍历数组对象,找到某项值
// 比如给id号,就可以返回它所在的对象
  var data = [{
    id: 1,
    name: '家电',
    goods: [{
      id: 11,
      gname: '冰箱',
      goods: [{
          id: 111,
          gname: '海尔'
        }, {
          id: 112,
          gname: '美的'
        },

      ]

    }, {
      id: 12,
      gname: '洗衣机'
    }]
  }, {
    id: 2,
    name: '服饰'
  }];
  console.log(data);

  //1.利用 forEach 去遍历里面的每一个对象
  function getID(json, id) {
    var obj = {};
    json.forEach(function (item) {
      // console.log(item); // 2个数组元素
      if (item.id == id) {
        // console.log(item);//找到了赋值即可
        obj = item;
        return obj;
        // 2. 如果里面还有goods这个数组并且数组的长度不为 0,把数组传递过去继续遍历
      } else if (item.goods && item.goods.length > 0) {
        obj = getID(item.goods, id);
      }
    });
    return obj;
  }
  console.log(getID(data, 112)); //{id: 112, gname: "美的"}
 ```

####  十一.防抖和节流:

防抖概念：任务频繁触发的情况下，只有任务触发的间隔超过指定间隔的时候，任务才会执行。(意思就是这件事儿需要等待，如果你反复催促，我就重新计时！)
应用场景有：搜索联想、input验证、resize

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>防抖</title>
</head>
<body>
  <button id="debounce">点我防抖！</button>

  <script>
    window.onload = function() {
      // 1、获取这个按钮，并绑定事件
      var myDebounce = document.getElementById("debounce");
      myDebounce.addEventListener("click", debounce(sayDebounce,2000));
    }

    // 2、防抖功能函数，接受传参
    function debounce(fn,delay) {
      // 4、创建一个标记用来存放定时器的返回值
      let timeout = null;
      return function() {
        // 5、每次当用户点击/输入的时候，把前一个定时器清除(这里是重点)
        clearTimeout(timeout);
        // 6、然后创建一个新的 setTimeout，
        // 这样就能保证点击按钮后的间隔内如果用户还点击了的话，就不会执行 fn 函数
        timeout = setTimeout(() => {
          fn.call(this, arguments);//这句话是调用fn,并把不确定的实参传过去
        }, delay);//这里设置限定时间
      };
    }

    // 3、需要进行防抖的事件处理
    function sayDebounce() {
      // ... 有些需要防抖的工作，在这里执行
      console.log("防抖成功！");
    }

  </script>
</body>
</html>

```
效果:

![img](https://user-gold-cdn.xitu.io/2019/3/12/169721dc213d832b?imageslim)







节流概念:指定时间间隔内只会执行一次任务。

应用场景有:scroll、touchmove

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>节流</title>
</head>
<body>

  <button id="throttle">点我节流！</button>

  <script>
    window.onload = function() {
      // 1、获取按钮，绑定点击事件
      var myThrottle = document.getElementById("throttle");
      myThrottle.addEventListener("click", throttle(sayThrottle,3000));
    }

    // 2、节流函数体
    function throttle(fn,delay) {
      // 4、通过闭包保存一个标记
      let canRun = true;
      return function() {
        // 5、在函数开头判断标志是否为 true，不为 true 则中断函数(这里是重点)
        if(!canRun) {
          return;
        }
        // 6、将 canRun 设置为 false，防止执行之前再被执行
        canRun = false;
        // 7、定时器
        setTimeout( () => {
          fn.call(this, arguments);
          // 8、执行完事件（比如调用完接口）之后，重新将这个标志设置为 true
          canRun = true;
        }, delay);
      };
    }

    // 3、需要节流的事件
    function sayThrottle() {
      console.log("节流成功！");
    }

  </script>
</body>
</html>
```

效果:

![img](https://user-gold-cdn.xitu.io/2019/3/12/169721de93ace1d0?imageslim)

#### 十二.重绘与回流(也叫重排)
重绘(repaint)：当元素样式的改变不影响布局时，浏览器将使用重绘对元素进行更新，此时由于只需要 UI 层面的重新像素绘制，因此损耗较少。
重绘应用场景:改变元素颜色,改变元素背景色.....

回流(reflow)：又叫重排（layout）。当元素的尺寸、结构或者触发某些属性时，浏览器会重新渲染页面，称为回流。此时，浏览器需要重新经过计算，计算后还需要重新页面布局，因此是较重的操作。
回流应用场景:浏览器窗口大小改变,元素尺寸/位置/内容发生改变,元素字体大小变化,添加或者删除可见的 DOM 元素......

总结:回流必定会触发重绘，重绘不一定会触发回流。重绘的开销较小，回流的代价较高。

那么，在工作中我们要如何避免大量使用重绘与回流呢？：
1.避免频繁操作样式，可汇总后统一一次修改
2.尽量使用 class 进行样式修改，而不是直接操作样式
3.减少 DOM 的操作，可使用字符串一次性插入


####  十三.浅深拷贝
我们知道对象是引用类型,假设有一个对象A和对象B,将对象A的值赋值给对象B,当你改了任意一个对象的值时,另一个对象的值都会一起变,而我们实际开发过程中也经常会遇到:后端小伙伴需要我将 接口返回的源数据和页面修改后新数据各发一份给它.这就需要用到对象的拷贝了.

1.浅拷贝:只拷贝对象的第一层就叫浅拷贝.
 浅拷贝方法:自己写函数,assign,concat,slice,扩展运算符方法这五种
 a.自己写浅拷贝

 ```js
     const obj1 = {
        name: "小明",
        hobby: ["吃饭", "睡觉"]

    };

    function shallowClone(obj) {
        const obj2 = {};
        for (let key in obj) {
            if (obj.hasOwnProperty(key)) {
                obj2[key] = obj1[key];
            }
          
        }
        return obj2;

    }
    const obj2=shallowClone(obj1);
    obj2.name="小张"
   console.log(obj2);//得到一个新的对象
   console.log(obj1);
   js
 ```
 b.利用Object.assign()方法
 ```js
 const obj1 = {
  username: 'LiangJunrong',
  skill: {
    play: ['basketball', 'computer game'],
    read: 'book',
  },
  girlfriend: ['1 号备胎', '2 号备胎', '3 号备胎'],
};
const obj2 = Object.assign({}, obj1);

console.log(obj2)
 ```
c.利用数组的concat()链接方法
concat方法不会改变原有数组,而是返回一个新数组.
```js
const arr1 = [
  1,
  {
    username: 'jsliang',
  },
];

let arr2 = arr1.concat();
console.log(arr2)
```
d.使用数组的slice()截取方法
slice() 方法原数组不会改变,而是返回一个新的数组对象，这一对象是一个由 begin 和 end 决定的原数组的浅拷贝..
```js
const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];

console.log(animals.slice(2, 4));
// expected output: Array ["camel", "duck"]
```
e.利用...展开运算符
```js
   const obj1 = {
        name: "小明",
        hobby: ["吃饭", "睡觉"]

    };
    const obj2 = { ...obj1}
    console.log(obj2); //得到一个新的对象
    console.log(obj1);
```
上面都只能拷贝一层,如果要拷贝对象两层或者以上,就要用深拷贝了
深拷贝的方法:
a.使用JSON.parse(JSON.stringyfy(oldObj))方法
```js
const arr1 = [
  1,
  {
    username: 'jsliang',
  },
];

let arr2 = JSON.parse(JSON.stringify(arr1));
```
该方法的局限性：
1、不能存放函数或者 Undefined，否则会丢失函数或者 Undefined；
2、不要存放时间对象，否则会变成字符串形式；
3、不能存放 RegExp、Error 对象，否则会变成空对象；
4、不能存放 NaN、Infinity、-Infinity，否则会变成 null；
5、……更多请自行填坑，具体来说就是 JavaScript 和 JSON 存在差异，两者不兼容的就会出问题。


b.使用第三方库Lodast中的_cloneDeep(oldObj)方法;
```js
var _ = require('lodash');

const obj1 = [
  1,
  'Hello!',
  { name: 'js1' },
  [
    {
      name: 'js2',
    }
  ],
]
const obj2 = _.cloneDeep(obj1);
```
c.jQuery的extend()方法
```js
      const obj1 = [
        1,
        'Hello!',
        { name: 'js1' },
        [
          {
            name: 'js2',
          }
        ],
      ]
      const obj2 = {};
      /**
       * @name jQuery深拷贝
       * @description $.extend(deep, target, object1, object2...)
       * @param {Boolean} deep 可选 true 或者 false，默认是 false，所以一般如果需要填写，最好是 true。
       * @param {Object} target 需要存放的位置
       * @param {Object} object 可以有 n 个原数据
       */
      $.extend(true, obj2, obj1);

```

####十四.事件循环(loop event)

##### Javascript为什么是单线程的？

js的作用是操作DOM，这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

(未完继续)

#### 十五.严格模式

 ES5增加了严格模式

  - 开启方法一:有的 script 脚本是严格模式，有的 script 脚本是正常模式，这样不利于文件合并，所以可以将整个脚本文件放在一个立即执行的匿名函数之中。这样独立创建一个作用域而不影响其他
    script 脚本文件。

    ```js
    (function (){
      //在当前的这个自调用函数中有开启严格模式，当前函数之外还是普通模式
    　　　　"use strict";
           var num = 10;
    　　　　function fn() {}
    })();
    //或者 
    <script>
      　"use strict"; //当前script标签开启了严格模式
    </script>
    <script>
      			//当前script标签未开启严格模式
    </script>
    ```

- 开启方法二: 为函数开启严格模式

  - 要给某个函数开启严格模式，需要把“use strict”;  (或 'use strict'; ) 声明放在函数体所有语句之前。

    ```js
    function fn(){
    　　"use strict";
    　　return "123";
    } 
    //当前fn函数开启了严格模式
    ```

##### 严格模式中的变化

严格模式对 Javascript 的语法和行为，都做了一些改变。

```js
'use strict'
num = 10 
console.log(num)//会报错,1.严格模式后不能使用未声明的变量
--------------------------------------------------------------------------------
var num2 = 1;
delete num2;//2.严格模式不允许删除变量
--------------------------------------------------------------------------------
function fn() {
 console.log(this); // 3.严格模式下普通全局作用域中函数中的 this 是 undefined
}
fn();  
---------------------------------------------------------------------------------
  function Star() {
    console.log(this);
    this.sex = '男';
  }
  // var ldh = Star();4.严格模式下,如果构造函数不加new调用this指向的是undefined;普通模式不加new构造函数this指向是window
  var ldh = new Star();
  console.log(ldh.sex);//严格模式,又没加new,this是undefined就不能赋值,赋值报错
----------------------------------------------------------------------------------
setTimeout(function() {
  console.log(this); //5.严格模式下，定时器 this 还是指向 window,没区别普通模式也是window(与3中普通函数是undefined不同)
}, 2000); 
```

#### 十六.高阶函数

  其实就是操作函数的函数,它函数的参数是一个函数或者将函数作为返回值进行输出

 #####  写法一:传递的参数就是一个函数
 ```js
  function fn(callback) {
    callback && callback();
  }
  fn(() => {
    console.log("高阶函数");
  })
 ```
  ##### 写法二:将函数作为返回值输出
  ```js
    function fn() {
    return () => {
      console.log("我是高阶函数");

    }
  }
  console.log(fn()());//调用
  ```