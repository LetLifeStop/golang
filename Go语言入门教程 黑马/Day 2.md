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

    