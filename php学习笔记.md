### php学习笔记

+ #### 变量的作用域

  1. 全局变量：就是用户定义的变量

     + 在php中只能全局空间使用，理论上函数内部是不可以访问的，这一点与`javascript`不一样

     + 当前脚本运行完成，变量才被释放

  1. 局部变量：就是函数内部定义的变量

     + 只能在当前函数中访问使用

     + 当前函数运行完成，变量被释放

  2. 超全局变量：就是系统自己定义的变量，如：`$_SERVER`, `$_POST`等九大变量

     + 在php的任意地方都可以访问使用

+ #### 在函数内部如何访问全局变量？

  1. 通过超全局变量`$GLOBAL`来访问。全局变量本质上就是给`$GLOBAL`数组添加元素，如：

     ```php
     <?php
         $a = 'this is a'; // 相当于: $GLOBALS['a'] = 'this is a'
     	function func () {
             // 在函数内部使用方式如下：
             echo $GLOBALS['a'];
         }
     	func();
     ```

  2. 通过形参实参的形式：

     ```php
     <?php
         $a = 'this is a';
     	function func ($paramsA) {
             echo $paramsA;
         }
     	func ($a);
     	// 或者通过引用传值的方式
     	//function func (&$paramsA) {
         //    echo $paramsA;
         //}
     	//func ($a);
     ```

  3. 通过`global`关键字：

     1. 如果函数外部有定义变量，则直接把 `global $a`指向外部`$a`的地址

     ```php
     <?php
     	$a = 'this is a';
     	function func () {
             global $a;
             echo $a;
         }
     	func();
     ```

     2. 如果函数外部没有定义变量，则会定义一个变量，内外部都可以访问

        ```php
        <?php
        	function func () {
                global $a;
            	$a = 'this is a';
                echo $a;
            }
        	func();
        	echo $a;
        ```

        **综上：如果真的需要内外部的互访，推荐使用函数参数的形式。**