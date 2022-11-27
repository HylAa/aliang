---
title: Go - Go语言Web开发框架介绍及简单使用
tags:
  - Go
categories: []
date: 2021-01-22 15:15:00
updated: 2021-01-22 15:15:00
top_img: https://upload.wikimedia.org/wikipedia/commons/0/05/Go_Logo_Blue.svg
cover: https://upload.wikimedia.org/wikipedia/commons/0/05/Go_Logo_Blue.svg
---

# 前言

> 我们做Java开发的时候 做Web开发脱离不了框架 例如 `Spring` `Mybatis` 等开发框架，当然我们学习Go 也可以作为Web开发来使用,就会用到接下来介绍的框架。
>
> ​																														-- 屏幕前的你一定是个很温柔的人吧



# 1. 简介

## 1.1. 介绍

- Gin是一个golang的微框架，封装比较优雅，API友好，源码注释比较明确，具有快速灵活，容错方便等特点
- 对于golang而言，web框架的依赖要远比Python，Java之类的要小。自身的`net/http`足够简单，性能也非常不错
- 借助框架开发，不仅可以省去很多常用的封装带来的时间，也有助于团队的编码风格和形成规范

## 1.2. 安装

要安装Gin软件包，您需要安装Go并首先设置Go工作区。

1.首先需要安装Go（需要1.10+版本），然后可以使用下面的Go命令安装Gin。

> go get -u github.com/gin-gonic/gin

2.将其导入您的代码中：

> import "github.com/gin-gonic/gin"

3.（可选）导入net/http。例如，如果使用常量，则需要这样做http.StatusOK。

> import "net/http"

## 1.3. hello word

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/thinkerou/favicon"
)

func main() {
	//启动服务
	server := gin.Default()
	//设置 favicon 
	server.Use(favicon.New("./fic.jpg"))
	//设置get接口
	server.GET("/hello", func(context *gin.Context) {
		context.JSON(200, gin.H{"msg": "go---阿良"})
	})
	//指定端口
	server.Run(":8082")
}

```
# 2. 路由

## 2.1. 基本路由

- gin 框架中采用的路由库是基于httprouter做的
- 地址为：https://github.com/julienschmidt/httprouter

```go
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/", func(c *gin.Context) {
        c.String(http.StatusOK, "hello word")
    })
    r.POST("/xxxpost",getting)
    r.PUT("/xxxput")
    //监听端口默认为8080
    r.Run(":8000")
}
```

## 2.2.Restful风格的API

- gin支持Restful风格的API
- 即Representational State Transfer的缩写。直接翻译的意思是"表现层状态转化"，是一种互联网应用程序的API设计理念：URL定位资源，用HTTP描述操作

1.获取文章 /blog/getXxx Get blog/Xxx

2.添加 /blog/addXxx POST blog/Xxx

3.修改 /blog/updateXxx PUT blog/Xxx

4.删除 /blog/delXxxx DELETE blog/Xxx

## 2.3.API参数

- 可以通过Context的Param方法来获取API参数
- localhost:8000/xxx/zhangsan

```go
package main

import (
    "net/http"
    "strings"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/user/:name/*action", func(c *gin.Context) {
        name := c.Param("name")
        action := c.Param("action")
        //截取/
        action = strings.Trim(action, "/")
        c.String(http.StatusOK, name+" is "+action)
    })
    //默认为监听8080端口
    r.Run(":8000")
}
```



## 2.4.URL参数

- URL参数可以通过DefaultQuery()或Query()方法获取
- DefaultQuery()若参数不村则，返回默认值，Query()若不存在，返回空串
- API?name=zs

```go
package main

import (
    "fmt"
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/user", func(c *gin.Context) {
        //指定默认值
        //http://localhost:8080/user 才会打印出来默认的值
        name := c.DefaultQuery("name", "枯藤")
        c.String(http.StatusOK, fmt.Sprintf("hello %s", name))
    })
    r.Run()
}
```

## 2.5.表单参数

- 表单传输为post请求，http常见的传输格式为四种：
  - application/json
  - application/x-www-form-urlencoded
  - application/xml
  - multipart/form-data
- 表单参数可以通过PostForm()方法获取，该方法默认解析的是x-www-form-urlencoded或from-data格式的参数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <form action="http://localhost:8080/form" method="post" action="application/x-www-form-urlencoded">
        用户名：<input type="text" name="username" placeholder="请输入你的用户名">  <br>
        密&nbsp;&nbsp;&nbsp;码：<input type="password" name="userpassword" placeholder="请输入你的密码">  <br>
        <input type="submit" value="提交">
    </form>
</body>
</html>
```
```go
package main
import (
    "fmt"
    "net/http"

    "github.com/gin-gonic/gin"
)
func main() {
    r := gin.Default()
    r.POST("/form", func(c *gin.Context) {
        types := c.DefaultPostForm("type", "post")
        username := c.PostForm("username")
        password := c.PostForm("userpassword")
        // c.String(http.StatusOK, fmt.Sprintf("username:%s,password:%s,type:%s", username, password, types))
        c.String(http.StatusOK, fmt.Sprintf("username:%s,password:%s,type:%s", username, password, types))
    })
    r.Run()
}
```

输出结果：

![img](https://www.topgoer.com/static/gin/1.1/5.png)

![img](https://www.topgoer.com/static/gin/1.1/6.png)

## 2.6.上传单个文件

- multipart/form-data格式用于文件上传
- gin文件上传与原生的net/http方法类似，不同在于gin把原生的request封装到c.Request中

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <form action="http://localhost:8080/upload" method="post" enctype="multipart/form-data">
          上传文件:<input type="file" name="file" >
          <input type="submit" value="提交">
    </form>
</body>
</html>
```
```go
package main
import (
    "github.com/gin-gonic/gin"
)
func main() {
    r := gin.Default()
    //限制上传最大尺寸
    r.MaxMultipartMemory = 8 << 20
    r.POST("/upload", func(c *gin.Context) {
        file, err := c.FormFile("file")
        if err != nil {
            c.String(500, "上传图片出错")
        }
        // c.JSON(200, gin.H{"message": file.Header.Context})
        c.SaveUploadedFile(file, file.Filename)
        c.String(http.StatusOK, file.Filename)
    })
    r.Run()
}
```

效果演示：

![img](https://www.topgoer.com/static/gin/1.1/7.png)

![img](https://www.topgoer.com/static/gin/1.1/8.png)

### 2.6.1. 上传特定文件

有的用户上传文件需要限制上传文件的类型以及上传文件的大小，但是gin框架暂时没有这些函数(也有可能是我没找到)，因此基于原生的函数写法自己写了一个可以限制大小以及文件类型的上传函数

```go
package main

import (
    "fmt"
    "log"
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.POST("/upload", func(c *gin.Context) {
        _, headers, err := c.Request.FormFile("file")
        if err != nil {
            log.Printf("Error when try to get file: %v", err)
        }
        //headers.Size 获取文件大小
        if headers.Size > 1024*1024*2 {
            fmt.Println("文件太大了")
            return
        }
        //headers.Header.Get("Content-Type")获取上传文件的类型
        if headers.Header.Get("Content-Type") != "image/png" {
            fmt.Println("只允许上传png图片")
            return
        }
        c.SaveUploadedFile(headers, "./video/"+headers.Filename)
        c.String(http.StatusOK, headers.Filename)
    })
    r.Run()
}
```

## 2.7.上传多个文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <form action="http://localhost:8000/upload" method="post" enctype="multipart/form-data">
          上传文件:<input type="file" name="files" multiple>
          <input type="submit" value="提交">
    </form>
</body>
</html>
```
```go
package main
import (
   "github.com/gin-gonic/gin"
   "net/http"
   "fmt"
)
// gin的helloWorld
func main() {
   // 1.创建路由
   // 默认使用了2个中间件Logger(), Recovery()
   r := gin.Default()
   // 限制表单上传大小 8MB，默认为32MB
   r.MaxMultipartMemory = 8 << 20
   r.POST("/upload", func(c *gin.Context) {
      form, err := c.MultipartForm()
      if err != nil {
         c.String(http.StatusBadRequest, fmt.Sprintf("get err %s", err.Error()))
      }
      // 获取所有图片
      files := form.File["files"]
      // 遍历所有图片
      for _, file := range files {
         // 逐个存
         if err := c.SaveUploadedFile(file, file.Filename); err != nil {
            c.String(http.StatusBadRequest, fmt.Sprintf("upload err %s", err.Error()))
            return
         }
      }
      c.String(200, fmt.Sprintf("upload ok %d files", len(files)))
   })
   //默认端口号是8080
   r.Run(":8000")
}
```

效果演示：

![img](https://www.topgoer.com/static/gin/1.1/9.png)

![img](https://www.topgoer.com/static/gin/1.1/10.png)

## 2.8.routes group

- routes group是为了管理一些相同的URL

```go
package main

import (
   "github.com/gin-gonic/gin"
   "fmt"
)

// gin的helloWorld

func main() {
   // 1.创建路由
   // 默认使用了2个中间件Logger(), Recovery()
   r := gin.Default()
   // 路由组1 ，处理GET请求
   v1 := r.Group("/v1")
   // {} 是书写规范
   {
      v1.GET("/login", login)
      v1.GET("submit", submit)
   }
   v2 := r.Group("/v2")
   {
      v2.POST("/login", login)
      v2.POST("/submit", submit)
   }
   r.Run(":8000")
}

func login(c *gin.Context) {
   name := c.DefaultQuery("name", "jack")
   c.String(200, fmt.Sprintf("hello %s\n", name))
}

func submit(c *gin.Context) {
   name := c.DefaultQuery("name", "lily")
   c.String(200, fmt.Sprintf("hello %s\n", name))
}
```

效果演示:

![img](https://www.topgoer.com/static/gin/1.1/11.png)

## 2.9.路由原理

- httproter会将所有路由规则构造一颗前缀树
- 例如有 root and as at cn com

![img](https://www.topgoer.com/static/gin/1.1/12.png)

## 3.0.路由拆分与注册

###  3.0.1. 基本的路由注册

下面最基础的gin路由注册方式，适用于路由条目比较少的简单项目或者项目demo。

```go
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func helloHandler(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
        "message": "Hello www.topgoer.com!",
    })
}

func main() {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

### 3.0.2. 路由拆分成单独文件或包

当项目的规模增大后就不太适合继续在项目的main.go文件中去实现路由注册相关逻辑了，我们会倾向于把路由部分的代码都拆分出来，形成一个单独的文件或包：

我们在routers.go文件中定义并注册路由信息：

```go
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func helloHandler(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
        "message": "Hello www.topgoer.com!",
    })
}

func setupRouter() *gin.Engine {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
    return r
}
```

此时main.go中调用上面定义好的setupRouter函数：

```go
func main() {
    r := setupRouter()
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

此时的目录结构：

```go
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers.go
```

把路由部分的代码单独拆分成包的话也是可以的，拆分后的目录结构如下：

```
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers
    └── routers.go
```

routers/routers.go需要注意此时setupRouter需要改成首字母大写：

```go
package routers

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func helloHandler(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
        "message": "Hello www.topgoer.com",
    })
}

// SetupRouter 配置路由信息
func SetupRouter() *gin.Engine {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
    return r
}
```

main.go文件内容如下：

```go
package main

import (
    "fmt"
    "gin_demo/routers"
)

func main() {
    r := routers.SetupRouter()
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

### 3.0.3. 路由拆分成多个文件

当我们的业务规模继续膨胀，单独的一个routers文件或包已经满足不了我们的需求了，

```go
func SetupRouter() *gin.Engine {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
  r.GET("/xx1", xxHandler1)
  ...
  r.GET("/xx30", xxHandler30)
    return r
}
```

因为我们把所有的路由注册都写在一个SetupRouter函数中的话就会太复杂了。

我们可以分开定义多个路由文件，例如：

```
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers
    ├── blog.go
    └── shop.go
```

routers/shop.go中添加一个LoadShop的函数，将shop相关的路由注册到指定的路由器：

```
func LoadShop(e *gin.Engine)  {
    e.GET("/hello", helloHandler)
  e.GET("/goods", goodsHandler)
  e.GET("/checkout", checkoutHandler)
  ...
}
```

routers/blog.go中添加一个LoadBlog的函数，将blog相关的路由注册到指定的路由器：

```go
func LoadBlog(e *gin.Engine) {
    e.GET("/post", postHandler)
  e.GET("/comment", commentHandler)
  ...
}
```

在main函数中实现最终的注册逻辑如下：

```go
func main() {
    r := gin.Default()
    routers.LoadBlog(r)
    routers.LoadShop(r)
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

### 3.0.4. 路由拆分到不同的APP

有时候项目规模实在太大，那么我们就更倾向于把业务拆分的更详细一些，例如把不同的业务代码拆分成不同的APP。

因此我们在项目目录下单独定义一个app目录，用来存放我们不同业务线的代码文件，这样就很容易进行横向扩展。大致目录结构如下：

```
gin_demo
├── app
│   ├── blog
│   │   ├── handler.go
│   │   └── router.go
│   └── shop
│       ├── handler.go
│       └── router.go
├── go.mod
├── go.sum
├── main.go
└── routers
    └── routers.go
```

其中app/blog/router.go用来定义post相关路由信息，具体内容如下：

```go
func Routers(e *gin.Engine) {
    e.GET("/post", postHandler)
    e.GET("/comment", commentHandler)
}
```

app/shop/router.go用来定义shop相关路由信息，具体内容如下：

```go
func Routers(e *gin.Engine) {
    e.GET("/goods", goodsHandler)
    e.GET("/checkout", checkoutHandler)
}
```

routers/routers.go中根据需要定义Include函数用来注册子app中定义的路由，Init函数用来进行路由的初始化操作：

```go
type Option func(*gin.Engine)

var options = []Option{}

// 注册app的路由配置
func Include(opts ...Option) {
    options = append(options, opts...)
}

// 初始化
func Init() *gin.Engine {
    r := gin.New()
    for _, opt := range options {
        opt(r)
    }
    return r
}
```

main.go中按如下方式先注册子app中的路由，然后再进行路由的初始化：

```go
func main() {
    // 加载多个APP的路由配置
    routers.Include(shop.Routers, blog.Routers)
    // 初始化路由
    r := routers.Init()
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

转自：https://www.liwenzhou.com/posts/Go/gin_routes_registry/