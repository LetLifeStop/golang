* ## 并行和并发

  ​	并行（parallel）：指在同一时刻，有多条指令在多个处理器上同时执行

  ​	并发（concurrency）：指在同一时刻只能有一条指令执行，但多个进程指令被快速轮换，所以造成了宏观并行，但其实是微观上多个进程快速交替的执行

* ## Go语言并发优势

  ​	Go从语言层面就支持了并发。同时并发程序的内存管理是非常复杂的，而Go语言提供了自动垃圾回收机制。

  ​	Go为并发编程而内置的上层API基于[CSP](https://www.cnblogs.com/sunsky303/p/9115530.html)（communcating  sequential processes, 顺序通信进程）模型，即显示锁都是可以避免的，因为Go语言通过安全的通道法送和接收数据以实现同步

* ## goroutine

  ​	goroutine说到底就是协程，比线程更小，十几个goroutine可能体现在底层就是五六个线程，Go语言内部实现了这些goroutine之间的内存共享。执行goroutine只需要极少的栈内存（4 ~ 5KB），但也会根据相应得数据伸缩。 

  ```go
  // 协程的创建
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func newTask() {
  	for {
  		fmt.Println("this is a new task")
  		time.Sleep(time.Second)
  	}
  }
  
  func main() {
      // 创建goroutine
  	go newTask()
  
  	for {
  		fmt.Println("this is a main task")
  		time.Sleep(time.Second)
  	}
      /*
      this is a new task
      this is a main task
      this is a new task
      this is a main task
      this is a main task
      this is a new task
      ...
      */
  }
  
  // 主协程退出后，其他子协程也会跟着退出
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func main() {
  
  	go func() {
  		for {
  		fmt.Println("this is a second task")
  		time.Sleep(time.Second)
  		}
  	}()
  
  	var i int = 0
  	for {
  		i++
  		fmt.Println("main i = ", i)
  		time.Sleep(time.Second)
  
  		if i == 2 {
  			break
  		}
  	}
  
  }
  /*
      main i =  1
      this is a second task
      main i =  2
      this is a second task
      this is a second task
      成功: 进程退出代码 0
  */
  
  // 主协程先退出导致子协程没来得及完全调用
  // 可以人称主协程和子协程同时执行，然后主协程执行完之后，子协程还没有调用完，然后就随着主协程的退出而退出了
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func main() {
  
  	go func() {
  		for {
  			fmt.Println("this is a second task")
  			time.Sleep(time.Second)
  		}
  	}()
  }
  /*E:/gocode/src/differ_project/src/src.exe [E:/gocode/src/differ_project/src]
  成功: 进程退出代码 0.
  */
  ```

* ## runtime包

  ```go
  // Gosched的使用
  package main
  
  import (
  	"fmt"
  	"runtime"
  )
  
  func main() {
  
  	go func() {
  		for i := 0; i < 5; i++ {
  			fmt.Println("this is go")
  		}
  	}()
  
  	for i := 0; i < 2; i++ {
  		// main线程让出时间片，先让别的协程执行完毕后再执行该协程
  		runtime.Gosched()
  		fmt.Println("this is main")
  	}
  }
  
  //Goext的使用
  package main
  
  import (
  	"fmt"
  	"runtime"
  )
  
  func main() {
  
  	go func() {
  		defer fmt.Println("A.defer")
  
  		func() {
  			defer fmt.Println("B.defer")
  			runtime.Goexit()
  			fmt.Print("B")
  		}()
  
  		fmt.Println("B")
  	}()
  
  	// 目的是不让主线程的写书对子线程产生影星
  	for {
  	}
  	
  	/*
  	B.defer
  	A.defer
  	*/
  }
  
  // GOMAXPROCS()的使用
  package main
  
  import (
  	"fmt"
  	"runtime"
  )
  
  func main() {
      
  	// 可以用来设置可以并行计算的CPU核数的最大值，返回值是之前的值
  	n := runtime.GOMAXPROCS(1)
      
  	fmt.Println("n = ", n)
  
  	for {
  		go fmt.Print(1)
  		fmt.Print(0)
  	}
      
      // 当参数为1时，最多同时只有一个goroutine被执行，所以一次性会连续打印很多1.当参数为2时，最多同时有两个goroutine被执行，所以和参数为1时相比，连续的1会变少
  }
  ```

* ## channel

  ​	goroutine运行在相同的地址空间，goroutine奉行通过通信来共享内存，而不是共享内存来通信

  ​	引用类型channel是CSP模式的具体实现，用于多个goroutine通信。内部实现了同步，确保并发安全

  [阻塞与非阻塞的IO网络读写](https://www.cnblogs.com/charlesblc/p/6202402.html)

  ```go
  // 可以通过make函数来创建channel
  // 等价于make(chan Type, 0)
  make(chan Type)
  // 当capacity = 0时，channel是无缓冲阻塞读写的，当capacity > 0时，channel是有缓冲，非阻塞的，直到写满capacity个元素才阻塞写入
  make(chan Type, capacity)
  
  // channel通过操作符 <-来接受和法送数据
  // 发送value到channel
  channel <- value
  // 接受并且将其丢弃
  <-channel
  // 从channel中接收数据并且赋值给x
  x := <-channel
  // 功能同上，同时检查通道是否已关闭或者为空
  x, ok := <-channel
  ```

* ## channel创建

  ```go
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func Printer(str string) {
  	for _, data := range str {
  		fmt.Printf("%c", data)
  		time.Sleep(time.Second)
  	}
  }
  
  func person1() {
  	Printer("hello")
  }
  
  func person2() {
  	Printer("world")
  }
  
  func main() {
  
  	go person1()
  	go person2()
      // 在这种情况下，两个线程之间会有竟态条件
  	// hwoelrllod 
  	for {
  	}
  }
  
  // 通过channel进行控制
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func Printer(str string) {
  	for _, data := range str {
  		fmt.Printf("%c", data)
  		time.Sleep(time.Second)
  	}
  }
  
  var ch = make(chan string)
  
  func person1() {
  	Printer("hello")
  	// 给管道写数据，发送
  	ch <- "test"
  }
  
  func person2() {
  	// 从管道取数据，接受
  	// 如果通道没有数据，该goroutine会阻塞
  	<-ch
  	Printer("world")
  }
  
  func main() {
  
  	go person1()
  	go person2()
  	// helloworld
  	for {
  	}
  }
  ```

* ## 缓冲/非缓冲channel

  ```go
  // 无缓冲
  // 写的时候，对方没有进行读取会造成阻塞
  // 读的时候，如果channel中没有数据也会造成阻塞
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func Printer(str string) {
  	for _, data := range str {
  		fmt.Printf("%c", data)
  		time.Sleep(time.Second)
  	}
  }
  
  var ch = make(chan string)
  
  func person1() {
  	Printer("hello")
  	// 给管道写数据，发送
  	ch <- "test"
  }
  
  func person2() {
  	// 从管道取数据，接受
  	// 如果通道没有数据，该goroutine会阻塞
  	<-ch
  	Printer("world")
  }
  
  func main() {
  
  	ch := make(chan int, 0)
  	// 打印缓冲区剩余数据个数，缓冲区大小
  	fmt.Printf("len(ch) = %d, cap(ch) = %d\n", len(ch), cap(ch))
  
  	go func() {
  		for i := 0; i < 3; i++ {
  			fmt.Printf("子协程：i = %d,", i)
  			ch <- i
  			fmt.Printf("子协程 len(ch) = %d, cap(ch) = %d\n", len(ch), cap(ch))
  		}
  	}()
  
  	time.Sleep(2 * time.Second)
  
  	for i := 0; i < 3; i++ {
  		num := <-ch
  		fmt.Println("num = ", num)
  	}
  	/*
  	len(ch) = 0, cap(ch) = 0
  	子协程：i = 0,子协程 len(ch) = 0, cap(ch) = 0
  	子协程：i = 1,num =  0
  	num =  1
  	子协程 len(ch) = 0, cap(ch) = 0
  	子协程：i = 2,子协程 len(ch) = 0, cap(ch) = 0
  	num =  2
  	*/
  }
  
  // 缓冲
  // 是一种在被接受前能存储一个或者多个值的通道
  // 如果给定了一个缓冲区容量，channel就是异步的。只要缓冲区有未使用的空间用于发送数据或者还包含可以接收的数据，那么其通信就会无阻塞的进行下去
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func Printer(str string) {
  	for _, data := range str {
  		fmt.Printf("%c", data)
  		time.Sleep(time.Second)
  	}
  }
  
  var ch = make(chan string)
  
  func person1() {
  	Printer("hello")
  	// 给管道写数据，发送
  	ch <- "test"
  }
  
  func person2() {
  	// 从管道取数据，接受
  	// 如果通道没有数据，该goroutine会阻塞
  	<-ch
  	Printer("world")
  }
  
  func main() {
  
  	ch := make(chan int, 3)
  	// 打印缓冲区剩余数据个数，缓冲区大小
  	fmt.Printf("len(ch) = %d, cap(ch) = %d\n", len(ch), cap(ch))
  
  	go func() {
  		for i := 0; i < 3; i++ {
  			fmt.Printf("子协程：i = %d,", i)
  			ch <- i
  			fmt.Printf("子协程 len(ch) = %d, cap(ch) = %d\n", len(ch), cap(ch))
  		}
  	}()
  
  	time.Sleep(2 * time.Second)
  
  	for i := 0; i < 3; i++ {
          // 读管道中的内容，没有内容前会阻塞
  		num := <-ch
  		fmt.Println("num = ", num)
  	}
  	/*
  	len(ch) = 0, cap(ch) = 3
      子协程：i = 0,子协程 len(ch) = 1, cap(ch) = 3
      子协程：i = 1,子协程 len(ch) = 2, cap(ch) = 3
      子协程：i = 2,子协程 len(ch) = 3, cap(ch) = 3
      num =  0
      num =  1
      num =  2
  	*/
  }
  ```

* ## 关闭channel

  ```go
  package main
  
  import (
  	"fmt"
  	_ "time"
  )
  
  func main() {
  	ch := make(chan int)
  
  	go func() {
  		for i := 0; i < 5; i++ {
  			ch <- i
  		}
  		close(ch)
  	}()
  
  	for {
          // 如果ok为true，代表channel没有关闭
  		if num, ok := <-ch; ok == true {
  			fmt.Println("num is ", num)
  		} else {
  			break
  		}
  	}
      /*
      // 或者采用range的方法，当channel关闭后，range自动结束循环
      for num := range ch {
  		fmt.Println("num = ", num)
  	}
      */
  	
  	/*
  	num is  0
  	num is  1
  	num is  2
  	num is  3
  	num is  4
  	*/
  }
  ```
  * channel不需要像文件一样经常去关闭，只有确实没有数据发送才会去关闭channel
  * guanbichannel，无法向channel再发送数据，否则会引起panic错误
  * 对于nil channel，无论收发都会被阻塞

* ## 单方向的channel

  ```go
  // 单向channel变量的声明
  // ch1是单向channel，只用于写float64类型的数据，表示数据进入管道
  var ch1 chan<- float64
  // ch2是单向channel，只用于读int类型的数据，表示数据从管道中出来
  var ch2 <-chan int
  // 双向channel能隐式转换为单向channel，不能将单向channel转换成普通channel
  
  // demo
  package main
  
  import (
  	_ "fmt"
  	_ "time"
  )
  
  func main() {
  	ch := make(chan int)
  	var writeCh chan<- int = ch
  	var readCh <-chan int = ch
  	writeCh <- 666
      <-readCh
  	/*
  	<-writeCh
  	.\main.go:13:2: invalid operation: <-writeCh (receive from send-only type chan<- int)
  	*/
  	
  }
  ```

  ​	可以应用在生产者消费者模型上，在生产者中，只允许往channel中写入数据；在消费者中，只允许从channel中读取数据

* ## 定时器

  ```go
  // Timer
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func main() {
  	timer := time.NewTimer(2 * time.Second)
  	fmt.Println("当前时间：", time.Now())
  
  	// 2s后往timer.C写数据，有数据后就可以从timer.C读取
  	t := <-timer.C
  	fmt.Println("t = ", t)
  	/*
  	当前时间： 2021-02-21 13:49:48.224041 +0800 CST m=+0.007007001
  	t =  2021-02-21 13:49:50.224211 +0800 CST m=+2.007177001
  	*/
  }
  // 实现延时功能可以通过sleep，newTimer还有After
  
  // 实现计时器停止
  timer.Stop()
  
  // 实现定时器重置
  timer.Reset(1 * time.Second)
  
  
  // timer不能多次循环使用，ticker可以循环使用
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func main() {
  	timer := time.NewTimer(2 * time.Second)
  	fmt.Println("当前时间：", time.Now())
  
  	i := 0
  	for {
  		<-timer.C
  		i++
  		fmt.Println("i = ", i)
  
  		if i == 5 {
  			timer.Stop()
  			break
  		}
  	}
  	/*
  	当前时间： 2021-02-21 16:33:29.8620756 +0800 CST m=+0.006000701
  	i =  1
  	fatal error: all goroutines are asleep - deadlock!
  	
  	goroutine 1 [chan receive]:
  	main.main()
  		E:/gocode/src/differ_project/src/main.go:14 +0xf1
  	*/
  }
  
  
  //Ticker
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func main() {
  	ticker := time.NewTicker(2 * time.Second)
  	fmt.Println("当前时间：", time.Now())
  
  	i := 0
  	for {
  		<-ticker.C
  		i++
  		fmt.Println("i = ", i)
  
  		if i == 5 {
  			ticker.Stop()
  			break
  		}
  	}
  	/*
  	当前时间： 2021-02-21 16:35:10.8615233 +0800 CST m=+0.006999701
  	i =  1
  	i =  2
  	i =  3
  	i =  4
  	i =  5
  	成功: 进程退出代码 0.
  	*/
  }
  ```

  [**NewTimer，NewTicker，After对比**](https://blog.csdn.net/guyan0319/article/details/90450958)

* ## select

  ​	通过select可以监听channel上的数据流动。

  ​	select的用法与switch使用非常类似，每个选择条件由case语句来描述

  ​	select和switch相比，switch语句可以选择任何可以使用相等比较的条件相比，select的每一个case语句里必须是一个IO操作

  ​	在一个select语句中，Go语言会按照顺序从头到尾评估每一个发送和接受的语句。如果其中的任意一条语句可以继续执行（没有被阻塞），那就从可以执行的语句中任意选择一条来使用；如果没有一条语句可以执行，分为两种情况。如果有default，直接执行default；如果没有default，select语句将被阻塞，直到有一个case可以执行下去

* ## 用select实现斐波那契数列

  ```go
  package main
  
  import (
  	"fmt"
  
  	_ "time"
  )
  
  func fibonacci(ch chan<- int, quit <-chan bool) {
  	x, y := 1, 1
  	for {
  		select {
  		case ch <- x:
  			x, y = y, x+y
  		case flag := <-quit:
  			fmt.Println("flag = ", flag)
  			return
  		}
  	}
  }
  
  func main() {
  	// 数字通信, 通过CS模型实现
  	ch := make(chan int)
  	// 程序是否结束
  	quit := make(chan bool)
  
  	go func() {
  		for i := 0; i < 8; i++ {
              // 输出数列
  			num := <-ch
  			fmt.Println("num = ", num)
  		}
          // 停止斐波那契数列的计算
  		quit <- true
  	}()
  
  	// 生产者
  	fibonacci(ch, quit)
  }
  ```

* ## select实现超时机制

  ```go
  package main
  
  import (
  	"fmt"
  	"time"
  )
  
  func main() {
  	ch := make(chan int)
  	quit := make(chan bool)
  
  	go func() {
  		for {
  			select {
  			case num := <-ch:
  				fmt.Println("num = ", num)
  			case <-time.After(3 * time.Second):
  				fmt.Println("超时")
  				quit <- true
  			}
  		}
  	}()
  
  	<-quit
  	// 经过三秒后，输出"超时"
  }
  ```