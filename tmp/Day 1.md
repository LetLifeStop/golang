* # Go语言介绍

  * 为了让程序员有更高的生产效率，Go语言专门针对多处理器系统应用程序的编程进行了优化，使用Go语言不仅速度快，而且更加安全，支持并行进程
  * 优势
    * 不依赖其他库
    * 静态类型语言，但是有动态语言的感觉。静态类型指的是在编译的时候就能检测出来大多数问题，动态语言的感觉就是可以有很多的包可以使用
    * 内置runtime（内存管理），支持垃圾回收
    * 支持并发
    * 跨平台编译
  * Go适合用来做什么
    * 服务器编程
    * 分布式系统
    * 网络编程
    * 内存数据库
    * 云平台

* ## **环境搭建**

  * ### 下载链接

    * https://studygolang.com/dl

  * ### 下载版本

    [图片示例](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%201.assets/1.png)

  * ### 安装过程

    一路Next,注意路径不要有中文

  * ### 判断是否安装完成

    * 通过cmd，调用go version 
  * 调用go env
  
  * ### Go语言环境测试

    [图片示例](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%201.assets/2.png)

  * ### LiteIDE安装

    * ### url

      https://sourceforge.net/projects/liteide/files/x37.3/

    * ### 版本

      [图片示例](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%201.assets/3.png)

    * 运行测试

      [图片示例](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%201.assets/4.png)
  
      **学习资料**

    * Go语言官网： https://golang.org/

  * Go中文社区：https://studygolang.com
    
    * Go中文在线文档：https://studygolang.com/pkgdoc
  
* # 第一个Go程序

  ```go
  // 1. go语言以包为单位
  // 2. 每个文件必须先声明包
  // 3. 程序必须有一个main包
  package main
  
  import "fmt"
  
  func main() { // 左括号必须和函数名同行
  	fmt.Println("hello world")
  }
  ```

  notes:

  LitelIDE有一个缺点，就是同一目录下如果有多个go文件，如果存在多个main入口的话，会提示重复定义。

  解决方法：

  * 可以通过新建文件夹，在不同的文件夹下创建文件来解决

  * 通过cmd调用命令

    * 方法一

    1. 首先进入存储代码的目录下，cd命令，dir显示目录下存在哪些文件
    2. 通过go build命令编译go文件
    3. 执行生成的exe即可成功运行程序

    * 方法二:

      直接通过go run test.go命令运行代码，该过程不会生成exe可执行程序

      

* # 基础类型

  * ## 变量

    ```go
    package main
    
    import "fmt"
    
    func main() {
    	// 只是声明，没有初始化的变量，默认值为0
    	// 同一个{}里，生命的变量是唯一的
    	// 可以同时声明多个变量 var b, c int
    
    	var a int
    	fmt.Println("a = ", a)
    	// a = 0
    
    	//变量的初始化，声明变量时，同时赋值
    	var b int = 10
    	fmt.Println("b = ", b)
    	// b = 10
    
    	// 自动推导类型，必须初始化，通过初始化的值来确定类型
    	c := 30
    	fmt.Printf("c type is %T\n", c)
    	// c type is int
    
    	// printf和println的区别
    	// Println自动换行，Printf会把要输出的内容放在%x的位置
    	fmt.Println("c is ", c)
    	// c is 30
    
    	// 多重赋值
    	t1, t2 := 10, 20
    	fmt.Printf("t1 is %d, t2 is %d\n", t1, t2)
    	// t1 is 10, t2 is 20
    
    	t1, t2 = t2, t1
    	fmt.Println("t1 is", t1, "t2 is ", t2)
    	// t1 is 20, t2 is 10
    
    	// 匿名变量 “_“，丢去数据不做处理
    	var tmp int
    	tmp, _ = t1, t2
    	fmt.Println("tmp = ", tmp)
    	// tmp = 20
    ```

  * ## 常量

    ```go
    package main
    
    import "fmt"
    
    func main() {
    	//常量的声明，使用
    	const t4 int = 10
    	fmt.Println("t4 = ", t4)
    	// t4 = 10
    
    	// 如果没有提前定义就进行赋值的话，不需要添加冒号，即可提前判断类型
    	const t5 = 20.1
    	fmt.Printf("t5 type is %T\n", t5)
    	// t5 type is float64
    
    	// 多个变量定义
    	var t6 int
    	var t7 float64
    
    	// 上述两行代码可以简化成如下代码
    	/*
    		var (
    			t6 int
    			t7 float64
    		)
    	*/
    	t6, t7 = 10, 3.14
    	fmt.Println("t6 = ", t6)
    	fmt.Println("t7 = ", t7)
    	// t6 = 10
    	// t7 = 3.14
    
    	// 多个常量定义
    	const t8 int
    	const t9 float64 = 11.5
    
    	// 上述两行代码可以用如下代码代替
    	/*
    		const (
    			t8 int     = 11
    			t9 float64 = 11.5
    		)
    	*/
    
    	// 可以继续简化
    	/*
    		const (
    			t8  = 11
    			t9  = 11.5
    		)
    	*/
    
    	fmt.Println("t8 = ", t8)
    	fmt.Println("t9 = ", t9)
    	// t8 = 11
    	// t9 = 11.5
    }
    ```

    枚举iota
    
    ```go
    package main
    
    import "fmt"
    
    func main() {
    	// iota常量自动生成器，从0开始，搁一行每次自动加1
    	const (
    		a = iota
    		b = iota
    		c = iota
    	)
    	fmt.Printf("a = %d, b = %d, c = %d\n", a, b, c)
    	// a = 0, b = 1, c = 2
    
    	// iota遇到const，重置为0
    	const d = iota
    	fmt.Printf("d is %d\n", d)
    	// d is 0
    
    	const (
    		a1 = iota
    		b1
    		c1
    	)
    
    	fmt.Printf("a1 = %d, b1 = %d, c1 = %d\n", a1, b1, c1)
    	// a1 = 0, b1 = 1, c1 = 2
    
    	// 如果是同一行，值是相同的
    	const (
    		i          = iota
    		j1, j2, j3 = iota, iota, iota
    		k          = iota
    	)
    	fmt.Printf("i = %d, j1 = %d, j2 = %d, j3 = %d, k = %d\n",
    		i, j1, j2, j3, k)
    	// i = 0, j1 = 1, j2 = 1, j3 = 1, k = 2
    }
    
    ```
    
  * ## 基础类型分类
  
    [图片链接](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%201.assets/5.png)
  
    ps：[byte和rune的区别](https://www.jianshu.com/p/4fbf529926ca)，如果要存储汉字的话，byte存储不下，rune可以存储下。
  
    * 字符
  
      ```go
      package main
      
      import "fmt"
      
      func main() {
      	var ch byte
      	ch = 97
      	fmt.Printf("%c, %d\n", ch, ch)
      	// a, 97
      
      	ch = 'a'
      	fmt.Printf("%c, %d\n", ch, ch)
      	// a, 97
      
      	fmt.Printf("大写: %d, 小写: %d\n", 'A', 'a')
      	// 大写: 65, 小写: 97
      
      	fmt.Printf("大写转小写: %c\n", 'A'+32)
      	// 大写转小写：a
      
      }
      ```
    
    * 字符串
    
      ```go
      package main
      
      import "fmt"
      
      func main() {
      	var str string
      
      	// 字符串都是隐藏了一个结束符  '\0'
      	str = "a"
      	fmt.Println("str = ", str)
      	// str = a
      	
      	str = "hello go"
      	// 操作字符串的某个字符，下标从0开始
      	fmt.Printf("str[0] = %c, str[1] = %c\n", str[0], str[1])
      	// str[0] = h, str[1] = e
      }
      ```
    
    * 复数
    
      ```go
      package main
      
      import "fmt"
      
      func main() {
      	var t complex128
      	t = 2.1 + 3.14i
      	fmt.Println("t = ", t)
      	// t = (2.1 + 3.14i)
      
      	// 通过内建函数获取复数的实部和虚部
      	fmt.Println("real(t) = ", real(t), "imag(t) = ", imag(t))
      	// real(t) = 2.1 imag(t) = 3.14
      }
      ```
    
    * 格式化输出
    
      ```go
      package main
      
      import "fmt"
      
      func main() {
      	a := 10
      	b := "abc"
      	c := 'a'
      	d := 3.14
      
      	// 注意c的类型，对应的是acsii的值
      	fmt.Printf("%T, %T, %T, %T\n", a, b, c, d)
      	// int, string, int32, float64
      
      	fmt.Printf("a = %d, b = %s, c = %c, d = %f\n", a, b, c, d)
      	// a = 10, b = abc, c = a, d = 3.140000
      
      	// %v是自动匹配格式，注意字符的格式以及float的表示
      	fmt.Printf("a = %v, b = %v, c = %v, d = %v\n", a, b, c, d)
      	// a = 10, b = abc, c = 97, d = 3.14
      
      }
      ```
    
    * 类型转换
    
      ```go
      package main
      
      import "fmt"
      
      func main() {
      
      	// bool类型和int类型之间无法转换，即bool类型和int类型是不兼容类型
      	// int类型和char类型直接可以转换，即int类型和char类型是兼容类型
      	var ch byte
      	// 字符类型本质上就是整型
      	ch = 'a'
      	// 类型转换，把ch对应的ascii取出之后转换成int，再给t赋值
      	t := int(ch)
      	fmt.Println("t = ", t)
      	// t = 97
      }
      ```
    
    * 类型别名
    
      ```go
      package main
      
      import "fmt"
      
      func main() {
      
      	type bigint int64
      	var x bigint = 100
      
      	fmt.Printf("x type is %T\n", x)
      	// x type is main.bigint
      
      	type (
      		myint int
      		mystr string
      	)
      }
      
      ```
    
  * ## 流程控制
  
    * 选择结构
  
      ```go
      // 1. go语言以包为单位
      // 2. 每个文件必须先声明包
      // 3. 程序必须有一个main包
      package main
      
      import "fmt"
      
      func main() { // 左括号必须和函数名同行
      	// if支持一个初始化语句，初始化语句和判断条件通过分号隔开
      	if a := 11; a == 10 {
      		fmt.Println("a =", a)
      	} else if a == 12 {
      		fmt.Println("a = ", a)
      	} else {
      		fmt.Println("a = ", a)
      	}
      	// a = 11
      }
      ```
  
    * switch 
  
      ```go
      // 1. go语言以包为单位
      // 2. 每个文件必须先声明包
      // 3. 程序必须有一个main包
      package main
      
      import "fmt"
      
      func main() { // 左括号必须和函数名同行
      	// go语言保留了break关键字，如果不写break关键字的话，默认包含
      	num := 4
      	switch num {
      	case 1:
      		fmt.Println("按下的是1楼")
      	case 2:
      		fmt.Println("按下的是2楼")
      	case 3:
      		fmt.Println("按下的是3楼")
      	default:
      		fmt.Printf("按下的是%d楼\n", num)
      	}
      	// 按下的是4楼
          
          // 如果不想跳出switch的话，可以通过添加fallthrough关键字，当匹配成功之后，该case以及后面的case中的操作都会直接执行
          num := 2
          // 支持一个初始化
          // swtch num := 1; num{
      	switch num {
      	case 1:
      		fmt.Println("按下的是1楼")
      		fallthrough
          // 还可以改成case 2，3，4 多种条件
      	case 2:
      		fmt.Println("按下的是2楼")
      		fallthrough
      	case 3:
      		fmt.Println("按下的是3楼")
      		fallthrough
      	default:
      		fmt.Printf("按下的是%d楼\n", num)
      	}
      	// 按下的是2楼
          // 按下的是3楼
          // 按下的是2楼
      }
      ```
  
    * 循环语句 range
  
      ```go
      package main
      
      import "fmt"
      
      func main() {
      	str := "abc"
      	var i int = 0
      	for i, data := range str {
      		fmt.Printf("str[%d] = %c\n", i, data)
      	}
      
      	for i := range str {
      		fmt.Printf("str[%d] = %c\n", i, str[i])
      	}
      
      	for i, _ = range str {
      		fmt.Printf("str[%d] = %c\n", i, str[i])
      	}
          // 上述三个的输出相同
      	// str[0] = a
      	// str[1] = b
      	// str[2] = c
      }
      
      ```
  
    * goto
  
      ```go
      package main
      
      import "fmt"
      
      func main() {
          // goto只能在本函数中跳转
      	fmt.Println("1")
      	goto End
      	fmt.Println("2")
      End:
      	fmt.Println("3")
      	// 1
          // 3
      }
      ```
  
  * ## 总结
  
    大部分和c++非常像，就是在语法方面会有所不同。
  
    