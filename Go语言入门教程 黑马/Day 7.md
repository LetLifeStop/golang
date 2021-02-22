* ## TCP服务器端编写

  * ## 流程

    ![image-20210222100126908](E:\selfgit\golang\Go语言入门教程 黑马\Day 7.assets\image-20210222100126908.png)

  * ### 代码

    ```go
    package main
    
    import (
    	"fmt"
    	"net"
    )
    
    func main() {
    	// 监听
    	listener, err := net.Listen("tcp", "127.0.0.1:8000")
    	if err != nil {
    		fmt.Println("err = ", err)
    		return
    	}
    
    	defer listener.Close()
    
    	// 阻塞等待用户连接
    	//for {
    	conn, err := listener.Accept()
    	if err != nil {
    		fmt.Println("err = ", err)
    		return
    	}
    
    	buf := make([]byte, 1024)
    	n, err := conn.Read(buf)
    	if err != nil {
    		fmt.Println("err = ", err)
    		return
    	}
    
    	fmt.Println("buf = ", string(buf[:n]))
    	defer conn.Close()
    	//	}
    }
    ```

* ## TCP客户端编写

  ```go
  package main
  
  import (
  	"fmt"
  	"net"
  )
  
  func main() {
      // 主动连接服务器
  	conn, err := net.Dial("tcp", "127.0.0.1:8000")
  	if err != nil {
  		fmt.Println("err = ", err)
  		return
  	}
  
  	defer conn.Close()
  
  	conn.Write([]byte("are u ok?"))
  
  }
  ```

* ## 并发服务器

  ```go
  package main
  
  import (
  	"fmt"
  	"net"
  )
  
  func Handler(conn net.Conn) {
  	fmt.Println("Connect success")
      
  	buf := make([]byte, 1024)
  	n, err := conn.Read(buf)
  	if err != nil {
  		fmt.Println("Read, err = ", err)
  		return
  	}
  
  	fmt.Println("buf = ", string(buf[:n]))
  }
  
  func main() {
  	// 监听
  	listener, err := net.Listen("tcp", "127.0.0.1:8000")
  	if err != nil {
  		fmt.Println("Listen, err = ", err)
  		return
  	}
  
  	defer listener.Close()
  
  	// 阻塞等待用户连接
  	var conn net.Conn
  	// var err error
  	for {
  		conn, err = listener.Accept()
  		if err != nil {
  			fmt.Println("Accept, err = ", err)
  			return
  		}
  
  		// 每一个客户端发送建立连接请求都会有对应的协程处理
  		go Handler(conn)
  	}
  	defer conn.Close()
  
  }
  ```

* ## 获取文件属性

  os.Stat函数

* ## 传输文件的实现

  ```go
  // client
  package main
  
  import (
  	"fmt"
  	"io"
  	"net"
  	"os"
  )
  
  func SendFile(path string, conn net.Conn) {
  	// 以只读的方式打开文件
  	f, err := os.Open(path)
  	if err != nil {
  		fmt.Println("os.Open err = ", err)
  		return
  	}
  
  	buf := make([]byte, 1024*4)
  	// 读文件内容
  	for {
  		// 先把文件内容读取到buf中
  		n, err := f.Read(buf)
  		if err != nil {
  			if err == io.EOF {
  				fmt.Println("File Send Over")
  			} else {
  				fmt.Println("f.Read err = ", err)
  			}
  			return
  		}
  		conn.Write(buf[:n])
  	}
  }
  
  func main() {
  	fmt.Println("请输入需要传输的文件")
  	var path string
  	fmt.Scan(&path)
  
  	// 获取文件名
  	info, err := os.Stat(path)
  	if err != nil {
  		fmt.Println("os.Stat err = ", err)
  		return
  	}
  
  	// 连接到服务器
  	conn, err := net.Dial("TCP", "127.0.0.1:8888")
  	if err != nil {
  		fmt.Println("net.Dial err = ", err)
  		return
  	}
  
  	defer conn.Close()
  
  	// 先发送给接收者文件名
  	_, err = conn.Write([]byte(info.Name()))
  	if err != nil {
  		fmt.Println("conn.Write err = ", err)
  		return
  	}
  
  	var n int
  	buf := make([]byte, 1024)
  	// 接受对方的回复，如果回复内容为ok，说明对方可以接受该文件
  	n, err = conn.Read(buf)
  	if err != nil {
  		fmt.Println("conn.Read err = ", err)
  		return
  	}
  
  	if string(buf[:n]) == "ok" {
  		SendFile(path, conn)
  	}
  
  }
  
  // server
  package main
  
  import (
  	"fmt"
  	"io"
  	"net"
  	"os"
  )
  
  func ReceiveFile(path string, conn net.Conn) {
  	f, err := os.Create(path)
  	if err != nil {
  		fmt.Println("os.Create err = ", err)
  		return
  	}
  
  	buf := make([]byte, 1024*4)
  	for {
  		n, err := conn.Read(buf)
  		if err != nil {
  			if err == io.EOF {
  				fmt.Println("File receive over")
  			} else {
  				fmt.Println("conn.Read err = ", err)
  			}
  			return
  		} else {
  			f.Write(buf[:n])
  		}
  	}
  }
  
  func main() {
  	listener, err := net.Listen("tcp", "127.0.0.1:8888")
  	if err != nil {
  		fmt.Println("net.Listen err = ", err)
  		return
  	}
  
  	defer listener.Close()
  
  	conn, err := listener.Accept()
  	if err != nil {
  		fmt.Println("listener.Accept() err = ", err)
  		return
  	}
  
  	buf := make([]byte, 1024)
  	var n int
  	n, err = conn.Read(buf)
  	if err != nil {
  		fmt.Println("conn.Read err = ", err)
  		return
  	}
  
  	fileName := string(buf[:n])
  
  	// 回复ok
  	_, err = conn.Write([]byte("ok"))
  
  	// 接受文件内容
  	ReceiveFile(fileName, conn)
  }
  ```

* ## 并发聊天服务器

  * ### 思路

  ![image-20210222140754494](E:\selfgit\golang\Go语言入门教程 黑马\Day 7.assets\image-20210222140754494.png)

  * ### 代码

    ```go
    // server
    package main
    
    import (
    	"fmt"
    	"net"
    	"time"
    )
    
    type Client struct {
    	// client和server发送数据的管道
    	C chan string
    	// 用户名
    	Name string
    	// IP+PORT
    	Addr string
    }
    
    var onLineMap map[string]Client
    
    var message = make(chan string)
    
    // 对map进行遍历，如果message中有数据，该协程会将message中的数据 发送到每个client结构体中的channel
    func Manager() {
    	onLineMap = make(map[string]Client)
    
    	for {
    		// 阻塞等待广播消息
    		msg := <-message
    
    		// 给每一个在线成员的channel中送入广播消息
    		for _, cli := range onLineMap {
    			cli.C <- msg
    		}
    	}
    }
    
    // 遍历每个结构体的channel，如果channel中有数据则通过conn.Write将channel中数据发送到客户端
    func WriteMsgToClient(cli Client, conn net.Conn) {
    	// range的作用是确保在server中定义的client结构体中的channel信息能完整发送給client
    	for msg := range cli.C {
    		conn.Write([]byte(msg + "\n"))
    	}
    }
    
    func MakeMsg(cli Client, msg string) (buf string) {
    	buf = "[" + cli.Addr + "]" + cli.Name + msg
    	return
    }
    
    // 等待处理新的链接；对客户上线信息进行广播，同时创建新的协程用来接收客户端的信息
    func HandleConn(conn net.Conn) {
    	defer conn.Close()
    
    	// 对方是否主动退出
    	isQuit := make(chan bool)
    
    	// 对方是否有数据发送，用来判断是否超时
    	hasData := make(chan bool)
    
    	// 获取客户端的网络地址
    	cliAddr := conn.RemoteAddr().String()
    
    	// 默认用户名和网络地址相同
    	cli := Client{make(chan string), cliAddr, cliAddr}
    	onLineMap[cliAddr] = cli
    
    	// 进行消息的发送
    	go WriteMsgToClient(cli, conn)
    
    	// 当有人上线时进行广播
    	message <- MakeMsg(cli, "log in")
    
    	// 新建一个协程，接受用户发送过来的数据
    	// 如果把go去掉，别的client无法检测到当前client下线的原因：一直卡在for循环中，根本没有可能调用下面的select
    	go func() {
    		buf := make([]byte, 2048)
    		for {
    			n, err := conn.Read(buf)
    
    			// 对方断开或者程序出现error
    			if n == 0 {
    				isQuit <- true
    				fmt.Println("conn.Read err = ", err)
    				return
    			}
    
    			msg := buf[:n]
    			message <- MakeMsg(cli, string(msg))
    			// 如果有数据，即进行数据的写入
    			hasData <- true
    		}
    	}()
    
    	for {
    		// 通过select实现广播下线信息
    		select {
    		case <-isQuit:
    			delete(onLineMap, cliAddr)
    			// 对谁下线进行广播
    			message <- MakeMsg(cli, "log out")
    			return
    		case <-hasData:
    
    		case <-time.After(10 * time.Second):
    			delete(onLineMap, cliAddr)
    			message <- MakeMsg(cli, "time out")
    		}
    	}
    
    }
    
    func main() {
    	listener, err := net.Listen("tcp", "127.0.0.1:8000")
    	if err != nil {
    		fmt.Println("net.Listen err = ", err)
    		return
    	}
    
    	defer listener.Close()
    
    	// 对在线成员进行遍历，如果有数据要进行广播，通过该协程可以实现
    	go Manager()
    
    	for {
    		conn, err := listener.Accept()
    		if err != nil {
    			fmt.Println("listener.Accept err = ", err)
    			continue
    		}
    		// 主协程处理用户连接
    		go HandleConn(conn)
    	}
    
    }
    
    
    // client
    package main
    
    import (
    	"fmt"
    	"net"
    )
    
    func main() {
    	// 主动连接服务器
    	conn, err := net.Dial("tcp", "127.0.0.1:8000")
    	if err != nil {
    		fmt.Println("err = ", err)
    		return
    	}
    
    	buf := make([]byte, 1024)
    	var n int
    
    	for {
    		n, err = conn.Read(buf)
    		if err != nil {
    			fmt.Println("conn.Read err = ", err)
    		}
    		fmt.Println("buf = ", string(buf[:n]))
    	}
    
    	defer conn.Close()
    }
    ```