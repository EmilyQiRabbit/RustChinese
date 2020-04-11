## References and Borrowing
## 引用（References）与借用（Borrowing）

The issue with the tuple code in Listing 4-5 is that we have to return the `String` to the calling function so we can still use the `String` after the call to `calculate_length`, because the `String` was moved into `calculate_length`.
代码示例 4-5 中使用了元组的代码存在的问题是，由于 `String` 值被移动进入了函数 `calculate_length`，因而我们就必须将 `String` 值的变量再返回出去，以便我们在函数 `calculate_length` 调用完成后还可以继续使用它。

Here is how you would define and use a `calculate_length` function that has a reference to an object as a parameter instead of taking ownership of the value:
如下代码展示了如何定义并使用以对象引用作为参数的函数 `calculate_length`，而非取走值的所有权：

Filename: src/main.rs
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

First, notice that all the tuple code in the variable declaration and the function return value is gone. Second, note that we pass &`s1` into `calculate_length` and, in its definition, we take `&String` rather than `String`.
首先我们可以注意到，变量声明和函数返回值中的元组代码都去掉了。其次，传递到 `calculate_length` 函数中的是 `&s1`，并且在函数定义中，`&String` 替代了 `String`。

These ampersands are **references**, and they allow you to refer to some value without taking ownership of it. Figure 4-5 shows a diagram.
这些符号即表示引用（references），我们使用它就可以使用某些值而无需取走其所有权。如图 4-5 所示。

![&String s pointing at String s1](https://raw.githubusercontent.com/EmilyQiRabbit/RustChinese/master/images/references.png)

Figure 4-5: A diagram of `&String s` pointing at `String s1`
图 4-5：引用 `&String s` 指向 `String s1` 示意图

> Note: The opposite of referencing by using `&` is dereferencing, which is accomplished with the dereference operator, `*`. We’ll see some uses of the dereference operator in Chapter 8 and discuss details of dereferencing in Chapter 15.
> 注：使用 `&` 符号引用相反的操作是解引用（dereferencing），使用解引用操作符 `*` 完成。我们将会在第八章接触到解引用操作的一些实际应用，并在第十五章详细讨论。

Let’s take a closer look at the function call here:
下面我们来详细解析这个函数调用：

```rs
let s1 = String::from("hello");

let len = calculate_length(&s1);
```

The &`s1` syntax lets us create a reference that refers to the value of `s1` but does not own it. Because it does not own it, the value it points to will not be dropped when the reference goes out of scope.
`&s1` 语法创建了一个指向 `s1` 的值的引用，但是它并不拥有该值。由于 `&s1` 不拥有堆上的值，那么当它离开作用域时它指向的值也就不会被销毁。

Likewise, the signature of the function uses `&` to indicate that the type of the parameter `s` is a reference. Let’s add some explanatory annotations:
同样，使用了 `&` 的函数签名意味着其参数 `s` 是一个引用。我们为代码增加一些注释：

```rs
fn calculate_length(s: &String) -> usize { // s 是对 String 的引用
    s.len()
} // 这里，s 离开作用域。但是由于它并不具有所有权，因此不需任何特殊操作。
```

The scope in which the variable `s` is valid is the same as any function parameter’s scope, but we don’t drop what the reference points to when it goes out of scope because we don’t have ownership. When functions have references as parameters instead of the actual values, we won’t need to return the values in order to give back ownership, because we never had ownership.
变量 `s` 有效的作用域和任何函数参数都是一样的，而由于它不具有所有权，离开作用域时也就无需任何释放内存的操作。当函数以引用而非具有所有权的值作为参数时，我们无需返回该值以返回其所有权，因为压根就没取走所有权。

We call having references as function parameters borrowing. As in real life, if a person owns something, you can borrow it from them. When you’re done, you have to give it back.
我们将使用引用作为函数参数这种形式为借用（borrowing）。正如实际生活中，你可以想某些人借走他们的东西。用完了之后再归还。

So what happens if we try to modify something we’re borrowing? Try the code in Listing 4-6. Spoiler alert: it doesn’t work!
那么如果我们试图修改借用的变量会怎么样呢？尝试编译代码示例 4-6。剧透警告：根本行不通！

Filename: src/main.rs
文件名：src/main.rs

This code does not compile!
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

Listing 4-6: Attempting to modify a borrowed value
代码示例 4-6：尝试修改借用的值

Here’s the error:
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

Just as variables are immutable by default, so are references. We’re not allowed to modify something we have a reference to.
引用和变量一样，默认都是不可改的。我们不可以修改引用指向的值。

## Mutable References
## 可变引用

We can fix the error in the code from Listing 4-6 with just a small tweak:
稍作调整我们就能修复代码示例 4-6 的错误：

Filename: src/main.rs
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

First, we had to change `s` to be `mut`. Then we had to create a mutable reference with `&mut s` and accept a mutable reference with `some_string: &mut String`.
首先，我们需要将 `s` 改为 `mut` 类型变量。接下来我们使用代码 `&mut s` 创建可变引用，并使用代码 `some_string: &mut String` 接收可变引用。

But mutable references have one big restriction: you can have only one mutable reference to a particular piece of data in a particular scope. This code will fail:
但可变引用有一个很大的限制：在特定定义域内，特定数据只能有一个可变引用。因而下面这段代码无法运行：

Filename: src/main.rs
文件名：src/main.rs

This code does not compile!
这段代码无法编译！
```rs
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s;

println!("{}, {}", r1, r2);
```

Here’s the error:
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

This restriction allows for mutation but in a very controlled fashion. It’s something that new Rustaceans struggle with, because most languages let you mutate whenever you’d like.
可变引用可以使用，但我们必须遵守该约束条件。这对 Rust 新手来说可能难以适应，因为在大多数语言中，变量都可以任意修改。

The benefit of having this restriction is that Rust can prevent data races at compile time. A data race is similar to a race condition and happens when these three behaviors occur:
而这种限制的好处是，Rust 有能力避免编译时数据竞争（data race）。数据竞争和竞争条件类似，可由如下三种操作造成：

* Two or more pointers access the same data at the same time.
* 两个或更多指针同时访问同一数据。
* At least one of the pointers is being used to write to the data.
* 至少一个指针被用于写入数据。
* There’s no mechanism being used to synchronize access to the data.
* 没有同步数据访问的机制。

Data races cause undefined behavior and can be difficult to diagnose and fix when you’re trying to track them down at runtime; Rust prevents this problem from happening because it won’t even compile code with data races!
数据竞争会导致一些未定义的行为，运行时难以追踪、诊断并修复；Rust 防止了这种问题的发生，因为它根本不会编译存在数据竞争的代码！

As always, we can use curly brackets to create a new scope, allowing for multiple mutable references, just not simultaneous ones:
和以前一样，我们可以使用花括号创建新的作用域，这样可以使用多个可变引用，只是它们不能同时存在：

```rs
let mut s = String::from("hello");

{
    let r1 = &mut s;

} // r1 离开作用域，从这之后就可以创建新的引用了。

let r2 = &mut s;
```

A similar rule exists for combining mutable and immutable references. This code results in an error:
同时使用可变与不可变引用时也有类似的规则。如下代码也会报错：

This code does not compile!
这段代码无法编译！
```rs
let mut s = String::from("hello");

let r1 = &s; // 没问题
let r2 = &s; // 没问题
let r3 = &mut s; // 这是个大问题

println!("{}, {}, and {}", r1, r2, r3);
```

Here’s the error:
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

Whew! We also cannot have a mutable reference while we have an immutable one. Users of an immutable reference don’t expect the values to suddenly change out from under them! However, multiple immutable references are okay because no one who is just reading the data has the ability to affect anyone else’s reading of the data.
哇哦！当已经有不可修改引用时，也不能再创建可变引用了。使用不可变引用时当然不希望引用的值忽然发生了变化！但是创建多个不可修改引用却是可以的，因为所有读取数据的人都无法对其他人的数据造成影响。

Note that a reference’s scope starts from where it is introduced and continues through the last time that reference is used. For instance, this code will compile because the last usage of the immutable references occurs before the mutable reference is introduced:
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

The scopes of the immutable references `r1` and `r2` end after the `println!` where they are last used, which is before the mutable reference `r3` is created. These scopes don’t overlap, so this code is allowed.
不可变引用 `r1` 和 `r2` 的作用域在最后一次使用 `println!` 后结束了，在这之后可变引用 `r3` 才被创建。作用域没有重叠，因而代码可行。

Even though borrowing errors may be frustrating at times, remember that it’s the Rust compiler pointing out a potential bug early (at compile time rather than at runtime) and showing you exactly where the problem is. Then you don’t have to track down why your data isn’t what you thought it was.
尽管有时报错会很恼人，但是记住这是 Rust 编译器在尽早（编译时而非运行时）指出潜在的问题，并精准展示出问题所在。这样你就不必费心思在运行时追踪为何数据并不是所期望的那样了。

## Dangling References

In languages with pointers, it’s easy to erroneously create a dangling pointer, a pointer that references a location in memory that may have been given to someone else, by freeing some memory while preserving a pointer to that memory. In Rust, by contrast, the compiler guarantees that references will never be dangling references: if you have a reference to some data, the compiler will ensure that the data will not go out of scope before the reference to the data does.

Let’s try to create a dangling reference, which Rust will prevent with a compile-time error:

Filename: src/main.rs

This code does not compile!

```rs
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
}
```

Here’s the error:

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

This error message refers to a feature we haven’t covered yet: lifetimes. We’ll discuss lifetimes in detail in Chapter 10. But, if you disregard the parts about lifetimes, the message does contain the key to why this code is a problem:

> this function's return type contains a borrowed value, but there is no value 
> for it to be borrowed from.

Let’s take a closer look at exactly what’s happening at each stage of our `dangle` code:

Filename: src/main.rs

This code does not compile!

```rs
fn dangle() -> &String { // dangle returns a reference to a String

    let s = String::from("hello"); // s is a new String

    &s // we return a reference to the String, s
} // Here, s goes out of scope, and is dropped. Its memory goes away.
  // Danger!
```

Because `s` is created inside `dangle`, when the code of `dangle` is finished, `s` will be deallocated. But we tried to return a reference to it. That means this reference would be pointing to an invalid `String`. That’s no good! Rust won’t let us do this.

The solution here is to return the `String` directly:

```rs
fn no_dangle() -> String {
    let s = String::from("hello");

    s
}
```

This works without any problems. Ownership is moved out, and nothing is deallocated.

## The Rules of References

Let’s recap what we’ve discussed about references:

* At any given time, you can have either one mutable reference or any number of immutable references.
* References must always be valid.

Next, we’ll look at a different kind of reference: slices.
