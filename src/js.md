# javaScript相关

![](https://mmbiz.qpic.cn/mmbiz_png/j3vcKBBdH47zMOrbgZmcJBjmGf14BjsRJk9DClHa3WicvyBQ4VUREAvAsovekGbLbPUYIwEw0ibzQ7mDQHldJARQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## DOMContentLoaded和load事件的区别

浏览器解析完文档便能触发 DOMContentLoaded 事件；如果文档中包含脚本，则脚本会阻塞文档解析，而脚本需要等位于脚本前面的css加载完才能执行。在任何情况下，DOMContentLoaded 的触发不需要等待图片等其他资源加载完成。

接下来，我们来说说load，页面上所有的资源（图片，音频，视频等）被加载以后才会触发load事件，简单来说，页面的load事件会在DOMContentLoaded被触发之后才触发。

我们在 jQuery 中经常使用的 $(document).ready(function() { // ...代码... }); 其实监听的就是 DOMContentLoaded 事件，而 $(document).load(function() { // ...代码... }); 监听的是 load 事件。在用jquery的时候，我们一般都会将函数调用写在ready方法内，就是页面被解析后，我们就可以访问整个页面的所有dom元素，可以缩短页面的可交互时间，提高整个页面的体验。

我们为什么一再强调将css放在头部，将js文件放在尾部?

浏览器生成Dom树的时候是一行一行读HTML代码的，script标签放在最后面就不会影响前面的页面的渲染。那么问题来了，既然Dom树完全生成好后页面才能渲染出来，浏览器又必须读完全部HTML才能生成完整的Dom树，script标签不放在body底部是不是也一样，因为dom树的生成需要整个文档解析完毕。
现代浏览器为了更好的用户体验,渲染引擎将尝试尽快在屏幕上显示的内容。它不会等到所有HTML解析之前开始构建和布局渲染树。也就是说浏览器能够渲染不完整的dom树和cssom，尽快的减少白屏的时间。假如我们将js放在header，js将阻塞解析dom，dom的内容会影响到First Paint，导致First Paint延后。所以说我们会将js放在后面，以减少First Paint的时间，但是不会减少DOMContentLoaded被触发的时间。

CSS如果放置底部，浏览器阻止内容逐步呈现，浏览器在等待最后一个css文件下载完成的过程中，就出现了“白屏”（新打开连接时为白屏，尔后先出现文字，图片，样式最后出现）。这点非常严重，因为在网速非常慢的情况下，css下载时间比较长，这样就给用户带来“白屏”的时间自然也就很长了，用户体验非常差。 将CSS放在底部，页面可以逐步呈现，但在CSS下载并解析完毕后，已经呈现的文字和图片就要需要根据新的样式重绘，这是一种不好的用户体验。部分的内容将被解析并显示。


## 执行上下文

 当代码在JavaScript中运行时，执行代码的环境非常重要，并将概括为以下几点：
- 全局执行上下文 — 这是默认或者说基础的上下文，任何不在函数内部的代码都在全局上下文中。它会执行两件事：创建一个全局的 window 对象（浏览器的情况下），并且设置 this 的值等于这个全局对象。一个程序中只会有一个全局执行上下文。
- 函数执行上下文 — 每当一个函数被调用时, 都会为该函数创建一个新的上下文。每个函数都有它自己的执行上下文，不过是在函数被调用时创建的。函数上下文可以有任意多个。每当一个新的执行上下文被创建，它会按定义的顺序（将在后文讨论）执行一系列步骤。
- Eval 函数执行上下文 — 执行在 eval 函数内部的代码也会有它属于自己的执行上下文，但由于 JavaScript 开发者并不经常使用 eval，所以在这里我不会讨论它。
 
 
 函数调用的时候创建执行上下文压入栈  执行完推出


---

## 作用域

作用域决定这个变量的生命周期及其可见性。 当我们创建了一个函数或者 {} 块，就会生成一个新的作用域。需要注意的是，通过 var 创建的变量只有函数作用域，而通过 let 和 const 创建的变量既有函数作用域，也有块作用域。


## 闭包


闭包只存储外部变量的引用，而不会拷贝这些外部变量的值。

闭包与封装性

当函数可以记住并访问所在的词法作用域，即使函数是在当前词法作用域之外执行，这时 就产生了闭包。

闭包是那些引用了外部作用域中变量的函数。

纯函数是那些没有引用外部作用域中变量的函数，它们通常返回一个值并且没有副作用。

在 Javascript 中，局部变量会随着函数的执行完毕而被销毁，除非还有指向他们的引用。当闭包本身也被垃圾回收之后，这些闭包中的私有状态随后也会被垃圾回收。通常我们可以通过切断闭包的引用来达到这一目的。(设置为null)



## this

在全局执行上下文中，this 的值指向全局对象。(在浏览器中，this引用 Window 对象)。

在函数执行上下文中，this 的值取决于该函数是如何被调用的。如果它被一个引用对象调用，那么 this 会被设置成那个对象，否则 this 的值被设置为全局对象或者 undefined（在严格模式下）


1. 由new调用?绑定到新创建的对象。
2. 由call或者apply(或者bind)调用?绑定到指定的对象。
3. 由上下文对象调用?绑定到那个上下文对象。
4. 默认:在严格模式下绑定到undefined，否则绑定到全局对象。
5. ES6 中的箭头函数并不会使用四条标准的绑定规则，而是根据当前的词法作用域来决定 this，具体来说，箭头函数会继承外层函数调用的 this 绑定(无论 this 绑定到什么)。这 其实和ES6之前代码中的self = this机制一样。


## apply/call/bind

apply(this, [arguments])

call(this, ...arguments);


bind(this) 返回一个函数


```
if (!Function.prototype.bind) {
  Function.prototype.bind = function (oThis) {
    if (typeof this !== "function") {
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError("Function.prototype.bind - what is trying to be bound is not callable");
    }

    var aArgs = Array.prototype.slice.call(arguments, 1), 
        fToBind = this, // this在这里指向的是目标函数
        fNOP = function () {},
        fBound = function () {
          return fToBind.apply(this instanceof fNOP
                 ? this //此时的this就是new出的obj
                 : oThis || this,//如果传递的oThis无效，就将fBound的调用者作为this

               //将通过bind传递的参数和调用时传递的参数进行合并，并作为最终的参数传递
               aArgs.concat(Array.prototype.slice.call(arguments)));
        };
    fNOP.prototype = this.prototype;
    //将目标函数的原型对象拷贝到新函数中，因为目标函数有可能被当作构造函数使用
    fBound.prototype = new fNOP();
    //返回fBond的引用，由外部按需调用
    return fBound;
  };
}
```




## 原型链

![](https://raw.githubusercontent.com/mqyqingfeng/Blog/master/Images/prototype5.png)

```
function Person () {

}

var person = new Person();

person.constructor = Person.prototype.constructor = Person;

Object.prototype.__proto__ === null

```


```
//组合模式创建对象
function Person(name, age, job){
    this.name = name; 
    this.age = age;
    this.job = job;
    this.friends = ["Shelby", "Court"];
  }
Person.prototype = { 
    constructor : Person, 
    sayName : function(){
        alert(this.name); 
    }
}
var person1 = new Person("Nicholas", 29, "Software Engineer"); 
var person2 = new Person("Greg", 27, "Doctor");
```

```
//寄生组合继承
function SuperType(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function(){ 
    alert(this.name);
};


function inheritPrototype(SubType, SuperType) {
    var prototype = Object(SuperType.prototype);
    prototype.constructor = SubType;
    SubType.prototype = prototype;
}

function SubType(name, age){ 
    SuperType.call(this, name);
    this.age = age; 
}
inheritPrototype(SubType, SuperType);
SubType.prototype.sayAge = function(){ alert(this.age);};

```


## 深拷贝


## 垃圾回收


## 事件循环
