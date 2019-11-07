# 0.Hello World

### 示例代码如下

```text
//main包中的main函数是程序的入口
package main  //包声明，表明代码所属模块

import "fmt"
import "os"

//函数实现,main函数不接受任何参数,通过os.Args获取参数
func main(){
	if len(os.Args) > 1{
		fmt.Println("Hello World! This is " + os.Args[1])
		os.Exit(0)	//main函数无任何返回值，通过exit来返回状态
	}
	os.Exit(-1)
}

//代码运行方式:
//1.直接运行: go run 1_hello.go
//2.编译运行: go build 1_hello.go; ./hello
```

