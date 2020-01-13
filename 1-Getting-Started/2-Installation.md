# Rust 安装

我们要做的第一步是安装 Rust。我们将会通过使用 `rustup` 完成 Rust 的安装，它是用于管理 Rust 版本和相关工具的命令行工具。这一步需要联网下载。

> 注意：如果您出于某些原因不想使用 `rustup`，那么请查阅 [Rust 官方安装文档](https://www.rust-lang.org/tools/install)寻求备选项。

下面的步骤将会安装最新稳定版本 Rust 编译器。Rust 的稳定性能确保本书所有编译示例将能够使用 Rust 最新版本继续完成编译。不同版本之间的编译输出可能稍有不同，这是由于 Rust 会经常优化其错误提示和警告信息。换言之，您使用如下步骤安装的最新稳定版本 Rust，都能如你我所期的正常运行本书中的内容。

> ### 命令行标记

> 在本书所有章节中，我们都将展示一些用于终端的命令。这些需要您输入至终端的命令行将会以 `$` 符号开始。您无需键入 `$`；它只是用作命令行起始符号。而那些没有以 `$` 开始的段落通常代表前一个命令的输出。另外，PowerShell 专用示例将会使用符号 `>` 而非 `$`。

## 在 Linux 或 macOS 系统安装 `rustup`

如果您使用的系统是 Linux 或 macOS，那么请打开终端，并输入如下命令：

```sh
$ curl https://sh.rustup.rs -sSf | sh
```

此命令将会下载一段脚本，并开始安装 `rustup` 工具，它将会帮我们安装最新稳定版本 Rust。过程中可能需要您输入密码。如果安装成功，将会显示出：

```sh
Rust is installed now. Great!
```

如果您乐于探索，可以下载这段脚本并在运行之前先研究一下。

安装脚本会自动将 Rust 添加至系统环境变量 PATH 中，并于下次登录时生效。如果您希望马上开始使用而无需重启终端，可在 shell 中运行如下命令，手动将 Rust 添加至系统环境变量 PATH：

```sh
$ source $HOME/.cargo/env
```

或者您可以将下面这行代码加入到 *~/.bash_profile* 文件中：

```sh
$ export PATH="$HOME/.cargo/bin:$PATH"
```

另外，您还需要某种类型的链接器（linker）。您可能已经安装过，但如果您在尝试编译 Rust 程序时发现了链接器无法执行的错误，那么就意味着您的系统中还没有安装，需要手动安装一下。C 编译器通常都带有正确的链接器。查看您的平台文档即可了解如何安装 C 编译器。同样的，依赖于 C 代码的一些 Rust 常用包也需要 C 编译器。因此，费心安装一下也是值得的。

## 在 Windows 系统安装 `rustup`

如果您使用 Windows，请在浏览器打开 https://www.rust-lang.org/tools/install 并按照说明安装 Rust。安装过程中，您将收到一条消息，为您说明您需要 Visual Studio 2013 或更新版的 C++ 构建工具。获取该构建工具最简单的方法是安装 [Visual Studio 2019 版构建工具](https://www.visualstudio.com/downloads/#build-tools-for-visual-studio-2019)。相关工具及说明位于「其他工具和框架」章节。

本书中使用的命令都能在 *cmd.exe* 和 PowerShell 两者中运行。如果存在特殊命令需要有所区别的，我们将会说明应该使用哪个运行。

## 更新与卸载

当您使用 `rustup` 安装 Rust 后，更新至最新版本是非常简单的。在 shell 中运行如下更新脚本：

```sh
$ rustup update
```

如果需要卸载 Rust 和 `rustup`，在 shell 中运行如下卸载脚本：

```sh
$ rustup self uninstall
```

## 排除故障

如果想要检查自己是否正确安装 Rust，可在 shell 中输入：

```sh
$ rustc --version
```

您将能够看到最新稳定版的版本号、提交（commit）哈希值，以及提交日期，显示信息格式如下：

```sh
rustc x.y.z (abcabcabc yyyy-mm-dd)
```

如果您看到了这条信息，那就说明 Rust 已经成功安装了！但如果您没有看到并且您使用得是 Windows 系统，请检查 Rust 是否在系统变量 `%PATH%` 中。如果系统变量无误但是 Rust 依旧没能正常工作，那接下来可以通过如下数个方式寻求帮助。最简单的方法是去 [Discord 网站 Rust 官方](https://discordapp.com/invite/rust-lang)的 #beginners 频道。在这里，你可以和其他 Rustacean（我们给自己的傻傻昵称）一起讨论解决方案。另外一些优质资源比如 [Rust 用户论坛](https://users.rust-lang.org) 以及 [Stack Overflow](https://stackoverflow.com/questions/tagged/rust)。

## 本地文档

Rust 安装包括了本文档的本地拷贝，您可以用于离线阅读。运行 `rustup doc` 即可在浏览器打开本地文档。

当您不确定标准库中类型/函数的功能或使用方法的时候，都可以参考应用程序接口（即 application programming interface，API）文档！
