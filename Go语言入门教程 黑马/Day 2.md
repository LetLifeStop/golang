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
  
    

