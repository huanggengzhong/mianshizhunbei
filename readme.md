#### 一.数据类型
在javascript当中数据类型总共分为两类:基本类型和引用类型;基本类型是有6种分别是:null,undefined,boolean,number,string和symbol(es6新增,表示独一无二的值,具体可以看阮一峰的介绍);引用类型统称为Object对象,主要包括对象,数组和函数.

基本类型和引用类型的区别:

  主要是存储位置不一样,基本类型直接存储在栈当中,而引用类型的指针是存储在栈中,对应的引用类型对象是存储在堆中的.

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

5.原型链方法:

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



6.构造函数和原型链组合方法:(实际就是一部分写在构造函数里,一部分写在原型里)

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

7.动态原型方法:(实际构造函数里面加了一个方法判断,判断方法不存在的情况下才加方法)

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
9.稳妥的构造函数方法:

```js
    function Person(name,age,fnData) {
        var person =new Object()
        person.sayHi=function(){
            console.log(fnData);
        }
        return person
    }
    var person=Person("小明",18,"hi")
    console.log(person.sayHi());//用哪个值构造函数只写哪个,让不用的属性无法访问
```





 #### 四.this指向和上下文模式



#### 五.原型链
完整的原型链图:

![1573056393934](C:\Users\91583\AppData\Roaming\Typora\typora-user-images\1573056393934.png)



#### 六.继承

