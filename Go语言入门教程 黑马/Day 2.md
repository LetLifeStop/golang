* # 函数

  * ## 普通参数列表

    ```go
    package main
    
    import "fmt"
    
    // 还可以简写成如下
    func Myfunc(a, b string, c float64) {
    //func Myfunc(a string, b string, c float64) {
    	fmt.Println("a = ", a, "b = ", b, "c = ", c)
    }
    func main() {
    	Myfunc("123", "456", 3.14)
    	// a = 123 b = 456 c = 3.14
    }
    ```

  * ## 不定参数类型

    ```go
    package main
    
    import "fmt"
    
    // 注意不定参数要放在参数列表中的最后一个位置
    func Myfunc(args ...int) {
    	fmt.Println("len(args) = ", len(args))
    	for i := 0; i < len(args); i++ {
    		fmt.Printf("args[%d] = %d\n", i, args[i])
    	}
    }
    
    func main() {
    	Myfunc()
    	// len(args) = 0
    	
    	Myfunc(1)
    	// len(args) = 1
    	// args[0] = 1 
    	
    	Myfunc(1, 2)
    	// len(args) = 2
    	// args[0] = 1
    	// args[1] = 2
    	Myfunc(1, 2, 3
    	// len(args) = 3
    	// args[0] = 1
    	// args[1] = 2
    	// args[1] = 3
    }
    ```

  * 不定参数截取

    ```go
    package main
    
    import "fmt"
    
    // 注意不定参数要放在参数列表中的最后一个位置
    func Myfunc2(args ...int) {
    	for _, data := range args {
    		fmt.Println("data = ", data)
    	}
    }
    
    func Myfunc(args ...int) {
    	// 只把第0个到第2 - 1个作为参数传递
    	Myfunc2(args[:2]...)
    	// data = 1
    	// data = 2
    
    	//从第2个到最后一个作为参数传递
    	Myfunc2(args[2:]...)
    	// data = 3
    }
    
    func main() {
    
    	Myfunc(1, 2, 3)
    }
    ```

  * 关于go语言中有返回值的函数需要留意的写法

    ```go
    package main
    
    import "fmt"
    
    // 这里的result代表函数返回值的名称
    // 如果有返回值，那么必须在函数的内部添加return语句
    // 函数名称，根据约定，函数名首字母小写为private，大写为public
    func Myfunc() (result int) {
    	result = 666
    	return
    }
    
    // 如果只有一个返回值，且不声明返回值变量，那么你可以省略
    func Myfunc() int {
    	return 666
    }
    
    func main() {
    
    	ret := Myfunc()
    	fmt.Println("ret = ", ret)
        // ret = 666
    }
    ```

  * 函数类型

    ```go
    package main
    
    import "fmt"
    
    func Add(a, b int) int {
    	return a + b
    }
    
    // FuncType为定义的函数名
    // 这个函数名代表了func(int ,int) int 这个函数类型
    type FuncType func(int, int) int
    
    func main() {
        // 函数名代表的是一个变量，可以用来进行赋值
    	var fTest FuncType = Add
    	//fTest = Add
    	result := fTest(1, 2)
    	fmt.Println("result = ", result)
    }
    ```

  * 回调函数

    ```go
    package main
    
    import "fmt"
    
    type FuncType func(int, int) int
    
    func Add(a, b int) int {
    	return a + b
    }
    
    func Del(a, b int) int {
    	return a - b
    }
    
    // 回调函数，fTets参数为函数类型，这个函数就是回调函数
    // 多态，多种形态，调用同一个接口，不同的参数，可以实现不同的功能
    func Calc(a, b int, fTest FuncType) (result int) {
    	fmt.Println("Calc")
    	result = fTest(a, b)
        // 不建议写成 result = Add(a, b),这样就无法实现多态了
    	return
    }
    
    func main() {
    	a := Calc(1, 1, Del)
        // a := Calc(1, 1, Add)
    	fmt.Println("a = ", a)
    }
    ```
    
  * 匿名函数与闭包

    ```go
    package main
    
    import "fmt"
    
    func main() {
    
    	a := 10
    	str := "123"
    
    	// 匿名函数，没有函数名称和定义
    	f1 := func() {
    		fmt.Println("a = ", a)
    		fmt.Println("str = ", str)
    		// a = 10
    		// str = 123
    	}
    
    	// 调用定义的匿名函数
    	f1()
    
    	// 定义匿名函数并且调用
    	func() {
    		fmt.Printf("a = %d, str = %s\n", a, str)
    		// a = 10, str = 123
    	}()
    
    	// 带参数的匿名函数
    	f2 := func(i, j int) {
    		fmt.Printf("i = %d, j = %d\n", i, j)
    		// i = 10, j = 20
    	}
    	f2(10, 20)
    
    	// 定义带参数的匿名函数，同时调用
    	func(i, j int) {
    		fmt.Printf("i = %d, j = %d\n", i, j)
    		// i = 30, j = 40
    	}(30, 40)
    
    	// 匿名函数，定义之后主动调用，并且有返回值
    	x, y := func(i, j int) (max, min int) {
    		if i > j {
    			max, min = i, j
    		} else {
    			max, min = j, i
    		}
    		return
    	}(50, 60)
    
    	fmt.Printf("x = %d, y = %d\n", x, y)
    	// x = 60, y = 50
    }
    ```
    
  * 闭包的特点

    ```go
    package main
    
    import "fmt"
    
    // 返回值为一个匿名函数，返回一个int类型的函数
    func test() func() int {
    	var x int
    
    	return func() int {
    		x++
    		return x * x
    	}
    }
    
    func main() {
        // f来调用闭包函数
        // f并不关心这些捕获了的变量和常量是否已经超出了作用域
        // 只要还有闭包在使用它，这些变量就还会存在，并且记录值
    	f := test()
    	fmt.Println(f())
    	// 1
    	fmt.Println(f())
    	// 4
    	fmt.Println(f())
    	// 9
    	fmt.Println(f())
    	// 16
    	fmt.Println(f())
    	// 25
    
    	f2 := test()
    	fmt.Println(f2())
    	// 1
    	fmt.Println(f())
    	// 36
        
        // f进行再次重新赋值，之前存储的值即不存在
        f = test()
    	fmt.Println(f())
    	// 1
    	fmt.Println(f())
    	// 4
    }
    ```

  * defer的使用

    ```go
    package main
    
    import "fmt"
    
    func test(x int) {
    	var t int = 100 / x
    	fmt.Println("t = ", t)
    }
    
    func main() {
    	// 按照最后进来的最先执行的原则
    
    	defer fmt.Println("111")
    	// 如果函数出现问题，这个函数之前的defer也能按照顺序使用
        // 对于这个函数的后面，因为这个程序按照这个流程已经在test函数卡住了，所以后面的defer就不会调用
    	// test(0)
    	// 111
    	defer fmt.Println("222")
    	defer fmt.Println("333")
    	// 333
    	// 222
    	// 111
    }
    
    
    func main() {
    
    	defer fmt.Println("111")
        // 但如果把test(0)改成defer test(0), 所有的defer都会调用, 即使这个test(0)有bug
    	defer test(0)
    	defer fmt.Println("222")
    	defer fmt.Println("333")
        
    	// 333
    	// 222
    	// 111
    }
    ```

  * defer和匿名函数结合使用

    ```go
    package main
    
    import "fmt"
    
    func main01() {
    	a := 10
    	b := 20
    
    	defer func() {
    		fmt.Printf("defer: a = %d, b = %d\n", a, b)
    	}()
    	// 函数的调用是在main结束之前
    	// 但是这里的参数的值是main函数结束之前的a，b的值
    	
    	a = 11
    	b = 22
    	fmt.Printf("main: a = %d, b = %d\n", a, b)
    
    	// main: a = 11, b = 22
    	// defer: a = 11, b = 22
    }
    
    func main() {
    	a := 10
    	b := 20
    
    	defer func(a, b int) {
    		fmt.Printf("defer: a = %d, b = %d\n", a, b)
    	}(a, b)
    	// 匿名函数虽然是在main函数结束之前被调用
    	// 但是a，b的值作为函数参数的值是在该函数上方的代码中a，b的值
    	
    	a = 11
    	b = 22
    	fmt.Printf("main: a = %d, b = %d\n", a, b)
    
    	// main: a = 11, b = 22
    	// defer: a = 10, b = 20
    }
    ```

  * 获取命令行参数

    ```go
    package main
    
    import (
    	"fmt"
    	"os"
    	// 需要引入新的包
    )
    
    func main() {
    	// 通过字符串的方法来接受用户传递的参数
    	str_arg := os.Args
    	
    	n := len(str_arg)
    	fmt.Println("len of str_arg is ", n)
        
        for k, v := range str_arg {
    		fmt.Printf("the key is %d, the val is %s\n", k, v)
    	}
        // len of str_arg is  4
    	// the key is 0, the val is test.exe
    	// the key is 1, the val is 1
    	// the key is 2, the val is 2
    	// the key is 3, the val is 3
    }
    
    ```

  * 局部变量

    ```go
    package main
    
    import (
    	"fmt"
    )
    
    func main() {
    
    	{
    		i := 10
    		fmt.Println("i = ", i)
    	}
    
    	if flag := 3; flag == 3 {
    		fmt.Println("flag = ", flag)
    	}
    
    	// 这一句是非法的，flag的作用域在上个if已经结束了
    	//  当执行到定义变量时，才会开始分配空间，离开作用域自动释放
    	flag = 4
    }
    
    ```

  * 不同作用域的同名变量

    ```go
    package main
    
    import "fmt"
    
    var a byte
    
    func main1() {
    	// 不同作用域，允许定义同名变量
    	var a int
    
    	fmt.Printf("%T\n", a)
    	// int
    }
    
    func main2() {
    	// 当执行语句要使用变量时，采用就近原则
    	// var a int
    
    	fmt.Printf("%T\n", a)
    	// uint8
    }
    
    func main3() {
    	var a int
    
    	fmt.Printf("1: %T\n", a)
    	// 1: int
    
    	{
    		var a float32
    		fmt.Printf("2: %T\n", a)
    		// 2: float32
    	}
    
    	// 特别要注意在test函数中，并没有主句变量a，所以使用的全局变量a
    	test()
    	// 3: uint8
    }
    
    func test() {
    	fmt.Printf("3: %T\n", a)
    }
    ```

  * 工作区介绍

    Go语言必须放在工作区中。工作区包含三个目录

    * src目录: 通过代码包的形式组织并保存Go源码文件，比如.go, .c等 （**需要自己创建**）
    * pkg目录: 存放经过由go install 命令构建安装后的安装包（包含Go库源码文件）的.a归档文件（**自动生成**）
    * bin目录: 与pkg类似，经过go install命令完成安装后，保存由Go命令源码文件生成的可执行文件（**自动生成**）

    src包含所有的源代码，pkg和bin目录无需手动创建；**需要特别注意**，只有当环境变量GOPATH只包含一个工作区的路径时，go install才会把命令源码安装当当前工作区的bin目录下。如果环境变量GOPATH中包含多个工作区的目录途径，当执行go install时就会失败，此时必须设置环境变量GOBIN

  * 导入包

    ```go
    package main
    
    // 给包名起别名
    import io "fmt"
    
    // 代表忽略该包
    import _ "os"
     
    func main() {
    	io.Println("this is a test")
    }
    ```

  * **同级目录下的工程管理**

    * 多个源文件时，文件必须放在src目录下

    * 设置GOPATH环境变量（可以通过go env命令查看相关路径）

      修改环境变量为项目目录

      [图片1](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/1.png)

      文件夹层次如下：

      [图片2](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/2.png)

    * 同一个目录下包名必须一样，即package xxx

    * 同一个目录下，调用其余文件定义的 函数，无需包名引用

      * main.go

        [图片3](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/3.png)

      * test.go

        [图片4](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/4.png)

      * 运行结果

        [图片5](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/4.png)

  * **不同级目录下的工程管理**

    * 不同目录，包名需保持不一致

      * 文件夹层次如下：

        [图片6](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/6.png)

        [图片7](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/7.png)

    * 文件中调用不同包里面的函数，格式为：包名.函数名()

      * 程序代码如下：

        [图片8](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/8.png)

        [图片9](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/9.png)

    * 调用别的包的函数，这个包中的函数如果首字母为小写，其余的包则无法使用该包中的函数；如果首字母为大写，其余的包则能调用该包中的函数。

    * 在调节环境变量的时候，为了减少工作量，可以通过如下形式进行环境变量的配置

      [图片10](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/10.png)

      [图片11](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%202.assets/11.png)

      勾选**编译目录使用自定义GOPATH**，**自定义GPOPATH**，然后将项目路径复制到空框中即可。

  * initial函数的介绍

    图片12

    * 如果main.go导入一个包，程序会先调用包中的init函数，然后再调用本身的init函数，之后再调用main函数
    * 如果要忽略掉一个包，即将这个包的别名设置为 _ ,这样可以实现不调用包中的函数，而只调用该包中的init函数

  * go_install命令

    可以用来生成pck目录和bin目录，黑马上的教程没有试验成功，从github上找了一个解决办法成功了。解决办法是两个目录分别通过go install命令生成

    * 设置GOPATH和GOBIN

      首先说一下文件夹目录

      E:

      图片13

      图片14

      GOPATH和GOBIN的设置

      图片15

    * 通过go install命令生成pkg目录

      ```cmd
      E:\gocode\src\differ_project\src\calc> go install
      ```

    * 通过go install命令生成src目录

      ```cmd
      E:\gocode\src\differ_project\src> go install
      ```

      **注意两个命令的使用目录是不同的，pkg是到对应的包中使用，src是到项目的src目录下使用。**

  