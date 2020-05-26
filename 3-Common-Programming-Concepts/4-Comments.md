# 注释

所有的程序员都尽力让他们的代码易于理解，但有时额外的说明也是必要的。这时就需要在源码中留下注释，这些注释会被编译器忽略，但是当他人阅读代码时，它们可能就很有价值了。

如下是一个简单的注释：

```rs
#![allow(unused_variables)]
fn main() {
// hello, world
}
```

Rust 中的注释以两个斜杠开始，到该行结尾结束。而对于多行注释，那么每一行的开头都要有 `//`，如下所示：

```rs
// So we’re doing something complicated here, long enough that we need
// multiple lines of comments to do it! Whew! Hopefully, this comment will
// explain what’s going on.
```

注释也可以写在代码行的末尾：

文件名：src/main.rs

```rs
fn main() {
    let lucky_number = 7; // I’m feeling lucky today
}
```

但是如下这样，把注释单独写在所要注解的代码行上面则更为常见：

文件名：src/main.rs

```rs
fn main() {
    // I’m feeling lucky today
    let lucky_number = 7;
}
```
Rust 中还有另一种注释，即文档注释，我们将会在第 14 章的 “将 crate 发布到 Crates.io” 章节中详细介绍。
