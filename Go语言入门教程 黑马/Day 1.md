* # **Go语言介绍**

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

  * 

    ![https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%201.assets/1.png](E:\selfgit\golang\Go语言入门教程 黑马\Day 1.assets\1.png)

  * ### 安装过程

    一路Next,注意路径不要有中文

  * ### 判断是否安装完成

    * 通过cmd，调用go version 
    * 调用go env

  * ### Go语言环境测试

    ![](E:\selfgit\golang\Go语言入门教程 黑马\Day 1.assets\2.png)

  * ### LiteIDE安装

    * ### url

      https://sourceforge.net/projects/liteide/files/x37.3/

    * ### 版本

      ![](E:\selfgit\golang\Go语言入门教程 黑马\Day 1.assets\3.png)

      

    * ![](E:\selfgit\golang\Go语言入门教程 黑马\Day 1.assets\4.png)

      ctrl + r  运行代码

  * ## **学习资料**

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

    