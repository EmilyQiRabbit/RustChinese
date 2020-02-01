# 数据类型

Rust 中所有值都属于某一确定的数据类型，数据类型确定了数据所属的类别，Rust 会因此以不同方式处理数据。本章节我们会学到数据类型的两个子集：标量（scalar）和复合（compound）。

时刻牢记，Rust 是静态类型语言，即编译时 Rust 必须能获取到所有变量的类型。通常情况下，编译器能够基于变量值和使用方法，推断出我们想要使用得是什么类型。而在某些情况下，推断结果包括多种可能，例如第二章的“对比猜测值与秘密数字”章节中，我们使用 `parse` 将 `String` 类型转化为数字类型，此时就必须加上类型注解，如下所示：

```rs
#![allow(unused_variables)]
fn main() {
    let guess: u32 = "42".parse().expect("Not a number!");
}
```

而如果我们没有添加类型注解，Rust 就会报错，这意味着编译器需要开发者提供更多关于所用变量类型的信息。Rust 报错信息如下：

```sh
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^
  |         |
  |         cannot infer type for `_`
  |         consider giving `guess` a type
```

下面我们将会看到各种不同数据类型的类型注解。

## 标量类型

标量类型代表了一个独立的值。Rust 有四种主要的标量类型：整数类型、浮点数、布尔值和字符类型。也许你在其他编程语言中也曾见到过它们。现在我们将深入探讨它们在 Rust 中是如何运作的。

### 整数类型

整数即没有小数部分的数字。在第二章中我们曾经使用过整数类型 `u32`。这种整数类型的值是无符号整数（有符号整数类型以 `i` 开头，而无符号的则以 `u` 开头），它占用 32 比特空间。如下表格 3-1 展示了 Rust 所有内建整数类型。有符号和无符号这两列下的每个变体（例如 `i16`）都可以用来声明整数值的类型。

表格 3-1：Rust 中的整数类型

| 占用空间长度 | 有符号	| 无符号 |
| :----: | :----: | :----: |
| 8-bit	| `i8` | `u8` | 
| 16-bit | `i16` | `u16` | 
| 32-bit | `i32` | `u32` | 
| 64-bit | `i64` | `u64` | 
| 128-bit | `i128` | `u128` | 
| arch | `isize` | `usize` | 

每种变体都可能是有符号或无符号的，并且也指明了大小。有符号和无符号表示数字是否可能为负数 —— 换句话说，有符号数需要一个符号来指定正负，而无符号数一定是正数，因此就不需要符号表示。这和我们书写数字很类似：当符号有意义时，数字前面就需要写一个加号或者减号；而当数字一定是正数时，就不需要写符号了。有符号数使用[补码形式](https://en.wikipedia.org/wiki/Two%27s_complement)存储。

有符号类型的变体可以存储 <img src="https://latex.codecogs.com/gif.latex?-2^{n-1}" title="-2^{n-1}" /> 到 <img src='https://latex.codecogs.com/gif.latex?2^{n - 1} - 1'/> 之内的数字（包括 <img src='https://latex.codecogs.com/gif.latex?-2^{n - 1}'/> 和 <img src='https://latex.codecogs.com/gif.latex?2^{n - 1} - 1'/>），其中 n 表示该变体使用的比特数。因此 `i8` 可以存储 <img src='https://latex.codecogs.com/gif.latex?-2^7'/> 到 <img src='https://latex.codecogs.com/gif.latex?2^7 - 1'/> 的数字，也就是 -128 到 127。而无符号类型的变体存储从 0 到 <img src='https://latex.codecogs.com/gif.latex?2^n - 1'/> 的数字，因此 `u8` 可以存储从 0 到 <img src='https://latex.codecogs.com/gif.latex?2^8 - 1'/> 即 0 到 255 的数字。

另外，`isize` 和 `usize` 两类则需要依赖于程序运行的设备：如果您的设备是 64 比特的，那么该值占用空间长度也为 64 比特；32 比特设备同理。

我们可以使用表 3-2 中的任何格式编写数字。注意，除了 Byte 之外的所有格式都可以添加类型后缀，例如 `57u8`，同时 `_` 符号可用作分隔线以方便读数，例如 `1_000`。

表 3-2：Rust 中的整数编写格式

| 数字编写格式 | 示例
| :----: | :----: |
| Decimal |	`98_222` |
| Hex |	`0xff` |
| Octal |	`0o77` |
| Binary |	`0b1111_0000` |
| Byte (仅限 `u8` 类型) |	`b'A'` |

那么我们如何选择使用何种整数类型呢？如果编程时我们并不能确定，使用 Rust 的默认类型通常是不错的选择，整数的默认类型是 `i32`：这个类型（的操作）通常是最快的，即使是在 64 比特系统也是如此。而当为某种集合建立索引时，最好的选择是使用 `isize` 或 `usize`。

> 整型溢出 <br/>
> 一个 `u8` 类型的变量能保存的数值为 0 到 255。而如果试图将变量值修改为一个此范围外的数字，例如 256，就会发生整型溢出。Rust 有一些很有意思的规则会涉及这种行为。当我们在调试模式编译时，Rust 会检查整型溢出，并在发生溢出时使程序 panic。每当程序由于错误而退出时，Rust 都会使用这种形式；我们将会在第九章的“不可恢复的错误与 panic”章节中详细讨论 panic。
> 而当我们使用标识 `--release` 在发版（release）模式下编译时，Rust 则不会检查整型溢出，也不会使程序 panic。而如果此时还是发生了整型溢出，Rust 会使用补码包装。简单来说，大于类型最大值的数值将会被“环绕”回该类型最小值处。例如如果是数值为 256 的 `u8` 类型，那么它就会变为 0，257 就会变为 1，以此类推。程序不会进入 panic，但是变量也不是你想要的那个值了。依赖整型溢出的环绕行为来完成功能其实是一种错误。而如果您想要明确指定让数值进行“环绕”，可以使用标准库类型 [`Wrapping`](https://doc.rust-lang.org/std/num/struct.Wrapping.html)。

### Floating-Point Types
### 浮点类型

Rust also has two primitive types for floating-point numbers, which are numbers with decimal points. Rust’s floating-point types are `f32` and `f64`, which are 32 bits and 64 bits in size, respectively. The default type is `f64` because on modern CPUs it’s roughly the same speed as `f32` but is capable of more precision.

Here’s an example that shows floating-point numbers in action:

Filename: src/main.rs

```rs
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

Floating-point numbers are represented according to the IEEE-754 standard. The `f32` type is a single-precision float, and `f64` has double precision.

### Numeric Operations
### 数值运算

Rust supports the basic mathematical operations you’d expect for all of the number types: addition, subtraction, multiplication, division, and remainder. The following code shows how you’d use each one in a `let` statement:

Filename: src/main.rs

```rs
fn main() {
    // addition
    let sum = 5 + 10;

    // subtraction
    let difference = 95.5 - 4.3;

    // multiplication
    let product = 4 * 30;

    // division
    let quotient = 56.7 / 32.2;

    // remainder
    let remainder = 43 % 5;
}
```

Each expression in these statements uses a mathematical operator and evaluates to a single value, which is then bound to a variable. Appendix B contains a list of all operators that Rust provides.

### The Boolean Type
### 布尔类型

As in most other programming languages, a Boolean type in Rust has two possible values: `true` and `false`. Booleans are one byte in size. The Boolean type in Rust is specified using `bool`. For example:

Filename: src/main.rs

```rs
fn main() {
    let t = true;

    let f: bool = false; // with explicit type annotation
}
```

The main way to use Boolean values is through conditionals, such as an `if` expression. We’ll cover how `if` expressions work in Rust in the “Control Flow” section.

### The Character Type
### 字符类型

So far we’ve worked only with numbers, but Rust supports letters too. Rust’s `char` type is the language’s most primitive alphabetic type, and the following code shows one way to use it. (Note that `char` literals are specified with single quotes, as opposed to string literals, which use double quotes.)

Filename: src/main.rs

```rs
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let heart_eyed_cat = '😻';
}
```

Rust’s `char` type is four bytes in size and represents a Unicode Scalar Value, which means it can represent a lot more than just ASCII. Accented letters; Chinese, Japanese, and Korean characters; emoji; and zero-width spaces are all valid `char` values in Rust. Unicode Scalar Values range from `U+0000` to `U+D7FF` and `U+E000` to `U+10FFFF` inclusive. However, a “character” isn’t really a concept in Unicode, so your human intuition for what a “character” is may not match up with what a `char` is in Rust. We’ll discuss this topic in detail in “Storing UTF-8 Encoded Text with Strings” in Chapter 8.

## Compound Types
## 复合类型

Compound types can group multiple values into one type. Rust has two primitive compound types: tuples and arrays.

### The Tuple Type
### 元组类型

A tuple is a general way of grouping together a number of values with a variety of types into one compound type. Tuples have a fixed length: once declared, they cannot grow or shrink in size.

We create a tuple by writing a comma-separated list of values inside parentheses. Each position in the tuple has a type, and the types of the different values in the tuple don’t have to be the same. We’ve added optional type annotations in this example:

Filename: src/main.rs

```rs
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

The variable `tup` binds to the entire tuple, because a tuple is considered a single compound element. To get the individual values out of a tuple, we can use pattern matching to destructure a tuple value, like this:

Filename: src/main.rs

```rs
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```

This program first creates a tuple and binds it to the variable `tup`. It then uses a pattern with `let` to take `tup` and turn it into three separate variables, `x`, `y`, and `z`. This is called destructuring, because it breaks the single tuple into three parts. Finally, the program prints the value of `y`, which is `6.4`.

In addition to destructuring through pattern matching, we can access a tuple element directly by using a period (`.`) followed by the index of the value we want to access. For example:

Filename: src/main.rs

```rs
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

This program creates a tuple, `x`, and then makes new variables for each element by using their respective indices. As with most programming languages, the first index in a tuple is 0.

### The Array Type
### 数组类型

Another way to have a collection of multiple values is with an array. Unlike a tuple, every element of an array must have the same type. Arrays in Rust are different from arrays in some other languages because arrays in Rust have a fixed length, like tuples.

In Rust, the values going into an array are written as a comma-separated list inside square brackets:

Filename: src/main.rs

```rs
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

Arrays are useful when you want your data allocated on the stack rather than the heap (we will discuss the stack and the heap more in Chapter 4) or when you want to ensure you always have a fixed number of elements. An array isn’t as flexible as the vector type, though. A vector is a similar collection type provided by the standard library that is allowed to grow or shrink in size. If you’re unsure whether to use an array or a vector, you should probably use a vector. Chapter 8 discusses vectors in more detail.

An example of when you might want to use an array rather than a vector is in a program that needs to know the names of the months of the year. It’s very unlikely that such a program will need to add or remove months, so you can use an array because you know it will always contain 12 elements:

```sh
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```

You would write an array’s type by using square brackets, and within the brackets include the type of each element, a semicolon, and then the number of elements in the array, like so:

```rs
#![allow(unused_variables)]
fn main() {
    let a: [i32; 5] = [1, 2, 3, 4, 5];
}
```

Here, `i32` is the type of each element. After the semicolon, the number `5` indicates the array contains five elements.

Writing an array’s type this way looks similar to an alternative syntax for initializing an array: if you want to create an array that contains the same value for each element, you can specify the initial value, followed by a semicolon, and then the length of the array in square brackets, as shown here:

```rs
#![allow(unused_variables)]
fn main() {
    let a = [3; 5];
}
```

The array named `a` will contain `5` elements that will all be set to the value `3` initially. This is the same as writing `let a = [3, 3, 3, 3, 3];` but in a more concise way.

#### Accessing Array Elements
#### 访问数组元素

An array is a single chunk of memory allocated on the stack. You can access elements of an array using indexing, like this:

Filename: src/main.rs

```re
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

In this example, the variable named `first` will get the value `1`, because that is the value at index `[0]` in the array. The variable named `second` will get the value `2` from index `[1]` in the array.

#### Invalid Array Element Access
#### 无效数组元素访问

What happens if you try to access an element of an array that is past the end of the array? Say you change the example to the following code, which will compile but exit with an error when it runs:

Filename: src/main.rs

This code panics!
```rs
fn main() {
    let a = [1, 2, 3, 4, 5];
    let index = 10;

    let element = a[index];

    println!("The value of element is: {}", element);
}
```

Running this code using `cargo run` produces the following result:

```sh
$ cargo run
   Compiling arrays v0.1.0 (file:///projects/arrays)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/arrays`
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is
 10', src/main.rs:5:19
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

The compilation didn’t produce any errors, but the program resulted in a runtime error and didn’t exit successfully. When you attempt to access an element using indexing, Rust will check that the index you’ve specified is less than the array length. If the index is greater than or equal to the array length, Rust will panic.

This is the first example of Rust’s safety principles in action. In many low-level languages, this kind of check is not done, and when you provide an incorrect index, invalid memory can be accessed. Rust protects you against this kind of error by immediately exiting instead of allowing the memory access and continuing. Chapter 9 discusses more of Rust’s error handling.
