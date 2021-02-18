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

* ## 地址传递

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

* ## 数组定义，size必须为常量，但是下标可以通过变量访问

  ```go
  package main
  
  import _ "fmt"
  
  func main() {
      
  	const t int = 10
  	var a [t]int
  	var pos int = 1
  	a[pos] = 1
      
      // 数组作为函数参数时为值传递，形参的数组是实参的复制品
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

* ## 二维数组

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

* ## [随机数的使用](https://studygolang.com/pkgdoc)

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

* ## 冒泡排序

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

* 数组指针做函数参数

  ```go
   package main
  
  import "fmt"
  
  func func1(a [5]int) {
  	a[0] = 6
  	fmt.Println("func1 a = ", a)
  }
  
  func func2(a *[5]int) {
  	(*a)[0] = 6
  	fmt.Println("func2 a = ", *a)
  }
  
  func main() {
  	var a [5]int = [5]int{1, 2, 3, 4, 5}
  
  	func1(a)
  	fmt.Println("After func1 a = ", a)
  
  	func2(&a)
  	fmt.Println("After func2 a = ", a)
  	/*
      func1 a =  [6 2 3 4 5]
  	After func1 a =  [1 2 3 4 5]
  	func2 a =  [6 2 3 4 5]
  	After func2 a =  [6 2 3 4 5]
  	*/
  }
  ```

* ## 切片

  切片并不是数组或者数组指针，它通过内部指针和相关的引用属性引用数组片段来实现变长的方案。

  ​	slice并不是真正意义上的动态数组，只是一个引用类型

  ```go
  package main
  
  import "fmt"
  
  func main() {
  	var a [5]int = [5]int{1, 2, 3, 4, 5}
      // 1代表下标的起点，3代表下标的末尾点，按照左闭右开的原则
  	// 4代表这个切片的容量为4 - 1 = 3
      // 数组中的[]长度是固定的，但是slice的size是可以修改的
  	array := a[1:3:4]
  
  	fmt.Println("array = ", array)
      // 代表储存的元素个数
  	fmt.Println("array len = ", len(array))
  	// 代表这个slice总共能存储多少元素
  	fmt.Println("array cap = ", cap(array))
  	
  	/*
  	array =  [2 3]
  	array len =  2
  	array cap =  3
  	*/
  }
  ```

* ## 切片的创建

  ```go
  package main
  
  import "fmt"
  
  func main() {
  	// 自动推导类型，同时对s1进行初始化
  	s1 := []int{1, 2, 3, 4}
  	fmt.Println("s1 = ", s1)
  
  	// 借助make函数创建slice，格式为make(切片类型，长度，容量)
  	s2 := make([]int, 5, 10)
  	fmt.Printf("len = %d, cap = %d\n", len(s2), cap(s2))
  
  	// 容量也可以省去，让编译器自动推导
  	s3 := make([]int, 5)
  	fmt.Printf("len = %d, cap = %d\n", len(s3), cap(s3))
  }
  ```

* ## 切片的截取

  [图片1](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%203.assets/1.png)

* ## 切片和底层数组关系

  ```go
  package main
  
  import "fmt"
  
  func main() {
  	s := []int{0, 1, 2, 3, 4, 5, 6, 7}
  	
  	// 修改切片的某个元素，也会修改对应的底层数组的元素
  	s1 := s[2:5]
  	fmt.Println(s1)
  	// [2 3 4]
  	s1[2] = 100
  	fmt.Println(s1, s)
  	// [2, 3, 100], [0, 1, 2, 3, 100, 5, 6, 7]
  	
  	s2 := s1[2:6]
  	s2[3] = 200
  	fmt.Println(s)
  	// [0 1 2 3 100 5 6 200]
  }
  ```

* ## append函数

  图片2

* ## copy函数

  ```go
  package main
  
  import "fmt"
  
  func main() {
  	s := []int{0, 1, 2, 3, 4, 5, 6, 7}
  
  	s1 := s[6:]
  	// [6, 7]
  	s2 := s[:5]
  	// [0, 1, 2, 3, 4]
  	// 函数原型为copy(dst, src), 复制长度以len小的为准
  	// 从下标0开始，以len小的下标结束
  	copy(s2, s1)
  
  	fmt.Println(s2)
  	// [6, 7, 2, 3, 4]
  	fmt.Println(s)
  	// [6, 7, 2, 3, 4, 5, 6, 7]
  }
  ```

* ## 切片做函数参数

  切片做函数参数时是通过引用传递的形式实现的，注意切片和数组区分

* ## Map

  Go语言中的map是一种内置的数据结构，它是一个无序的kv集合

  ```go
  package main
  
  import "fmt"
  
  func main() {
  	// int代表key类型，string代表value类型
  	info := map[int]string{
  		110: "mike",
  		111: "yoyo",
  		112: "lily"}
  
  	fmt.Println("info = ", info)
  	// info =  map[110:mike 111:yoyo 112:lily]
      
      
  }
  
  ```

  **在一个map中所有的键都是唯一的，而且必须支持 == 和 != 操作符的类型，切片，函数以及包含切片的数据结构类型因为巨有引用的语义，所以不能用来作为映射的键**

* ## Map的创建, 初始化

  ```go
  package main
  
  import "fmt"
  
  func main() {
  
  	m1 := make(map[int]string)
  	fmt.Println("m1 = ", m1)
  	fmt.Println("len = ", len(m1))
  
      // ·
  	m2 := make(map[int]string, 10)
  	fmt.Println("m1 = ", m2)
  	fmt.Println("len = ", len(m2))
      package main
  
  	m3 := map[int]string{1: "mike", 2: "go"}
  	fmt.Println("m3 = ", m3)
  	
      for key, value := range m3 {
  		fmt.Printf("%d ---> %s\n", key, value)
  	}
  	/*
  	m1 =  map[]
  	len =  0
  	m1 =  map[]
  	len =  0
  	m3 =  map[1:mike 2:go] 
  	for key, value := range m {
  		fmt.Printf("%d ---> %s\n", key, value)
  	}
  	m =  map[1:mike 2:go]
  	1 ---> mike
  	2 ---> go
  	*/
      
      m4 := map[int]string{1: "mike", 2: "go"}
  
	// 如何判断一个key是否存在
  	// 第一个返回值为key所对应的value，第二个返回值为key是否存在的条件
  	value, ok := m4[1]
  	if ok == true {
  		fmt.Println("m4[1] = ", value)
  	} else {
  		fmt.Println("key 不存在")
  	}
      // m4[1] =  mike
      
      // 删除key为1的内容
      delete(m3, 1)
  }
  ```
  
* ## map作为函数参数的时候是引用传递

  ```go
  package main
  
  import "fmt"
  
  func test(m map[int]string) {
  	delete(m, 2)
  }
  
  func main() {
  	m := map[int]string{1: "mike", 2: "yoyo"}
  	fmt.Println("m = ", m)
  	// m =  map[1:mike 2:yoyo]
  	test(m)
  	fmt.Println("m = ", m)
  	// m =  map[1:mike]
  }
  
  ```

* ## 结构体普通变量，指针变量初始化

  ```go
  package main
  
  import "fmt"
  
  type Student struct {
  	id   int
  	name string
  	sex  byte
  }
  
  func main() {
      // 顺序初始化，每个成员必须初始化
  	var s1 Student = Student{1, "test1", 'm'}
  	fmt.Println("s1 = ", s1)
  	// s1 =  {1 test1 109}
      
      // 指定成员初始化，没有初始化的成员自动赋值为0
  	s2 := Student{name: "test2"}
  	fmt.Println("s2 = ", s2)
      // s2 =  {0 test2 0}
      
      // 指针变量初始化
      var s3 *Student = &Student{name: "test3"}
  	fmt.Println("s2 = ", *s3)
  	// s2 =  {0 test3 0}
  }
  ```

* ## 结构体成员的使用：指针变量

  ```go
  package main
  
  import "fmt"
  
  type Student struct {
  	id   int
  	name string
  	sex  byte
  }
  
  func main() {
  	var s Student
  	var p1 *Student = &s
  
      // 通过指针操作成员 p1.id和(*p1).name完全等价
  	p1.id = 1
  	(*p1).name = "test"
  
  	fmt.Println("*p1 = ", *p1)
  	// *p1 =  {1 test 0}
  }
  ```

* ## 结构体的比较

  ​	同类型的结构体之间可以相互赋值，结构体之间比较支持 == 和 != 符号，但是不支持 < 或者 > 

* ## 结构体做函数参数

  ​	是通过值传递的形式实现，所以函数内部对函参进行修改，不会对原结构体产生影响

  ​	可以通过引用传递的形式实现函数对原参数做改变

  ```go
  package main
  
  import "fmt"
  
  type Student struct {
  	id   int
  	name string
  	sex  byte
  }
  
  func test(p *Student) {
  	p.id = 2
  	fmt.Println("*p = ", *p)
  	// *p =  {2 test 0}
  }
  
  func main() {
  	var s Student
  	var p1 *Student = &s
  
  	p1.id = 1
  	(*p1).name = "test"
  
  	test(&s)
  
  	fmt.Println("*p1 = ", *p1)
  	// *p1 =  {2 test 0}
  }
  ```

* ## Go语言可见性验证

  ​	如果想使用别的包的函数，结构体类型，结构体成员。函数名，类型名，结构体成员变量名，首字母**必须大写**。如果首字母是小写，那只能在同一个包里使用

  

  