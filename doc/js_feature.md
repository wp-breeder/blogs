## js 特性(坑)介绍不完全指南

前言: js有很多其他编程语言没有的特性(坑)和概念,接下来我们一起来感受一下。

### 基础知识

一张图了解面向对象

![oop](https://raw.githubusercontent.com/wp-breeder/blogs/master/_images/js/oop.jpg)

### 1. javascript this 、call 、 applyapply

#### 1. this除去不常用的 with 和 eval 的情况，具体到实际应用中， this 的指向大致可以分为以下 4 种:

- 作为对象的方法调用

>当函数作为对象的方法被调用时， this 指向该对象：

```javascript
    var obj = {
        a: 1,
        getA: function(){
            console.log( this === obj ); // 输出： true
            console.log( this.a ); // 输出: 1
        }
    };
    obj.getA();
```
- 作为普通函数调用

>当函数不作为对象的属性被调用时，也就是我们常说的普通函数方式，此时的 this 总是指向全局对象。在浏览器的 JavaScript 里，这个全局对象是 window 对象。

```javascript
    global.name = 'globalName';
    var myObject = {
        name: 'breeder',
        getName: function(){
            return this.name;
        }
    };
    var getName = myObject.getName;
    console.log( getName() ); // globalName
    function func(){
        "use strict"
        console.log(this); // 输出： undefined
    }   
    func();
```
- 构造器调用

>JavaScript 中没有类，但是可以从构造器中创建对象，同时也提供了 new 运算符，使得构造器看起来更像一个类。除了宿主提供的一些内置函数，大部分 JavaScript 函数都可以当作构造器使用。构造器的外表跟普通函数一模一样，它们的区别在于被调用的方式。当用 new 运算符调用函数时，该函数总会返回一个对象，通常情况下，构造器里的 this 就指向返回的这个对象

```javascript
    var MyClass = function(){
        this.name = 'breeder';
    };
    var obj = new MyClass();
    console.log(obj.name); // 输出： breeder
```

>但用 new 调用构造器时，还要注意一个问题，如果构造器显式地返回了一个 object 类型的对象，那么此次运算结果最终会返回这个对象，而不是我们之前期待的 this：

```javascript
    var MyClass = function(){
        this.name = 'breeder';
        return { // 显式地返回一个对象
            name: 'anne'
        }
    };
    var obj = new MyClass();
    console.log(obj.name); // 输出： anne
```
>如果构造器不显式地返回任何数据，或者是返回一个非对象类型的数据，就不会造成上述问题：

```javascript
    var MyClass = function(){
            this.name = 'breeder'
            return 'anne'; // 返回 string 类型
        };
    var obj = new MyClass();
    console.log(obj.name); // 输出： breeder
```

- Function.prototype.call 或 Function.prototype.apply 调用。

```javascript
    var obj1 = {
        name: 'breeder',
        getName: function(){
            return this.name;
        }
    };
    var obj2 = {
        name: 'anne'
    };
    console.log( obj1.getName() ); // 输出: breeder
    console.log( obj1.getName.call( obj2 ) ); // 输出： anne
```

```javascript
    var obj = {
        myName: 'breeder',
        getName: function(){
            return this.myName;
        }
    };
    console.log( obj.getName() ); // 输出： 'breeder'
    var getName2 = obj.getName;
    console.log( getName2() ); // 输出： undefined
```

#### 2. call 与 apply 区别和用途

1. apply

&nbsp;&nbsp;&nbsp;&nbsp;接受两个参数，第一个参数指定了函数体内 this 对象的指向，第二个参数为一个带下标的集合，这个集合可以为数组，也可以为类数组， apply 方法把这个集合中的元素作为参数传递给被调用的函数：

```javascript
    var func = function( a, b, c ){
        console.log( [ a, b, c ] ); // 输出 [ 1, 2, 3 ]
    };
    func.apply( null, [ 1, 2, 3 ] );
```

2. call

&nbsp;&nbsp;&nbsp;&nbsp;传入的参数数量不固定，跟 apply 相同的是，第一个参数也是代表函数体内的 this 指向，从第二个参数开始往后，每个参数被依次传入函数：

```javascript
    var func = function( a, b, c ){
        console.log( [ a, b, c ] ); // 输出 [ 1, 2, 3 ]
    };
    func.call( null, 1, 2, 3 );
```

&nbsp;&nbsp;&nbsp;&nbsp;当调用一个函数时， JavaScript 的解释器并不会计较形参和实参在数量、类型以及顺序上的区别， JavaScript 的参数在内部就是用一个数组来表示的。从这个意义上说， apply 比 call 的使用率更高，我们不必关心具体有多少参数被传入函数，只要用 apply 一股脑地推过去就可以了。
&nbsp;&nbsp;&nbsp;&nbsp;call 是包装在 apply 上面的一颗语法糖，如果我们明确地知道函数接受多少个参数，而且想一目了然地表达形参和实参的对应关系，那么也可以用 call 来传送参数。当使用 call 或者 apply 的时候，如果我们传入的第一个参数为 null，函数体内的 this 会指向默认的宿主对象，在浏览器中则是 window：

```javascript
    var func = function( a, b, c ){
        console.log( this === global); // 输出 true
    };
    func.apply( null, [ 1, 2, 3 ] );
    //但如果是在严格模式下，函数体内的 this 还是为 null：
    var func = function( a, b, c ){
        "use strict";
        console.log ( this === null ); // 输出 true
    }
    func.apply( null, [ 1, 2, 3 ] );
```

3. call和apply的用途

<font color="red" >能够熟练使用 call 和 apply，是真正成为一名 JavaScript 程序员的重要一步，
下面将详细介绍 call 和 apply 在实际开发中的用途</font>

- 改变 this 指向

call 和 apply 最常见的用途是改变函数内部的 this 指向

```javascript 
    var obj1 = {
        name: 'breeder'
    };
    var obj2 = {
        name: 'anne'
    };
    global.name = 'window';
    var getName = function(){
        console.log ( this.name );
    };
    getName(); // 输出: window
    getName.call( obj1 ); // 输出: breeder
    getName.call( obj2 ); // 输出: anne
```

再看另一个例子， document.getElementById 这个方法名实在有点过长，我们大概尝试过用一个短的函数来代替它，如同 `prototype.js` 等一些框架所做过的事情：

```javascript
    var getId = function( id ){
        //return document.getElementById( id );
    };
    getId( 'div1' );
```

- Function.prototype.bind
大部分高级浏览器都实现了内置的 Function.prototype.bind，用来指定函数内部的 this指向，即使没有原生的 Function.prototype.bind 实现，我们来模拟一个也不是难事，代码如下：

```javascript
    Function.prototype.bind = function( context ){
        var self = this; // 保存原函数
        return function(){ // 返回一个新的函数
            return self.apply( context, arguments ); // 执行新的函数的时候，会把之前传入的 context
            // 当作新函数体内的 this
        }
    };
    var obj = {
        name: 'breeder'
    };
    var func = function(){
        console.log ( this.name ); // 输出： breeder
    }.bind( obj);
    func();
```

```javascript
Function.prototype.bind = function(){
        var self = this, // 保存原函数
        context = [].shift.call( arguments ), // 需要绑定的 this 上下文
        args = [].slice.call( arguments ); // 剩余的参数转成数组
        return function(){ // 返回一个新的函数
            return self.apply( context, [].concat.call( args, [].slice.call( arguments ) ) );
            // 执行新的函数的时候，会把之前传入的 context 当作新函数体内的 this
            // 并且组合两次分别传入的参数，作为新函数的参数
        }
    };
    var obj = {
        name: 'breeder'
    };
    var func = function( a, b, c, d ){
        console.log ( this.name ); // 输出： breeder
        console.log ( [ a, b, c, d ] ) // 输出： [ 1, 2, 3, 4 ]
    }.bind( obj, 1, 2 );
    func( 3, 4 );

```

- 借用其他对象的方法

1. 借用方法的第一种场景是“借用构造函数”，通过这种技术，可以实现一些类似继承的效果([Javascript继承机制的设计思想](http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html)):

```javascript
    var A = function( name ){
        this.name = name;
    };
    var B = function(){
        A.apply( this, arguments );
    };
    B.prototype.getName = function(){
        return this.name;
    };
    var b = new B( 'breeder' );
    console.log( b.getName() ); // 输出： 'breeder'
```

2. 借用方法的第二种运用场景借用系统对象

```javascript
    //数组求最大值
    console.log(Math.max.apply(null, [1, 5, 10]))
    //将一个数组插入另一个数组的指定位置
    var a = [1,2,3,7,8,9];
    var b = [4,5,6];
    var insertIndex = 3;
    [].splice.apply(a, [].concat(insertIndex, 0, b));
    console.log(a);
    // a: 1,2,3,4,5,6,7,8,9
    
```

3. 借用其他对象的方法要满足两个条件

- 对象本身要可以存取属性

```javascript
    //我们无法在 number 身上存取其他数据
    var a = 1;
    Array.prototype.push.call( a, 'first' );
    console.log ( a.length ); // 输出： undefined
    console.log ( a[ 0 ] ); // 输出： undefined
```

- 对象的 length 属性可读写

```javascript 
    //函数的 length 属性就是一个只读的属性，表示形参的个数，我们尝试把一个函数当作 this Array.prototype.push：
    var func = function(){};
    Array.prototype.push.call( func, 'first' );
    console.log ( func.length );
    // 报错： cannot assign to read only property ‘length’ of function(){}
```

```javascript 
    var a = {};
    Array.prototype.push.call( a, 'first' );
    console.log ( a.length ); // 输出： 1
    console.log ( a[ 0 ] ); // first
    console.log(Math.max.apply(null, [1, 5, 10]))
```
