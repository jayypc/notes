## JavaScript权威指南
- 调试时候控制台输出信息
  `console.log()`
- alert()更具侵入性
  `alert()`
- 数据类型
  - **对象**是名/值对的集合,特殊的对象:全局对象,数组,函数
     - 通过**.** 和**[ ]**来访问 `book.topic 或 book["topic"]`
     - 可以直接通过**.**来创建一个新属性
     - `book.contents = {}`是一个空对象,它没有属性
  - **数组** 
     - `var empty = [] `是空数组,length为0
  - 数组和对象中都可以包含另一个数组或者对象
- **表达式**-->**语句**--> **函数**
- **对象/数组**+属性值为**函数**=**方法**(仅null和undefined无法拥有方法的值)
```
//数组中包含的两个元素都是对象(名/值对)
var points = [
	{x:0,y:0},
	{x:1,y:1}
]
//定义一个勾股定理的方法
points.dist = function(){
	var p1 = this[0];
	var p2 = this[1];
	var a = p2.x - p1.x;
	var b = p2.y - p1.y;
	return Math.sqrt(a*a + b*b);
}
```
- **包装对象**通过`new String(s)`,`new Number()`,`new Boolean()`等构造函数创建一个临时对象,达到调用某些属性的功能.null和undefined没有包装对象,访问它们的属性会造成类型错误
```
//字符串不是对象为什么有属性呢
var s = "hello";
var word = s.substring(s.indexOf("h")+1)
//t的值是undefined,由于第二行和第三行的s是不同的对象
var s = "test";
s.len = 4;
var t = s.len;
//可得字符串,数字和布尔值的属性都是只读的,并且不能给它们定义新属性,这个是有别于对象的
```
- 构造函数的使用
```
//定义一个构造函数,一般以大写字母开头
function Point(x,y){
	this.x = x;
	this.y = y;
}
//通过给构造函数prototype对象赋值定义方法
Point.prototype.r = function(){
	return Math.sqrt(this.x*this.x + this.y*this.y);
}
//使用关键词new来创建一个实例
var p = new Point(1,1);
p.r();
```
- Js操控Web内容元素
```
var a =document.createElement("div");//获取元素
function hide(e,reflow){
	if(reflow){
		e.style.display="none";//隐藏元素,所占空间随之消失
	}else{
		e.style.visibility="hidden";//隐藏元素,保留所占空间
	}
}
hide(a,false)
```
- JavaScript是以一种面向对象的语言.不严格地讲,这意味着我们不用全局的定义函数去操作不同类型的值,数据类型本身可以定义方法(method)来使用值.
> 例如,要对数组a中的元素进行排序,不必要将a传入sort()函数,而是调用a的一个方法sort()   

- NaN和任何值都不相等,包括自身

```
//判断一个变量是否是非数字值
x==NaN //这是错误的
x!=x //结果为true时说明x是非数字(字符串或对象)与isNaN()函数功能一致
```
- 字符串中可以直接使用[]来访问字符串的单个字符

```javascript
s = "hello";
s[0]  //结果是"h",与charAt()方法功能一致
```
- 尽管RegExp不是基本类型,但它仍具有直接量写法,在两条斜线之间的文本构成.它是具有实用API的特殊对象

```
var text = "testing:1,2,3"; //文本示例
var pattern = /\d+/g  //匹配所有包含一个或者多个数字的实例
pattern.test(text)  //=>true 匹配成功
text.search(pattern) //=> 9 首次匹配成功的位置
text.match(pattern) // => ["1","2","3"] 所有匹配组成数组
text.replace(pattern,"#") //=>testing:#,#,#"
text.split(/\D+/) //=>["","1","2","3"] //用非数字字符截取字符串
```
- 任意JavaScript的值都可以被转换为布尔值

```
//这些值会被转换为false
undefined
null
0
-0
NaN
"" //空字符串
//所有其他值,包括对象和数组都会被转为true
```

- JavaScript解释器启动时(或者web浏览器加载新页面的时候),将创建一个新的**全局对象**,并给它定义一组定义的初始属性:
  - *全局属性*,比如undefined,Infinity和NaN
  - *全局函数*,比如isNaN(),parseInt()和eval()
  - *构造函数*,比如Date(),RegExp(),String(),Object()和Array()
  - *全局对象*,比如Math和JSON
```
//不在任何函数内的JavaScript代码,用JavaScript关键字this来引用全局对象
var global = this; //定义一个引用全局对象的全局变量
```
- 在客户端JavaScript中,在其表示的浏览器窗口中Window对象充当了**全局对象**,也针对web浏览器和客户端JavaScript定义了一少部分其他全局属性.同时可以用一个属性**window**引用其自身,它可以代替**this**来引用全局对象
- 类型转换
|       值        |     字符串     |  数字  |  布尔值  |          对象           |
| :------------: | :---------: | :--: | :---: | :-------------------: |
|   undefined    | "undefined" | NaN  | false |   throws TypeError    |
|      null      |   "null"    |  0   | false |                       |
|      true      |   "true"    |  1   |       |   new Boolean(true)   |
|     false      |   "false"   |  0   |       |   new Bolean(false)   |
|     ""空字符串     |             |  0   | false |    new String("")     |
|   "1.2"非空数字    |             | 1.2  | true  |   new String("1.2")   |
|   "one"非空非数字   |             | NaN  | true  |   new String("one")   |
|       0        |     "0"     |      | false |     new Number(0)     |
|       -0       |     "0"     |      | false |     new Number(0)     |
|      NaN       |    "NaN"    |      | false |    new Number(NaN)    |
|    Infinity    | "Infinity"  |      | true  | new Number(Infinity)  |
|   -Infinity    | "-Infinity" |      | true  | new Number(-Infinity) |
|    1无穷大,非零     |     "1"     |      | true  |     new Number(1)     |
|     {}任意对象     |             |      | true  |                       |
|     []任意数组     |     ""      |  0   | true  |                       |
|   [9]一个数字元素    |     "9"     |  9   | true  |                       |
|   ['a']其他数组    |             | NaN  | true  |                       |
| function{}任意函数 |             | NaN  | true  |                       |
- 显示转换
```
Number("3")    //=> 3
String(false)  //=> "false"或使用false.toString()
Boolean([])    //=> true
Object(3)      //=> new Number(3)
//除了null和undefined之外任何值都有toString()方法
//Number类定义的toString()可以接受表示转换基数的可选参数
var n = 17;
binary_string = n.toString(2);//转换为"10001"
octal_string = "0"+n.toString(8);//转换为"021"
hex_string = "0x"+n.toString(16);//转换为"0x11"
//Object()不会报错,返回一个新创建的空对象
```
- 变量声明

```javascript
var message = "hello";
// 一个var关键字声明多个变量
var i,sum;
//将变量的初始赋值和变量声明合写在一起
var b = 0,j = 1,m = 2;
```
- 变量作用域
  - 在函数体内,**局部变量**的优先级高于同名的全局变量
  - 声明**局部变量**的时候必须用var,不然就是在声明一个全局变量
- 函数作用域和声明提前
  - **函数作用域:**变量在声明它们的函数体以及这个函数体嵌套的任意函数体内都是有定义的
  - **声明提前:**在JavaScript函数里声明的所有变量(但不涉及赋值)都被"提前"至函数体的顶部
```
var scope = "global";
function f(){
	console.log(scope);//输出"undefined",而不是"global"
	var scope = "local";//变量在这里赋初始值,但变量本身在函数体内任何地方都有定义
	console.log(scope);//输出"local"
}
```
- 作为属性的变量
  当声明一个JavaScript全局变量时,实际上是定义了全局对象的一个属性

```
var truevar = 1; //声明一个不可删除的全局变量
fakevar = 2; //创建一个全局对象的一个可删除的属性
this.fakevar2 = 3; //同上
delete truevar //=>false 变量并没有被删除
delete fakevar //=>true 变量被删除
delete fakevar2 //=>true 变量被删除
```

- 对象创建表达式

```
//如果一个对象创建表达式不需要传入任何参数,可以省略空圆括号
new Point(2,3)
new Object
new Date
```
- 运算符的结合性

```javascript
//一元操作符,赋值和三元条件运算符都具有从右到左的结合性
x = ~-y; //等同于 x=~(-y)
w = x = y =z; //等同于 w = (x = (y = z));
q = a?b:c?d:e?f:g;//等同于 q=a?b:(c?d:(e?f:g));
```

- eval()只有一个参数,如果传入的不是字符串,直接返回这个参数;如果是字符串,它会把字符串当成JavaScript代码进行编译,编译成功就开始执行这段代码,返回字符串中的最后一个表达式或语句的值,如果没有值,则返回undefined

```
eval("x"); //如果一个函数定了以局部变量x,这个会返回x的值
eval("x=1");//会改变这个x的值
eval("var y = 3;")//声明一个新的局部变量,在ECMAScript 5严格模式下,不能定义新的变量和函数
//如果在最顶层代码中调用eval(),会作用于全局变量和全局函数
```
- 一元运算符
  - **in:**测试属性是否存在
  - **instanceof:**测试对象类
  - **typeof:**检测操作数类型
  - **delete:**删除属性
  - **void:**返回undefined值
```javascript
var point = {x:1,y:1};
"x" in point        //true 对象中有一个名为"x"的属性
"toString" in point //true 对象继承了toString()方法
var data = [7,8,9];
"0" in data         //true 数组包含元素"0"
1 in data           //true 数字转换为字符串
```
```javascript
var d = new date();
d instanceof Date;   //true d是由Date()创建的
d instanceof Object; //true 所有对象都是Object的实例
d instanceof Number; //false
var a = [1,2,3];
a instanceof Array;  //true
a instanceof Object; //true 所有数组都是对象
a instanceof RegExp; //false
```

```
(typeof value == "string")?"'"+value+"'":value
//所有对象和数组的typeof运算结果都是"object",所以可以区分对象和其他原始值
typeof (i) //也可以带上圆括号
```

|       x        |                typeof x                 |
| :------------: | :-------------------------------------: |
|   undefined    |               "undefined"               |
|      null      |                "object"                 |
| "true"或"false" |                "boolean"                |
|    任意数字或NaN    |                "number"                 |
|     任意字符串      |                "string"                 |
|      任意函数      |               "function"                |
|  任意内置对象(非函数)   |                "object"                 |
|     任意宿主对象     | 由编译器各自实现的字符串,但不是以上除"object","function"外 |

```javascript
var o = {x:1,y:2};  
delete o.x;
"x" in o  //false

var a = [1,2,3];
delete a[2];
a.length //3 注意删除这个元素,会在删除操作留下一个"洞",实际上没有修改数组长度

//delete只能作用于左值,如果不是将返回true
delete 1; //参数不是左值,返回true
```

```
//void经常用在javascript:URL中
<a href="javascript:void window.open();">打开一个新窗口</a>
```

