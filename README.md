# lagou-task-01-01
ES新特性和JS异步编程、TypeScript

###### 1.说出执行结果，并解释为什么？
```javascript
var a = [];
for (var i = 0; i < 10; i++) {
    a[i] = function () {
        console.log(i);
    };
}
a[6]();
```
答：10。
原因：使用var声明的变量i的作用域为全局作用域，每次for循环变量i的值都会发生变化，当循环结束时i的值为10，故在执行a[6]()时结果为10。若想输入6，使用let声明变量i，形成块级作用域，或者使用闭包，如下:
```js
//使用let声明
var a = [];
for (let i = 0; i < 10; i++) {
    a[i] = function () {
        console.log(i);
    };
}
a[6]();

//使用闭包
var a = [];
for (var i = 0; i < 10; i++) {
    a[i] = (function (i) {
        return function () {
            console.log(i);
        };
    })(i);
}
a[6]();
```



###### 2.说出执行结果，并解释为什么？
```javascript
var temp = 123;
if (true) {
    console.log(temp);
    let temp;
}
```
答：报错，用let声明变量形成块级作用域，在代码块内，使用let声明变量之前，该变量都是不可用的，造成‘暂时性死区’。若使用var声明，打印结果为123。



###### 3. 结合ES6新语法，用最简单的方式找出数组中的最小值？
```javascript
var arr = [12, 34, 32, 89, 4];
```
答： Math.min(...arr)。使用了ES6中的扩展运算符，将数组转为用逗号分隔的参数序列。使用ES5语法的实现方式为Math.min.apply(null,arr)。



###### 4. 请详细说明var、let、const三种声明变量方式的具体差别？
答：var为ES5中的语法，let、const为ES6中的语法。 差别如下。
（1）作用域不同。var声明的变量作用域为全局作用域或函数作用域；let、const声明的变量作用域为块级作用域，变量在代码块内有效。
（2）var声明变量会发生“变量提升”现象；let、const声明变量则不会。
（3）用var声明变量前，可以使用该变量；用let、const声明变量会形成“暂时性死区”。不允许声明前使用该变量。
（4）let、const不允许在相同作用域内重复声明同一个变量；var则允许。
（5）const和let的区别是，const用来声明一个只读的常量，一旦声明就不能改变，因此const一旦声明变量就必须立即初始化。需要注意的是，const保证的不是变量的值不能改动，而是变量指向的那个内存地址所保存的数据不得改动。



###### 5. 说出执行结果，并解释为什么？
```javascript
var a = 10;
var obj = {
    a: 20,
    fn () {
        setTimeout(() => {
            console.log(this.a)
        })
    }
}
obj.fn();
```

答: 20。this对象是在运行时基于函数的执行环境绑定的：在全局函数中，this指向的是window；当函数被当做某个对象的方法调用时，this等于该对象。而在箭头函数中，this是在定义函数时绑定的，不是在执行过程中绑定的。this对象的指向是可变的，但在箭头函数中，它是固定的。
this指向的固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。正是因为它没有this，所以也就不能用作构造函数。
箭头函数转成 ES5 的代码如下。
```javascript
// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;
  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}
```



###### 6. 简述 Symbol 类型的用途?
答：
（1）使用Symbol作为对象属性名。值得注意的是，Symbol类型的key不能通过Object.keys()或for..in..枚举出来，可以利用该特性可一些不需要对外操作或访问的属性使用Symbol来定义。可通过Object.getOwnPropertySymbols()来获取Symbol类型的属性。
（2）使用Symbol定义类的私有属性



###### 7. 说说什么是浅拷贝，什么是深拷贝？
答：
（1）区别。浅拷贝只拷贝对象中第一层的属性；深拷贝是对对象及对象的所有子对象进行拷贝。
（2）实现方法。浅拷贝可以使用for..in..遍历对象或Object.assign()方法实现；深拷贝可以使用JSON.parse(JSON.stringify(obj))或递归拷贝实现。



###### 8. 谈谈你是如何理解JS异步编程的，Event Loop是做什么的，什么是宏任务，什么是微任务？
答：（一）JavaScript是单线程的脚本语言，所谓“单线程”就是指一次只能执行一个任务，如果有多个任务就必须要排队，只有前面的任务完成了，才能执行后面的任务。这种模块带来的坏处是只要有一个任务耗时很长后面的任务就必须要排队等着，造成浏览器假死。为了解决这个问题，JavaScript将任务的执行模式分成两种：同步和异步。
	   同步模式就是上面描述的模式，后一个任务需要等待前一个任务结束后才能执行，程序的执行顺序和任务的排列顺序是一致的、同步的；异步模式则不同，每一个任务有一个或者多个回调函数（callback），前一个任务结束后，不是执行后一个任务，而是执行回调函数，同时，后面的任务也不等前一个任务结束就执行，所以程序的执行顺序和任务的排列顺序是不一致的、异步的。可以通过回调函数、事件监听、发布/订阅、Promise来实现异步编程。

		（二）异步执行的运行机制如下。
		（1） 所有同步任务都在主线程中执行，形成一个执行栈。
		（2） 主线程以外，还有一个“任务队列”，只要异步任务有了运行结果，就在“任务队列”中排队添加一个事件。
		（3） 程序中会优先执行“执行栈”中的同步任务，执行完毕后系统读取“任务队列”，将其中排队的事件压入“执行栈”中执行。
		（4）主线程不断从“任务队列”中读取事件，这个运行机制成为“事件循环（Event Loop）”，如下图。
![事件运行机制](https://img-blog.csdnimg.cn/20200521221705585.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzc2MjE1MA==,size_16,color_FFFFFF,t_70#pic_center)
（三）任务分为宏任务（macrotask）和微任务（microtask）。

 - 宏任务包括整体代码script、setTimeout、setInterval、I/O、UI render。
 - 微任务主要是：Promise、process.nextTick、MutationObserver。
      在挂起任务时，JS 引擎会将所有任务按照类别分到这两个队列中。在读取任务时，首先在 macrotask 的队列中取出第一个任务，执行完毕后取出 microtask 队列中的所有任务顺序执行；之后再取 macrotask 任务，周而复始，直至两个队列的任务都取完。


###### 9. 将下面的异步代码使用Promise改进？
```javascript
setTimeout(() => {
    var a = "hello";
    setTimeout(function () {
        var b = "lagou";
        setTimeout(function () {
            var c = "I ❤ U";
            console.log(a + b + c);
        }, 10);
    }, 10);
}, 10);
```
答：
```javascript
const timeout = function (time, data) {
    const promise = new Promise(function (resolve, reject) {
        setTimeout(() => {
            resolve(data);
        }, time);
    });
    return promise;
};

timeout(10, "hello")
    .then((res) => {
        return timeout(10, res + "lagou");
    })
    .then((res) => {
        return timeout(10, res + "I ❤ U");
    })
    .then((res) => {
        console.log(res);
    });
```



###### 10. 简述TypeScript与JavaScript之间的关系？
答：TypeScript是JavaScript的一个超集，它可以编译成纯JavaScript，相比JavaScript它提供了**类型系统**和**对ES6的支持**。



###### 11. 谈谈Typescript的优缺点？
答：**优点：**
（1）TypeScript 增强了代码的可读性和可维护性。
 - 类型系统实际上是最好的文档，大部分函数看类型定义就能知道如何使用了
 - 可以再编译阶段发现大部分错误
 - 增强了编辑器和IDE的功能，包括代码补全、接口提示、跳转到定义等
 
（2） TypeScript非常包容
-  .js文件可以直接重命名为.ts文件
- 即使编辑错误也能生成JavaScript文件
- 兼容第三方库，即使第三方库不是用ts编写的，也可以编写单独的类型文件供ts文件读取

（3） TypeScript拥有活跃的社区 

**缺点：**
（1） 有一定的学习成本
（2） 需要定义类型增加了一定的开发成本
（3）  集成到构建流程中需要一些工作量
（4）  可能和一些库结合的不是很完美