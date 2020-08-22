## JavaScript学习知识

### 基本知识

#### 输出

```js
window.alert()

document.write()

innerHTML

console.log()

// 打印带有样式的内容，%c 相当于一个占位符，后面的文本会应用上其样式
console.log('这是一段带有样式的%c文本', 'font-size: 20px;color: red;')
```



#### 注释

```js
//这是一行注释

/*
* 这是多行注释
*/
```



#### 数据类型

+ 基本数据类型：字符串（String）、数字(Number)、布尔(Boolean)、对空（Null）、未定义（Undefined）、Symbol
+ 引用数据类型：对象(Object)、数组(Array)、函数(Function)

+ **动态数据**

   JavaScript 拥有动态类型。这意味着相同的变量可用作不同的类型，如：

  ```js
  var x;               // x 为 undefined
  var x = 5;           // 现在 x 为数字
  var x = "John";      // 现在 x 为字符串
  ```

+ **字符串**

  字符串是存储字符的变量，字符串可以是引号中的任意文本，可以是单引号也可以是双引号，单引号和双引号可以共同使用，但是要成对出现。还可以是字符模板，如：

  ```js
   var name = 'zhangsan' // 单引号
   var name = "zhangsan" // 双引号
   var name = 'this is "zhangsan"' //混合使用
   var name = `zhangsan` //字符串模板
  ```

+ **数值**

  ```js
  /*
  		* 在JS中所有的数值都是Number类型，
      * 	包括整数和浮点数（小数）
    * 
      * JS中可以表示的数字的最大值
    * 	Number.MAX_VALUE
      * 		1.7976931348623157e+308
    * 
      * 	Number.MIN_VALUE 大于0的最小值
      * 		5e-324
      * 
      *  如果使用Number表示的数字超过了最大值，则会返回一个
    * 		Infinity 表示正无穷
      * 		-Infinity 表示负无穷
    * 		使用typeof检查Infinity也会返回number
      *  NaN 是一个特殊的数字，表示Not A Number
    * 		使用typeof检查一个NaN也会返回number
  */
  
  //在JS中整数的运算基本可以保证精确
  //如果使用JS进行浮点运算，可能得到一个不精确的结果, 所以千万不要使用JS进行对精确度要求比较高的运算，解决办法
  //1. 通过toFixed()方法进行取舍
  //2. 把需要计算的数字升级（乘以10的n次幂）成计算机能够精确识别的整数，等计算完毕再降级（除以10的n次幂）
  ```

+ **布尔**

  布尔（逻辑）只能有两个值：true 或 false。

  ```js
    /*
   * Boolean 布尔值
     * 	布尔值只有两个，主要用来做逻辑判断
   * 	true
     * 		- 表示真
     * 	false
     * 		- 表示假
     * 
     * 使用typeof检查一个布尔值时，会返回boolean
   */
  var x = true;
  var y = false;
  ```

+ 数组

  创建数组的方式：

  ```js
  //数组下标是基于零的，所以第一个项目是 [0]，第二个是 [1]，以此类推。
  var array = new Array()
  var array = []
  ```

+ 对象

  ```js
  //对象由花括号分隔。在括号内部，对象的属性以名称和值对的形式 (name : value) 来定义。属性由逗号分隔：
  var person={firstname:"John", lastname:"Doe", id:5566};
  ```

   对象属性有两种寻址方式：

  ```js
    name=person.lastname;
    name=person["lastname"];
  ```

+ **Undefined和Null**

  null这个值专门用来表示一个为空的对象

  使用typeof检查一个null值时，会返回object

  Undefined（未定义）类型的值只有一个，就undefind

  当声明一个变量，但是并不给变量赋值时，它的值就是undefined

  使用typeof检查一个undefined时也会返回undefined

  ```js
    /*
    
    相同点：
  
    	两者都表示空，在if判断的时候也都会转成false，几乎是等价的
    
    不同点:
    
     null表示"没有对象"，即该处不应该有值。典型用法是：
    
    （1） 作为函数的参数，表示该函数的参数不是对象。
    
  	（2） 作为对象原型链的终点。
  	*/
  ```

  **undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。**典型用法是：

  ```js
    （1）变量被声明了，但没有赋值时，就等于undefined。
    
    （2) 调用函数时，应该提供的参数没有提供，该参数等于undefined。
    
    （3）对象没有赋值的属性，该属性的值为undefined。
    
    （4）函数没有返回值时，默认返回undefined。
  ```

+ **数据类型转换**

  + 转String

    ```js
     /*
      	方式一： 
      		调用被转换数据类型的toString()方法
          该方法不会影响到原变量，它会将转换的结果返回
          但是注意：null和undefined这两个值没有toString()方法，
          如果调用他们的方法，会报错
        方式二：
          调用String()函数，并将被转换的数据作为参数传递给函数
          使用String()函数做强制类型转换时，
          对于Number和Boolean实际上就是调用的toString()方法
          但是对于null和undefined，就不会调用toString()方法
          它会将 null 直接转换为 "null"
          将 undefined 直接转换为 "undefined"
      */ 
    ```

  + 转Number

    ```js
    /*
    	方式一：
    	使用Number()函数
     			- 字符串 --> 数字
     				1.如果是纯数字的字符串，则直接将其转换为数字
     				2.如果字符串中有非数字的内容，则转换为NaN
     				3.如果字符串是一个空串或者是一个全是空格的字符串，则转换为0
    			- 布尔 --> 数字
     				true 转成 1
     				false 转成 0
    
     			- null --> 数字     0
    
     			- undefined --> 数字 NaN
     	方式二：
     	- 这种方式专门用来对付字符串
    	- parseInt() 把一个字符串转换为一个整数
    	- parseFloat() 把一个字符串转换为一个浮点数
    */
    ```

  + 转Boolean

    ```js
    /*
    - 使用Boolean()函数
     		- 数字 ---> 布尔
     			- 除了0和NaN，其余的都是true
     
     		- 字符串 ---> 布尔
     			- 除了空串，其余的都是true
     
     		- null和undefined都会转换为false
     
     		- 对象也会转换为true
    */
    ```

  

#### 变量/常量

+ **区别：**

   随着代码的运行，某个值以后可能被改变，称之不变量，若是以后不会被改变，则称之为常量

+ **变量全名规则**

  ```js
  1、以字母、下划线、$开头；
  2、区分大小写；
  3、由字母、下划线、$或数字组成；
  4、不能使用关键字和保留字
  ```

+ **作用域**

  JS中作用域有：全局作用域、函数作用域。没有块作用域的概念。ECMAScript 6(简称ES6)中新增了块级作用域。块作用域由 { } 包括，if语句和for语句里面的{ }也属于块作用域。

+ **var**

  var定义变量，没有块的概念，可以跨块访问，不能跨函数访问，不初始化出现undefined，不会报错。有变量提升的现象

+ **let**

  let定义变量，只能在块作用域里访问，也不能跨函数访问，对函数外部无影响。没有变量提升的现象

+ **const**

  const定义常量，只能在块作用域里访问，也不能跨函数访问，使用时必须初始化(即必须赋值)，而且不能修改。没有变量提升的现象

#### 运算符

```js
/*
* 运算符也叫操作符
* 	通过运算符可以对一个或多个值进行运算,并获取运算结果
* 	比如：typeof就是运算符，可以来获得一个值的类型
* 		它会将该值的类型以字符串的形式返回
* 		number string boolean undefined object
*/
```

+ 算数运算符

  ```js
  /* 	算数运算符
  * 		当对非Number类型的值进行运算时，会将这些值转换为Number然后在运算
  * 			任何值和NaN做运算都得NaN
  * 		+
  * 			+可以对两个值进行加法运算，并将结果返回
  * 			 如果对两个字符串进行加法运算，则会做拼串
  * 				会将两个字符串拼接为一个字符串，并返回
  * 			任何的值和字符串做加法运算，都会先转换为字符串，然后再和字符串做拼串的操作
  * 		-
  * 			- 可以对两个值进行减法运算，并将结果返回
  * 		*
  * 			* 可以对两个值进行乘法运算
  * 		/
  * 			/ 可以对两个值进行除法运算
  * 		%
  * 			% 取模运算（取余数）
  */
  ```

  

+ 赋值运算符

  赋值运算符左侧的操作数必须是变量、对象属性或数组元素，也称为左值。例如，下面的写法是错误的，因为左侧的值是一个固定的值，不允许操作。

  ```js
  /*
  * =
  * 	可以将符号右侧的值赋值给符号左侧的变量
  * += 
  * 	a += 5 等价于 a = a + 5
  * -=
  * 	a -= 5 等价于 a = a - 5
  * *=
  * 	a *= 5 等价于 a = a * 5
  * /=
  * 	a /= 5 等价于 a = a / 5
  * %=
  * 	a %= 5 等价于 a = a % 5
  */
  var a = 10;		
  //a = a + 5;
  //a += 5;		
  //a -= 5;	
  //a *= 5;
  // a = a%3;
  a %= 3;		
  console.log("a = "+a);
  ```

  

+ 比较运算符

  | 大小运算符 | 说明                                                         |
  | ---------- | ------------------------------------------------------------ |
  | <          | 如果第一个操作数小于第二个操作数，则返回true；否则返回 false |
  | <=         | 如果第一个操作数小于或等于第二个操作数，则返回true；否则返回 false |
  | >          | 如果第一个操作数大于或等于第二个操作数，则返回true；否则返回 false |
  | >=         | 如果第一个操作数大于第二个操作数，则返回true；否则返回 false |

  比较运算中的操作数可以是任意类型的值，但是在执行运算时，会被转换为**数字或字符串**，然后再进行比较。如果是数字，则比较大小；如果是字符串，则根据字符编码表中的编号值从左到右逐个比较每个字符。

+ 逻辑运算符

  逻辑运算符包括：逻辑与`&&`、逻辑或`||`和逻辑非`!`。

  + **逻辑与**是一种**短路逻辑**，如果**左侧**表达式为 false，则直接短路返回结果，不再运算右侧表达式。运算逻辑如下：

    - 第 1 步：计算第一个操作数（左侧表达式）的值。
    - 第 2 步：检测第一个操作数的值。如果左侧表达式的值可转换为 false（如 null、undefined、NaN、0、""、false），那么就会结束运算，直接返回第一个操作数的值。
    - 第 3 步：如果第一个操作数可以转换为 true，则计算第二个操作数（右侧表达式）的值。
    - 第 4 步：返回第二个操作数的值。

  + 逻辑或也是一种短路逻辑，如果左侧表达式为 true，则直接短路返回结果，不再运算右侧表达式。运算逻辑如下：

    - 第 1 步：计算第一个操作数（左侧表达式）的值。
    - 第 2 步：检测第一个操作数的值。如果左侧表达式的值可转换为 true，那么就会结束运算，直接返回第一个操作数的值。
    - 第 3 步：如果第一个操作数可以转换为 false，则计算第二个操作数（右侧表达式）的值。
    - 第 4 步：返回第二个操作数的值。

  + 逻辑非运算`!`是布尔取反操作（NOT）。作为一元运算符，直接放在操作数之前，把操作数的值转换为布尔值，然后取反并返回。

    ```js
    console.log( ! {} );  //如果操作数是对象，则返回false
    console.log( ! 0 );  //如果操作数是0，则返回true
    console.log( ! (n = 5));  //如果操作数是非零的任何数字，则返回false
    console.log( ! null );  //如果操作数是null，则返回true
    console.log( ! NaN );  //如果操作数是NaN，则返回true
    console.log( ! Infinity );  //如果操作数是Infinity，则返回false
    console.log( ! ( - Infinity ));  //如果操作数是-Infinity，则返回false
    console.log( ! undefined );  //如果操作数是undefined，则返回true
    ```

    如果对操作数执行两次逻辑非运算操作，就相当于把操作数转换为布尔值。

    **逻辑与和逻辑或运算的返回值不必是布尔值，但是逻辑非运算的返回值一定是布尔值。**

+ 等值运算符

  | 等值检测运算符 | 说明                                                       |
  | -------------- | ---------------------------------------------------------- |
  | ==（相等）     | 比较两个操作数的值是否相等                                 |
  | !=（不相等）   | 比较两个操作数的值是否不相等                               |
  | ===（全等）    | 比较两个操作数的值是否相等，同时检测它们的类型是否相同     |
  | !==（不全等）  | 比较两个操作数的值是否不相等，同时检测它们的类型是否不相同 |

  - 如果操作数是布尔值，则先转换为数值，其中 false 转为 0，true 转换为 1。
  - 如果一个操作数是字符串，另一个操作数是数字，则先尝试把字符串转换为数字。
  - 如果一个操作数是字符串，另一个操作数是对象，则先尝试把对象转换为字符串。
  - 如果一个操作数是数字，另一个操作数是对象，则先尝试把对象转换为数字。
  - 如果两个操作数都是对象，则比较引用地址。如果引用地址相同，则相等；否则不等。

+ 条件运算符

  条件运算符是唯一的三元运算符，其语法格式如下：

  ```js
  b ? x : y
  ```

  b 操作数必须是一个布尔型的表达式，x 和 y 是任意类型的值。

  - 如果操作数 b 的返回值为 true，则执行 x 操作数，并返回该表达式的值。
  - 如果操作数 b 的返回值为 false，则执行 y 操作数，并返回该表达式的值。

#### 流程控制

+ if / else / else if

  在正常情况下，JavaScript 脚本是按顺序从上到下执行的，这种结构被称为顺序结构。如果使用 if、else/if 或 switch 语句，可以改变这种流程顺序，让代码根据条件选择执行的方向，这种结构被称为分支结构。

  ```js
  // if 语法格式：
  if(expr)
      statement
  // 如果表达式 expr 的值为真，则执行语句 statement；否则，将忽略语句 statement。
  
  // if else 语法格式：   else 语句仅在 if 或 else/if 语句的条件表达式为假的时候执行。语法格式如下：
  if(expr)
      statement1
  else
      statement2
      
  // if else if else 语法格式:
  if (expr)
    	statement1
  else if (expr)
    	statement2
  else 
    	statement3
  ```

  

+ switch

  switch 语句专门用来设计多分支条件结构。与 else/if 多分支结构相比，switch 结构更简洁，执行效率更高。语法格式如下：

  ```js
  switch (expr) {
      case value1 :
          statementList1
          break;
      case value2 :
          statementList2
          break;
      ...
      case valuen :
          statementListn
          break;
      default :
          default statementList
  }
  ```

  switch 语句根据表达式 expr 的值，依次与 case 后表达式的值进行全等比较，如果相等，则执行其后的语句段，只有遇到 break 语句，或者 switch 语句结束才终止；如果不相等，则继续查找下一个 case。switch 语句包含一个可选的 default 语句，如果在前面的 case 中没有找到相等的条件，则执行 default 语句，它与 else 语句类似。

+ for / for in / for of 

  ```js
  for(①初始化表达式;②条件表达式;④更新表达式){
    ③语句...
  }
  ```

  for循环的执行流程：

  + 1. 执行初始化表达式，初始化变量（初始化表达式只会执行一次）
  + 2. 执行条件表达式，判断是否执行循环。
  + 3. 如果为true，则执行循环 3 ，如果为false，终止循环
  + 4. 执行更新表达式，更新表达式执行完毕继续重复 2

  ```js
  for ( var variable in <object | array>)
      statement
  ```

  variable 表示一个变量，可以在其前面附加 var 语句，用来直接声明变量名。in 后面是一个对象或数组类型的表达式。在遍历对象或数组过程中，把或取的每一个值赋值给 variable。

  然后执行 statement 语句，其中可以访问 variable 来读取每个对象属性或数组元素的值。执行完毕，返回继续枚举下一个元素，以此类推知道所有元素都被枚举为止。

  **对于数组来说，值是数组元素的下标；对于对象来说，值是对象的属性名或方法名。**

  ```js
  for ( var variable of object)
      statement
  ```

  for...of是es6标准，用来遍历value值，遍历数组，不能遍历普通对象

+ while / do while

  ```js
  // while
  while (expr)
      statement
  // do while
  do
      statement
  while(expr)
  ```

  do/while 与 while 循环非常相似，区别在于表达式的值是在每次循环结束时检查，而不是在开始时检查。因此 do/while 循环能够保证至少执行一次循环，而 while 循环就不一定了，如果表达式的值为假，则直接终止循环不进入循环。

  ```js
  /*
  * 假如投资的年利率为5%，试求从1000块增长到5000块，需要花费多少年
  * 
  * 1000 1000*1.05
  * 1050 1050*1.05
  */
  
  //定义一个变量，表示当前的钱数
  var money = 1000;
  			
  //定义一个计数器
  var count = 0;
  			
  //定义一个while循环来计算每年的钱数
  while(money < 5000){
    money *= 1.05;
  				
    //使count自增
    count++;
  }
  
  //console.log(money);
  console.log("一共需要"+count+"年");
  ```

  

+ break / continue

  JavaScript break 和 continue 关键字都可以用在 for 和 while 循环结构中，表示跳出循环；break 关键字还可以用在 switch case 选择结构中，表示结束当前的选择语句。

  **break 和 continue 关键字可以在中途改变循环结构和分支结构的流程方向。**

  break 语句能够结束当前 for、for/in、while、do/while 或者 switch语句的执行；

  continue 语句用在循环结构内，用于跳过本次循环中剩余的代码，并在表达式的值为真时，继续执行下一次循环。

#### 函数

##### 声明：函数声明后不会立即执行，只有在手动调用的时候执行

```js
// 方式一：function 关键字
function func(params){
  代码体
}
// 方式二：表达式
var func = function (params) {
  代码体
}
// 两个声明方式都可以
// 不同点：
   1、表达式方式的函数实际上是一个 匿名函数 (函数没有名称)。函数存储在变量中，不需要函数名称，通常通过变量名来调用。
   2、表达式方式的函数没有函数提升的现象，如：
```

```js
func() // Uncaught TypeError: func is not a function
var func = function () {
  console.log('表达式函数')
}
// 以上代码由于先进行了调用，但表达式函数没有函数提升的功能，所以会报错
```

```js
func() // function 函数
function func () {
  console.log('function 函数')
}
// 以上代码由于是function函数声明的，所有会有函数提升的功能，所以即使先调用了func()函数，也可以正常执行
```

##### 调用：函数只有在调用的时候才会被执行

```js
// 普通调用(常用)
function func () {
  console.log('函数被调用了')
}
func() // 函数被调用了


// 自调用函数： IIEF
(function (){
  console.log('IIEF函数执行了')
})() // IIEF函数执行了
```

##### 参数

```js
// 形参
function func (a, b) { //
  return a + b;
}
//a,b两个参数在函数定义的时候声明，用来接收函数调用的时候传入的参数，我们称之为形参。相当于在函数内部声明了两个变量

//实参
func(1, 2) // 2
//1,2 两个参数在函数调用的时候传入具有实际意义的数据，分别对应着函数声明的时候定义的a,b两个变量。我们称之为实参
```

```js
//参数的对应
//在js中，一般情况下形参的位置和实参的位置一一对应，不过在参数的数量上可以不相等，如形参比实参多，或者是实参比形参多，分开讨论：
//1、形参的个数大于实参的个数，也就是说在调用函数的时候，传入的参数不如声明函数的时候形参数量多：
function func (a, b, c) {
  return a + b + c
}
func(1, 2) // 3
// 如果形参的个数比实参多，会按照位置一一对应，没有对应的形参默认值为：undefined，上面的func函数的参数最终为：a=1, b=2, c=undefined
//2、形参的个数小于实参的个数，也就是说在调用函数的时候，传入的参数比如声明函数的时候形参数量多：
function func (a, b) {
  return a + b
}
func(1, 2, 3) // 3
// 如果形参的个数比实参多，会按照位置一一对应，多余的实参会在arguments对象里面存着
```

```js
// 函数内部的变量：arguments
// 每个函数在内部都有两个变量：arguments, this（后面会说到）
// arguments对象（注意：arguments是一个对象，而不是一个数组）会把调用时候传入的所有参数保存下来，其作用就是为了函数有不定数目的参数。
// 需要注意的是，虽然arguments很像数组，但它是一个对象。数组专有的方法（比如slice和forEach），不能在arguments对象上直接使用。
function func (a, b) {
  console.log(arguments) // Arguments(6): [1, 2, 3, 4, 5, 6]
  return a + b
}
func(1, 2, 3, 4, 5, 6) // 3
```

##### 返回值

```js
// 每一个函数都会有返回值，如果不写 return 会默认返回 undefined
function func () {
  console.log('function')
}
var result = func()
console.log(result) // undefined
```

```js
// 如果显示的写 return 则后面跟的 就是function函数的返回值，如果只写一个 return 则返回的是 undefined，return关键字有结束函数的作用，可以用来做流程控制
function func(a, b) {
  return a + b
}
var result = func(1, 2)
console.log(result) // 3

function test(a) {
  if (a < 0){
    return
  }
  return a + a
}
var result = test(-1)
console.log(result) // undefined
var result = test(1)
console.log(result) // 2
```

##### 函数作用域

作用域（scope）指的是变量存在的范围。在 ES5 的规范中，JavaScript 只有两种作用域：一种是全局作用域，变量在整个程序中一直存在，所有地方都可以读取；另一种是函数作用域，变量只在函数内部存在。ES6 又新增了块级作用域，后面会讲到

```js
// 对于在函数外部声明的变量，则是全局作用域，函数是可以访问的，我们称之为 ‘全局变量’
var a = 10
function func(){
  console.log(a)
}
func() // 10
// 上面的代码，在函数外部定义了一个变量a，则可以在函数内部使用
```

```js
// 变量覆盖
var a = 10
function func(){
  var a = 20
  console.log(a)
}
func() // 20
// 上面的代码，如果在函数内部同样定义了一个与外部同名的变量，则会覆盖外部的变量，结果会输出：20
```

```js
// 如果在函数内部定义的变量，则作用域只能是在函数内部，在外部调用是没法访问的，我们称之为 '局部变量'
function func(){
  var a = 20
}
console.log(a) //  ReferenceError: a is not defined
```

```js
// 如果在函数内部定义的变量，没有用 var let const 关键字声明，则相当于定义了一个全局的变量
function func() {
  a = 10
  console.log(a)
}
func() // 10
console.log(a) // 10


//上面代码相当于：
var a = 10
function func(){
  console.log(a)
}
func() // 10
console.log(a) // 10
```

```js
// 函数内部变量提升
function func(){
  console.log(a)
  var a = 10
}
func() // undefined
// 注意：只能是用 var 关键字定义的变量才会有变量提升的现象，用 let 或者是 const 定义的则不会，如果上面的代码把 var 换成 let 或者 const 则运行会报错：uncaught ReferenceError: Cannot access 'a' before initialization 
// 上面代码运行结果之所以会是undefined，是因为在函数内部用 `var` 关键字声明的变量会有变量提升的现象，上面代码和下面的代码是一样的：
function func(){
  var a = undefined
  console.log(a)
  a = 10
}
// 所以会是 undefined
```

##### 传递方式：值传递，引用传递

```js
// 值传递：函数参数如果是原始类型的值（数值、字符串、布尔值），传递方式是传值传递。这意味着，在函数体内修改参数值，不会影响到函数外部。
var a = 10
console.log(a) // 10
function func(x){
  x = 20
  console.log(x) // 20
}
func(a)
console.log(a) // 10
// 上面代码的传递方式是值传递：相当于把变量 a 复制了一份传递给了函数 func ，在函数内部怎么更改，都不会影响外面的变量 a 
```

```js
// 引用传递：复合类型的值（数组、对象、其他函数），传递方式是传址传递。也就是说，传入函数的原始值的地址，因此在函数内部修改参数，将会影响到原始值
var a = {
  name: '张三',
}
console.log(a) // { name: '张三' }
function func(person) {
  person.name = '李四'
  console.log(person) // { name: '李四' }
}
func(a)
console.log(a) // { name: '李四' }
// 上面代码的传递方式是引用传递：相当于把对象的地址传递给了函数 func，变量 a 和 person 都是指向同一对象，所以在func 里面改变对象同样会影响外面的对象，因为是同一个对象 

// 注意：如果是在函数内部更改对象的属性，则会影响整个对象，如果是替换了整个参数对象，则就不会对影响原始值
var a = {
  name: '张三',
}
console.log(a) // { name: '张三' }
function func(person) {
  person = {
    name: '李四',
  }
  console.log(person) // { name: '李四' }
}
func(a)
console.log(a) // { name: '张三' }
// 上面代码不会影响 a ，原因是在函数内部直接把 person 赋值了一个新的对象，而  a 不会影响
```

#### 对象

对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合

##### 定义

```js
// 感知一下什么是对象
var person = {
  name: '张三',
  age: 10
}
// 上面代码，大括号就定义了一个对象，它被赋值给变量person，这个对象有两个成员（两个键值对），name 和 age 是键，'张三' 和 10 是具体的值， 两者之间用 ' : ' 隔开，成员之间（键值对）之间用 ',' 隔开，最后一个成员后面可以不用加逗号，也可以加。 
```

##### 键名

```js
// 键名：
// 对象的所有键名都是字符串，如果在书写时没有加引号，则会自己动转成字符串，但如果不符合命名规则的，则不会自动转成字符串，必须手动加上引号。
var person = {
  name: '张三',
  'age': 10
}
console.log(person) // { name: '张三', age: 10 }

//不合法的命名，必须手动添加引号
// 这样的会报错
var person = {
  +name: '张三',
  a ge: 10
}
//这样不会
var person = {
  '+name': '张三',
  'a ge': 10
}
```

##### 键值

```js
// 键值：
// 对象的键名又称为忏悔，它的键值可以是任何类型的数据，如果键值是一个函数类型的，则我们把这个属性称为方法，可以通过对象进行调用：
var person = {
  name: '张三',
  sayHello: function(){
    console.log('hello')
  }
}
person.sayHello() // hello

// 对象的属性可以在动态创建，不一定在声明的时候指定
var person = {}
person.name = '张三'
person.age = 10
person.sayHello = function(){
  console.log('hello')
}
console.log(person) // {age: 10, name: "张三", sayHello: ƒ ()}
```

##### 引用类型

对象是一个引用类型的数据结构，如果两个不同的变量指向同一个对象，则其中一个变量修改了对象的内部结构，都会引起另一个变量的改变，因为这两个变量都是引用的同一对象

```js
var person = {
  name: '张三',
  age: 10
}
console.log(person) // { name: '张三', age: 10 }
var p = person
p.name = '李四'
console.log(person) // { name: '李四', age: 10 }
```

##### 获取属性

```js
// 读取对象属性有两种方式：
// 1、通过 . 运算符获取
// 2、通过 [] 运算符获取
var person = {
  name: '张三',
  age: 10
}
var name = person.name
console.log(name) // 张三
var age = person['age']
console.log(age) // 10
// 注意：
// 通过点运算符获取值的时候不用加引号: person.name 不可以写成 person.'name'
// 通过方括号运算符获取值的时候则必须加引号: person['age']，如果不加引号，会被引擎理解为一个变量 age，如：
var name = 'foo'
var person = {
  name: '张三',
  age: 10
}
var value = person[name] 等价于：var value = person['foo']
// 方括号里可以进行表达式：
var value = person['na' + 'me'] 等价于：var value = person['name']
var value = person[1 + 2] 等价于：var value = person[3] // 3 会被自动转成 字符串

//如果对象的属性名中数值，则不能使用点运算符，只能使用方括号
var person = {
  123: '张三'
}
person.123 // 报错
person[123] // 张三
```

##### 赋值属性

```js
// 如果点运算符或者方括号运算符后面带着等号运算符，则是给这个属性赋值
var person = {
  name: '张三',
  age: 10
}
console.log(person) // { name: '张三', age: 10 }
person.name = '李四'
person['age'] = 20
console.log(person) // { name: '李四', age: 20 }
```

##### 获取所有的键名（属性）

```js
// 可以通过 Objec.keys方法获取一个对象中的所有的属性名称
var person = {
  name: '张三',
  age: 10
}
var keys = Object.keys(person)
// 返回是一个字符串数组，里面包含着所有的对象属性名
console.log(keys) //  ["name", "age"]
```

##### 删除属性

```js
// 可以用 delete 关键字删除某个对象的某个属性，返回一个boolean
var person = {
  name: '张三',
  age: 10
}
console.log(person) // { name: '张三', age: 10 }
delete person.age // true
console.log(person) // { name: '张三' }
```

##### 检查是否包含某个属性

```js
// 通过 ' in '关键字可以检查一个属性是否存在，注意：是检查键名，而不是键值
var person = {
  name: '张三',
  age: 10
}
var result = 'name' in person
console.log(result) // true
result = 'address' in person
console.log(result) // false
// 注意：in 关键字会检查自身的和继承父类的属性，所以不能通过 in 判断某个属性是否在某个对象中，如：
result = 'toString' in person
console.log(result) // true
// toString 是继承的object的，通过 in 关键字也会检查出
```

```js
// 可以通过对象的hasOwnProperty方法来检测一个属性是否存在于自身中，而不会检测从父类继承过来的属性
var person = {
  name: '张三'
}
var result = person.hasOwnProperty('name') // true
console.log(result)
result = person.hasOwnProperty('toString') // false
console.log(result)
```

##### 属性的遍历

```js
// 可以使用 for in 来遍历某个对象的属性
// 它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。
// 它不仅遍历对象自身的属性，还遍历继承的属性。
var person = {
  name: '张三',
  age: 10
}
for( var key in person ){
  console.log(key) // 依次输出 name age
}
// 也可以能过 Object.keys()获取所有的属性之后，再遍历 属性数组
var keys = Object.keys(person)
for(var i = 0; i < keys.length; i++) {
  console.log(person[keys[i]])
}
```

#### 字符串

##### 定义

字符串就是零个或多个排在一起的字符，放在单引号或双引号之中。一般我们都是用单引号，最好是统一使用一种风格的，要么都是使用单引号要么都是使用双引号，不要一会用单引号一会使用双引号。

```js
// 如果要在单引号字符串的内部，使用单引号，就必须在内部的单引号前面加上反斜杠，用来转义。双引号同理
var str = 'this is \'string\''
```

```js
// 字符串默认只能写在一行内，分成多行将会报错
var str = 'a
b
c'
// 以上代码会报错
```

##### 字符串与数组

```js
// 字符串可以看作是一个字符数组
var str = 'hello'
console.log(str[0]) // h
// 如果数组下标越界了或者不是一个数字，则会输出undefined
console.log(str[10]) // undefined
console.log(str['a']) // undefined

//注意：字符数组不改动态改变元素，也不能删除元素
str[0] = 'a' // 对于str还是无效的
delete str[0] // 对于str是无效的
```

##### length 属性

```js
// length 属性是返回字符的长度，是一个只读的属性，这就意味着我们不改变length的值，在字符串被创建了就会有此属性
var str = 'hello'
console.log(str.length) // 5
```



### 高级进阶

#### 构造函数，this 指针，new 关键字

#### 构造函数

**重新理解什么是对象：**

​     Javascript语言是一门面象对象的语言，我们现在主流的的开发思想都是面象对象编程（Object Oriented Programming，简称：OOP），要开发一个复杂的系统，则需要把一个个复杂的关系，抽象成一个个对象，然后每个对象互相分工与合作，从而完成一个复杂的系统。

​    在我们的现实生活中，对象无处不在，一辆车，一本书，一个人等等都是对象，对于我们前端程序员来说一张网页，一个window，一个**div**， 一个**css**样式 等都可描述成对象 

​    对象是一个容器，封装了不同的属性与方法，如

```js
var person = {
  name: '张三',
  age: 10,
  sayInfo: function (){
    console.log(this.name, this.age)
  }
}
// 对于上面的代码来说，就定义一个person对象，name和age是对象的属性状态，而sayInfo则是对象的方法，和现实联系在一起就是：一个人有一个姓名和年龄两个状态，这个人有个功能会说出自己的姓名和年龄
```

我们都知道，js中对象是生成的，那么生成这个对象的就需要一个模板，通过这个模板就能生成一个个的对象，在编程中我们把这个模板称之为 **类**，通过个**类**生成的对象，就称为类的实例。

在Java或者C#等一些语言中都有类的概念，在Javascript中则是通过函数来描述类这个概念，我们称这样的函数为**构造函数**  

构造函数就是一个普通的函数，通常我们把构造函数的第一个字母大写：

```js
var Person = function () {
  this.name = '张三'
  this.age = 10
}
// 定义了一个Person构造函数，首字母是大写的
// 函数体里面的 this 就代表了以后我们要生成的实例对象。
// 要通过构造函数生成实例对象，就得用 new 关键字，下面开始介绍 new 关键字
```

#### new 关键字

##### 基本用法

```js
function Person(){
  this.name = '张三'
}
var p = new Person()
console.log(p.name) // 张三

// 上面代码，就是通过 new 关键字，以Person函数为模板生成的对象，然后再把对象的地址赋值给了变量 p ，在函数内部 this.name意思就是为对象创建了一个 name 属性，值为 '张三'
```

如果忘记写 new 关键字，则函数就是一次普通的调用：

```js
function Person(){
  this.name = '张三'
}
var p = Person()
console.log(p) // undefined
console.log(p.name) // 报错：Uncaught TypeError: Cannot read property 'name' of undefined

//注意：访问 name 属性，还是可以访问到的，相当于在全局定义了一个 name 变量，而 this 则是指向的 window 对象，其实就是给window对象动态添加了一个 name 属性，
```

##### new 关键字的流程原理

当使用new关键字创建一个对象的时候会依次执行以下几个步骤：

1. 创建一个空对象，这个空对象就是要返回的对象
2. 把空对象的原型指向构造函数的prototype属性，即：____proto____ = prototype
3. 把空对象的地址赋值给构造函数的this关键字
4. 执行构造函数的函数体

##### 返回值的问题

对于函数来说，运行完之后，都会有一个返回，如果没有明确写return xxx，或者只写了一个return 都会返回undefined。但是对于构造函数来说，有点区别：

1. 如果不写 **return** 默认返回 **this** 所指的对象
2. 如果只写了 **return** 也是返回 **this** 所指的对象
3. 如果**return**后面跟着是一个**对象**，则返回这个**对象**
4. 如果**return**后面跟着是一个非对象，则返回**this**所指的对象

**总结：也就是说只有当 return 后面是一个对象的时候，才会返回这个对象，否则其它情况下都是返回 this 指针所指的对象**

#### 实例对象，继承，原型链，静态方法，Objec对象

