# 函数的定义
 ``` JavaScript
 function fn(){
     console.log(1)
 }
 ```

 ``` JavaScript
 let fn=function(){
      console.log(1)
 }
 ```

 ``` JavaScript
 let fn=function fn1(){
     console.log(1)
 }
 ```
 ``` JavaScript
(function(){
  
  console.log(1)

}())
 ```

 ``` JavaScript
 let obj={
     fn:function(){

     }
 }

 ```
 以上是在`ES5`中常用的创建函数的方法。在`ES6`中新增了`箭头函数`，下面来比较一下两者的区别

```JavaScript
 // ES5
 let fn=function(x,y){
     return x+y;
 }
 //ES6
 const fn=(x,y)=>{return x+y}

```
在`箭头函数`中`function`关键字是可以不写的，参数直接写在`()`里面，后面用`=>`连接方法体。

如果函数只有一个参数那么括号也是可以省略不写的，比如
``` JavaScript
 const fn=x=>{return x+1};
```
如果函数的执行部分只有一句话，则还可以省去大括号`{}`和`return`关键字，比如
``` JavaScript
const fn=x=>x+1
```
在对象内声明函数
``` JavaScript
 // ES5

  var obj={
       fn:function(){
           console.log(1)
       }
  }

  // ES6
  let obj{
      fn(){
          console.log(1)
      }
  }

//可直接省去:和function关键字
```
# 参数默认值
``` JavaScript
//ES5
function fn(a,b){
    a=a||0
    b=b||0
    return a+b;
}
```
在`ES5`中函数的默认值我们都要在函数体内进行判断的，以防别人调用时传参错误，事例中的函数只有两个参数相对还比较容易处理的，但是一旦参数比较多的情况下就比较麻烦了，也会影响代码的阅读，在`ES6`中已经有了比较好的解决方案了。

``` JavaScript
let fn=(x=0,y=0)=>x+y
fn() //输出0
fn(1) //输出1
fn(1,2) //输出3 
```
>在`ES6`中可以在函数声明的时候直接给参数一个默认值
# 函数的this
>`箭头函数`还有一个亮点，就在它本身是没有`this`的

``` JavaScript
 var name='windowName'
 var obj={
  name:'objName',
  fn:function(){
    console.log(this.name)
  }  
}
obj.fn() //输出 objName 
function fn1(){
  console.log(this.name)
}
fn1() //输出windowName
```
看到这种情况可能就有点不明白，同样是`this.name`为什么出来的结果却是不一样的。这个问题肯定困扰着很多初学者，网上也有很多关于`this`指向问题的文章，其实我觉得并没有那么复杂，我们只要换一种写法就一目了然了
``` JavaScript
 var name='windowName'
 var obj={
  name:'objName',
  fn:function(){
    console.log(this.name)
  }  
}
obj.fn.call(obj) //输出 objName 
function fn1(){
  console.log(this.name)
}
fn1.call(window) //输出windowName
```
`this`并不是函数创建时所在的`this`，而是在调用的时候决定的，所有总是很难搞明白它究竟是什么。从上面的例子我们可以看出来**函数的`this`其实就是`call`的第一个参数**，就是这么简单。
 > `this`就是`call`的第一个参数

 > `this`就是`call`的第一个参数
 
 > `this`就是`call`的第一个参数

``` JavaScript

var name='小亮'
var obj={
  name:'小明',
  fn:function(){
    console.log(this.name)
  }
   
}

function fn1(age){
  console.log(this.name+","+age+"岁了")
}
fn1.call(obj,18) //输出 小明,18岁了
```
通过这段代码再次证明`this`是`call`的第一个参数

使用`call`调用函数这样虽然可以明确的看出`this`是什么，但是这种写法始终还是不那么优雅，而且`this`也给代码增加了太多的不确定性，下面我们看箭头函数是怎么解决这个问题的

``` javaScript
var obj = {
  i: 10,
  b: () => console.log(this.i, this),
  c: function() {
    console.log( this.i, this)
  }
}
obj.b(); 
// undefined, Window{...}
obj.c(); 
// 10, Object {...}
```
>箭头函数不会创建自己的this,它只会从自己的作用域链的上一层继承this

 ``` javaScript
 let fn=(a,b)=>{

  console.log(arguments)
}
 fn(1,2)

 // arguments is not defined
 ```
  > 箭头函数不绑定Arguments对象

``` javaScript

var Foo = () => {};
var foo = new Foo(); // TypeError: Foo is not a constructor
```
> 箭头函数不能用作构造器，和 `new`一起用会抛出错误。