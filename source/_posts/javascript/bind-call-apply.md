---
title: 深入理解bind、call和apply的作用
date: 2020-06-26 01:43:59
categories: 
        - javascript
tags: JavaScript
---

在JavaScript语言中，bind、call、apply都是用来改变函数的this对象的指向的。说白了就是都可以用来代替另一个对象调用一个方法，将一个函数的对象的执行期上下文从初始的上下文改变为由当前对象的上下文。

在深入讲解它们之前，我先讲一个小故事：
从前有一只猪和一只狗，突然有一天猪开口说话了：我是猪，我能吃又能睡！
狗一听乐了，谁还不会吃和睡了？当即不服叫道：我是狗，我也能吃能睡！（我还会叫呢，但是这句它没有说出口，但是它的确有这个行为的能力）

故事讲完了，请用JS代码完全描述上面的故事，很无聊吧？哈哈！如果是你，我相信你也会写出跟如下一样的代码：

{% codeblock lang:js %}
var pig = {
    name : "pig",
    behavior : { // 行为
        sleep : "sleep",
        eat : "eat"
    },
    say : function () {
        console.log("I’m a " + this.name + ", and i can " + this.behavior.eat + " and " + this.behavior.sleep);
    }
}

var dog = {
    name : "dog",
    behavior : { //行为
        sleep : "sleep",
        eat : "eat",
        bark : "wangwang!"
    }
}
pig.say(); // I'm a pig, and i can eat and sleep
pig.say.call(dog); // I'm a dog, and i can eat and sleep
pig.say.apply(dog); // I'm a dog, and i can eat and sleep
{% endcodeblock %}

上述例子中，会发现在狗这个对象中并没有定义say()这个方法，但是通过猪的对应方法.call/apply(狗）就能让狗也实现say()的功能。

那么是不是可以理解为狗拿着猪的功能做自己想要做的事情呢（或者说利用别人的方法做自己想做的事情）？通俗点说call和apply还真就是这层作用。

同样的，bind()也可以实现这种功能：

{% codeblock lang:js %}
pig.say.bind(dog); // 这样？
// 可以，不会报错，但是也不会有任何效果。
// bind() 将返回一个函数，所以需要在上面代码的基础上加个执行()
pig.say.bind(dog)(); // I'm a dog, and i can eat and sleep
{% endcodeblock %}

在这个层面上乍一看，三者实现的功能都一样啊，那不是没区别了吗。能没区别吗，制定标准的人又不傻，如果真的都一样，至于让三个都存在么？存在即合理，也就是说肯定有其不一样的地方，下面继续说说它们的不同之处：

假设猪的say方法需要两个参数，一个是猪的身高height，一个是猪的体重weight：

{% codeblock lang:js %}
var pig = {
    name : "pig",
    behavior : { // 行为
        sleep : "sleep",
        eat : "eat"
    },
    say : function (height, weight) {
        console.log("I'm a " + this.name + ", and i can " + this.behavior.eat + " and " + this.behavior.sleep);
        console.log("My height is " + this.height + "cm, my weight is " + this.weight + "KG"); 
    }
}
var dog = {
    name : "dog",
    behavior : { //行为
        sleep : "sleep",
        eat : "eat",
        bark : "wangwang!"
    }
}
pig.say.call(dog, 75, 50); 
// I'm a dog, and i can eat and sleep
// My height is 75cm, my weight is 50KG
{% endcodeblock %}

读到这里你可能已经猜到哪里有区别了，那就是函数传参问题。call的参数列表第一位是欲使用的对象，第二位开始则是被调用函数的本身的参数往后以此类推。而apply的参数列表第一位跟call的第一个参数相同，但是只能接受两个参数，第二个参数只能接受数组，如下：

{% codeblock lang:js %}
pig.say.apply(dog, [75, 50]);
// I'm a dog, and i can eat and sleep
// My height is 75cm, my weight is 50KG
{% endcodeblock %}

再来看看bind()的传参方法：

{% codeblock lang:js %}
pig.say.bind(dog)(75, 50);
// I'm a dog, and i can eat and sleep
// My height is 75cm, my weight is 50KG
{% endcodeblock %}

总体上看上去，call和apply并没有什么不同，唯一的区别就是在于参数列表。

call : 可以接受多个参数，第二个参数开始可以接受一连串参数。这个方法主要用在js对象各方法相互调用的时候，使当前this实例指针保持一致，或者在特殊情况下需要改变this指针。如果没有提供新的对象参数，那么Global对象被用作当前新对象。

apply : 最多只能有两个参数，第一个参数为当前新对象，第二个参数为数组（Array）。如果给该方法传递多个参数，则把参数都写进这个数组里面，当然，即使只有一个参数，也要写进数组里。如果第二个数组参数不是一个有效的数组或arguments对象，那么将导致一个TypeError。如果没有提供数组参数和当前新对象任何一个参数，那么Global对象将被用作当前新对象，并且无法被传递任何参数。

## apply的一些其他巧妙用法

### 1. Math.max 可以实现得到数组中最大的一项：

因为Math.max不支持Math.max([param1,param2])也就是数组，但是它支持Math.max(param1,param2...)，所以可以根据apply的特点来解决 var max=Math.max.apply(null,array)，这样就轻易的可以得到一个数组中的最大项（apply会将一个数组转换为一个参数接一个参数的方式传递给方法）这块在调用的时候第一个参数给了null，这是因为没有对象去调用这个方法，我只需要用这个方法帮我运算，得到返回的结果就行，所以直接传递了一个null过去。

用这种方法也可以实现得到数组中的最小项：Math.min.apply(null,array)

### 2. Array.prototype.push可以实现两个数组的合并

同样push方法没有提供push一个数组，但是它提供了push(param1,param2...paramN)，同样也可以用apply来转换一下这个数组，即：

{% codeblock lang:js %}
var arr1=new Array("1","2","3");
var arr2=new Array("4","5","6");
Array.prototype.push.apply(arr1,arr2);    //得到合并后数组的长度，因为push就是返回一个数组的长度
{% endcodeblock %}

# call / apply 实现继承

比如说猪身上的能吃能睡，狗也具备，那么狗就没必要自己再写这部分代码给自己了，直接拿猪的过来用不就行了。

{% codeblock lang:js %}
function Pig(name, eat, sleep, weight) {
    this.name = name;
    this.eat = eat;
    this.sleep = sleep;
    this.weight = weight;
}
function Dog(name, eat, sleep, weight) {
    Pig.call(this, name, eat, sleep, weight);
    // Pig.apply(this, [name, eat, sleep, weight]);
}

var dog = new Dog("dog", "吃一盆", "睡一天", "100KG");
console.log(dog);
// Dog {name: "dog", eat: "吃一盆", sleep: "睡一天", weight: "100KG"}
{% endcodeblock %}

