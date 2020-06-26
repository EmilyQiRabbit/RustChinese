# An Example Program Using Structs
# 应用结构体编程示例

To understand when we might want to use structs, let’s write a program that calculates the area of a rectangle. We’ll start with single variables, and then refactor the program until we’re using structs instead.
为了更好的理解应用结构体的场景，我们来一起写一个计算矩形面积的小程序吧。我们先从一个变量开始，然后再使用结构体重构程序。

Let’s make a new binary project with Cargo called rectangles that will take the width and height of a rectangle specified in pixels and calculate the area of the rectangle. Listing 5-8 shows a short program with one way of doing exactly that in our project’s src/main.rs.
我们使用 Cargo 新建一个名为 rectangles 的二进制项目，它能获取矩形的长宽，并计算其面积。代码示例 5-8 就是项目 src/main.rs 文件中的小程序，是一种计算面积的方式。

Filename: src/main.rs
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

Listing 5-8: Calculating the area of a rectangle specified by separate width and height variables
代码示例 5-8：分别使用宽度和高度两个变量计算矩形面积

Now, run this program using `cargo run`:
现在，使用 `cargo run` 运行项目：

```sh
$ cargo run
   Compiling structs v0.1.0 (file:///projects/structs)
    Finished dev [unoptimized + debuginfo] target(s) in 0.42s
     Running `target/debug/structs`
The area of the rectangle is 1500 square pixels.
```

Even though Listing 5-8 works and figures out the area of the rectangle by calling the area function with each dimension, we can do better. The width and the height are related to each other because together they describe one rectangle.
尽管代码示例 5-8 能正常运行，并能通过调用 `area` 函数计算出矩形的面积，但我们其实能做得更好。矩形的宽高描述的是同一个矩形，因而它们是相互关联的。

The issue with this code is evident in the signature of `area`:
这段代码的问题明显在于 `area` 函数的签名：

```rs
fn area(width: u32, height: u32) -> u32 {
```

The `area` function is supposed to calculate the area of one rectangle, but the function we wrote has two parameters. The parameters are related, but that’s not expressed anywhere in our program. It would be more readable and more manageable to group width and height together. We’ve already discussed one way we might do that in “The Tuple Type” section of Chapter 3: by using tuples.
`area` 函数是为了计算一个矩形的面积，但是却需要两个参数。这两个参数是相关的，但是程序中没有任何地方对此作出说明。将宽度和高度组合起来，可以让代码的可读性和可管理性都更好。我们在第三章“元组类型”部分已经讨论过一种可行的方式了，那就是使用元组。

## Refactoring with Tuples
## 使用元组重构程序

Listing 5-9 shows another version of our program that uses tuples.
代码示例 5-9 是程序的另一个版本，它使用了元组。

Filename: src/main.rs
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

Listing 5-9: Specifying the width and height of the rectangle with a tuple
代码示例 5-9：使用元组指定矩形的宽高

In one way, this program is better. Tuples let us add a bit of structure, and we’re now passing just one argument. But in another way, this version is less clear: tuples don’t name their elements, so our calculation has become more confusing because we have to index into the parts of the tuple.
一方面来说，程序结构更合理了。元组的结构让我们可以只传入一个参数。但另一方面，这个版本的代码不够明确：元组没有为元素命名，我们就只能使用索引获取元组内元素，这样这个计算就变得让人有些费解。

It doesn’t matter if we mix up width and height for the area calculation, but if we want to draw the rectangle on the screen, it would matter! We would have to keep in mind that `width` is the tuple index `0` and `height` is the tuple index `1`. If someone else worked on this code, they would have to figure this out and keep it in mind as well. It would be easy to forget or mix up these values and cause errors, because we haven’t conveyed the meaning of our data in our code.
如果我们不慎将宽高弄混，这对面积的计算不会造成影响，但如果我们想要在屏幕上画出这个矩形，那么宽高的顺序就很重要了！我们必须牢记 `width` 是元组的 `0` 号元素，而 `height` 是 `1` 号。如果其他人也要使用这段代码，他们也必须弄清楚并牢记这一点。而因为这段代码里无法传递给开发者数据含义的信息，人们会很容易忘记或者弄混顺序并导致错误。

## Refactoring with Structs: Adding More Meaning
## 使用结构体重构程序：增加了更多含义

We use structs to add meaning by labeling the data. We can transform the tuple we’re using into a data type with a name for the whole as well as names for the parts, as shown in Listing 5-10.
我们使用结构体为数据增加标签，以此为代码增加含义。我们可以将元组转化为一种整体有名字，同时每个部分也有名字的数据类型，如代码示例 5-10 所示。

Filename: src/main.rs
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

Listing 5-10: Defining a `Rectangle` struct
代码示例 5-10：定义 `Rectangle` 结构体

Here we’ve defined a struct and named it `Rectangle`. Inside the curly brackets, we defined the fields as `width` and `height`, both of which have type `u32`. Then in `main`, we created a particular instance of `Rectangle` that has a width of 30 and a height of 50.
我们在这里定义了一个名为 `Rectangle` 的结构体。在大括号内，我们定义了字段 `width` 和 `height`，它们的类型都是 `u32`。然后在 `main` 函数中，我们创建了一个具体的 `Rectangle` 实例，它的宽是 30，高是 50。

Our `area` function is now defined with one parameter, which we’ve named `rectangle`, whose type is an immutable borrow of a struct `Rectangle` instance. As mentioned in Chapter 4, we want to borrow the struct rather than take ownership of it. This way, `main` retains its ownership and can continue using `rect1`, which is the reason we use the `&` in the function signature and where we call the function.
现在，`area` 函数定义中只需要一个参数了，我们将其命名为 `rectangle`，它的类型是结构体 `Rectangle` 实例的不可变引用。我们在第四章中讨论过，我们希望借用结构体而无需取得其所有权。因此我们在函数签名和调用的时候都使用了 `&`，这样，`main` 函数将能继续保有结构体所有权并继续使用 `rect1`。

The `area` function accesses the `width` and `height` fields of the `Rectangle` instance. Our function signature for `area` now says exactly what we mean: calculate the area of `Rectangle`, using its width and height fields. This conveys that the `width` and `height` are related to each other, and it gives descriptive names to the values rather than using the tuple index values of `0` and `1`. This is a win for clarity.
`area` 函数现在可以获取得是 `Rectangle` 实例的 `width` 和 `height` 字段。函数签名现在能准确表述我们想传达的含义：使用宽高字段计算矩形面积。同时也传达出了 `width` 和 `height` 是互相关联的信息，并且为这两个值提供了描述性的名字，而不是使用元组那样的 `0` 和 `1` 索引。这种方式清晰了很多。

## Adding Useful Functionality with Derived Traits
## 使用派生 trait 增加实用功能

It’d be nice to be able to print an instance of `Rectangle` while we’re debugging our program and see the values for all its fields. Listing 5-11 tries using the `println!` macro as we have used in previous chapters. This won’t work, however.
如果能在调试程序的时候打印出 `Rectangle` 实例用来观察其字段的值，那就再好不过了。代码示例 5-11 尝试使用我们在上一章使用过的 `println!` 宏来完成。但这段代码无法运行。

Filename: src/main.rs
文件名：src/main.rs

This code does not compile!
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

Listing 5-11: Attempting to print a `Rectangle` instance
代码示例 5-11：尝试打印出 `Rectangle` 实例

When we compile this code, we get an error with this core message:
代码编译时报错的关键信息是：

```sh
error[E0277]: `Rectangle` doesn't implement `std::fmt::Display`
```

The `println!` macro can do many kinds of formatting, and by default, the curly brackets tell `println!` to use formatting known as `Display`: output intended for direct end user consumption. The primitive types we’ve seen so far implement `Display` by default, because there’s only one way you’d want to show a `1` or any other primitive type to a user. But with structs, the way `println!` should format the output is less clear because there are more display possibilities: Do you want commas or not? Do you want to print the curly brackets? Should all the fields be shown? Due to this ambiguity, Rust doesn’t try to guess what we want, and structs don’t have a provided implementation of `Display`.
`println!` 宏无法完成很多类型的格式化，默认情况下，`println!` 认为大括号需要使用 `Display` 格式：意在给直接终端用户提供可查看的输出。目前我们见过的类型都默认实现了 `Display`，因为对于 `1` 或者其他基本类型来说，只有一种向用户展示的方法。但是对于结构体，`println!` 格式化要输出数据的方式就很不明确，有多种可能的展示：是否需要展示逗号？是否需要打印大括号？是否所有的字段都要被展示？鉴于这些模棱两可的问题，Rust 不会去猜测我们想要什么，结构体不提供 `Display` 的实现。

If we continue reading the errors, we’ll find this helpful note:
如果我们继续阅读报错信息，将会发现这一段帮助信息：

```rs
   = help: the trait `std::fmt::Display` is not implemented for `Rectangle`
   = note: in format strings you may be able to use `{:?}` (or {:#?} for pretty-print) instead
```

Let’s try it! The `println!` macro call will now look like `println!("rect1 is {:?}", rect1);`. Putting the specifier `:?` inside the curly brackets tells `println!` we want to use an output format called `Debug`. The `Debug` trait enables us to print our struct in a way that is useful for developers so we can see its value while we’re debugging our code.
我们来试试看！现在 `println!` 宏的调用将会是 `println!("rect1 is {:?}", rect1);` 这样。我们将标识符 `:?` 放入大括号中，告知 `println!` 我们希望使用 `Debug` 格式化输出。`Debug` trait 允许我们以一种对开发者很有帮助的方式打印出结构体，以便我们在调试代码时可以看到所有的值。

Compile the code with this change. Drat! We still get an error:
修改后再次编译代码。卧槽！又报错了：

```sh
error[E0277]: `Rectangle` doesn't implement `std::fmt::Debug`
```

But again, the compiler gives us a helpful note:
但同时编译器也再次给出了帮助信息：

```sh
   = help: the trait `std::fmt::Debug` is not implemented for `Rectangle`
   = note: add `#[derive(Debug)]` or manually implement `std::fmt::Debug`
```

Rust does include functionality to print out debugging information, but we have to explicitly opt in to make that functionality available for our struct. To do that, we add the annotation `#[derive(Debug)]` just before the struct definition, as shown in Listing 5-12.

Filename: src/main.rs
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

Listing 5-12: Adding the annotation to derive the `Debug` trait and printing the `Rectangle` instance using debug formatting

Now when we run the program, we won’t get any errors, and we’ll see the following output:

```sh
$ cargo run
   Compiling structs v0.1.0 (file:///projects/structs)
    Finished dev [unoptimized + debuginfo] target(s) in 0.48s
     Running `target/debug/structs`
rect1 is Rectangle { width: 30, height: 50 }
```

Nice! It’s not the prettiest output, but it shows the values of all the fields for this instance, which would definitely help during debugging. When we have larger structs, it’s useful to have output that’s a bit easier to read; in those cases, we can use `{:#?}` instead of `{:?}` in the `println!` string. When we use the `{:#?}` style in the example, the output will look like this:

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

Rust has provided a number of traits for us to use with the `derive` annotation that can add useful behavior to our custom types. Those traits and their behaviors are listed in Appendix C. We’ll cover how to implement these traits with custom behavior as well as how to create your own traits in Chapter 10.

Our `area` function is very specific: it only computes the area of rectangles. It would be helpful to tie this behavior more closely to our `Rectangle` struct, because it won’t work with any other type. Let’s look at how we can continue to refactor this code by turning the `area` function into an `area` method defined on our `Rectangle` type.
