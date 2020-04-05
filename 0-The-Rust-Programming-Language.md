# Rust 编程语言

*编辑者：Steve Klabnik 和 Carol Nichols，同时感谢 Rust 社区的贡献*

本文档假设读者使用 1.37.0 或更新版本的 Rust，并在所有项目的 *Cargo.toml* 中配置了 `edition="2018"` 以便使用 2018 版 Rust 语法。如果你想要安装或更新 Rust，可以参考[第一章节的安装教程](https://doc.rust-lang.org/book/ch01-01-installation.html)；如果你想了解版本信息，可以查看最新章节[附录 E](https://doc.rust-lang.org/book/appendix-05-editions.html)。

2018 版 Rust 引入了众多优化，这让 Rust 变得更友好并且易于掌握。本文档这次迭代作出了大量修改，可以反映出这些优化：

* 重写了第七章：“使用包（package）、包装箱（crate）和模块（module）管理正在扩张的项目”的绝大部分内容。2018 版的模块系统与路径的工作方式更加连贯一致。

* 第十章中的“作为参数的 Trait”以及“返回实现 Trait 的类型”是新加入的章节，它们阐述了最新的 `impl Trait` 语法

* 第十一章中的新章节：“使用`Result<T, E>`进行测试”阐述了如何使用操作符 `?` 编写测试代码。

* 删除了第十九章中的“高级生命周期”章节，因为由于编译器的进步，这一章节中所讲述的构造方式会更加少见。

* 之前的附录 D：“宏（Macros）”，移动到了第十九章中的“宏（Macros）”章节，并扩充了过程宏（procedural macro）的内容。

* 附录 A：“关键词（Keywords）”，加入了对新特性原生标识符（raw identifier）的解释，原生标识符能够让 2015 版的代码和 2018 版的代码可以交互。

* 附录 D 重命名为“实用开发工具”，包括各种最新发布的可以帮助开发者写 Rust 代码的工具。

* 我们更正了书中不少细节错误和不精确用词。在此感谢那些为我们反馈错误的读者！

注意，即使你升级了 Rust 编译器，已经编译了的任何更早版本的 Rust 代码还是可以继续编译，无需项目在 *Cargo.toml* 中配置 `edition="2018"`。Rust 的向后兼容性能保证这一点！

本书的在线版 HTML 文档在：https://doc.rust-lang.org/stable/book/，离线版会和 Rust 一起使用 `rustup` 命令安装；运行 `run rustup docs --book` 即可打开。

本书可以在[paperback and ebook format from No Starch Press](https://nostarch.com/rust)网站获取。
