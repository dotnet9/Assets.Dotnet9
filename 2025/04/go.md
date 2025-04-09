1. 创建go项目：

```bash
go mod init github.com/dotnet9/firtGin
```

2. 获取gin模块

```bash
go get github.com/gin-gonic/gin
```

提示

```bash
go: module github.com/gin-gonic/gin: Get "https://proxy.golang.org/github.com/gin-gonic/gin/@v/list": dial tcp 142.250.217.81:443: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.
```

解决[参考]([【GO】安装Gin过程中报错go: module github.com/gin-gonic/gin: Get “https://proxy.golang.org/github.com/gin-goni-CSDN博客](https://blog.csdn.net/weixin_56526539/article/details/146141631)),添加go代理：

```bash
go env -w GOPROXY=https://goproxy.cn,direct
```

再次执行gin模块安装

```bash
go get github.com/gin-gonic/gin
```

成功：

```bash
PS D:\github\go\firstGin> go get github.com/gin-gonic/gin
go: downloading github.com/gin-gonic/gin v1.10.0
go: downloading github.com/gin-contrib/sse v0.1.0
go: downloading github.com/mattn/go-isatty v0.0.20
...
```

创建go文件first.go

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/hi", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "hi, this is my first go app",
		})
	})
	r.Run()
}
```

运行：

```bash
go run first.go
```

浏览器访问：

```ba
http://127.0.0.1:8080/hi