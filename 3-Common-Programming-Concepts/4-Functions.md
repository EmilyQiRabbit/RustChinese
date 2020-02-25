# 函数

在 Rust 代码中，函数随处可见。我们已经遇到过 Rust 中最重要的 main 函数了，它是程序的入口。同时我们也知道了关键字 `fn`，使用它可以声明新的函数。

Rust 代码中，我们习惯使用蛇式命名法来为函数和变量命名。即所有字母都使用小写并用下划线连接。如下程序包含了一个函数定义的示例：

文件名：src/main.rs

```rs
fn main() {
    println!("Hello, world!");

    another_function();
}

fn another_function() {
    println!("Another function.");
}
```

函数声明以 `fn` 开头，并需要在函数名后跟一组括号。而花括号将会为编译器提供函数体开始和结束的位置信息。

使用函数名加上一组括号即可调用任意函数。由于 `another_function` 函数已经在程序中被定义过，所以它可以在 `main` 函数中被调用。注意这里我们是在 `main` 函数后定义 `another_function` 函数；其实将其定义在 `main` 函数之前也是完全可以的。Rust 不关心我们在哪里定义函数，只要定义过即可。

为了进一步探索函数，现在我们新建名为 functions 的二进制项目。将上文 `another_function` 示例代码拷贝进 src/main.rs 并运行。我们将能看到如下输出：

```sh
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.28 secs
     Running `target/debug/functions`
Hello, world!
Another function.
```

程序将会按 `main` 函数中的顺序逐行执行。首先 “Hello, world!” 信息被打印出来，然后 `another_function` 函数被调用，并打印出其相应的信息。

## 函数参数

函数也可以被定义为有参数（parameter）的形式，参数是特殊的变量，是函数签名的一部分。当函数有参数时，我们可以为其参数提供确切的值。技术上来说，这些具体的值被称为 argument（其对应的中文也是参数），但是在日常交流中，人们通常不会区分 parameter 和 argument 的概念，它们都可以用来指函数定义时的变量或者调用函数时的具体值。

下面这版重写了 `another_function` 函数的代码为我们展示出了 Rust 中函数的参数：

文件名：src/main.rs

```rs
fn main() {
    another_function(5);
}

fn another_function(x: i32) {
    println!("The value of x is: {}", x);
}
```

尝试运行程序；我们将会得到如下输出：

```sh
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 1.21 secs
     Running `target/debug/functions`
The value of x is: 5
```

`another_function` 函数的声明中包含了一个名为 `x` 的参数。`x` 的类型被指定为 `i32`。当 `5` 被传入 `another_function` 函数时，宏 `println!` 将 `5` 放到格式化字符串中花括号的位置中。

在函数签名中，必须声明所有参数的类型。这在 Rust 设计中是一个反复斟酌后作出的决定：函数定义需要类型注解意味着，编译器不需要开发者在其他位置使用类型注解来指明函数的意图。

如果希望函数有多个参数时，可以使用逗号分隔不同参数，如下所示：

文件名：src/main.rs

```rs
fn main() {
    another_function(5, 6);
}

fn another_function(x: i32, y: i32) {
    println!("The value of x is: {}", x);
    println!("The value of y is: {}", y);
}
```

这段代码示例创建了一个有两个参数的函数，两个参数都是 `i32` 类型。接下来函数打印出了两个参数的值。注意，函数的参数不需要总是同一类型，只是在这个例子中碰巧相同罢了。

我们来运行这段代码。使用上面的示例代码替换掉 functions 项目下 src/main.rs 文件中的程序代码，然后使用 `cargo run` 运行：

```sh
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/functions`
The value of x is: 5
The value of y is: 6
```

因为我们使用了 `5` 作为参数 `x` 的值，`6` 作为参数 `y` 的值来调用函数，因此两段字符串就连同这两个值被打印了出来。

## 包含了语句和表达式的函数体

函数体由一系列语句和一个可选的结尾表达式组成。虽然目前为止我们只介绍了没有表达式结尾的函数，但我们已经见过作为语句一部分的表达式了。Rust 是一门基于表达式的语言，这是我们需要理解的一个 Rust 不同于其他语言的重要区别，其他语言都无此特点，下面我们就来学习什么是语句和表达式，以及它们分别是如何影响函数体的。

我们已经使用过语句和表达式了。语句是指令，它执行某些操作但并没有返回值。而表达式会计算并返回某个值。我们来看几个例子：

使用 `let` 关键字创建变量并赋值，这就是一个语句。在下面示例中，`let y = 6;` 就是一个语句。

文件名：src/main.rs

```rs
fn main() {
    let y = 6;
}
```
示例 3-1：`main` 函数的声明中包含了一个语句

函数声明也属于语句；前面这个例子本身就是一个语句。

语句没有返回值。因此我们不可以像如下代码那样将 `let` 语句赋值给另一个变量，如果这样做了，代码就会报错：

Filename: src/main.rs
文件名：src/main.rs

```rs
fn main() {
    let x = (let y = 6);
}
```
运行该程序的报错信息如下：

```sh
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error: expected expression, found statement (`let`)
 --> src/main.rs:2:14
  |
2 |     let x = (let y = 6);
  |              ^^^
  |
  = note: variable declaration using `let` is a statement
```

语句 `let y = 6` 没有返回值，因此 `x` 不会和任何值绑定。这一点和其他语言例如 C 或 Ruby 不同，这些语言的赋值操作会返回所赋的值。所以我们就可以这么写：`x = y = 6`，此时 `x` 和 `y` 的值都是 `6`；但在 Rust 中不能这样写。

Expressions evaluate to something and make up most of the rest of the code that you’ll write in Rust. Consider a simple math operation, such as `5 + 6`, which is an expression that evaluates to the value `11`. Expressions can be part of statements: in Listing 3-1, the `6` in the statement `let y = 6;` is an expression that evaluates to the value `6`. Calling a function is an expression. Calling a macro is an expression. The block that we use to create new scopes, `{}`, is an expression, for example:

文件名：src/main.rs

```rs
fn main() {
    let x = 5;

    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {}", y);
}
```

This expression:

```rs
{
    let x = 3;
    x + 1
}
```

is a block that, in this case, evaluates to `4`. That value gets bound to `y` as part of the `let` statement. Note the `x + 1` line without a semicolon at the end, which is unlike most of the lines you’ve seen so far. Expressions do not include ending semicolons. If you add a semicolon to the end of an expression, you turn it into a statement, which will then not return a value. Keep this in mind as you explore function return values and expressions next.

## 带返回值的函数

Functions can return values to the code that calls them. We don’t name return values, but we do declare their type after an arrow (`->`). In Rust, the return value of the function is synonymous with the value of the final expression in the block of the body of a function. You can return early from a function by using the `return` keyword and specifying a value, but most functions return the last expression implicitly. Here’s an example of a function that returns a value:

Filename: src/main.rs

```rs
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {}", x);
}
```

There are no function calls, macros, or even `let` statements in the `five` function—just the number `5` by itself. That’s a perfectly valid function in Rust. Note that the function’s return type is specified too, as `-> i32`. Try running this code; the output should look like this:

```sh
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/functions`
The value of x is: 5
```

The `5` in `five` is the function’s return value, which is why the return type is `i32`. Let’s examine this in more detail. There are two important bits: first, the line `let x = five();` shows that we’re using the return value of a function to initialize a variable. Because the function `five` returns a `5`, that line is the same as the following:

```rs
#![allow(unused_variables)]
fn main() {
  let x = 5;
}
```

Second, the `five` function has no parameters and defines the type of the return value, but the body of the function is a lonely `5` with no semicolon because it’s an expression whose value we want to return.

Let’s look at another example:

Filename: src/main.rs

```rs
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

Running this code will print `The value of x is: 6`. But if we place a semicolon at the end of the line containing `x + 1`, changing it from an expression to a statement, we’ll get an error.

Filename: src/main.rs

This code does not compile!
```rs
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1;
}
```

Compiling this code produces an error, as follows:

```sh
error[E0308]: mismatched types
 --> src/main.rs:7:28
  |
7 |   fn plus_one(x: i32) -> i32 {
  |  ____________________________^
8 | |     x + 1;
  | |          - help: consider removing this semicolon
9 | | }
  | |_^ expected i32, found ()
  |
  = note: expected type `i32`
             found type `()`
```

The main error message, “mismatched types,” reveals the core issue with this code. The definition of the function `plus_one` says that it will return an `i32`, but statements don’t evaluate to a value, which is expressed by `()`, an empty tuple. Therefore, nothing is returned, which contradicts the function definition and results in an error. In this output, Rust provides a message to possibly help rectify this issue: it suggests removing the semicolon, which would fix the error.
