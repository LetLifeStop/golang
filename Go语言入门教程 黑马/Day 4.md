* ## 面向对象编程

  尽管Go语言中没有封装，继承，多态这些概念，但是可以通过别的方式实现这些特性

  * 封装：通过方法实现
  * 继承：通过匿名字段实现
  * 多态：通过接口实现

* ## 匿名字段初始化

  ```go
  package main
  
  import "fmt"
  
  type Person struct {
  	name string
  	sex  byte
  	age  int
  }
  
  type Student struct {
  	Person
  	id   int
  	addr string
  }
  
  func main() {
  	var s1 Student = Student{Person{"test_name_01", 'm', 11}, 1, "test_addr_01"}
  	fmt.Println("s1 = ", s1)
  	// s1 =  {{test_name_01 109 11} 1 test_addr_01}
  
  	var s2 Student = Student{Person: Person{"test_name_02", 'm', 11}}
  	// ++v 会将信息显示的更清晰
  	fmt.Printf("s2 = %++v\n", s2)
  	// s2 = {Person:{name:test_name_02 sex:109 age:11} id:0 addr:}
  
  	var s3 Student = Student{Person: Person{name: "test_name_03"}}
  	fmt.Printf("s3 = %++v\n", s3)
  	// s3 = {Person:{name:test_name_03 sex:0 age:0} id:0 addr:}
  
  }
  ```

* ## 结构体中的同名字段

  ```go
  package main
  
  import "fmt"
  
  type Person struct {
  	name string
  	sex  byte
  	age  int
  }
  
  type Student struct {
  	Person
  	id   int
  	name string
  }
  
  func main() {
  	var s Student
  	
      //  默认规则（纠结规则），如果当前的结构体可以本结构体找到此成员，就操作该结构体
      // 如果没有在本结构体找到该成员，就会从继承的字段中寻找
  	s.name = "test"
  	fmt.Printf("s = %++v\n", s)
  	// s = {Person:{name: sex:0 age:0} id:0 name:test}
  }
  
  // version 2：
  package main
  
  import "fmt"
  
  type Person struct {
  	name string
  	sex  byte
  	age  int
  }
  
  type Student struct {
  	Person
  	id int
  	//name string
  }
  
  func main() {
  	var s Student
  
  	s.name = "test"
  	fmt.Printf("s = %++v\n", s)
  	// s = {Person:{name:test sex:0 age:0} id:0}
  }
  ```

* ## 非结构体匿名字段

  ```go
  package main
  
  import "fmt"
  
  type Person struct {
  	name string
  	sex  byte
  	age  int
  }
  
  type Student struct {
  	Person // 结构体匿名字段
  	int    // 基础类型的匿名字段
  }
  
  func main() {
  	var s Student
  
  	s.int = 5
  	fmt.Printf("s = %++v\n", s)
  	// s = {Person:{name: sex:0 age:0} int:5}
  }
  ```

* ## 方法

  ​	在面向对象编程中，一个对象就是一个值或者变量，在这个对象中可以包含函数，这种带有接收者的函数即称之为方法。本质上，一个方法就是一个和特殊类型关联的函数。

  ```go
  func (receiver ReceiverType) funcName(Paramemters) (result)
  ```

  * 参数receiver可以任意使用，如果方法中未使用，可以省略参数名 
  * receiver类型可以是 T 或者 T* 。 T不能是接口或者指针
  * 方法不支持重载，即不能定义名称相同但是参数不同的方法 (**只要接收者不同，就算两个method函数名相同，也是不同的方法**)

* ## 面向过程和面向对象的区别

  ```go
  package main
  
  import "fmt"
  
  // 面向过程
  func Add01(a, b int) int {
  	return a + b
  }
  
  // 面向对象，给某个类型绑定一个函数
  type long int
  // tmp成为接收者
  func (tmp long) Add02(other long) long {
  	return tmp + other
  }
  
  func main() {
  	var res1 int
  	res1 = Add01(1, 1)
  	fmt.Println("res = ", res1)
  	// res =  2
  	
  	var r, res2 long = 1, 0
  	res2 = r.Add02(3)
  	fmt.Println("res2 = ", res2)
  	// res2 =  4
  }
  ```

* ## 结构体类型添加方法

  ```go
  package main
  
  import "fmt"
  
  type Person struct {
  	age int
  	sex byte
  }
  
  func (tmp Person) PrintInfo() {
  	fmt.Println("tmp = ", tmp)
  }
  
  func (p *Person) SetInfo(_age int, _sex byte) {
  	p.age = _age
  	p.sex = _sex
  }
  
  func main() {
  	var p2 Person
  	(&p2).SetInfo(1, 'm')
  	p2.PrintInfo()
  	// tmp =  {1 109}
  }
  ```

* ## 方法集

  ​	类型的方法集是指可以被该类型的值调用的所有的方法的集合

  ​	用实例value或者指针调用方法（包括匿名字段）不收方法集约束，编译器总是查找所有的方法，并自动转换receiver实参

  ​	类型 *T方法集包含全部的receiver T + *T方法

  ```go
  package main
  
  import "fmt"
  
  type Person struct {
  	age int
  	sex byte
  }
  
  func (tmp Person) PrintInfo() {
  	fmt.Println("tmp = ", tmp)
  }
  
  func (tmp *Person) PrintPointer() {
  	fmt.Println("tmp = ", tmp)
  }
  
  func main() {
  	var p *Person = &Person{1, 'm'}
  	p.PrintInfo()
  	p.PrintPointer()
  	(*p).PrintInfo()
  	(*p).PrintPointer()
  	/*
  	tmp =  {1 109}
  	tmp =  &{1 109}
  	tmp =  {1 109}
  	tmp =  &{1 109}
  	*/
  }
  ```

* ## 方法的继承

  ```go
  package main
  
  import "fmt"
  
  type Person struct {
  	age int
  	sex byte
  }
  
  func (tmp Person) PrintPersonInfo() {
  	fmt.Println("tmp = ", tmp)
  }
  
  type Student struct {
  	Person
  	id int
  }
  
  /*
  // 方法的重写遵循，就近原则
  // 如果当前的结构体定义了该函数，首先调用该结构体作为接受者的method
  // 如果当前的结构体没有定义该函数，则会去匿名字段中寻找method
  func (tmp Student) PrintPersonInfo() {
  	fmt.Println("tmp = ", tmp)
  	// tmp =  {{1 0} 1}
  }
  */
  
  func main() {
  	s := Student{Person{age: 1}, 1}
  	s.PrintPersonInfo()
  	//tmp =  {1 0}
  
  }
  
  ```

* ## 方法值

  ```go
  package main
  
  import "fmt"
  
  type Person struct {
  	age int
  	sex byte
  }
  
  func (tmp Person) PrintPersonInfo() {
  	fmt.Println("tmp = ", tmp)
  }
  
  type Student struct {
  	Person
  	id int
  }
  
  func (p Student) SetInfoPointer() {
  	fmt.Println("this is SetInfoPointer")
  }
  
  func main() {
  	s := Student{Person{age: 1}, 1}
  	s.SetInfoPointer()
  	// this is SetInfoPointer
      
  	// 保存method入口地址，把s隐藏
      // pFunc不清楚如何进行赋值
  	pFunc := s.SetInfoPointer
  	pFunc()
  	// this is SetInfoPointer
  
      // 第二种使用method值的方法，先赋值，然后显式把接收者传递过去
  	pFunc2 := (Student).SetInfoPointer
  	pFunc2(s)
  	// this is SetInfoPointer
  }
  ```

* ## 接口

  ​	interface是一个自定义类型，接口类型描述了一系列方法的集合

  ​	接口类型是一种抽象的类型，不会暴露所代表的对象的内部结构和支持这个独享的基础操作的集合，inferface只会展示出自己的方法。因此接口不能实例化

  ​	接口只有方法声明，没有实现，没有数据字段

* ## 接口的定义和实现

  ```go
  package main
  
  import "fmt"
  
  // inferface的接口一般以er结尾
  type Humaner interface {
  	sayHi()
  }
  
  type Student struct {
  	name string
  	id   int
  }
  
  // Student结构体实现了这个方法
  func (tmp Student) sayHi() {
  	fmt.Println("this is Student sayHi")
  }
  
  type Mystr string
  
  // Mystr实现了这个方法
  func (tmp Mystr) sayHi() {
  	fmt.Println("this is Mystr sayHi")
  }
  
  func main() {
  	var i Humaner
  
  	s := &Student{"test", 666}
  	i = s
  	i.sayHi()
  	// this is Student sayHi
  
  	var str Mystr
  	str = "tt"
  	i = &str
  	i.sayHi()
  	// this is Mystr sayHi
  }
  ```

* ## interface的多态表现

  ```go
  package main
  
  import "fmt"
  
  // inferface的接口一般以er结尾
  type Humaner interface {
  	sayHi()
  }
  
  type Student struct {
  	name string
  	id   int
  }
  
  // Student结构体实现了这个方法
  func (tmp Student) sayHi() {
  	fmt.Println("this is Student sayHi")
  }
  
  type Mystr string
  
  // Mystr实现了这个方法
  func (tmp Mystr) sayHi() {
  	fmt.Println("this is Mystr sayHi")
  }
  
  func Mul(t Humaner) {
  	t.sayHi()
  }
  func main() {
  
  	s := &Student{"test", 666}
  
  	var str Mystr
  	str = "tt"
  	
      // 调用同意函数，不同的表现，会出现不同的形态
  	Mul(s)
      // this is Student sayHi
  	Mul(&str)
      // this is Mystr sayHi
  }
  ```

* ## 接口的继承

  ```go
  package main
  
  import "fmt"
  
  // inferface的接口一般以er结尾
  // 子集
  type Humaner interface {
  	sayHi()
  }
  
  // 超集
  type Studenter interface {
  	Humaner
  	sing(lrc string)
  }
  
  type Student struct {
  	name string
  	id   int
  }
  
  // Student结构体实现了这个方法
  func (tmp Student) sayHi() {
  	fmt.Println("this is Student sayHi")
  }
  
  func (tmp Student) sing(lrc string) {
  	fmt.Println("this is Student sing", lrc)
  }
  func main() {
  	var i Studenter
  
  	v := Student{"test", 1}
  	i = &v
  
  	// 继承的method
  	i.sayHi()
  	// this is Student sayHi
  	i.sing("123")
  	// this is Student sing 123
  
  }
  ```

* ## 接口之间的转换

  **超集可以转换成子集，反之不成立**

* ## 空接口

  ​	空接口不包含任何的方法，正因如此，所有的类型都实现了空接口，所以空接口可以存储任意类型的数值。类比void*

  ```go
  package main
  
  import "fmt"
  
  func main() {
  	var i interface{} = 1
  	var i interface{} = abc
      ...
  }
  ```

    当函数可以接受任意的对象实例时，我们会将其声明为interface{}

  ```go
  func Printf(fmt string, args ...interface{})
  func Println(args ...interface{})
  ```

* ## 通过if实现类型断言

  ```go
  package main
  
  import "fmt"
  
  type Student struct {
  	id   int
  	name string
  }
  
  func main() {
  	t := make([]interface{}, 3)
  	t[0] = 1
  	t[1] = "hello"
  	t[2] = Student{1, "test"}
  
  	// 第一个返回下标，第二个返回下标对应的value
  	for index, data := range t {
  		if value, ok := data.(int); ok == true {
  			fmt.Printf("t[%d], type is int, value is %d\n", index, value)
  		} else if value, ok := data.(string); ok == true {
  			fmt.Printf("t[%d], type is string, value is %s\n", index, value)
  		} else if _, ok := data.(Student); ok == true {
  			fmt.Printf("t[%d], type is Student\n", index)
  		}
  	}
  	/*
  	t[0], type is int, value is 1
  	t[1], type is string, value is hello
  	t[2], type is Student
  	*/
  }
  ```

* ## 通过switch实现类型断言

  ```go
  package main
  
  import "fmt"
  
  type Student struct {
  	id   int
  	name string
  }
  
  func main() {
  	t := make([]interface{}, 3)
  	t[0] = 1
  	t[1] = "hello"
  	t[2] = Student{1, "test"}
  
  	for index, data := range t{
  		switch value := data.(type) {
  		case int:
  			fmt.Printf("t[%d], type is int, value is %d\n", index, value)
  		case string:
  			fmt.Printf("t[%d], type is string, value is %s\n", index, value)
  		case Student:
  			fmt.Printf("t[%d], type is Student\n", index)
  		}
  	}
  	
  	/*
  	t[0], type is int, value is 1
  	t[1], type is string, value is hello
  	t[2], type is Student
  	*/
  }
  ```

  