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

  ​	一般而言，当panic发生时，**程序会中断运行**，并立即执行该goroutine中的defer函数

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

  ​	当发生panic之后会导致程序崩溃。panic可以将当前的程序从运行时的panic状态中恢复并重新获得流程控制权
  
  ​	recover只有在defer调用的函数中才有效
  
  ​	如果调用了内置函数recover，并且定义了该defer语句发生了panic异常，recover会使程序从panic中恢复，并且返回panic value，导致panic异常的函数不会继续执行，但能正常返回。在未发生panic时调用recover，recover会返回异常。
  
  ```go
  package main
  
  import (
  	"fmt"
  )
  
  func test(x int) {
  	defer func() {
  		if err := recover(); err != nil {
  			fmt.Println(err)
  		}
  	}()
  
  	var a [10]int
  	a[x] = 11
  
  }
  
  func main() {
  	test(20)
  }
  ```
  
* ## 字符串处理

  ```go
  // 功能：字符串s中是否包含substr，返回bool
  func Contains(s, substr string) bool 
  
  // 功能：字符串连接，把slice a通过seq连接起来
  func Join(a []string, seq string) string 
  /*
  	s := []string{"foo", "bar", "baz"}
  	fmt.Println(strings.Join(s, ", "))
  	// foo, bar, baz
  */
  
  // 在字符串s中查找seq所在的位置，如果存在的话，返回位置值，否则返回 -1
  func Index(s, seq string) int 
  
  // 功能：重复s字符串count次，最后返回构造的字符串
  func Repeat(s string, count int) string
  
  // 功能：在s字符串中，把old字符串替换为new字符串，n代表替换次数，如果n < 0 表示全部替换
  func Replace(s, old, new string, n int) string
  
  // 功能：把s字符串按照seq分割，返回slice
  func Split(s, seq string) []string 
  /*
  fmt.Printf("%q\n", string.Split("a,b,c", ","))
  // ["a" "b" "c"]
  */
  
  // 功能：在s字符串的头部和尾部去除cutset指定的字符串
  func Trim(s string, cutset string) string 
  /*
  fmt.Printf("[%q]", strings.Trim("!! test !!", "!"))
  ["test"]
  */
  
  // 功能：去除s字符串的空格符，并且按照空格分隔string，返回slice
  func Fields(s string) []string
  ```

* ## 字符串转换

  ```go
  // Append
  package main
  
  import (
  	"fmt"
  	"strconv"
  )
  
  func main() {
  	// Append系列函数可以将多种类型转换为字符串后，添加到现有的切片中
  	str := make([]byte, 0, 100)
  	// 通过2进制的形式添加
  	str = strconv.AppendInt(str, 12, 2)
  	str = strconv.AppendBool(str, true)
  	str = strconv.AppendQuote(str, "abc")
  	str = strconv.AppendQuoteRune(str, '单')
  
  	fmt.Println(string(str))
  	// 1100true"abc"'单'
  }
  
  // Format
  package main
  
  import (
  	"fmt"
  	"strconv"
  )
  
  func main() {
  	// Format系列函数把其他类型转换成string类型
  	a := strconv.FormatBool(false)
  	b := strconv.FormatInt(1234, 10)
  	c := strconv.Itoa(1023)
  	// 'f'指的是打印格式为小数，1指的是小数点的位数，64以float64处理
  	d := strconv.FormatFloat(3.14, 'f', 1, 64)
  	fmt.Println(a, b, c, d)
  	// false 1234 1023 3.1
  
  }
  ```

  **[parse系列](http://www.verydoc.net/go/00003878.html)**

* ## 正则表达式

  [图片1](https://github.com/LetLifeStop/golang/blob/master/Go%E8%AF%AD%E8%A8%80%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%20%E9%BB%91%E9%A9%AC/Day%205.assets/1.png)

  ```go
  package main
  
  import (
  	"fmt"
  	"regexp"
  )
  
  func main() {
  	buf := "abc azx a7c"
  
  	// 1. 解释规则，该函数会解析正则表达式，如果成功返回解释器
  	reg1 := regexp.MustCompile("a.c")
  	if reg1 == nil {
  		fmt.Println("err = ", reg1)
  		return
  	}
  	
  	// 2. 根据规则提取关键信息，第二个参数代表提取的个数，< 0时为全部提取
  	reg2 := reg1.FindAllStringSubmatch(buf, 1)
  	fmt.Println("reg2 = ", reg2)
  }
  ```

  [示例](https://www.cnblogs.com/nulige/p/10260149.html)

* ## JSON介绍

  ​	JSON是一种轻量级的数据交换格式，在易于编写和阅读的同时，也易于程序解析和生成。

  ​	使用Go语言内置的encoding/json标准库，我们可以使用Go程序生成和解析JSON格式的书

  ```go
  package main
  
  import (
  	"encoding/json"
  	"fmt"
  )
  
  // map也可以生成JSON
  type Message struct {
  	Company  string
      // Company  string `json:"company"` 二次编码，如果使用二次编码的话，生成的json文件对应的名称就会变成company
  	Subjects []string
  	Isok     bool
      // Isok     bool `json:"-"`如果使用"-"的话，代表这个Isok字段不会输出到屏幕
      // Isok     bool `json:",string"` 如果想要将bool类型转换为string，可以通过该途径实现
  }
  
  func main() {
  	s := Message{"ITcast", []string{"test1", "test2", "test3"}, false}
  
  	buf, err := json.Marshal(s)
  	
  	if err != nil {
  		fmt.Println("err = ", err)
  		return
  	}
  	fmt.Println("buf", string(buf))
  	// buf {"Company":"ITcast","Subjects":["test1","test2","test3"],"Isok":false}
  	
  	
  	/*
  	func MarshalIndent(v interface{}, prefix, indent string) ([]byte, error)
  	marshalIndent和Marshal相比，多了一个通过Indent对输出进行格式化处理，没遗憾多了一个prefix前缀，根据缩进嵌套，后面跟着一个或者多个缩进副本
  	buf, err := json.MarshalIndent(s, "", " ")
  	
  	fmt.Println("buf", string(buf))
  	buf {
   	 "Company": "ITcast",
       "Subjects": [
         "test1",
    	   "test2",
         "test3"
   	   ],
   	  "Isok": false
  	 }
  	*/
  }
  ```

* ## JSON解析到结构体

  ```go
  package main
  
  import (
  	"encoding/json"
  	"fmt"
  )
  
  type Message struct {
  	Company  string
  	Subjects []string
  	Isok     bool
  }
  
  func main() {
  
  	jsonBuf := `
  	{
   	 "Company": "ITcast",
       "Subjects": [
         "test1",
    	   "test2",
         "test3"
   	 ],
   	"Isok": false
  	}
  	`
  	var tmp Message
  
  	err := json.Unmarshal([]byte(jsonBuf), &tmp)
  
  	if err != nil {
  		fmt.Println("err = ", err)
  		return
  	}
  	fmt.Println("tmp", tmp)
  	// tmp {ITcast [test1 test2 test3] false}
  }
  
  // 如果不想要全部的字段，可以定义一个结构体变量，只定义自己所想要的字段
  
  package main
  
  import (
  	"encoding/json"
  	"fmt"
  )
  
  func main() {
  
  	jsonBuf := `{"Company":"ITcast","Subjects":["test1","test2","test3"],"Isok":false}`
      
      // 在结构体中只定义自己所需的变量
  	type Message struct {
  		Subjects []string
  	}
  	var tmp Message
  
  	err := json.Unmarshal([]byte(jsonBuf), &tmp)
  
  	if err != nil {
  		fmt.Println("err = ", err)
  		return
  	}
  	fmt.Println("tmp", tmp)
  	// tmp {[test1 test2 test3]}
  }
  ```

  在json解码到map的时候，因为类型需要通过遍历来确定，我们可以通过**类型断言**来控制

* ## [文件操作](https://studygolang.com/pkgdoc)

  ```go
  // write的使用
  package main
  
  import (
  	"fmt"
  	"os"
  )
  
  func WriteFile(path string) {
  	f, err := os.Create(path)
  	if err != nil {
  		fmt.Println("Create file failed!")
  		return
  	}
  
  	defer f.Close()
  	var buf string
  	for i := 0; i < 10; i++ {
  		buf = fmt.Sprintf("i = %d\n", i)
  		f.WriteString(buf)
  	}
  }
  
  func main() {
  	path := "./test.txt"
  	WriteFile(path)
  
  }
  
  // read的使用
  package main
  
  import (
  	"fmt"
  	"io"
  	"os"
  )
  
  func WriteFile(path string) {
  	f, err := os.Open(path)
  	if err != nil {
  		fmt.Println("Create file failed!")
  		return
  	}
  
  	defer f.Close()
  
  	// buf为2k的大小
  	buf := make([]byte, 1024*2)
  
  	// n代表从文件读取内容的长度
  	n, err1 := f.Read(buf)
  	// 文件读取出错并且err不是结尾
  	if err1 != nil && err1 != io.EOF {
  		fmt.Println("err1 = ", err1)
  		return
  	}
  
  	fmt.Println("buf = ", string(buf[:n]))
  	/*
  	buf =  i = 0
  	i = 1
  	i = 2
  	i = 3
  	i = 4
  	i = 5
  	i = 6
  	i = 7
  	i = 8
  	i = 9
  	*/
  }
  
  func main() {
  	path := "./test.txt"
  	WriteFile(path)
  
  }
  
  // 借助buffio实现按行读取内容
  package main
  
  import (
  	"bufio"
  	"fmt"
  	"io"
  	"os"
  )
  
  func WriteFile(path string) {
  	f, err := os.Open(path)
  	if err != nil {
  		fmt.Println("Create file failed!")
  		return
  	}
  
  	defer f.Close()
  
  	// 新建一个缓冲区，把内容先放到缓冲区
  	r := bufio.NewReader(f)
  
  	for {
  		// 遇到'\n'结束读取
  		buf, err := r.ReadBytes('\n')
  		if err != nil {
  			if err == io.EOF {
  				// 文件读取结束，跳出for循环
  				break
  			}
  			fmt.Println("err = ", err)
  		}
  		fmt.Printf("111 buf = %s\n 111", string(buf))
  		/*
  		 111 buf = i = 0
  
  		 111111 buf = i = 1
  		
  		 111111 buf = i = 2
  		
  		 111111 buf = i = 3
  		
  		 111111 buf = i = 4
  		
  		 111111 buf = i = 5
  		
  		 111111 buf = i = 6
  		
  		 111111 buf = i = 7
  		
  		 111111 buf = i = 8
  		
  		 111111 buf = i = 9
  		
  		 111
  		*/
  		
  	}
  }
  
  func main() {
  	path := "./test.txt"
  	WriteFile(path)
  
  }
  ```

* ## 拷贝文件

  ```go
  package main
  
  import (
  	_ "bufio"
  	"fmt"
  	"io"
  	"os"
  )
  
  func main() {
  	list := os.Args
  	if len(list) != 3 {
  		fmt.Println("args size wrong")
  		return
  	}
  
  	srcFileName := list[1]
  	dstFileName := list[2]
  	if srcFileName == dstFileName {
  		fmt.Println("srcFileName equals dstFilename!")
  		return
  	}
  
  	sF, err1 := os.Open(srcFileName)
  	if err1 != nil {
  		fmt.Println("err1 = ", err1)
  		return
  	}
  
  	dF, err2 := os.Create(dstFileName)
  	if err2 != nil {
  		fmt.Println("err2 = ", err2)
  		return
  	}
  
  	defer sF.Close()
  	defer dF.Close()
  
  	buf := make([]byte, 1024*2)
  
  	for {
  		n, err := sF.Read(buf)
  
  		if err == nil {
  			if err == io.EOF {
  				break
  			} else {
  				fmt.Println("err = ", err)
  				return
  			}
  		}
  
  		dF.WriteString(string(buf[:n]))
  	}
  }
  
  ```