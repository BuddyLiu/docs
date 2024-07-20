---
title:      go build 用法
date:       2024-07-20
tags:
- go
- build
---

usage: go build [-o output] [build flags] [packages]

Build compiles the packages named by the import paths, along with their dependencies, but it does not install the results.

Build 编译由导入路径命名的包及其依赖项，但不会安装结果。

If the arguments to build are a list of .go files from a single directory, build treats them as a list of source files specifying a single package.

如果要生成的参数是单个目录中的 .go 文件列表，则 build 会将它们视为指定单个包的源文件列表。

When compiling packages, build ignores files that end in ‘_test.go’.

编译包时，build 会忽略以“_test.go”结尾的文件。

When compiling a single main package, build writes the resulting executable to an output file named after the last non-major-version component of the package import path. The ‘.exe’ suffix is added when writing a Windows executable. So ‘go build example/sam’ writes ‘sam’ or ‘sam.exe’. ‘go build example.com/foo/v2’ writes ‘foo’ or ‘foo.exe’, not ‘v2.exe’.

编译单个主包时，build 会将生成的可执行文件写入以包导入路径的最后一个非主要版本组件命名的输出文件。“.exe”后缀是在编写 Windows 可执行文件时添加的。所以“go build example/sam”写成“sam”或“sam.exe”。“go build example.com/foo/v2”写的是“foo”或“foo.exe”，而不是“v2.exe”。

When compiling a package from a list of .go files, the executable is named after the first source file. ‘go build ed.go rx.go’ writes ‘ed’ or ‘ed.exe’.

从 .go 文件列表编译包时，可执行文件以第一个源文件命名。'go build ed.go rx.go' 写成 'ed' 或 'ed.exe'。

When compiling multiple packages or a single non-main package, build compiles the packages but discards the resulting object, serving only as a check that the packages can be built.

编译多个包或单个非主包时，build 会编译包，但会丢弃生成的对象，仅作为检查包是否可以构建。

The -o flag forces build to write the resulting executable or object to the named output file or directory, instead of the default behavior described in the last two paragraphs. If the named output is an existing directory or ends with a slash or backslash, then any resulting executables will be written to that directory.

### -o 

标志强制生成将生成的可执行文件或对象写入命名的输出文件或目录，而不是最后两段中描述的默认行为。如果命名的输出是现有目录或以斜杠或反斜杠结尾，则任何生成的可执行文件都将写入该目录。

The build flags are shared by the build, clean, get, install, list, run, and test commands:
生成标志由 build、clean、get、install、list、run 和 test 命令共享：


### -C dir

Change to dir before running the command. Any files named on the command line are interpreted after changing directories. If used, this flag must be the first one in the command line.

在运行命令之前更改为 dir。更改目录后，将解释命令行上命名的任何文件。如果使用，则此标志必须是命令行中的第一个标志。


### -a

force rebuilding of packages that are already up-to-date.

强制重建已更新的软件包。


### -n

print the commands but do not run them.

打印命令，但不要运行它们。

### -p n

the number of programs, such as build commands or test binaries, that can be run in parallel. The default is GOMAXPROCS, normally the number of CPUs available.

可以并行运行的程序（如生成命令或测试二进制文件）的数量。默认值为 GOMAXPROCS，通常为可用 CPU 的数量。


### -race

enable data race detection. Supported only on linux/amd64, freebsd/amd64, darwin/amd64, darwin/arm64, windows/amd64, linux/ppc64le and linux/arm64 (only for 48-bit VMA).

启用数据争用检测。仅在 linux/amd64、freebsd/amd64、darwin/amd64、darwin/arm64、windows/amd64、linux/ppc64le 和 linux/arm64 上受支持（仅适用于 48 位 VMA）。


### -msan

enable interoperation with memory sanitizer. Supported only on linux/amd64, linux/arm64, linux/loong64, freebsd/amd64 and only with Clang/LLVM as the host C compiler. PIE build mode will be used on all platforms except linux/amd64.

启用与内存清理程序的互操作。仅在 linux/amd64、linux/arm64、linux/loong64、freebsd/amd64 上受支持，并且仅支持 Clang/LLVM 作为主机 C 编译器。PIE 构建模式将在除 linux/amd64 以外的所有平台上使用。


### -asan

enable interoperation with address sanitizer. Supported only on linux/arm64, linux/amd64, linux/loong64. Supported on linux/amd64 or linux/arm64 and only with GCC 7 and higher or Clang/LLVM 9 and higher. And supported on linux/loong64 only with Clang/LLVM 16 and higher.

启用与地址清理程序的互操作。仅在 linux/arm64、linux/amd64、linux/loong64 上受支持。在 linux/amd64 或 linux/arm64 上受支持，并且仅支持 GCC 7 及更高版本或 Clang/LLVM 9 及更高版本。并且仅在 linux/loong64 上支持 Clang/LLVM 16 及更高版本。


### -cover

enable code coverage instrumentation.

启用代码覆盖率检测。


### -covermode set,count,atomic

set the mode for coverage analysis. The default is “set” unless -race is enabled, in which case it is “atomic”. The values: set: bool: does this statement run? count: int: how many times does this statement run? atomic: int: count, but correct in multithreaded tests; significantly more expensive. Sets -cover.

设置覆盖率分析模式。除非启用了 -race，否则默认值为 “set”，在这种情况下，它是 “atomic”。值：set：bool：此语句是否运行？count： int：此语句运行多少次？atomic： int： count，但在多线程测试中是正确的;贵得多。设置 -cover。


### -coverpkg pattern1,pattern2,pattern3

For a build that targets package ‘main’ (e.g. building a Go executable), apply coverage analysis to each package matching the patterns. The default is to apply coverage analysis to packages in the main Go module. See ‘go help packages’ for a description of package patterns. Sets -cover.

对于以包“main”为目标的构建（例如，构建 Go 可执行文件），将覆盖率分析应用于与模式匹配的每个包。默认设置是将覆盖率分析应用于主 Go 模块中的包。有关包模式的描述，请参阅“go help packages”。设置 -cover。


### -v

print the names of packages as they are compiled.

在编译包时打印包的名称。

### -work

print the name of the temporary work directory and do not delete it when exiting.

打印临时工作目录的名称，退出时不要将其删除。

### -x

print the commands. 

打印命令。

### -asmflags '[pattern=]arg list'

arguments to pass on each go tool asm invocation.

参数来传递每个 GO 工具 ASM 调用。

### -buildmode mode

build mode to use. See ‘go help buildmode’ for more.

要使用的构建模式。有关详细信息，请参阅“转到帮助构建模式”。

### -buildvcs

Whether to stamp binaries with version control information (“true”, “false”, or “auto”). By default (“auto”), version control information is stamped into a binary if the main package, the main module containing it, and the current directory are all in the same repository. Use -buildvcs=false to always omit version control information, or

是否使用版本控制信息（“true”、“false”或“auto”）标记二进制文件。默认情况下（“auto”），如果主包、包含它的主模块和当前目录都在同一个存储库中，则版本控制信息将标记到二进制文件中。使用 -buildvcs=false 始终省略版本控制信息，或者

### -buildvcs=true 

to error out if version control information is available but cannot be included due to a missing tool or ambiguous directory structure.

如果版本控制信息可用，则出错，但由于缺少工具或目录结构不明确，无法包含。

### -compiler name
name of compiler to use, as in runtime.Compiler (gccgo or gc).

要使用的编译器的名称，如在运行时中。编译器（gccgo 或 gc）。

### -gccgoflags ‘[pattern=]arg list’
arguments to pass on each gccgo compiler/linker invocation.

参数来传递每个 GCCGO 编译器/链接器调用。

### -gcflags ‘[pattern=]arg list’

arguments to pass on each go tool compile invocation.

参数来传递每个 Go 工具编译调用。

### -installsuffix suffix

a suffix to use in the name of the package installation directory, in order to keep output separate from default builds. If using the -race flag, the install suffix is automatically set to race or, if set explicitly, has _race appended to it. Likewise for the -msan and -asan flags. Using a -buildmode option that requires non-default compile flags has a similar effect.

要在软件包安装目录的名称中使用的后缀，以便将输出与默认版本分开。如果使用 -race 标志，则安装后缀会自动设置为 race，或者，如果显式设置，则_race附加到它。-msan 和 -asan 标志也是如此。使用需要非默认编译标志的 -buildmode 选项具有类似的效果。

### -ldflags ‘[pattern=]arg list’

arguments to pass on each go tool link invocation.

参数来传递每个 Go 工具链接调用。

### -linkshared

build code that will be linked against shared libraries previously created with -buildmode=shared.

生成代码，这些代码将链接到以前使用 -buildmode=shared 创建的共享库。

### -mod mode

module download mode to use: readonly, vendor, or mod. By default, if a vendor directory is present and the go version in go.mod is 1.14 or higher, the go command acts as if -mod=vendor were set. Otherwise, the go command acts as if -mod=readonly were set. See https://golang.org/ref/mod#build-commands for details.

要使用的模块下载模式：ReadOnly、Vendor 或 Mod。默认情况下，如果存在供应商目录，并且 go.mod 中的 go 版本为 1.14 或更高版本，则 go 命令的作用就像设置了 -mod=vendor 一样。否则，go 命令的行为就像设置了 -mod=readonly 一样。有关详细信息，请参阅 https://golang.org/ref/mod#build-commands。


### -modcacherw

leave newly-created directories in the module cache read-write instead of making them read-only.

将新创建的目录保留在模块缓存中读写，而不是将它们设置为只读。

### -modfile file

in module aware mode, read (and possibly write) an alternate go.mod file instead of the one in the module root directory. A file named “go.mod” must still be present in order to determine the module root directory, but it is not accessed. When -modfile is specified, an alternate go.sum file is also used: its path is derived from the

在模块感知模式下，读取（并可能写入）备用 go.mod 文件，而不是模块根目录中的文件。必须仍存在名为“go.mod”的文件才能确定模块根目录，但该文件不会被访问。指定 -modfile 时，还会使用备用 go.sum 文件：其路径派生自

### -modfile flag 

by trimming the “.mod” extension and appending “.sum”.

通过修剪“.mod”扩展名并附加“.sum”。

### -overlay file

read a JSON config file that provides an overlay for build operations. The file is a JSON struct with a single field, named ‘Replace’, that maps each disk file path (a string) to its backing file path, so that a build will run as if the disk file path exists with the contents given by the backing file paths, or as if the disk file path does not exist if its backing file path is empty. Support for the -overlay flag has some limitations: importantly, cgo files included from outside the include path must be in the same directory as the Go package they are included from, and overlays will not appear when binaries and tests are run through go run and go test respectively.

读取为生成操作提供覆盖的 JSON 配置文件。该文件是一个 JSON 结构，其中包含一个名为“Replace”的字段，该字段将每个磁盘文件路径（字符串）映射到其后备文件路径，以便生成将运行，就好像磁盘文件路径存在且后备文件路径提供的内容一样，或者如果其后备文件路径为空，则磁盘文件路径不存在。对 -overlay 标志的支持有一些限制：重要的是，从 include 路径外部包含的 cgo 文件必须与包含它们的 Go 包位于同一目录中，并且当二进制文件和测试分别通过 go run 和 go test 运行时，覆盖层不会出现。

### -pgo file

specify the file path of a profile for profile-guided optimization (PGO). When the special name “auto” is specified, for each main package in the build, the go command selects a file named “default.pgo” in the package’s directory if that file exists, and applies it to the (transitive) dependencies of the main package (other packages are not affected). Special name “off” turns off PGO. The default is “auto”.

指定配置文件的文件路径，以便进行配置文件引导优化 （PGO）。当指定特殊名称“auto”时，对于构建中的每个主包，go 命令会在包的目录中选择一个名为“default.pgo”的文件（如果该文件存在），并将其应用于主包的（可传递）依赖项（其他包不受影响）。特殊名称“off”关闭 PGO。默认值为“auto”。

### -pkgdir dir

install and load all packages from dir instead of the usual locations. For example, when building with a non-standard configuration, use -pkgdir to keep generated packages in a separate location.

从 dir 安装和加载所有包，而不是通常的位置。例如，使用非标准配置进行构建时，请使用 -pkgdir 将生成的包保存在单独的位置。

### -tags tag,list

a comma-separated list of additional build tags to consider satisfied during the build. For more information about build tags, see ‘go help buildconstraint’. (Earlier versions of Go used a space-separated list, and that form is deprecated but still recognized.)

在生成过程中要考虑满足的其他生成标记的逗号分隔列表。有关构建标签的详细信息，请参阅“转到帮助构建约束”。（早期版本的 Go 使用空格分隔的列表，该表单已被弃用，但仍可识别。

### -trimpath

remove all file system paths from the resulting executable. Instead of absolute file system paths, the recorded file names will begin either a module path@version (when using modules), or a plain import path (when using the standard library, or GOPATH).

从生成的可执行文件中删除所有文件系统路径。记录的文件名将不是绝对文件系统路径，而是模块path@version（使用模块时）或普通导入路径（使用标准库或 GOPATH 时）开头。

### -toolexec ‘cmd args’

a program to use to invoke toolchain programs like vet and asm. For example, instead of running asm, the go command will run ‘cmd args /path/to/asm '. The TOOLEXEC_IMPORTPATH environment variable will be set, matching 'go list -f ' for the package being built.

用于调用 VET 和 ASM 等工具链程序的程序。例如，go 命令将运行 'cmd args /path/to/asm'，而不是运行 asm。将设置TOOLEXEC_IMPORTPATH环境变量，与正在构建的包的 'go list -f ' 匹配。

The -asmflags, -gccgoflags, -gcflags, and -ldflags flags accept a space-separated list of arguments to pass to an underlying tool during the build. To embed spaces in an element in the list, surround it with either single or double quotes. The argument list may be preceded by a package pattern and an equal sign, which restricts the use of that argument list to the building of packages matching that pattern (see ‘go help packages’ for a description of package patterns). Without a pattern, the argument list applies only to the packages named on the command line. The flags may be repeated with different patterns in order to specify different arguments for different sets of packages. If a package matches patterns given in multiple flags, the latest match on the command line wins. For example, ‘go build -gcflags=-S fmt’ prints the disassembly only for package fmt, while ‘go build -gcflags=all=-S fmt’ prints the disassembly for fmt and all its dependencies.

-asmflags、-gccgoflags、-gcflags 和 -ldflags 标志接受以空格
分隔的参数列表，以便在构建过程中传递到底层工具。若要在列表中的元素中嵌入空格，请用单引号或双引号将其括起来。参数列表前面可以有一个包模式和一个等号，这将该参数列表的使用限制为构建与该模式匹配的包（有关包模式的描述，请参阅“转到帮助包”）。如果没有模式，参数列表仅适用于命令行上命名的包。可以使用不同的模式重复这些标志，以便为不同的包集指定不同的参数。如果包与多个标志中给出的模式匹配，则命令行上的最新匹配项优先。例如，“go build -gcflags=-S fmt”仅打印包 fmt 的反汇编，而“go build -gcflags=all=-S fmt”打印 fmt 及其所有依赖项的反汇编。

For more about specifying packages, see ‘go help packages’. For more about where packages and binaries are installed, run ‘go help gopath’. For more about calling between Go and C/C++, run ‘go help c’.

有关指定包的详细信息，请参阅“转到帮助包”。有关软件包和二进制文件安装位置的更多信息，请运行“go help gopath”。有关在 Go 和 C/C++ 之间调用的更多信息，请运行“go help c”。

Note: Build adheres to certain conventions such as those described by ‘go help gopath’. Not all projects can follow these conventions, however. Installations that have their own conventions or that use a separate software build system may choose to use lower-level invocations such as ‘go tool compile’ and ‘go tool link’ to avoid some of the overheads and design decisions of the build tool.

注意：Build 遵循某些约定，例如“go help gopath”所描述的约定。然而，并非所有项目都可以遵循这些约定。具有自己的约定或使用单独软件构建系统的安装可以选择使用较低级别的调用，例如“go tool comcompable”和“go tool link”，以避免构建工具的一些开销和设计决策。