### 一.  go安装

1. 去<https://golang.google.cn/dl/> 下载go的安装包，window的默认是.msi结尾的
2. window安装的时候默认会把go加入到环境变量中,GOROOT是go语言的安装目录，GOPATH这是自己go的工作目录，可以设置多个使用;分隔开,当编写go程序时不指定GOPATH,则默认使用第一个。
3. 重启doc窗口，输入go version,如果出现go的版本信息，则证明go已经安装完成

### 二. go的hello world

将文件命名为hello.go,并进行保存

```go
package main

import "fmt"

func main() {
   fmt.Println("Hello, World!")
}
```

进入hello.go的目录中，在doc窗口输入go run hello.go,将会输出**Hello,World!**

至此，我们就完成了go的简单入门

### 三. go语言结构

1. go语言的基础组成有以下几个部分：

   - 包声明
   - 引入包
   - 函数
   - 变量
   - 语句 & 表达式
   - 注释

2. helloworld程序的解析

   ```go
   package main
   /*
   	package main定义了包名，必须在源文件中非注释的第一行指明
   	package main表示一个可独立执行的程序，每个go应用程序都包含一个名为main的包
   */
   //告诉go编译器这个程序需要引入fmt包，fmt包实现了格式化IO的函数
   import "fmt"
   
   //程序开始执行的函数，main函数式每一个可执行程序所必须包含的，一般来说都是在启动后第一个执行的函数（如果有 init() 函数则会先执行该函数）
   func main() {
       //可将字符串输出到控制台
      fmt.Println("Hello, World!")
      /**
      	当变量或者函数以一个大写字母开头，这个变量或者函数可以被外部的包引用，相等于public修饰的变量，当为小写字母开头的时候，只能在真个包内部可见，相等于protected修饰的变量
      */
   }
   ```

### 四. go语言基础语法

1. go中的变量定义

   ```go
   package main
   
   import "fmt"
   
   /**
   在函数外定义变量,使用var关键字，
   首字母小写的变量默认范围是本包内，首字母大写的作用为是全局的
   int 默认值为0，string默认值为空字符串
   go中变量名在前，变量类型在后
   */
   
   var aa int
   var ss string = "23"
   
   /**
   go可以自动进行类型推断，省略掉变量类型
   省略掉类型的变量需要付初始值
    */
   var a = 12
   var s = "sd"
   
   //可以使用()同时初始化多个变量
   var (
   	ab = 13
   	cd = "12"
   )
   
   //在函数内给变量赋值,使用func定义函数
   func val()  {
   	//定义的变量必须使用
   	var ss = "12"
   	//也可以使用:= 给变量赋值
   	vs := 23
   	fmt.Println(ss, vs)
   }
   
   func main() {
   	fmt.Println(aa, ss)
   	fmt.Println(a, s)
   	fmt.Println(ab, cd)
   	val()
   }
   
   ```

2. go内置变量类型

   | 类型                | 描述                             |
   | ------------------- | -------------------------------- |
   | 布尔型(bool)        | 只能是true或者false              |
   | 数字类型            | int,float32,float64等            |
   | 字符串类型(string)  | 使用UTF-8编码标识的Unicode文本   |
   | 派生类型            | 不是基本类型(以下都属于派生类型) |
   | 指针类型(Pointer)   |                                  |
   | 数组类型            |                                  |
   | 结构化类型(struct)  |                                  |
   | 函数类型            |                                  |
   | 切片类型            |                                  |
   | 接口类型(interface) |                                  |
   | Map类型             |                                  |

   所有的数字类型，包含基本类型和浮点数字类型，复数数字类型

   | 类型       | 描述                         |
   | ---------- | ---------------------------- |
   | unit8      | 无符号8位整型                |
   | unit16     | 无符号16位整型               |
   | uint32     | 无符号32位整型               |
   | uint64     | 无符号64位整型               |
   | int8       | 有符号8位整型                |
   | int16      | 有符号8位整型                |
   | int32      | 有符号8位整型                |
   | int64      | 有符号8位整型                |
   | float32    | 32位浮点型                   |
   | float64    | 64浮点型                     |
   | complex64  | 32位实数和虚数               |
   | complex128 | 64位实数和虚数               |
   | byte       | 类似uint8                    |
   | rune       | 类似int32,相等于java中char   |
   | uint       | 32或者64位                   |
   | int        | 与uint一样                   |
   | uintptr    | 无符号整型，用于存放一个指针 |

   go中的所有数据类型都需要强制转换

   ```go
   package main
   
   import (
   	"math/cmplx"
   	"fmt"
   	"math"
   )
   
   func euler()  {
   	c := 3 + 4i
   	fmt.Println(cmplx.Abs(c))
   	//欧拉公式,各个编程语言对浮点数的操作都会有精度损失
   	fmt.Println(cmplx.Pow(math.E, 1i * math.Pi) + 1)
   	fmt.Printf("%.3f\n", cmplx.Exp(1i * math.Pi) + 1)
   }
   
   /**
   	在go语言中，不存在隐式类型转换
   	都需要强制把变量类型转换成他需要的
    */
   func triangle()  {
   	var a, b int = 3, 4
   	var c int
   	c = int(math.Sqrt(float64(a * a + b * b)))
   	fmt.Println(c)
   }
   
   func main() {
   
   	euler()
   	triangle()
   	
   }
   
   ```

3. go中的常量和枚举，go中使用**const**来定义常量，go中没有原生的枚举类型，使用一组常量来表示enum

   ```go
   package main
   
   import "fmt"
   
   /**
   	go中使用const关键字来定义常量
   	常量不需要全部大写
   	当定义的常量不指定类型时，它会在使用时自动推断类型
   	比如3,4可以使int，也可以是float
    */
   func constes()  {
   	const filename = "abc.txt"
   	const a, b = 3, 4
   
   	fmt.Println(filename, a, b)
   }
   
   /**
   	go中没有原生的枚举类型
   	使用一组const定义的常量来表示枚举
   	可以使用iota递增的表示一组枚举
    */
   func enum()  {
   	const (
   		java = 1
   		python = 2
   		php = 3
   		javascript = 4
   	)
   
   	fmt.Println(java, python, php, javascript)
   
   	const (
   		java1 = iota
   		python1
   		php1
   		javascript1
   	)
   
   	fmt.Println(java1, python1, php1, javascript1)
   
   }
   
   func main() {
   	constes()
   
   	enum()
   }
   
   ```

4. go中条件分支，常用的还是**if，else；switch，case**这些，只是和java等有一些细微的差别

   ```go
   package main
   
   import (
   	"io/ioutil"
   	"fmt"
   )
   
   /**
   	case后面的break可以省略，默认添加
    */
   func swithtest(i int)  {
   	switch i {
   	case 1:
   		fmt.Println("one")
   	case 2:
   		fmt.Println("two")
   	case 3:
   		fmt.Println("three")
   	default:
   		fmt.Println("other")
   	}
   }
   
   /**
   	switch后面的表达式可以省略
   	在case后面进行条件判断
    */
   func scores(score int) string {
   	result := ""
   	switch {
   	case score < 60:
   		result = "F"
   	case score < 80:
   		result = "C"
   	case score < 90:
   		result = "B"
   	case score <= 100:
   		result = "A"
   	default:
   		panic("socre is err")
   	}
   
   	return result
   }
   
   func main() {
   
   	const filename  = "abc.txt"
   	content, err := ioutil.ReadFile(filename)
   	//go中的if后面不需要写()
   	if err != nil {
   		fmt.Println(err)
   	} else {
   		fmt.Printf("%s\n", content)
   	}
   
   	//还可以这样来写if表达式
   	if content, err := ioutil.ReadFile(filename); err != nil {
   		fmt.Println(err)
   	} else {
   		fmt.Printf("%s\n", content)
   	}
   
   	swithtest(2)
   
   	fmt.Println(
   		scores(50),
   		scores(60),
   		scores(70),
   		scores(95),
   		scores(100),
   	)
   
   }
   
   ```

5. 在go中，只有**for**一个循环方式，for的初始条件，结束条件，递增条件等都可以省略

   ```go
   package main
   
   import (
   	"fmt"
   	"strconv"
   	"os"
   	"bufio"
   )
   
   /**
   	最普通的for循环，可以省略()
    */
   func common()  {
   	sum := 0
   	for i := 0; i <= 100; i ++  {
   		sum += i
   	}
   
   	fmt.Println(sum)
   }
   
   /**
   	省略了初始条件的for循环
    */
   func conventToBin(v int) string {
   	result := ""
   
   	for ; v > 0; v /= 2 {
   		result = strconv.Itoa(v % 2) + result
   	}
   
   	return result
   }
   
   /**
   	省略初始条件和递增条件
    */
   func printFile(filename string) {
   	conent, err := os.Open(filename)
   	if err != nil {
   		panic(err)
   	}
   
   	scanner := bufio.NewScanner(conent)
   
   	for scanner.Scan() {
   		fmt.Println(scanner.Text())
   	}
   }
   
   /**
   	所有条件都省略，就是所谓的死循环
    */
   func forever()  {
   	for {
   		fmt.Println("abc")
   	}
   }
   
   func main() {
   
   	common()
   	fmt.Println(
   		conventToBin(5),
   		conventToBin(13),
   		conventToBin(24),
   		conventToBin(30),
   	)
   
   	printFile("abc.txt")
   	forever()
   	
   }
   
   ```

6. 在go中使用func关键字来定义函数，跟定义变量一样，也是函数名在前，返回类型在后

   函数的返回值可以使多个，一般第二个返回值用来处理错误

   函数的参数也可以是一个函数

   函数的参数列表支持可变参数

   ```go
   package main
   
   import (
   	"fmt"
   	"math"
   )
   
   /**
   	使用func来定义函数
   	函数的返回值类型写在函数名之后
   	如果没有返回值，则可以省略不写
    */
   func eval(a int, b int, op string) int {
   	result := 0
   
   	switch op {
   	case "+":
   		result = a + b
   	case "-":
   		result = a - b
   	case "*":
   		result = a * b
   	case "/":
   		result = a / b
   	default:
   		panic("unsupporte operation " + op)
   	}
   
   	return result
   }
   
   /**
   	函数可定义多个返回值
    */
   func div(a, b int) (int, int)  {
   	return a / b, a % b
   }
   
   /**
   	函数可定义多个返回值,
   	可以给多个返回值命名,
   	一般多个返回值的第二个类型为error，用来返回错误信息
    */
   func divs(a, b int) (q, r int)  {
   	return a / b, a % b
   }
   
   /**
   	在go中，函数的参数也可以使一个函数
    */
   func apply(op func(int, int) int, a, b int) int {
   	return op(a, b)
   }
   
   /**
   	go支持可变参数
    */
   func sum(numbers ... int) int {
   	s := 0
   	for i := range numbers {
   		s += numbers[i]
   	}
   
   	return s
   }
   
   
   func main() {
   	fmt.Println(eval(3, 4, "*"))
   	fmt.Println(div(13, 4))
   	q, r := divs(13, 3)
   	fmt.Println(q, r)
   
   	//当存在多个返回值时，只想接受部分返回值，可以使用_来代替不想返回的值
   	s, _ := divs(9, 5)
   	fmt.Println(s)
   	
   	fmt.Println(apply(func(i int, i2 int) int {
   		return int(math.Pow(float64(i), float64(i2)))
   	}, 3, 4))
   
   	fmt.Println(sum(1, 2, 3, 4, 5))
   }
   
   ```

7. go中的指针和参数传递

   go的指针不能进行运算

   在go中，只有值传递，可以通过指针来实现引用传递

   ```go
   package main
   
   import "fmt"
   
   /**
   	由于是值传递，所以没有改变参数的值
    */
   func swap(a, b int)  {
   	b, a = a, b
   }
   
   /**
   	使用指针来改变参数的值
    */
   func swapPoint(a, b *int)  {
   	*b, *a = *a, *b
   }
   
   func main() {
   
   	//变量是一种使用方便的占位符，用于引用计算机内存地址
   	//在变量前面放入一个&,就可以方便的返回一个变量的地址值
   	i := 10
   	fmt.Println(&i)
   
   	//一个指针变量指向了一个变量的地址值，在对应的类型前面加上*来定义一个指针
   	var point *int
   	point = &i
   	fmt.Println(point)
   
   	/**
   		定义指针变量。
   		为指针变量赋值。
   		访问指针变量中指向地址的值。
   	 */
   
   	 pi := 20   //声明一个变量
   	 var po *int     //声明一个指针
   	 po = &pi        //给一个指针赋值
   	 fmt.Println(po) //访问指针变量的地址
   	 fmt.Println(*po) //访问指针变量的值，需要在指针的变量前加一个*号
   
   	 a, b := 1, 3
   	 swap(a, b)
   	 fmt.Println(a, b)
   	 swapPoint(&a, &b)
   	 fmt.Println(a, b)
   }
   
   ```

   

