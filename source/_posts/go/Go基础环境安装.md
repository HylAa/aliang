---
title: Go - Go的基础环境安装
tags:
  - Go
categories: []
date: 2021-01-22 15:15:00
updated: 2021-01-22 15:15:00
top_img: https://upload.wikimedia.org/wikipedia/commons/0/05/Go_Logo_Blue.svg
cover: https://upload.wikimedia.org/wikipedia/commons/0/05/Go_Logo_Blue.svg
---

# 前言



> 时光然然 Java已经干了4年多虽然很多知识还是不会，但是Java整体体系大概摸清了。
>
> 我一直在思索未来要做什么，思考了几次后，思考起了未来，认为 云原生、区块链 在未来会爆发，那么 Go 语言就变得很重要了，以后尽量多学一学Go语言，尽量做到体系化学习。
>
> ​																																-- 阿良 唯有热爱，能抵岁月漫长 



# 语言环境安装：Windows 下安装 Go

Windows 安装包：点击进入官方下载

![Windows 开发环境](https://cdn.learnku.com/uploads/images/201905/24/21793/ZLFeozMINM.png!large)

## 下载

打开下载完的 Windows Installer 程序包 (.msi) 进行安装，默认情况下.msi 文件会安装在 c:\Go 目录下，可选择自定义路径进行安装。

## 测试
安装完毕后，打开命令行程序，输入：
go help

返回结果如下则代表安装成功：

![Windows 开发环境](https://cdn.learnku.com/uploads/images/201905/24/21793/DM3iyiZwtw.png!large)



# Go 语言环境安装：Mac 下安装 Go

在 Mac 系统中安装 Go 的方式有很多种，这里我们介绍最简单方便的一种，那就是使用 Google 为 Mac 用户提供的已编译好的二进制软件安装包，我们只需下载并安装软件包就可以进行 Go 程序的开发了。

## 下载并安装
**注意**： 如果是从旧版本的 Go 升级，必须先卸载现有版本。

首先访问 golang.google.cn/dl/ ，点击 Apple macOS 中的链接下载软件包至本地。

![下载](https://cdn.learnku.com/uploads/images/201905/20/7636/ZPVXXyYg7O.png!large)


然后运行软件包，遵照提示点几下按钮就安装好了。安装程序将安装 Go 编译器、工具、库文件至 /usr/local/go 目录，并把` /usr/local/go/bin`目录添加至 PATH 环境变量中，这样用户就可在任意路径下访问此目录中的可执行文件了。

![安装成功](https://cdn.learnku.com/uploads/images/201905/20/7636/bbwBqSLXy0.png!large)

## 测试
安装完成后，可通过配置工作区目录并构建一个简单的程序来检查 Go 是否是正确安装的。

在你的用户目录（$HOME）下创建一个 `go` 子目录，这是默认的 Go 工作区目录，如果你想将工作区目录设置为其它目录，那你需要自己额外设置 GOPATH 环境变量指向那个目录。

在工作区目录中再创建一个 `src/hello` 子目录，并在这个子目录下新建一个` hello.go` 文件，内容如下：

```go
package main
import "fmt"
func main() {
    fmt.Printf("hello, world\n")
}
```


然后，在终端中，进入` src/hello` 目录并输入 `go build `命令编译该程序：

```shell
$ cd $HOME/go/src/hello
$ go build
```

此命令将在 `src/hello` 目录中构建生成一个名为 hello 的二进制可执行文件。执行它，如果输出了 hello, world 问候语：

```shell
$ ./hello
hello, world
```


那么，恭喜你，你的 Go 环境是正确安装的。

可以运行 `go install` 安装这个二进制可执行文件至你的工作区目录中的 bin 子目录，也可以运行 `go clean -i `删除它。

### 安装多个版本的 Go
有时，为确保我们的程序包在多个 Go 版本中都能测试通过，我们需要在同一台电脑上安装多个 Go 版本。那么只要你安装好了一个版本的 Go，就可以通过 `go get` 命令来安装其它版本的 Go，假设我们要安装 1.10.7 版本的 Go，可输入以下命令：
```shell
$ go get golang.org/dl/go1.10.7
```
在命令执行完成后，我们可以在工作区目录的 bin 子目录中看到多出了个名为 go1.10.7 的可执行文件，然后用这个可执行文件下载该版本对应的 SDK 包：
```shell
$ cd $HOME/go/bin
$ ./go1.10.7 download
Downloaded   0.0% (   15175 / 90335150 bytes) ...
Downloaded   0.1% (   97083 / 90335150 bytes) ...
Downloaded   0.5% (  490299 / 90335150 bytes) ...
Downloaded   1.5% ( 1325883 / 90335150 bytes) ...
Downloaded   3.1% ( 2833211 / 90335150 bytes) ...
Downloaded   5.4% ( 4881211 / 90335150 bytes) ...
Downloaded   7.4% ( 6667067 / 90335150 bytes) ...
Downloaded   9.6% ( 8665915 / 90335150 bytes) ...
Downloaded  11.7% (10566459 / 90335150 bytes) ...
Downloaded  15.1% (13646651 / 90335150 bytes) ...
Downloaded  17.3% (15645499 / 90335150 bytes) ...
Downloaded  20.9% (18856763 / 90335150 bytes) ...
.
.
.
Downloaded  97.0% (87636795 / 90335150 bytes) ...
Downloaded  99.9% (90225467 / 90335150 bytes) ...
Downloaded 100.0% (90335150 / 90335150 bytes)
Unpacking /Users/xxxxx/sdk/go1.10.7/go1.10.7.darwin-amd64.tar.gz ...
Success. You may now run 'go1.10.7'
```
SDK 下载完成后，可以象下面这样使用这个新下载的版本：

```shell
$ go1.10.7 version
go version go1.10.7 linux/amd64
```

能够用此方法安装的所有 Go 版本列出在 下载页 中。如果你想知道这些 Go 版本的安装位置，可以通过环境变量 GOROOT 来获得其安装位置。例如：

```shell
$ go1.10.7 env GOROOT
/Users/xxxxx/sdk/go1.10.7
```


要卸载额外安装的版本，只需删除其 GOROOT 环境变量对应的目录和存在于 Go 工作区 bin 子目录中的 goX.Y.Z 二进制文件。

卸载 Go
要卸载系统中已有的 Go，删除 `/usr/local/go` 目录即可。

另外还应从 PATH 环境变量中将` /usr/local/go/bin `移除。 因为我们是通过软件包安装的 Go，那么直接删除 `/etc/paths.d/go `文件就好了。
