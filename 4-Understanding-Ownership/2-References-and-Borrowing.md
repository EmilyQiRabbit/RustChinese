## 引用（References）与借用（Borrowing）

代码示例 4-5 中使用了元组的代码存在的问题是，由于 `String` 值被移动进入了函数 `calculate_length`，因而我们就必须将 `String` 值的变量再返回出去，以便我们在函数 `calculate_length` 调用完成后还可以继续使用它。

如下代码展示了如何定义并使用以对象引用作为参数的函数 `calculate_length`，而非取走值的所有权：

文件名：src/main.rs

```rs
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

首先我们可以注意到，变量声明和函数返回值中的元组代码都去掉了。其次，传递到 `calculate_length` 函数中的是 `&s1`，并且在函数定义中，`&String` 替代了 `String`。

这些符号即表示引用（references），我们使用它就可以使用某些值而无需取走其所有权。如图 4-5 所示。

![&String s pointing at String s1](https://raw.githubusercontent.com/EmilyQiRabbit/RustChinese/master/images/references.png)

图 4-5：引用 `&String s` 指向 `String s1` 示意图

> 注：使用 `&` 符号引用相反的操作是解引用（dereferencing），使用解引用操作符 `*` 完成。我们将会在第八章接触到解引用操作的一些实际应用，并在第十五章详细讨论。

下面我们来详细解析这个函数调用：

```rs
let s1 = String::from("hello");

let len = calculate_length(&s1);
```

`&s1` 语法创建了一个指向 `s1` 的值的引用，但是它并不拥有该值。由于 `&s1` 不拥有堆上的值，那么当它离开作用域时它指向的值也就不会被销毁。

同样，使用了 `&` 的函数签名意味着其参数 `s` 是一个引用。我们为代码增加一些注释：

```rs
fn calculate_length(s: &String) -> usize { // s 是对 String 的引用
    s.len()
} // 这里，s 离开作用域。但是由于它并不具有所有权，因此不需任何特殊操作。
```

变量 `s` 有效的作用域和任何函数参数都是一样的，而由于它不具有所有权，离开作用域时也就无需任何释放内存的操作。当函数以引用而非具有所有权的值作为参数时，我们无需返回该值以返回其所有权，因为压根就没取走所有权。

我们将使用引用作为函数参数这种形式为借用（borrowing）。正如实际生活中，你可以想某些人借走他们的东西。用完了之后再归还。

那么如果我们试图修改借用的变量会怎么样呢？尝试编译代码示例 4-6。剧透警告：根本行不通！

文件名：src/main.rs

这段代码无法编译！

```rs
fn main() {
    let s = String::from("hello");

    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world");
}
```

代码示例 4-6：尝试修改借用的值

报错信息为：

```sh
error[E0596]: cannot borrow immutable borrowed content `*some_string` as mutable
 --> error.rs:8:5
  |
7 | fn change(some_string: &String) {
  |                        ------- use `&mut String` here to make mutable
8 |     some_string.push_str(", world");
  |     ^^^^^^^^^^^ cannot borrow as mutable
```

引用和变量一样，默认都是不可改的。我们不可以修改引用指向的值。

## 可变引用

稍作调整我们就能修复代码示例 4-6 的错误：

文件名：src/main.rs

```ts
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

首先，我们需要将 `s` 改为 `mut` 类型变量。接下来我们使用代码 `&mut s` 创建可变引用，并使用代码 `some_string: &mut String` 接收可变引用。

但可变引用有一个很大的限制：在特定定义域内，特定数据只能有一个可变引用。因而下面这段代码无法运行：

文件名：src/main.rs

这段代码无法编译！
```rs
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s;

println!("{}, {}", r1, r2);
```

报错信息为：

```sh
error[E0499]: cannot borrow `s` as mutable more than once at a time
 --> src/main.rs:5:14
  |
4 |     let r1 = &mut s;
  |              ------ first mutable borrow occurs here
5 |     let r2 = &mut s;
  |              ^^^^^^ second mutable borrow occurs here
6 |
7 |     println!("{}, {}", r1, r2);
  |                        -- first borrow later used here
```

可变引用可以使用，但我们必须遵守该约束条件。这对 Rust 新手来说可能难以适应，因为在大多数语言中，变量都可以任意修改。

而这种限制的好处是，Rust 有能力避免编译时数据竞争（data race）。数据竞争和竞争条件类似，可由如下三种操作造成：

* 两个或更多指针同时访问同一数据。
* 至少一个指针被用于写入数据。
* 没有同步数据访问的机制。

数据竞争会导致一些未定义的行为，运行时难以追踪、诊断并修复；Rust 防止了这种问题的发生，因为它根本不会编译存在数据竞争的代码！

和以前一样，我们可以使用花括号创建新的作用域，这样可以使用多个可变引用，只是它们不能同时存在：

```rs
let mut s = String::from("hello");

{
    let r1 = &mut s;

} // r1 离开作用域，从这之后就可以创建新的引用了。

let r2 = &mut s;
```

同时使用可变与不可变引用时也有类似的规则。如下代码也会报错：

这段代码无法编译！
```rs
let mut s = String::from("hello");

let r1 = &s; // 没问题
let r2 = &s; // 没问题
let r3 = &mut s; // 这是个大问题

println!("{}, {}, and {}", r1, r2, r3);
```

报错信息为：

```sh
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
 --> src/main.rs:6:14
  |
4 |     let r1 = &s; // 没问题
  |              -- immutable borrow occurs here
5 |     let r2 = &s; // 没问题
6 |     let r3 = &mut s; // 这是个大问题
  |              ^^^^^^ mutable borrow occurs here
7 |
8 |     println!("{}, {}, and {}", r1, r2, r3);
  |                                -- immutable borrow later used here
```

哇哦！当已经有不可修改引用时，也不能再创建可变引用了。使用不可变引用时当然不希望引用的值忽然发生了变化！但是创建多个不可修改引用却是可以的，因为所有读取数据的人都无法对其他人的数据造成影响。

我们注意到，引用的作用域从声明起到起最后使用为止。例如，这段代码能够编译，因为不可变引用最后使用的位置要先于可变引用的声明：

```rs
let mut s = String::from("hello");

let r1 = &s; // 没问题
let r2 = &s; // 没问题
println!("{} and {}", r1, r2);
// r1 and r2 are no longer used after this point
// 从这之后 r1 和 r2 都没用了

let r3 = &mut s; // 没问题
println!("{}", r3);
```

不可变引用 `r1` 和 `r2` 的作用域在最后一次使用 `println!` 后结束了，在这之后可变引用 `r3` 才被创建。作用域没有重叠，因而代码可行。

尽管有时报错会很恼人，但是记住这是 Rust 编译器在尽早（编译时而非运行时）指出潜在的问题，并精准展示出问题所在。这样你就不必费心思在运行时追踪为何数据并不是所期望的那样了。

## 悬垂引用（Dangling References）

在那些有指针概念的语言中，很容易就会错误的创建出了垂悬指针（dangling pointer），即指针指向的内存可能已经被分配到了别处，这一般是由于释放内存却保留指针造成的。而 Rust 和其他语言不同，编译器会保证绝不会出现垂悬引用：当你拥有指向数据的引用时，编译器会确保数据不会在其引用之前离开作用域。

我们来尝试创建一个垂悬引用，此时 Rust 就会报出编译时错误来避免其发生：

文件名：src/main.rs

这段代码无法编译！
```rs
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
}
```

错误信息为：

```sh
error[E0106]: missing lifetime specifier
 --> main.rs:5:16
  |
5 | fn dangle() -> &String {
  |                ^ expected lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is
  no value for it to be borrowed from
  = help: consider giving it a 'static lifetime
```

错误信息涉及到一个我们还没接触过的特性：生命周期。我们将会在第十章详细讨论生命周期。但如果我们暂时忽略生命周期的部分，报错信息确实包括了代码错误的关键信息：

> this function's return type contains a borrowed value, but there is no value for it to be borrowed from.

让我们仔细检查下这段造成了垂悬的 `dangle` 函数中的每一步到底发生了什么：

文件名：src/main.rs

这段代码无法编译！

```rs
fn dangle() -> &String { // dangle 函数的返回值是 String 引用

    let s = String::from("hello"); // s 是一个新的 String 类型变量

    &s // 将 String 类型变量 s 的引用返回了
} // 这里，s 离开了作用域，它的内存已经被释放了。
  // 危险！
```

由于 `s` 在 `dangle` 函数中被创建，当函数结束时，`s` 也就会被释放。但是我们却尝试返回 `s` 的引用。这意味着这个引用将会指向一个已经无效的 `String`。这可不妙！Rust 不允许这种操作。

解决方法是直接返回 `String` 类型的变量。

```rs
fn no_dangle() -> String {
    let s = String::from("hello");

    s
}
```

这就没问题了。所有权被移出，不需要释放任何内存。

## 引用的规则

我们来总结下本小节对引用的讨论：

* 任何时间点上，要么只能有一个可变引用，要么只能有多个不可变引用。
* 引用必须总是有效的。

下面我们将会学习另一种引用：slice。
