# Hello, World!

完成安装后，我们就可以开始编写第一个 Rust 程序了。每当开始学习一门新的编程语言时，开发者们的传统是写一个可以在屏幕上打印 `Hello, world!` 的小程序，我们就这么办！

> 注意：本书假设读者熟悉基本的命令行知识。Rust 对开发者所使用的编辑方式、工具或者代码存储没有任何特殊要求，如果你更喜欢使用集成开发环境（即 integrated development environment，IDE），而不是使用命令行，这也是完全可以的，尽情使用你喜欢的 IDE 就好了。现在很多 IDE 都在某种程度支持 Rust；查看 IDE 文档即可获得详细信息。最近，Rust 团队也在致力于更多更棒的 IDE 支持，并取得了飞速的进展。

## 创建项目目录

我们首先要创建用来保存 Rust 代码的目录。其实无论把 Rust 代码存储在哪里都可以，但我们建议你在系统根目录下创建一个 projects 目录并将所有项目存储于此，这样更方便你理解和操作本书中的练习和项目代码。

打开终端并输入如下命令，这样就可以创建一个名为 projects 的目录，并在该目录下为我们的 “Hello, world!” 项目创建了目录。

对于 Linux、macOS，或者 Windows PowerShell 用户，请输入：

```sh
$ mkdir ~/projects
$ cd ~/projects
$ mkdir hello_world
$ cd hello_world
```

对于 Windows CMD 用户，请输入：

```sh
> mkdir "%USERPROFILE%\projects"
> cd /d "%USERPROFILE%\projects"
> mkdir hello_world
> cd hello_world
```

## 编写并运行 Rust 程序

接下来，我们创建一个名为 main.rs 的资源文件（Rust 文件都会以 .rs 扩展名结尾）。如果文件名中包含多个单词，请使用下划线分隔。例如，应使用 hello_world.rs，而不是 helloworld.rs。

打开我们刚刚创建的 main.rs 文件，然后输入下面的代码。

main.rs 文件：

```rs
fn main() {
    println!("Hello, world!");
}
```

这段程序代码能打印出 `Hello, world!`

保存文件然后再次打开终端窗口。如果你使用 Linux 或者 macOS 系统，输入如下命令，来编译并运行该文件：

```sh
$ rustc main.rs
$ ./main
Hello, world!
```

如果你使用 Windows 系统，使用命令 `.\main.exe` 代替 `./main`：

```sh
> rustc main.rs
> .\main.exe
Hello, world!
```

无论你使用哪种操作系统，字符串 `Hello, world!` 都可以在终端中被打印出来。如果你没看到如上的输出，请参考前一章节“安装”中的“排除故障”部分寻求帮助。

而如果如期打印出了 `Hello, world!`，那么恭喜！你已经正式完成了一个 Rust 程序，成为一名 Rust 开发者 —— 欢迎入坑！

## Rust 程序解析

我们来回顾分析刚才打印出“Hello, world!”的程序，一起解谜如下第一段代码片：

```rs
fn main() {

}
```

这几行代码定义了一个 Rust 函数。`main` 函数很特别：所有可执行 Rust 程序都以 `main` 开始。第一行定义了一个名为 `main` 的函数，它没有参数也没有返回值。而如有需要，参数应写在括号 `()` 里面。

同时我们注意到函数体被花括号 `{}` 包裹。在 Rust 中，所有的函数都应遵循该规则。通常的习惯是，将花括号的前半部分和函数声明放在同一行中，并在中间加上一个空格。

在本书撰写时，一个名为 `rustfmt` 的自动格式化工具正处于研发中。如果你希望 Rust 项目中所有代码都遵循某一标准格式，`rustfmt` 能够帮你完成。Rust 团队计划最终将 `rustfmt` 工具加入到例如 `rustc` 这样的标准 Rust 发行版中。有赖于你阅读本书时机的不同，你可能已经完成了安装！如果你想阅读更多关于 `rustfmt` 的内容，可以查看官方文档。

如下是包含在 `main` 函数内的代码：

```rs
    println!("Hello, world!");
```

一行代码完成了这个小小程序所有的功能：在屏幕上打印出字符。这里有四点很重要的细节。第一，Rust 代码要求使用四个空格缩进，而不是一个 tab。

第二，`println!` 调用了 Rust 宏（macro）。而如果我们调用得是一个函数，那写法应该是不加 `!` 后缀的 `println`。我们将会在第十九章详细讨论 Rust 宏。这里我们只需要记住，使用 `!` 符号意味着调用得是宏而非普通函数。

第三，`"Hello, world!"` 字符串。我们将这个字符串作为参数传入 `println!`，然后这个字符串就会在屏幕上被打印出来。

第四，我们使用分号 `;` 结束一行代码，这意味着该表达式已经结束，下一表达式可以开始。大部分的 Rust 代码都使用分号结尾。

## 编译和运行是不同的步骤

刚刚我们已经运行了一个新的 Rust 程序，现在我们来分析一下这个过程的步骤。

在运行 Rust 程序之前，我们必须使用 Rust 编译器对代码进行编译，方法是输入 `rustc` 命令和源文件名，比如：

```sh
$ rustc main.rs
```

如果你知道基本的 C 或 C++ 知识，就会注意到它和 `gcc` 以及 `clang` 很类似。编译成功后，Rust 会输出一个二进制可执行文件。

在 Linux 系统、macOS 系统以及 Windows 的 PowerShell 中，你可以在 shell 中使用 `ls` 命令查看这个可执行文件。在 Linux 系统和 macOS 系统中，你可以看到两个文件。而在 Windows 的 PowerShell 中，则可以看到三个同名文件，和使用 CMD 查看的结果一致。

```sh
$ ls
main  main.rs
```

如果使用 Windows 的 CMD，你需要输入：

```sh
> dir /B %= the /B option says to only show the file names =%
main.exe
main.pdb
main.rs
```

这里显示除了 .rs 扩展名的源文件，可执行文件（Windows 系统中显示 main.exe，其他平台只显示 main），以及如果你使用 Windows，还有带 .pdb 扩展名的调试信息文件。现在，我们可以像这样来运行 main 或 main.exe 文件：

```sh
$ ./main # 如果是 Windows，使用 \main.exe
```

如果 main.rs 就是我们的 “Hello, world!” 程序，那么终端就会打印出 `Hello, world!` 了。

如果你对动态编程语言如 Ruby、Python 或者 JavaScript 比较熟悉，你可能不习惯将编译和运行分成不同的两个步骤。Rust 是一种预编译静态类型语言，这意味着你可以编译程序然后只将可执行文件发给他人，他们就可以无需安装 Rust 直接运行了。而如果你提供给他人的是带 .rb, .py, 或 .js 扩展名的文件，他们则相应的必须安装 Ruby 或者 Python 或者 JavaScript。而安装之后，就只需一个命令即可编译并运行程序。编程语言设计肯定有得必有失。

使用 `rustc` 编译简单的程序是可行的，但随着项目的扩张，开发者会希望能更周全的管理，并让代码易于共享。下一章节，我们会介绍 Cargo 工具，它将会帮助你完成真正的 Rust 程序。
