#### 一.数据类型
在javascript当中数据类型总共分为两类:基本类型和引用类型;基本类型是有6种分别是:null,undefined,boolean,number,string和symbol(es6新增,表示独一无二的值,具体可以看阮一峰的介绍);引用类型统称为Object对象,主要包括对象,数组和函数.

基本类型和引用类型的区别:

  主要是存储位置不一样,基本类型直接存储在栈当中,而引用类型只是把指针存储在栈中,对应的值是存储在堆中的.

数据类型检测方法:

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


#### 五.修改函数内部this执行的方法(又叫函数的上下文调用模式)

我们可以使用Function.prototype里的call(),apply()和bind()方法,所以只要是函数,都可以使用这个方法.

```js
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
        console.log(this);    
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

#### 七.继承

#### 八.arguments关键字

每一个函数都有一个单独的arguments对象,它只能写在函数里面,,它用来获取函数传递过来的所有的实参,如果在函数里面修改了参数,arguments也会变,它是伪数组,没有数组的方法,但有数组的长度属性,所以,作用:可以根据参数的数量来执行不同的业务逻辑.

![1574431043155](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1574431043155.png)

