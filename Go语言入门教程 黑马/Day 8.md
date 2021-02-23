* ## Web工作方式

  ​	当输入URL的时候，浏览器会去请求DNS服务器，通过DNS服务器获取域名对应IP。然后通过IP地址找到IP对应的服务器后，要求建立TCP链接，等浏览器发送完HTTP Request包后，服务器接收到请求包并开始处理，然后返回Response包；客户端收到服务器的响应后开始渲染这个Response包里面的主题。等收到全部内容后，断开与服务器之间的TCP链接

* ## 响应报文测试方法

  ```go
  package main
  
  import (
  	"fmt"
  	"net/http"
  )
  
  func myHandler(w http.ResponseWriter, r *http.Request) {
  	fmt.Fprintln(w, "hello world")
  }
  
  func main() {
  	http.HandleFunc("/go", myHandler)
  
  	// 在指定的地址进行监听，开启一个HTTP
  	http.ListenAndServe("127.0.0.1:8000", nil)
  }
  
  // 浏览器直接访问 127.0.0.1:8000/go，就可以显示hello world
  ```

* ## [HTTP编程](https://studygolang.com/pkgdoc)

  ```go
  // server
  package main
  
  import (
  	"fmt"
  	"net/http"
  )
  
  // w, 给客户端回复数据；r，读取客户端发送的数据
  func HandConn(w http.ResponseWriter, req *http.Request) {
  	// 输出用户请求的方法
  	fmt.Println("method = ", req.Method)
  	fmt.Println("url = ", req.URL)
  	fmt.Println("header = ", req.Header)
  	fmt.Println("body = ", req.Body)
  	/*
  	method =  GET
  	url =  /
  	header =  map[Accept:[text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*//*;q=0.8,application/signed-exchange;v=b3;q=0.9] Accept-Encoding:[gzip, deflate, br] Accept-Language:[zh-CN,zh;q=0.9,en;q=0.8,zh-TW;q=0.7] Cache-Control:[max-age=0] Connection:[keep-alive] Sec-Ch-Ua:["Chromium";v="88", "Google Chrome";v="88", ";Not A Brand";v="99"] Sec-Ch-Ua-Mobile:[?0] Sec-Fetch-Dest:[document] Sec-Fetch-Mode:[navigate] Sec-Fetch-Site:[none] Sec-Fetch-User:[?1] Upgrade-Insecure-Requests:[1] User-Agent:[Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.182 Safari/537.36]]
  	body =  {}
  	*/
  	w.Write([]byte("hello go"))
  }
  
  func main() {
  	// 注册处理函数，用户连接时自动调用指定的处理函数
  	http.HandleFunc("/", HandConn)
  
  	// 监听绑定
  	http.ListenAndServe(":8000", nil)
  }
  
  // client
  package main 
  
  import (
  	"fmt"
  	"net/http"
  )
  
  func main() {
  	// Get函数发出HTTP/HTTPS请求
  	resp, err := http.Get("http://www.baidu.com")
  	if err != nil {
  		fmt.Println("http.get err = ", err) 
  		return
  	}
  	
  	defer resp.Body.Close()
  	
  	fmt.Println("status = ", resp.Status)
  	fmt.Println("statusCode = ", resp.StatusCode)
  	fmt.Println("Header = ", resp.Header)
  	fmt.Println("Body = ", resp.Body)
  	/*
  	status =  200 OK
  	statusCode =  200
  	Header =  map[Bdpagetype:[1] Bdqid:[0xfeefe01e00061845] Cache-Control:[private] Connection:[keep-alive] Content-Type:[text/html;charset=utf-8] Date:[Tue, 23 Feb 2021 04:40:45 GMT] Expires:[Tue, 23 Feb 2021 04:40:43 GMT] P3p:[CP=" OTI DSP COR IVA OUR IND COM " CP=" OTI DSP COR IVA OUR IND COM "] Server:[BWS/1.1] Set-Cookie:[BAIDUID=2E3189D54EEE340AED081BDE88D082A4:FG=1; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com BIDUPSID=2E3189D54EEE340AED081BDE88D082A4; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com PSTM=1614055245; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com BAIDUID=2E3189D54EEE340AC3B5490A4C63BC45:FG=1; max-age=31536000; expires=Wed, 23-Feb-22 04:40:45 GMT; domain=.baidu.com; path=/; version=1; comment=bd BDSVRTM=0; path=/ BD_HOME=1; path=/ H_PS_PSSID=33425_33402_33257_33344_31660_33604_33319_33267; path=/; domain=.baidu.com] Traceid:[1614055245026045057018370147824521582661] X-Ua-Compatible:[IE=Edge,chrome=1]]
  	Body =  &{[] 0x1199c030 <nil> <nil>}
  	*/
  	
  	buf := make([]byte, 4 * 1024)
  	var tmp string 
  	
  	for {
  		// 读取网页的全部内容
  		n, err := resp.Body.Read(buf)
  		if n == 0 {
  			fmt.Println("read err = ", err)
  			break
  		}
  		tmp += string(buf[:n])
  	}
  	fmt.Println("tmp = ", tmp)
  }
  ```

* ## 爬虫

  ### 四个步骤

  * 明确目标（要知道准备在哪个范围或者网站去搜索）
  * 爬（将所有的网站内容爬下来）
  * 取（去掉对我们没用的数据）
  * 处理数据（按照我们想要的方式存储和使用）

  ```go
  // 并发版爬虫
  package main
  
  import (
  	"fmt"
  	"net/http"
  	"os"
  	"strconv"
  )
  
  func HttpGet(url string) (result string, err error) {
  	// 通过Get的方式请求网页
  	resp, err1 := http.Get(url)
  	if err != nil {
  		err = err1
  		return
  	}
  
  	defer resp.Body.Close()
  
  	buf := make([]byte, 1024*4)
  	for {
  		n, err := resp.Body.Read(buf)
  		if n == 0 {
  			fmt.Println("resp.Body.Read err = ", err)
  			break
  		}
  		// 将爬取的内容写到result中
  		result += string(buf[:n])
  	}
  
  	return
  }
  
  func Spider(i int, page chan int) {
  	url := "https://tieba.baidu.com/p/7235301331?pn=" + strconv.Itoa(i)
  	fmt.Println("URL is ", url)
  	// 爬取url的内容
  	result, err := HttpGet(url)
  	if err != nil {
  		fmt.Println("HttpGet err = ", err)
  		return
  	}
  
  	// 创建文件
  	fileName := strconv.Itoa(i) + ".html"
  	f, err1 := os.Create(fileName)
  	if err1 != nil {
  		fmt.Println("os.Create err1 = ", err1)
  		return
  	}
  
  	// 往文件中写入爬取的内容
  	f.WriteString(result)
  	f.Close()
  
  	page <- i
  }
  
  func DoWork(start, end int) {
  	fmt.Printf("准备爬取")
  
  	page := make(chan int)
  
  	for i := start; i <= end; i++ {
  		/*
  			// 不可以直接用go，因为如果for循环走完之后
  			//  子协程们可能还没有爬完就被迫关闭了, 可以通过channel阻塞实现
  			// go Spider(i)
  		*/
  
  		// 通过channel实现
  		go Spider(i, page)
  	}
  
  	for i := start; i <= end; i++ {
  		fmt.Println("第%d个网站爬取成功", <-page)
  	}
  }
  
  func main() {
  	//  输入查询范围
  	DoWork(1, 20)
  }
  ```

* ##  对捧腹网内容进行爬取

  * ### 明确目标

    * 确认目标网址，https://www.pengfue.com/

    * 查看网页源码，寻找标题对应的源码

      ![image-20210223175619495](E:\selfgit\golang\Go语言入门教程 黑马\Day 8.assets\image-20210223175619495.png)

    * 进入href指向的URL，并且查找URL的规律

      ```css
      https://www.pengfue.com/index_1.html
      https://www.pengfue.com/index_2.html
      https://www.pengfue.com/index_3.html
      ```

      主页面规律

      ```css
      <h1 class="dp-b"><a href="https://www.pengfue.com/content_1857807_1.html" target="_blank">会放坏</a>
      
      <h1 class="dp-b"><a href="https://www.pengfue.com/content_1857806_1.html" target="_blank">肉多舒服</a>
      
      <h1 class="dp-b"><a href="https://www.pengfue.com/content_1857805_1.html" target="_blank">卸妆</a>
      ```

    * 查看URL页中的源码，寻找内容对应的源码

      ![image-20210223175908499](E:\selfgit\golang\Go语言入门教程 黑马\Day 8.assets\image-20210223175908499.png)

      段子规律

      ```css
      // 标题
      <h1>会放坏	</h1> 
      
      // 内容
      <div class="content-txt pt10"> 女：“我购物车里的那些水果牛奶饮料零食你赶紧给我买！” 男：“着什么急呀？” 女：“天越来越热了，会放坏的。”	<a id="prev" href
      ```

* ## 代码

  ```go
  package main
  
  import (
  	"fmt"
  	"net/http"
  	"os"
  	"regexp"
  	"strconv"
  	"strings"
  )
  
  func HttpGet(url string) (result string, err error) {
  	resp, err1 := http.Get(url)
  	if err1 != nil {
  		err = err1
  		return
  	}
  
  	defer resp.Body.Close()
  
  	buf := make([]byte, 4*1024)
  	for {
  		n, err2 := resp.Body.Read(buf)
  		if err2 != nil {
  			break
  		}
  
  		result += string(buf[:n])
  	}
  
  	return
  }
  
  func SpiderOneJoy(url string) (title, content string, err error) {
  	result, err1 := HttpGet(url)
  	if err1 != nil {
  		err = err1
  		return
  	}
  
  	// 取关键信息
  	// <h1>	</h1>
  	re := regexp.MustCompile(`<h1>(?s:(.*?))</h1>`)
  	if re == nil {
  		err = fmt.Errorf("%s", "regexp.MustComplie err")
  		return
  	}
  
  	// 取标题
  	tmpTitle := re.FindAllStringSubmatch(result, 1)
  	for _, data := range tmpTitle {
  		title = data[1]
  		// 把空格去掉
  		title = strings.Replace(title, " ", "", -1)
  		// 把\n去掉
  		title = strings.Replace(title, "\n", "", -1)
  		// 把\r去掉
  		title = strings.Replace(title, "\r", "", -1)
  		title = strings.Replace(title, "\t", "", -1)
  		break
  	}
  
  	// 取内容
  	// <div class="content-txt pt10">内容<a id="prev" href
  	re = regexp.MustCompile(`<div class="content-txt pt10">(?s:(.*?))<a id="prev" href="`)
  	if re == nil {
  		err = fmt.Errorf("%s", "regexp.MustComplie err")
  		return
  	}
  
  	tmpContent := re.FindAllStringSubmatch(result, 1)
  	for _, data := range tmpContent {
  		content = data[1]
  		// 把空格去掉
  		content = strings.Replace(content, " ", "", -1)
  		// 把回车去掉
  		content = strings.Replace(content, "\n", "", -1)
  		// 把换行去掉
  		content = strings.Replace(content, "\r", "", -1)
  		content = strings.Replace(content, "\t", "", -1)
  
  		break
  	}
  
  	/*
  		title is  会放坏
  		content is  女：“我购物车里的那些水果牛奶饮料零食你赶紧给我买！”男：“着什么急呀？”女：“天越来越热了，会放坏的。”
  	*/
  	return
  
  }
  
  func WriteMessageToFile(i int, fileTitle, fileContent []string) {
  
  	f, err := os.Create(strconv.Itoa(i) + ".txt")
  
  	if err != err {
  		fmt.Println("os.Create err = ", err)
  		return
  	}
  
  	defer f.Close()
  
  	n := len(fileTitle)
  	for i := 0; i < n; i++ {
  		f.WriteString(fileTitle[i])
  		f.WriteString(fileContent[i])
  		f.WriteString("\n~~~~~~~~\n")
  	}
  }
  func Spider(i int, page chan int) {
  	url := "https://www.pengfue.com/index_" + strconv.Itoa(i) + ".html"
  
  	fmt.Println("URL is ", url)
  
  	result, err := HttpGet(url)
  	if err != nil {
  		fmt.Println("HttpGet err = ", err)
  		return
  	}
  
  	/*
  		测试读取内容
  		fmt.Println("result = ", result)
  
  		f, err3 := os.Create("test.txt")
  		if err3 != err {
  			fmt.Println("os.Create err = ", err)
  			return
  		}
  		defer f.Close()
  		f.WriteString(result)
  	*/
  
  	// 取<h1 class="dp-b"><a href="https://www.pengfue.com/content_1857807_1.html"
  	// 解释正则表达式
  	re := regexp.MustCompile(`<h1 class="dp-b"><a href="(?s:(.*?))"`)
  	if re == nil {
  		fmt.Println("regexp.MustComplie err")
  		return
  	}
  
  	joyUrls := re.FindAllStringSubmatch(result, -1)
  	// fmt.Println("joyUrls is ", joyUrls)
  	// joyUrls is  [[<h1 class="dp-b"><a href="https://www.pengfue.com/content_1857807_1.html" https://www.pengfue.com/content_1857807_1.html]
  
  	fileTitle := make([]string, 0)
  	fileContent := make([]string, 0)
  
  	// 取网址，第一个返回下标，第二个返回内容
  	for _, data := range joyUrls {
  		/*/*
  		fmt.Println("url = ", data[1])
  		url =  https://www.pengfue.com/content_1857807_1.html
  		url =  https://www.pengfue.com/content_1857806_1.html
  		url =  https://www.pengfue.com/content_1857805_1.html
  		*/
  
  		// 开始爬取每一个笑话
  		// 标题，内容，err
  		title, content, err := SpiderOneJoy(data[1])
  		if err != nil {
  			fmt.Println("SpiderOneJoy err = ", err)
  			continue
  		}
  		// fmt.Println("title is ", title)
  		// fmt.Println("content is ", content)
  		// 追加标题和内容
  		fileTitle = append(fileTitle, title)
  		fileContent = append(fileContent, content)
  
  		WriteMessageToFile(i, fileTitle, fileContent)
  	}
  }
  
  func Work(start, end int) {
  	fmt.Println("准备爬取")
  
  	page := make(chan int)
  
  	for i := start; i <= end; i++ {
  		// 爬取主页面
  		go Spider(i, page)
  	}
  
  	for i := start; i <= end; i++ {
  		fmt.Println("第%d个网站爬取成功", <-page)
  	}
  }
  
  func main() {
  	var start, end int
  
  	fmt.Println("请输入start")
  	// 如果使用scanf分别输入start和end的话，输入结尾会有一个回车，\n也会当成一个字符传递给end。
  	// 可以这样连续输入start和end即可避免上述问题，fmt.Scanf("%d %d", &start, &end)
  	fmt.Scan(&start)
  	fmt.Println("请输入end")
  	fmt.Scan(&end)
  
  	Work(start, end)
  
  }
  ```