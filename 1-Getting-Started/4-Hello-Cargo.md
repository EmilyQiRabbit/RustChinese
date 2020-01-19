# Hello, Cargo!

Cargo 是 Rust 的构建系统和包管理工具。大部分 Rust 开发者都会使用 Cargo 管理项目，它能帮助完成如构建代码、下载并构建代码依赖库等很多任务。（我们将代码需要的仓库称为依赖（dependency））

到目前为止，我们写得都是最简单的 Rust 程序，它们不需要任何依赖。所以加入我们使用 Cargo 构建上一章节中的“Hello, world!”项目，那么就只需要 Cargo 中用来处理构建代码的部分。而当 Rust 程序变得复杂，我们就需要增加依赖，此时如果使用 Cargo 初始化项目，那么增加依赖就会变得容易得多。

由于绝大多数的 Rust 项目都会使用 Cargo，本书假定您也是一名 Cargo 使用者。如果您使用得是“安装”章节介绍得官方安装程序，那么 Cargo 会和 Rust 一起被安装到您的设备上。而如果您是通过其他路径安装的，可以在终端输入如下命令确认 Cargo 是否安装：

```sh
$ cargo --version
```

如果屏幕显示出了版本号，那么表明您已经安装了 Cargo！而如果是例如 `command not found` 这样的错误信息，那么请查阅您使用的安装文档来确定单独安装 Cargo 的方式。

## 使用 Cargo 创建项目

下面我们一起使用 Cargo 创建一个新的项目，看看它和之前的“Hello, world!”项目有什么区别。首先返回至 projects 目录（或者其他您决定用来保存代码的位置）。然后直接执行（任意操作系统均可）：

```sh
$ cargo new hello_cargo
$ cd hello_cargo
```

第一个命令会创建一个名为 hello_cargo 的目录。这也正是我们的项目名，同时 Cargo 会在这个目录下创建必要的文件。

打开 hello_cargo 目录然后查看文件列表。您会发现 Cargo 已经为我们生成了两个文件和一个目录：Cargo.toml 文件，以及 src 目录和该目录下的 main.rs 文件。同时，Cargo 也初始化好了 Git 仓库，并创建了 .gitignore 文件。

> 注：Git 是一个很常用的版本管理系统。您可以通过为 `cargo new` 命令增加 `--vcs` 标识来更改或忽略版本管理系统。运行 `cargo new --help` 可以查看所有可用选项。

使用任意文本编辑器打开 Cargo.toml。它的内容应该类似于：

文件名: Cargo.toml

```sh
[package]
name = "hello_cargo"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]
edition = "2018"

[dependencies]
```

👆由 `cargo new` 命令生成的 Cargo.toml 文件的内容

这个文件的格式是 TOML（即 Tom’s Obvious，Minimal Language），它是 Cargo 的配置文件。

文件第一行的 `[package]` 是一个段落标题，表明其后的内容是包配置信息。随着我们为文件增加更多的信息，还会有其他段落加入进来。

下面的四行配置了 Cargo 在编译程序时需要的信息：包名，版本号，作者，以及使用的 Rust 版本。Cargo 会从系统环境中获取您的姓名和邮箱信息，如果信息有误，您可以手动修改并保存。我们会在附录 E 中讨论 `edition` 的配置。

最后一行的 `[dependencies]` 是依赖段落的起始，您可以在这里罗列处项目依赖。在 Rust 中，代码包被称为箱（crate）。目前我们不需要其他的箱，但是后文会涉及，那时就会需要使用这个依赖段落了。

现在，我们打开 src/main.rs 文件看一看：

文件名：src/main.rs

```rs
fn main() {
    println!("Hello, world!");
}
```

Cargo 已经为我们生成了一个和前一章节一模一样的“Hello, world!”程序！到目前为止，本章节 Cargo 生成的项目和上一章节“Hello, world!”项目的区别是 Cargo 会把代码放在 src 目录下，同时根目录中包含文件 Cargo.toml。

Cargo 期望开发者将源文件都放置在 src 目录下。项目根目录只有 README 文件，许可证信息，配置文件，以及其他和代码无关的文件。可见使用 Cargo 可以帮助开发者更好的组织项目。代码皆有所属，各归各位。

而如果您没有使用 Cargo 初始化项目，就像我们上一章节中“Hello, world!”项目的那样，那您还可以将其转化为一个 Cargo 项目。只需要将项目代码放到 src 目录下，并创建一个合适的 Cargo.toml 文件即可。

## 构建并运行 Cargo 项目

现在我们一起看看使用 Cargo 构建和运行“Hello, world!”程序和之前有什么不同！在 hello_cargo 目录下，使用如下命令构建程序：

```sh
$ cargo build
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 2.85 secs
```

该命令在目录 target/debug 下（而不是当前目录下）创建了可执行文件 hello_cargo（如果是 Windows 系统，那么文件为 hello_cargo.exe）。我们可以通过这个命令执行它：

```sh
$ ./target/debug/hello_cargo # 如果您使用 Windows 系统，那么命令为 .\target\debug\hello_cargo.exe
Hello, world!
```

如果一切正常，终端就会显示出 `Hello, world!`。第一次运行 `cargo build` 时，Cargo 也会在顶层目录创建 Cargo.lock 文件。这个文件会追踪项目依赖的精确版本。现在我们的项目还没有使用依赖，所以文件里基本没有内容。您无需手动修改文件，Cargo 会帮您管理文件内容。

我们刚刚使用 `cargo build` 构建了一个程序，然后使用 `./target/debug/hello_cargo` 运行了它，其实我们还可以直接使用命令 `cargo run` 自动完成代码的编译和运行：

```sh
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

我们注意到，这次执行没有输出关于 Cargo 正在编译 `hello_cargo` 的信息。因为 Cargo 检测到文件并没有发生变化，所以就直接运行了二进制文件。而如果我们修改一下源文件，Cargo 就会在运行前重新构建，我们就会看到这样的输出信息：

```sh
$ cargo run
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.33 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

Cargo 还提供了命令 `cargo check`。它可以快速检查代码保证其可以编译，但却不会生成可执行文件：

```sh
$ cargo check
   Checking hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.32 secs
```

在什么场景下我们不想生成可执行文件呢？通常情况下，执行 `cargo check` 要比 `cargo build` 快得多，因为 `cargo check` 并不需要实际生成二进制文件。如果您只是想在编写过程中不断的检查代码，可以使用 `cargo check` 节省时间！因此，很多开发者都会在编程过程中定期运行 `cargo check` 来确保代码能正确编译。然后在代码真正就绪时使用 `cargo build` 生成可执行文件。

现在我们来回顾一下，目前为止我们学习了哪些关于 Cargo 的知识：

* 我们可以使用 `cargo build` 或者 `cargo check` 构建项目。
* 我们可以使用 `cargo run` 一步完成项目的构建和运行。
* 构建的结果会保存在 target/debug 目录下，而不是当前您编写代码的目录。

使用 Cargo 的另一个优势是，无论您使用得是什么操作系统，Cargo 命令都是相同的。所以本章节中我们并没有专门为不同操作系统提供特殊说明。

## 发版构建

当项目已经准备好发版了，您可使用 `cargo build --release` 进行优化编译。该命令会在 target/release 目录而不是 target/debug 目录下生成可执行文件。优化编译会保证 Rust 代码运行更快，同时编译的时间也会变长。所以通常会有两种不同的编译方式；一种用于开发模式，此时我们会频繁重构，并希望这个过程尽可能快；另一种用于构建交付给用户的最终程序，我们不需要频繁构建，但是希望它能响应更快。如果您需要测试代码运行时间，确保使用命令 `cargo build --release` 构建并使用 target/release 目录下的可执行文件。

## 把 Cargo 视作规范

对于简单的项目，Cargo 并没有展现出比 `rustc` 更多的优势，但随着程序变得复杂，Cargo 就能充分体现出自己的价值了。对于那些需要众多依赖箱的复杂项目，使用 Cargo 帮助构建就要容易得多。

尽管 `hello_cargo` 项目非常简单，但它也已经使用了很多可用于生产环境的工具，这些工具在您以后的 Rust 编程生涯中都会用到。事实上，如果您想编辑已存在的项目，可以使用如下命令用 Git 检出代码，进入项目目录然后开始构建：

```sh
$ git clone someurl.com/someproject
$ cd someproject
$ cargo build
```

如果您想要了解更多关于 Cargo 的信息，请查阅[官方文档](https://doc.rust-lang.org/cargo/)。

## 本章小结

我们已经开始了美好的 Rust 之旅！本章中我们一起学习了：

* 使用 `rustup` 安装最新稳定版本的 Rust
* 更新 Rust 版本
* 打开已安装在本地的文档
* 直接使用 `rustc` 完成 “Hello, world!” 程序
* 使用 Cargo 的规则创建并运行了一个新的项目

相信你已经准备好构建一个更加复杂的程序，进一步熟悉 Rust 代码的读写。在第二章，我们将会一起构建一个猜谜游戏的程序。而如果您更想从学习 Rust 常用编程概念开始，那么可以先阅读第三章然后再回到第二章的学习。
