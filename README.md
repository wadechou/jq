jquery 线性动画 事件队列
★jQuery动画高级用法queue
http://www.cnblogs.com/hh54188/archive/2011/04/09/1996469.html

不设置queen(),jquery的线性代码都是按照非动画函数来执行。
动画函数和自定义的非动画函数（function(){ $a.css();}）
自定义的必须调用dequeen();

jq只能设置执行的动画的最终动画，而没有直接执行到动画队列最终状态的方法。

queue("type",function(next){
    ...
    next();
})

问题1、
typ如果是自定义不是“fx”那么这个queue不会执行。
也就是说，自定义的queue要通过dequeue来调用。如果没有dequeu，这行代码讲跳过，非自定义的会直接添加，而且通过设置next。
回调函数，可以把fx插入到当前序列。

一般dequeue是写到queue外边的，$(this).dequeue();这可以代替next，同时不能在queue上设置type
设置了type就一般要把dequeue写到外边。同时要知道dequeue不是动画函数，他会马上执行，所以在调用dequeue的时候有两个方法。第一，写到queue方法里头，第二通过setTimeout
函数来设置。
或者delay(自定义的queue);而且delay要写到前面。这有点像jsonp的方式。


问题2、
默认情况下animate（这是动画函数）都有一个属性queue：true，让动画按队列来执行.animate({width: '50px'}, {duration: 1000});
也就是使用queue的目的是让这部分操作脱离fx，新建type就是新建new-fx，所以一定要设置queue为false来脱离原来的fx
而其他的本来就脱离fx的，所有queue是然它加入fx。


总结一下，就是有两种不同的需求，一种是加入队列（这是非动画，如果是动画，就不必写queue，动画本来就有queue。结果是某种非动画在特定的时间之后执行）；
一种是另起队列（结果是两种动画同时执行。）

delay()
$.fn.wait = function(time, type) {
 time = time || 1000;
 type = type || "fx";
 return this.queue(type, function() {
 var self = this;
 setTimeout(function() {
 $(self).dequeue();
 }, time);
 });
};
delay（）的本质是在队列里边插入一个空的列，然后这个列会设定延时取出执行。