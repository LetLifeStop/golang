* ## error接口

  Go语言引入了一个关于错误处理的标准模式，即error接口。

  ```go
  type error interface {
      Error() string
  }
  ```

* ## 创建error接口

  ```go
  package main
  
  import (
  	"errors"
  	"fmt"
  )
  
  func main() {
  	var err1 error = fmt.Errorf("%s", "this is normal error")
  	fmt.Println("err1 = ", err1)
  	// err1 =  this is normal error
  	
  	err2 := errors.New("this is normal err2")
  	fmt.Println("err2 = ", err2)
  	// err2 =  this is normal err2
  }
  ```

* ## error接口的应用

  ```go
  package main
  
  import (
  	"errors"
  	"fmt"
  )
  
  func MyDiv(a, b int) (result int, err error) {
  	err = nil
  	if b == 0 {
  		err = errors.New("分母不能为0")
  	} else {
  		result = a / b
  	}
  	return
  }
  func main() {
  	res, err := MyDiv(10, 0)
  	if err != nil {
  		fmt.Println("err = ", err)
  	} else {
  		fmt.Println("result = ", res)
  	}
      // err =  分母不能为0
  }
  ```

* ## panic

  ​	当遇到不可恢复的错误状态的时候，比如数组越界，空指针引用等等，这些运行时错误会引起panic异常。

  ​	我们不应通过调用panic函数来报告普通的错误，而应该把它当成报告致命错误的一种方式。

  ​	一般而言，当panic发生时，程序会中断运行，并立即执行该goroutine中的defer函数

  * panic只会触发当前Goroutine的延迟函数调用
  * panic允许在defer中嵌套多次使用

  ```go
  package main
  
  import (
  	"fmt"
  )
  
  func main() {
  	defer fmt.Println("in main")
  
  	defer func() {
  		defer func() {
  			panic("panic again and again")
  		}()
  		panic("panic again")
  	}()
  	panic("panic once")
  	
  	/*
  	in main
  	panic: panic once
  	panic: panic again
  	panic: panic again and again
  	...
  	*/
  }
  ```

* ## recover

  