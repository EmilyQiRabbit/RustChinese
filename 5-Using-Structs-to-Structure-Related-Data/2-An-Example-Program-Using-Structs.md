# 应用结构体编程示例

为了更好的理解应用结构体的场景，我们来一起写一个计算矩形面积的小程序吧。我们先从一个变量开始，然后再使用结构体重构程序。

我们使用 Cargo 新建一个名为 rectangles 的二进制项目，它能获取矩形的长宽，并计算其面积。代码示例 5-8 就是项目 src/main.rs 文件中的小程序，是一种计算面积的方式。

文件名：src/main.rs

```rs
fn main() {
    let width1 = 30;
    let height1 = 50;

    println!(
        "The area of the rectangle is {} square pixels.",
        area(width1, height1)
    );
}

fn area(width: u32, height: u32) -> u32 {
    width * height
}
```

代码示例 5-8：分别使用宽度和高度两个变量计算矩形面积

现在，使用 `cargo run` 运行项目：

```sh
$ cargo run
   Compiling structs v0.1.0 (file:///projects/structs)
    Finished dev [unoptimized + debuginfo] target(s) in 0.42s
     Running `target/debug/structs`
The area of the rectangle is 1500 square pixels.
```

尽管代码示例 5-8 能正常运行，并能通过调用 `area` 函数计算出矩形的面积，但我们其实能做得更好。矩形的宽高描述的是同一个矩形，因而它们是相互关联的。

这段代码的问题明显在于 `area` 函数的签名：

```rs
fn area(width: u32, height: u32) -> u32 {
```

`area` 函数是为了计算一个矩形的面积，但是却需要两个参数。这两个参数是相关的，但是程序中没有任何地方对此作出说明。将宽度和高度组合起来，可以让代码的可读性和可管理性都更好。我们在第三章“元组类型”部分已经讨论过一种可行的方式了，那就是使用元组。

## 使用元组重构程序

代码示例 5-9 是程序的另一个版本，它使用了元组。

文件名：src/main.rs

```rs
fn main() {
    let rect1 = (30, 50);

    println!(
        "The area of the rectangle is {} square pixels.",
        area(rect1)
    );
}

fn area(dimensions: (u32, u32)) -> u32 {
    dimensions.0 * dimensions.1
}
```

代码示例 5-9：使用元组指定矩形的宽高

一方面来说，程序结构更合理了。元组的结构让我们可以只传入一个参数。但另一方面，这个版本的代码不够明确：元组没有为元素命名，我们就只能使用索引获取元组内元素，这样这个计算就变得让人有些费解。

如果我们不慎将宽高弄混，这对面积的计算不会造成影响，但如果我们想要在屏幕上画出这个矩形，那么宽高的顺序就很重要了！我们必须牢记 `width` 是元组的 `0` 号元素，而 `height` 是 `1` 号。如果其他人也要使用这段代码，他们也必须弄清楚并牢记这一点。而因为这段代码里无法传递给开发者数据含义的信息，人们会很容易忘记或者弄混顺序并导致错误。

## 使用结构体重构程序：增加了更多含义

我们使用结构体为数据增加标签，以此为代码增加含义。我们可以将元组转化为一种整体有名字，同时每个部分也有名字的数据类型，如代码示例 5-10 所示。

文件名：src/main.rs

```rs
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

代码示例 5-10：定义 `Rectangle` 结构体

我们在这里定义了一个名为 `Rectangle` 的结构体。在大括号内，我们定义了字段 `width` 和 `height`，它们的类型都是 `u32`。然后在 `main` 函数中，我们创建了一个具体的 `Rectangle` 实例，它的宽是 30，高是 50。

现在，`area` 函数定义中只需要一个参数了，我们将其命名为 `rectangle`，它的类型是结构体 `Rectangle` 实例的不可变引用。我们在第四章中讨论过，我们希望借用结构体而无需取得其所有权。因此我们在函数签名和调用的时候都使用了 `&`，这样，`main` 函数将能继续保有结构体所有权并继续使用 `rect1`。

`area` 函数现在可以获取得是 `Rectangle` 实例的 `width` 和 `height` 字段。函数签名现在能准确表述我们想传达的含义：使用宽高字段计算矩形面积。同时也传达出了 `width` 和 `height` 是互相关联的信息，并且为这两个值提供了描述性的名字，而不是使用元组那样的 `0` 和 `1` 索引。这种方式清晰了很多。

## 使用派生 trait 增加实用功能

如果能在调试程序的时候打印出 `Rectangle` 实例用来观察其字段的值，那就再好不过了。代码示例 5-11 尝试使用我们在上一章使用过的 `println!` 宏来完成。但这段代码无法运行。

文件名：src/main.rs

这段代码无法编译！

```rs
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {}", rect1);
}
```

代码示例 5-11：尝试打印出 `Rectangle` 实例

代码编译时报错的关键信息是：

```sh
error[E0277]: `Rectangle` doesn't implement `std::fmt::Display`
```

`println!` 宏无法完成很多类型的格式化，默认情况下，`println!` 认为大括号需要使用 `Display` 格式：意在给直接终端用户提供可查看的输出。目前我们见过的类型都默认实现了 `Display`，因为对于 `1` 或者其他基本类型来说，只有一种向用户展示的方法。但是对于结构体，`println!` 格式化要输出数据的方式就很不明确，有多种可能的展示：是否需要展示逗号？是否需要打印大括号？是否所有的字段都要被展示？鉴于这些模棱两可的问题，Rust 不会去猜测我们想要什么，结构体不提供 `Display` 的实现。

如果我们继续阅读报错信息，将会发现这一段帮助信息：

```rs
   = help: the trait `std::fmt::Display` is not implemented for `Rectangle`
   = note: in format strings you may be able to use `{:?}` (or {:#?} for pretty-print) instead
```

我们来试试看！现在 `println!` 宏的调用将会是 `println!("rect1 is {:?}", rect1);` 这样。我们将标识符 `:?` 放入大括号中，告知 `println!` 我们希望使用 `Debug` 格式化输出。`Debug` trait 允许我们以一种对开发者很有帮助的方式打印出结构体，以便我们在调试代码时可以看到所有的值。

修改后再次编译代码。卧槽！又报错了：

```sh
error[E0277]: `Rectangle` doesn't implement `std::fmt::Debug`
```

但同时编译器也再次给出了帮助信息：

```sh
   = help: the trait `std::fmt::Debug` is not implemented for `Rectangle`
   = note: add `#[derive(Debug)]` or manually implement `std::fmt::Debug`
```

Rust 确实提供了打印调试信息的功能，但是我们必须在代码中明确选择出来，才能让结构体使用。因此，我们在结构体定义之前加上一段注释 `#[derive(Debug)]`，如代码示例 5-12 所示。

文件名：src/main.rs

```rs
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {:?}", rect1);
}
```

代码示例 5-12：增加注解来派生 `Debug` trait，并使用调试格式打印 `Rectangle` 实例

现在运行程序就没有任何报错了，我们将能够看到如下输出：

```sh
$ cargo run
   Compiling structs v0.1.0 (file:///projects/structs)
    Finished dev [unoptimized + debuginfo] target(s) in 0.48s
     Running `target/debug/structs`
rect1 is Rectangle { width: 30, height: 50 }
```

超棒！虽然不是最美观的输出，但是已经展示出了实例中所有字段的值，毫无疑问能够对调试有所帮助。而当我们有一些比较复杂的结构体时，以更易读的方式输出更实用；这时候，我们可以使用 `{:#?}` 替换 `println!` 字符串中的 `{:?}`。当我们在示例中使用 `{:#?}` 风格时，输出如下所示：

```rs
$ cargo run
   Compiling structs v0.1.0 (file:///projects/structs)
    Finished dev [unoptimized + debuginfo] target(s) in 0.48s
     Running `target/debug/structs`
rect1 is Rectangle {
    width: 30,
    height: 50,
}
```

Rust 为我们提供了很多可以配合 `derive` 注释使用的 trait，这些 trait 可以为我们的自定义类型增加很多实用的行为（behavior）。这些 trait 和行为罗列在附录 C 中。我们将会在第十章讲解如何通过自定义行为来实现这些 trait，以及如何创建自定义 trait。

我们的 `area` 函数非常特别：它只计算了矩形面积。能将这种计算面积的行为与 `Rectangle` 结构体结合得更紧密就好了，因为其他类型并不能应用这个函数。我们来看看下面我们如何继续重构这段代码，将 `area` 函数变为定义在 `Rectangle` 类型中的 `area` 方法。
