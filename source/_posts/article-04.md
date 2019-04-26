---
title: JavaScript原型与原型链
date: 2019-04-08 19:34:56
tags: js 原型 原型链
cover: /img/img-03.jpg
categories: js web 原型 原型链
---
> 概述

JavaScript是一种面向对象的程序设计语言，但是JS本身是没有“类”的概念，JS是靠原型和原型链实现对象属性的继承。在理解原型前，需要先知道对象的构造函数是什么，构造函数都有什么特点？

#### 1、构造函数

```javaScript
    function Person(name, gender) {
        this.name = name;
        this.gender = gender;
    }
```

上述代码，普通函数Person()，加上new关键字后，就构造了一个person对象，所以，构造函数的定义是在函数调用的时候，在函数前面加上new关键字，并总会返回一个对象。

#### 2、函数对象
　　Js中的对象分为一般对象和函数对象，JavaScript中的数据类型分为基本数据类型和引用数据类型，基本数据类型有6中（null,undefined,string,number,boolean,Symbol）。其余的数据类型统称为object数据类型，其中，包括Array，Date，Function等，所以函数可以称为函数对象。

```javaScript
    let foo = function(){

    }
    foo.name = "bar";
    foo.age = 24;
    console.log(foo instanceof Function)  //true
    console.log(foo.age)  // 24
```

#### 3、原型链
　　JavaScript 中的对象，有一个特殊的 [[prototype]] 属性, 其实就是对于其他对象的引用（委托）。当我们在获取一个对象的属性时，如果这个对象上没有这个属性，那么 JS 会沿着对象的 [[prototype]]链 一层一层地去找，最后如果没找到就返回 undefined;这条一层一层的查找属性的方式，就叫做原型链。

#### 4、原型
　　1. 每个函数都有一个原型属性 prototype 对象

```javaScript
    function Person() {

    }

    Person.prototype.name = 'JayChou';

    // person1 和 person2 都是空对象
    var person1 = new Person();
    var person2 = new Person();

    console.log(person1.name) // JayChou
    console.log(person2.name) // JayChou
```

通过构造函数创造的对象，对象在寻找name属性时，找到了构造函数的prototype对象上。这个构造函数的 prototype对象，就是原型
用示意图来表示：

<img src="/img/img-08.png"/>

查找对象实例属性时，会沿着原型链向上找，在现代浏览器中，标准让每个对象都有一个 __proto__ 属性，指向原型对象。那么，我们可以知道对象实例和函数原型对象之间的关系。

