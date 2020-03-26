# 块级作用域
 ## 使用var
  使用`var`声明的变量没有块级作用域。在语句块里声明的变量作用域是其所再函数或者`script`标签内,你可以在语句块外面访问到它。
   
   例如

   ``` JavaScript
var x = 1;
{
  var x = 2;
}
console.log(x); // 输出 2
   ```
   >上面的代码可以看出`var`是没有块级作用域的

   有的时候为了安全我们还是需要有自己的私有变量的，这个时候就要用到立即执行函数了
   ``` JavaScript
var a=2;

  (function(){
       var a=1
  }())

 console.log(a) //输出2
   ``` 

   ## 使用let和const
   相比之下，使用`let`和`const`声明的变量是有块级作用域的
   ``` JavaScript
let x = 1;
{
  let x = 2;
}
console.log(x); // 输出 1
   ```
   `x=2`被限制在它所在的作用域里面。



``` JavaScript

{
  let x = 1;
  let x = 2;
}
console.log(x); // 输出Uncaught SyntaxError: Identifier 'x' has already been declared
```
> 在同一个作用域中`let`不能重复声明变量
 ``` JavaScript
 console.log(a)
 let a=1;  
 //输出 a is not defined
 ```  
 `let`声明的属性在创建之前是无法使用的，必须先创建后使用。

`const`在作用域方面是和`let`一致的，不同的是`const`声明的变量不能修改值
``` JavaScript
const a=1;
a=2;
console.log(a) //输出Uncaught TypeError: Assignment to constant variable.
```

关于`let`常见的面试题

``` JavaScript
for(var i = 0; i < 10; i++){
    setTimeout(function(){
        console.log(i);
    });
}
 //输出10次10
```
和我们的预期效果是不一样的，出现这种情况的原因有两个
* `var`声明的变量没有块级作用域的限制
* `javaScrip`引擎的事件循环作用 机制在起作用。`for`循环的同步任务执行完之后才会从事件队列中取出异步回调函数执行，此时`i`的值已经是10

下面两种方法可以达到预期的效果

``` JavaScript
for(var i = 0; i < 10; i++){
    (function(t){
		setTimeout(function(){
        	console.log(t);
    	});
    })(i);
}

```
>使用`JavaScript`闭包特性可以做到，但是代码不易理解，写起来也比较麻烦

``` JavaScript
for(let i = 0; i < 10; i++){
    setTimeout(function(){
        console.log(i)
    },1000)
}
```
> 使用`let`可以很方便的解决这个问题