---
title: "Go embed包及其使用详解"
date: 2023-10-17T20:34:54+08:00
keywords: ["go", "embed"]
description: "embed 是在 Go 1.16 中新加包。它通过//go:embed 指令，可以在编译阶段将静态资源文件打包进编译好的程序中，并提供访问这些文件的能力。"
categories: ["技术文档"]
tags: ["go", "embed"]
thumbnail: ""
banner: ""
---

## embed 是什么

embed 是在 Go 1.16 中新加包。它通过//go:embed 指令，可以在编译阶段将静态资源文件打包进编译好的程序中，并提供访问这些文件的能力。

## 为什么需要 embed 包

- 部署过程更简单。传统部署要么需要将静态资源与已编译程序打包在一起上传，或者使用 docker 和 dockerfile 自动化前者，这在精神上是很麻烦的。
- 确保程序的完整性。在运行过程中损坏或丢失静态资源通常会影响程序的正常运行。
- 您可以独立控制程序所需的静态资源。
  最常见的方法（例如静态网站的后端程序）要求将程序连同其所依赖的 html 模板，css，js 和图片以及静态资源的路径一起上传到生产服务器。必须正确配置 Web 服务器，以便用户访问它。

现在，我们将所有这些资源都嵌入到程序中。我们只需要部署一个二进制文件并为程序本身配置它们即可。部署过程已大大简化。

## embed 的常用场景

以下列举一些静态资源文件需要被嵌入到程序中的常用场景：

- Go 模板：模板文件必须可用于二进制文件（模板文件需要对二进制文件可用）。 对于 Web 服务器二进制文件或那些通过提供 init 命令的 CLI 应用程序，这是一个相当常见的用例。 在没有嵌入的情况下，模板通常内联在代码中。例如示例 qbec init 的 init 命令：https://qbec.io/userguide/tour/#initialize-a-new-qbec-app
- 静态 web 服务：有时，静态文件（如 index.html 或其他 HTML，JavaScript 和 CSS 文件之类的静态文件）需要使用 golang 服务器二进制文件进行传输，以便用户可以运行服务器并访问这些文件。例如示例 web server 中嵌入静态资源文件：https://github.com/gobuffalo/toodo/tree/master/assets
- 数据库迁移：另一个使用场景是通过嵌入文件被用于数据库迁移脚本。参考示例数据库迁移文件：https://github.com/bigpanther/trober/tree/786dc471ea0d9b4a9e934d7e3c192de214f7c173/migrations
  embed 的基本使用
  embed 包是 golang 1.16 中的新特性，所以，请确保你的 golang 环境已经升级到了 1.16 版本。 下面来一起看看 embed 的基本语法

## embed 的基本语法

基本语法非常简单，首先导入 embed 包，然后使用指令//go:embed 文件名 将对应的文件或目录结构导入到对应的变量上。 例如： 在当前目录下新建文件 version.txt，并输入内容 0.0.1

```go
package main

import (
    _ "embed"
    "fmt"
)

//go:embed version.txt
var version string

func main() {
    fmt.Printf("version: %q\n", version)
}
```

## embed 的三种数据类型及使用

在 embed 中，可以将静态资源文件嵌入到三种类型的变量，分别为：字符串、字节数组、embed.FS 文件类型

将文件内容嵌入到字符串变量中

```go
package main

import (
    _ "embed"
    "fmt"
)

//go:embed version.txt
var version string

func main() {
    fmt.Printf("version %q\n", version)
}
```

将文件内容嵌入到字节数组变量中

```go
package main
import (
    _ "embed"
    "fmt"
)

//go:embed version.txt
var versionByte []byte

func main() {
    fmt.Printf("version %q\n", string(versionByte))
}
```

将文件目录结构映射成 embed.FS 文件类型。embed.FS 结构主要有 3 个对外方法

```go
// Open 打开要读取的文件，并返回文件的fs.File结构.
func (f FS) Open(name string) (fs.File, error)

// ReadDir 读取并返回整个命名目录
func (f FS) ReadDir(name string) ([]fs.DirEntry, error)

// ReadFile 读取并返回name文件的内容.
func (f FS) ReadFile(name string) ([]byte, error)
```

以下搭建一个简单的静态文件 web 服务为例。

首先，在项目根目录下建立如下静态资源目录结构

```bash
|-static
|---js
|------util.js
|---img
|------logo.jpg
|---index.html
```

```go
package main

import (
    "embed"
    "io/fs"
    "log"
    "net/http"
    "os"
)

func main() {
    useOS := len(os.Args) > 1 && os.Args[1] == "live"
    http.Handle("/", http.FileServer(getFileSystem(useOS)))
    http.ListenAndServe(":8888", nil)
}

//go:embed static
var embededFiles embed.FS

func getFileSystem(useOS bool) http.FileSystem {
    if useOS {
        log.Print("using live mode")
        return http.FS(os.DirFS("static"))
    }

    log.Print("using embed mode")

    fsys, err := fs.Sub(embededFiles, "static")
    if err != nil {
        panic(err)
    }
    return http.FS(fsys)
}
```

以上代码，分别执行 go run . live 和 go run .，然后在浏览器中运行 http://localhost:8888，默认显示 static 目录下的 index.html 文件内容。

当然，运行 go run . live 和 go run . 的不同之处在于编译后的二进制程序文件在运行过程中是否依赖 static 目录中的静态文件资源。

以下为验证步骤：

首先，使用编译到二进制文件的方式。

若文件内容改变，输出依然是改变前的内容，说明 embed 嵌入的文件内容在编译后不再依赖于原有静态文件了。

1、运行 go run .

2、修改 index.html 文件内容为 Hello China

3、浏览器输入 http://localhost:8888 查看输出。输出内容为修改之前的 Hello World

其次，使用普通的文件方式。若文件内容改变，输出的内容也改变，说明编译后依然依赖于原有静态文件。

1、go run . live

2、修改 index.html 文件内容为 delete

3、浏览器输入 http://localhost:8888 查看输出。输出修改后的内容：Hello China

## embed 使用中注意事项

1.在使用`//go:embed`指令的文件都需要导入 embed 包。 例如，以下例子 没有导入 embed 包，即不会正常运行

```go
package main

import (
    "fmt"
)

//go:embed file.txt
var s string

func main() {
    fmt.Print(s)
}
```

2.`//go:embed`指令只能用在包一级的变量中，不能用在函数或方法级别，像以下程序将会报错：

```go
package main

import (
    _ "embed"
    "fmt"
)

func main() {
    //go:embed file.txt
    var s string
    fmt.Print(s)
}
```

第三，当包含目录时，它不会包含以“.”或““开头的文件。但是如果使用通配符，比如`dir/*`，它将包含所有匹配的文件，即使它们以“."或""开头。 请记住，在您希望在 Web 服务器中嵌入文件但不允许用户查看所有文件的列表的情况下，包含 Mac OS 的.DS_Store 文件可能是一个安全问题。 出于安全原因，Go 在嵌入时也不会包含符号链接或上一层目录。
