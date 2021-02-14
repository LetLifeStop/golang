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
      
  	// 指针类型可以保存某个变量的地址，p是int*类型的变量
  	// *int保存int的地址，**int保存*int的地址，etc
  	var p *int
  	p = &a
  	*p = 666
  	fmt.Printf("p = %v, &a = %p\n", p, &a)
  }
  ```

* 地址传递

  ```go
  package main
  
  import "fmt"
  
  func swap(a, b *int) {
  	*a, *b = *b, *a
  	fmt.Printf("swap: a = %d, b = %d\n", *a, *b)
  	// swap: a = 20, b = 10
  }
  func main() {
  	a, b := 10, 20
  
  	swap(&a, &b)
  	fmt.Printf("main: a = %d, b = %d\n", a, b)
  	// main: a = 10, b = 20
  }
  ```
  
* 数组定义，size必须为常量，但是下标可以通过变量访问

  ```go
  package main
  
  import _ "fmt"
  
  func main() {
      
  	const t int = 10
  	var a [t]int
  	var pos int = 1
  	a[pos] = 1
      
      var a [5]int = [5]int{1, 2, 3, 4, 5}
      
  	b := [5]int{1, 2, 3, 4, 5}
  	fmt.Println("b = ", b)
  	// b =  [1 2 3 4 5]
  
      // 没有初始化的为0
  	c := [5]int{1, 2, 3}
  	fmt.Println("c = ", c)
  	// c = [1 2 3 0 0]
  
  	d := [5]int{2: 10, 4: 20}
  	fmt.Println("d = ", d)
  	// d = [0 0 10 0 20]
  }
  ```

* 二维数组

  ```go
  package main
  
  import "fmt"
  
  func main() {
  	var a [3][4]int
  
  	k := 0
  	for i := 0; i < 3; i++ {
  		for j := 0; j < 4; j++ {
  			k++
  			a[i][j] = k
  			if j == 3 {
  				fmt.Printf("a[%d][%d]= %d\n", i, j, a[i][j])
  			} else {
  				fmt.Printf("a[%d][%d] = %d ", i, j, a[i][j])
  			}
  		}
  	}
  	// a[0][0] = 1 a[0][1] = 2 a[0][2] = 3 a[0][3]= 4
  	// a[1][0] = 5 a[1][1] = 6 a[1][2] = 7 a[1][3]= 8
  	// a[2][0] = 9 a[2][1] = 10 a[2][2] = 11 a[2][3]= 12
      
      e := [3][4]int{1: {5, 6, 7, 8}}
  	fmt.Println("e = ", e)
      // e =  [[0 0 0 0] [5 6 7 8] [0 0 0 0]]
      
      // 支持比较， 支持 == 和 !=，比较对象包含类型，size，还有每一个值的大小
    // 支持同类型的数组可以赋值
      a := [3][4]int{1: {5, 6, 7, 8}}
  	b := [3][4]int{1: {5, 6, 7, 8}}
  	fmt.Println("a == b", a == b)
      // a == b true
  }
  ```
  
* [随机数的使用](https://studygolang.com/pkgdoc)

  ```go
  package main
  
  import (
  	"fmt"
  	"math/rand"
  	_ "time"
  )
  
  func main() {
  
  	// 设置种子，只需要一次
  	// 如果每一次的种子参数是不变的，那么每次程序运行产生的随机数都是相同的
  	rand.Seed(123)
  	
      // 将种子设置为时间，这样可以保证每一次rand出来的结果不同
      rand.Seed(time.Now().UnixNano())
      
  	for i := 0; i < 3; i++ {
  		fmt.Println("rand = ", rand.Int31())
  		/*
  		rand =  1248369035
  		rand =  56316249
  		rand =  536805403
  		*/
  		
          fmt.Println("rand = ", rand.Int31n(100))
          // 限制每一次rand
  	}
  
  }
  
  ```

* 冒泡排序

  ```go
  package main
  
  import "fmt"
  
  func swap(t1, t2 *int) {
  	*t1, *t2 = *t2, *t1
  }
  
  func main() {
  	var a [6]int = [6]int{1, 5, 2, 3, 8, 7}
  
  	for i := 0; i < 6; i++ {
  		for j := i + 1; j < 6; j++ {
  			if a[i] > a[j] {
  				swap(&a[i], &a[j])
  			}
  		}
  	}
  
  	fmt.Println("a = ", a)
  }
  
  ```

  