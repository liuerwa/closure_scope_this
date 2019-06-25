

<center>JS闭包，作用域，this</center>

一、闭包

1、变量的作用域

要理解闭包，首先必须理解JavaScript特殊的作用域。

变量的作用域分为两种：全局变量和局部变量。

a、函数内部可以直接读取全局变量：

    var n=999;
    function f1(){
        alert(n);
    }
    f1(); // 999

b、函数外部无法读取函数内部的局部变量

    function f1(){
    	var n=999;
    }
    alert(n); // error

c、函数内部声明变量时，必须使用var命令。如果不用的话，实际上声明了一个全局变量。

    function f1(){
    	n=999;
    }
    f1();
    alert(n); // 999

2、如何从外部读取局部变量

在函数的内部，再声明一个函数。

    function f1(){
        var n=999;
        function f2(){
            alert(n); //999
        }
    }

解释：在上述代码中，函数f2就被包括在函数f1里面，函数f1的所有局部变量对f2都是可见的。但是反过来就不行了，f2中的局部变量，在f1中是不可见的。这就是JavaScript语言特有的“链式作用域”结构，子对象会一级一级地向上寻找所有父对象的变量，所以，父对象的所有变量，子对象都是可见的，反之则不行。

既然在函数f2中可以读取函数f1中的局部变量，那么我们只要把函数f2作为返回值，就可以在函数f1外面读取它的局部变量了。

    function f1(){
    	var n=999;
    	function f2(){
    		alert(n); 
    	}
    	return f2;
    }
    var result=f1();
    result(); // 999

3、闭包的概念

上一节代码中的函数f2就是闭包。

我的理解是：闭包是能够读取其他函数内部变量的函数。

由于在JavaScript语言中，只有函数内部的子函数才能访问此函数的局部变量，因此可以简易的把闭包称为“定义在一个函数内部的函数”。

所以，在本质上，闭包是函数内部和函数外部连接的一座桥梁。

自己总结：闭包可以解决函数外部无法访问函数内部变量的问题。

闭包还有一个大的特点就是通过闭包我们可以让函数中的变量持久保持。

    function fn(){
    	var num = 5;
    	num+=1;
    	alert(num);
    }　　
    fn(); //6
    fn(); //6

为什么呢？因为函数一旦调用里面的内容就会被销毁，下一次调用又是一个新的函数，和上一个调用的不相关了。

    function fn(){
    	var num = 0;
    	return function(){
            num+=1;
        	alert(num);　　　
        };　　
    }　　
    var f = fn();
    f(); //1
    f(); //2

我们首页定义了一个fn函数，里面有个num默认为0，接着返回了一个匿名函数（也就是没有名字的函数）。我们在外部用f接收这个返回的函数。这个匿名函数干的事情就是把num加1。

　 这里之所以执行完这个函数num没有被销毁是因为那个匿名函数的问题，因为这个匿名函数用到了这个num，所以没有被销毁，一直保持在内存中，因此我们f()时num可以一直加。

4、闭包的用途

闭包可以用在许多地方。最大的两个用处：一是可以读取到函数内部的变量；二是可以让这些变量的值始终保存在内存中。

    function f1() {
        var n = 999;
        nAdd = function () {
          n += 1
        }
        function f2() {
          alert(n);
        }
        return f2;
    }
    var result=f1();
    result(); // 999
    nAdd();
    result(); // 1000

在上面代码中，其实result就是闭包f2函数，它一共运行了两次，第一次的值是999，第二次的值是1000。这证明了，函数f1中的局部变量n一直保存在内存中，并没有在函数f1调用后被自动清除。

为什么会这样呢？原因在于函数f1是函数f2的父函数，而函数f2被赋予了一个全局变量n，导致函数f2始终在内存中，而f2是依赖于f1的，所以f1也存在于内存中，不会在调用结束后，被垃圾回收机制回收。

注意：在上面代码中，"nAdd=function(){n+=1}"这一行，nAdd前面没有使用var，那么它就是一个全局变量。其次，它还是一个匿名函数，而这个匿名函数也是一个闭包，所以nAdd相当于一个setter，可以在函数外部调用它。

5、使用闭包的注意点

由于闭包会使得函数中的变量都保存到内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页性能的问题，在IE中可能会导致内存泄漏。解决方法是，在退出函数时，将不使用的局部变量删除。

闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

6、思考题

代码片段一：

    var name = "The Window";
    var object = {
    　　name : "My Object",
    　　getNameFunc : function(){
    　　　　return function(){
    　　　　　　return this.name;
    　　　　};
    　　},
        getName:function(){
        	alert(this.name);
    	} 
    };
    alert(object.getNameFunc()());   //The Window
    object.getName();   //My Object
    

解析：object.getNameFunc()返回的是一个匿名函数function(){return this.name;
}，此时this指向全局对象window，所以输出The Window；object.getName()的上下文是object，所以输出My Object。

代码片段二：

    var name = "The Window";
    var object = {
    　　name : "My Object",
    　　getNameFunc : function(){
    　　　　var that = this;
    　　　　return function(){
    　　　　　　return that.name;
    　　　　};
    　　}
    };
    alert(object.getNameFunc()());  //My Object
    

解析：getNameFunc 匿名函数属于object对象的函数,this 赋给 that,导致最后return的函数依赖getNameFunc ，不会被回收。

二、js中this的指向

1、this的一般情况

this的指向在函数定义的时候是不明确的，只有在函数被调用执行时才能确定其指向，实际上this最终指向的是那个调用他的对象。

- 例子1：

    function a(){
        var user = "追梦子";
        console.log(this.user); //undefined
        console.log(this); //Window
    }
    a();  //等价于 window.a();
    

这里的函数a实际上是被Window对象点击出来的，所以this指向的就是Window。

- 例子2：

    var o = {
        user:"追梦子",
        fn:function(){
            console.log(this.user);  //追梦子
        }
    }
    o.fn();  //等价于 window.o.fn();
    

这里的this指向的是对象o，因为你调用这个fn是通过o.fn()执行的，那自然指向就是对象o。

- 例子3：

    var o = {
        a:10,
        b:{
            a:12,
            fn:function(){
                console.log(this.a); //12
            }
        }
    }
    o.b.fn();
    

- 补充：

    var o = {
        a:10,
        b:{
            // a:12,
            fn:function(){
                console.log(this.a); //undefined
            }
        }
    }
    o.b.fn();
    

尽管对象b中没有属性a，这个this指向的也是对象b，因为this只会指向它的上一级对象，不管这个对象中有没有this要的东西。

- 特殊：

    var o = {
        a:10,
        b:{
            a:12,
            fn:function(){
                console.log(this.a); //undefined
                console.log(this); //window
            }
        }
    }
    var j = o.b.fn;
    j();
    

this永远指向最后调用他的对象。虽然fn是被b所引用，但是在给j赋值的时候并没有调用指向fn，而最后是window调用j来执行的，所以this指向windo。

2、构造函数版this

    function Fn(){
        this.user = "追梦子";
    }
    var a = new Fn();
    console.log(a.user); //追梦子
    

这里的对象a能够点出函数fn的user，是因为new关键字可以改变this的指向，将这个this指向对象a，a之所以是一个对象，是因为new关键字是创建一个对象。

3、当this碰到return

    function fn()  
    {  
        this.user = '追梦子';  
        return {};  
    }
    var a = new fn;  
    console.log(a.user); //undefined
    

    function fn()  
    {  
        this.user = '追梦子';  
        return function(){};
    }
    var a = new fn;  
    console.log(a.user); //undefined
    

    function fn()  
    {  
        this.user = '追梦子';  
        return 1;
    }
    var a = new fn;  
    console.log(a.user); //追梦子
    

    function fn()  
    {  
        this.user = '追梦子';  
        return undefined;  //替换成 return null 结果也是这样;
    }
    var a = new fn;  
    console.log(a.user); //追梦子
    

如果返回值是一个对象，那么this指向的就是这个对象，如果返回的不是一个对象，那么指向的还是函数的实例。

还有一点就是虽然null也是对象，但是在这里this还是指向那个函数的实例，因为null比较特殊。

三、for循环经典列子

- 例子1：

    window.onload = function(){
        var box = document.getElementById("box");
        var num = 0;
        function a(){
            console.log(num);
        }
        box.onclick = function(){
            num ++;
            a(); // 1,2,3,4....每次单击都会加1，说明函数引用外部变量是引用那个变量的最后一次的值。
        }
    }
    

- 例子2：

    window.onload = function(){
        var box = document.getElementById("box");
        var num = 0;
        for(var i=0;i<10;i++){
            box.onclick = function(){
                console.log(i); //总是打印10
            }
        }
    }
    

例子2中，每次点击都是打印10，而不是打印1,2,3,4...，这是为什么呢？因为当你for循环时，并没有执行这个函数，当你点击的时候执行此函数，它会发现它没有此变量i，于是向它的作用域链中查找此变量，而此时变量i的值为10，所有每次点击后输出的结果都为10。

例子3：

    window.onload = function(){
        var div = document.getElementsByTagName("div");
        var num = 0;
        for(var i=0;i<div.length;i++){
            (function(i){
                div[i].onclick = function(){
                    console.log(i);  //1,2,3,4.....
                }
            })(i)
        }  
    }
    

上面代码能够实现每次点击都出现不同的数字，但是这样会将i一直保存在内存中，比较消耗性能。
