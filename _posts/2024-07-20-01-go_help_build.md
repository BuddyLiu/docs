---
title:      go build 用法
date:       2024-07-20
tags:
- go
- build
---

### 深入理解 Go 语言中的 `go build` 命令

在 Go 语言中，`go build` 命令是一个基础且强大的工具，用于将 Go 源代码编译成可执行文件或库文件。本文将详细介绍 `go build` 的基本用法、选项以及一些高级功能，帮助开发者更好地利用这一命令进行项目的构建和管理。

#### 1. 基本用法

最简单的 `go build` 命令用法是在命令行中输入：
```bash
go build
```
它会编译当前目录下的所有 Go 源代码文件，并生成一个可执行文件（如果是 `main` 包），或者生成库文件（如果是其他包）。

如果需要编译特定的包，可以指定包的路径：
```bash
go build github.com/example/myapp
```

#### 2. 生成可执行文件和库文件

- **生成可执行文件**：当 `go build` 编译的包包含 `main` 函数时，它会生成一个可执行文件。例如：
  ```bash
  go build -o myapp main.go
  ```
  这会将 `main.go` 编译成一个名为 `myapp` 的可执行文件。

- **生成库文件**：对于没有 `main` 函数的包，`go build` 会生成一个 `.a` 文件，这是 Go 的静态库文件。这种文件通常用于构建可执行文件时的依赖项。

#### 3. 高级功能和选项

- **指定目标平台和架构**：通过设置 `GOOS` 和 `GOARCH` 环境变量，可以实现交叉编译，生成不同平台和架构的可执行文件。例如，要在 Linux 下编译一个可执行文件：
  ```bash
  GOOS=linux GOARCH=amd64 go build -o myapp-linux-amd64
  ```

- **并行编译**：使用 `-p` 标记可以指定并行编译的包的数量，以加快构建过程：
  ```bash
  go build -p 4
  ```

- **强制重新构建**：使用 `-a` 标记可以强制重新构建所有的包，即使它们已经是最新的：
  ```bash
  go build -a
  ```

- **编译标志和版本信息**：使用 `-ldflags` 标记可以向可执行文件或库文件的符号表中插入额外的信息，比如版本信息等：
  ```bash
  go build -ldflags "-X main.Version=1.2.3"
  ```

#### 4. 使用 build tags 进行条件编译

在 Go 中，可以使用 build tags 控制代码的编译。例如，只在 Linux 下编译特定的代码块：
```go
// +build linux

package main

import "fmt"

func main() {
    fmt.Println("Hello, Linux!")
}
```
然后使用 `go build` 命令时，指定对应的 build tags：
```bash
go build -tags linux
```

#### 5.下面是官方对于go build描述的翻译：

使用方法：go build [-o 输出文件] [编译标志] [包名]

Build 命令编译指定的包及其依赖项，但不安装结果。

如果 build 命令的参数是来自同一目录下的一组 .go 文件，
build 会将它们视为指定单个包的源文件列表。

编译包时，build 会忽略文件名以 '_test.go' 结尾的文件。

当编译单个主包时，build 会将生成的可执行文件命名为包导入路径中最后一个非主要版本组件的名称。在生成 Windows 可执行文件时会添加 '.exe' 后缀。
因此，'go build example/sam' 会生成 'sam' 或 'sam.exe'。
'go build example.com/foo/v2' 会生成 'foo' 或 'foo.exe'，而不是 'v2.exe'。

当从一组 .go 文件编译包时，生成的可执行文件将以第一个源文件的名称命名。
'go build ed.go rx.go' 会生成 'ed' 或 'ed.exe'。

当编译多个包或单个非主包时，build 会编译这些包，但会丢弃生成的对象，仅用作检查这些包能否编译成功。

-o 标志强制 build 将生成的可执行文件或对象写入指定的输出文件或目录，而不使用上述两段中描述的默认行为。如果指定的输出是一个已存在的目录或以斜杠或反斜杠结尾，那么任何生成的可执行文件都将写入该目录中。

编译标志可以被 build、clean、get、install、list、run 和 test 命令共享：

-C dir
	在运行命令前切换到指定的目录。
	在切换目录后，命令行上指定的任何文件名将被解释为相对于新目录的路径。
	如果使用此标志，它必须是命令行上的第一个标志。
-a
	强制重新编译已经是最新的包。
-n
	打印命令但不执行它们。
-p n
	可以并行运行的程序数量，如构建命令或测试二进制文件。
	默认值是 GOMAXPROCS，通常为可用的 CPU 数量。
-race
	启用数据竞态检测。
	仅支持在 linux/amd64、freebsd/amd64、darwin/amd64、darwin/arm64、windows/amd64、linux/ppc64le 和 linux/arm64（仅限于48位VMA）上。
-msan
	启用与内存污点检测器的交互操作。
	仅支持在 linux/amd64、linux/arm64、linux/loong64、freebsd/amd64 上，
	并且必须使用 Clang/LLVM 作为主机 C 编译器。
	在除 linux/amd64 外的所有平台上将使用 PIE 构建模式。
-asan
	启用与地址污点检测器的交互操作。
	仅支持在 linux/arm64、linux/amd64、linux/loong64 上。
	仅支持在 linux/amd64 或 linux/arm64 上，并且仅限于 GCC 7 及更高版本或 Clang/LLVM 9 及更高版本。
	在 linux/loong64 上，仅支持 Clang/LLVM 16 及更高版本。
-cover
	启用代码覆盖率仪器化。
-covermode set,count,atomic
	设置覆盖分析模式。
	默认情况下，如果启用了 -race，则为 "atomic"，否则为 "set"。
	可选值：
	set: 布尔值：此语句是否执行？
	count: 整数：此语句执行了多少次？
	atomic: 整数：计数，但在多线程测试中正确；成本昂贵得多。
	启用 -cover。
-coverpkg pattern1,pattern2,pattern3
	对目标为 'main' 的构建（例如构建 Go 可执行文件），将覆盖分析应用于与模式匹配的每个包。
	默认情况下，将覆盖分析应用于主 Go 模块中的包。
	详见 'go help packages' 了解包模式的描述。
	启用 -cover。
-v
	在编译时打印包的名称。
-work
	打印临时工作目录的名称，并在退出时不删除它。
-x
	打印执行的命令。
-asmflags '[pattern=]arg list'
	传递给每个 go 工具 asm 调用的参数。
-buildmode mode
	要使用的构建模式。详见 'go help buildmode' 了解更多信息。
-buildvcs
	是否将二进制文件标记为版本控制信息（"true"、"false" 或 "auto"）。
	默认情况下（"auto"），如果主包、包含它的主模块和当前目录都在同一存储库中，则将版本控制信息标记到二进制文件中。
	使用 -buildvcs=false 可以始终省略版本控制信息，或者使用 -buildvcs=true 如果由于缺少工具或不明确的目录结构而无法包含版本控制信息。
-compiler name
	要使用的编译器名称，如 runtime.Compiler（gccgo 或 gc）。
-gccgoflags '[pattern=]arg list'
	传递给每个 gccgo 编译器/链接器调用的参数。
-gcflags '[pattern=]arg list'
	传递给每个 go 工具 compile 调用的参数。
-installsuffix suffix
	要在包安装目录名称中使用的后缀，以便将输出与默认构建分开。
	如果使用了 -race 标志，则会自动将安装后缀设置为 race，
	或者如果显式设置了，则会附加 _race。对于需要非默认编译标志的 -buildmode 选项也有类似的影响。
-ldflags '[pattern=]arg list'
	传递给每个 go 工具 link 调用的参数。
-linkshared
	构建将链接到先前使用 -buildmode=shared 创建的共享库的代码。
-mod mode
	要使用的模块下载模式：readonly、vendor 或 mod。
	默认情况下，如果存在 vendor 目录，并且 go.mod 中的 Go 版本是 1.14 或更高版本，则 go 命令将以 -mod=vendor 设置运行。
	否则，go 命令将以 -mod=readonly 设置运行。
	详见 https://golang.org/ref/mod#build-commands 了解详情。
-modcacherw
	将新创建的目录保留在模块缓存中以读写方式，而不是只读方式。
-modfile file
	在模块感知模式下，读取（可能写入）替代的 go.mod 文件而不是模块根目录中的文件。
	仍然需要一个名为 "go.mod" 的文件以确定模块根目录，但它不会被访问。
	当指定 -modfile 时，还会使用替代的 go.sum 文件：其路径通过修剪 -modfile 标志的 ".mod" 扩展名并附加 ".sum" 衍生。
-overlay file
	读取提供构建操作覆盖的 JSON 配置文件。
	文件是一个 JSON 结构，具有名为 'Replace' 的单个字段，
	该字段将每个磁盘文件路径（字符串）

映射到其支持文件路径，
	使得构建将以磁盘文件路径存在的内容运行，该内容由支持文件路径给定，
	或者以磁盘文件路径不存在，如果其支持文件路径为空。
	对 -overlay 标志的支持有一些限制：重要的是，从包含路径外部包含的 cgo 文件必须与它们包含的 Go 包位于同一目录中，
	并且当通过 go run 和 go test 分别运行二进制文件和测试时，覆盖将不会显示。
-pgo file
	指定用于基于配置的优化（PGO）的文件路径。
	当指定特殊名称 "auto" 时，对于构建中的每个主包，
	如果包的目录中存在名为 "default.pgo" 的文件，则 go 命令会选择该文件，并将其应用于（传递的）主包的依赖项。
	其他包不受影响。特殊名称 "off" 关闭 PGO。默认为 "auto"。
-pkgdir dir
	安装并从 dir 加载所有包，而不是通常的位置。
	例如，在使用非标准配置构建时，使用 -pkgdir 将生成的包保留在单独的位置。
-tags tag,list
	逗号分隔的附加构建标签列表，用于在构建过程中考虑满足的标签。
	有关构建标签的更多信息，请参见 'go help buildconstraint'。
	（Go 的早期版本使用了空格分隔的列表，该形式已不推荐但仍被识别。）
-trimpath
	从生成的可执行文件中删除所有文件系统路径。
	记录的文件名将以模块路径@版本开头（使用模块时），
	或者以纯导入路径开头（使用标准库或 GOPATH）。
-toolexec 'cmd args'
	用于调用像 vet 和 asm 这样的工具链程序的程序。
	例如，而不是运行 asm，go 命令将运行 'cmd args /path/to/asm <asm 的参数>'。
	TOOLEXEC_IMPORTPATH 环境变量将被设置，匹配 'go list -f {{.ImportPath}}' 用于正在构建的包。

-asmflags、-gccgoflags、-gcflags 和 -ldflags 标志接受一个空格分隔的参数列表，
用于在构建过程中传递给底层工具。要在列表中的元素中嵌入空格，请使用单引号或双引号括起来。
参数列表可以以包模式和等号开头，限制该参数列表仅用于匹配该模式的包的构建（详见 'go help packages' 中的包模式描述）。
如果没有模式，则参数列表仅适用于命令行上命名的包。可以使用不同的模式重复这些标志，以指定不同集合的包的不同参数。
如果一个包匹配多个标志中给定的模式，那么命令行上最后匹配的标志将覆盖之前的匹配。
例如，'go build -gcflags=-S fmt' 仅打印 fmt 包的反汇编，而 'go build -gcflags=all=-S fmt' 打印 fmt 及其所有依赖项的反汇编。

有关更多关于指定包的信息，请参见 'go help packages'。
有关包和二进制文件安装位置的信息，请运行 'go help gopath'。
有关 Go 与 C/C++ 之间调用的信息，请运行 'go help c'。

注意：Build 遵循一些约定，例如 'go help gopath' 中描述的约定。然而，并非所有项目都能遵循这些约定。
对于那些有自己约定或使用独立软件构建系统的安装，可以选择使用低级别的调用，如 'go tool compile' 和 'go tool link'，
以避免构建工具的一些开销和设计决策。

#### 6. 总结

`go build` 是 Go 语言开发中一个不可或缺的工具，通过灵活的选项和功能支持多种开发和部署场景。本文介绍了其基本用法、高级功能以及如何利用环境变量和标记来优化和控制构建过程。熟练掌握 `go build` 命令能够有效提升 Go 项目的开发效率和管理能力。

通过这篇博客，希望读者能够更全面地了解和使用 `go build` 命令，为自己的 Go 项目构建出高效、可靠的可执行文件和库文件。