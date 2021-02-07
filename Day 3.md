* ## 变量的内存和地址，指针变量的使用

  ```go
  package main
  
  import "fmt"
  
  func main() {
  	var a int = 10
  	// a 代表的是内存的内容
      // &a 代表内存外面的标号，地址，即指针
  	fmt.Printf("a = %d\n", a)
  	// a = 10
  	
  	fmt.Printf("&a = %p\n", &a)
  	// &a = 0x11c0a1a0
      
  	// 保存某个变量的地址，需要指针类型，p是int*类型的变量
  	// *int保存int的地址，**int保存*int的地址etc
  	var p *int
  	p = &a
  	*p = 666
  	fmt.Printf("p = %v, &a = %p\n", p, &a)
  }
  ```

* ## new函数的使用

  