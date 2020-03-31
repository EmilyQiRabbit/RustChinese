# 所有权是什么？

所有权是 Rust 的核心特性之一。尽管该特性本身很直白且易于解释，但是它对 Rust 的影响十分深刻。

所有程序在运行时都必须对使用计算机内存的方式进行管理。有些语言具有垃圾回收机制，会在运行时不断搜索不再需要的内存；而另一些语言则要求开发者必须在代码中明确内存的分配和释放。Rust 则使用了第三种方式：由所有权系统通过一系列规则管理内存，编译器会在编译时对这些规则进行检查。而所有的所有权特性都不会让程序运行速度变慢。

由于对于很多开发者来说，所有权都是一个全新的概念，因此也需要一些时间来适应。而好消息是，你越熟悉 Rust 和所有权系统的规则，你就越能自然而然的写出安全高效的代码。跟上节奏，继续学习！

一旦理解了所有权，也就获得了理解 Rust 特性的坚实基础，而正是这些特定让 Rust 独一无二。本章中我们将通过完成一些代码示例来学习所有权，这些例子都基于一个很常见的数据结构：String。

> ## 栈（Stack）和堆（Heap）
> 
> 在很多其他编程语言中，我们不必经常考虑到堆栈。而在像 Rust 这种系统编程语言中，值是保存在栈还是堆中会更大程度的影响编程语言的行为和作出某些决策的原因。本章的后续内容涉及和堆栈相关的部分所有权，因而在这里我们先做一个简短的介绍。
> 
> 堆和栈都是代码运行时可以使用的内存块，但是它们的结构不同。栈按顺序存储获取到的值，但以相反的顺序移出。也被称为后进先出。想象这里有一叠盘子：当需要放入新的盘子时，我们把它放在顶端，而当需要使用盘子时，也从顶端拿走。从中间或者底部放入或取走盘子都不可行！增加数据称为进栈，而移出数据则称为出栈。
> 
> 所有存储在栈中的数据的大小必须已知且固定。而编译时未知大小或者可能变化的数据则必须存储在堆中。堆的组织更弱化一些：当我们向堆中增加数据时，需要请求一个特定大小的空间。操作系统会在堆中找到一段足够大的空位，将其标记为已占用，并返回指向该地址的指针。这个过程称为分配堆内存，有时直接简称为分配。进栈操作不属于分配。而由于指针的大小是已知且固定的，我们可以将指针存储在栈中，当时当我们想要访问实际数据时，则必须访问指针所指向的内存。
> 
> 想象一下去餐厅就座用餐的过程。你进入餐厅，说明就餐人数，服务员会为你找一个足够大的空桌并把你领到桌子旁。如果有人来晚了，他可以询问座位在哪里并找到你们。
> 
> 进栈操作比分配堆内存要快，因为进栈时，操作系统不需搜索用来容纳新数据的内存空间；新数据直接放在栈顶即可。而相比而言，分配堆内存则需要更多工作，因为操作系统必须首先找到足以容纳数据的空间，并记录数据，好为下一次分配作准备。
> 
> 读取堆数据比读取栈数据慢，因为读取堆数据必须通过指针。而跳跃的内存越少，当代处理器的速度就越快。我们再打个比方，假设某个餐厅服务员同时负责多桌的点餐。最高效的方式是拿到某一桌所有订单后再去下一桌。而从 A 桌拿到一份点餐单，然后去 B 桌拿，接着再回到 A，然后再去 B，这一流程就比较慢。同理，处理器处理距离近的数据（例如栈中的数据）要比较远的时候（例如堆中的数据）更能更好的完成任务。在堆上分配大量的空间也十分耗时。
> 
> 当代码调用某个函数时，传入该函数的值（包括可能指向堆中数据的指针）和函数内局部变量将会被压入栈中。函数结束时，这些值会从栈中移出。
> 
> 追踪哪些代码使用了堆上哪些数据，尽可能减少堆中的重复数据，及时清理堆上无用的数据以免空间耗尽，这些都是所有权系统要处理的问题。一旦您理解了所有权，就不需要总是考虑堆和栈了，但是搞清楚所有权可以管理堆数据，能帮助解释为何所有权要以这种方式工作。

## Ownership Rules
## 所有权规则

First, let’s take a look at the ownership rules. Keep these rules in mind as we work through the examples that illustrate them:
首先，我们一起来看看所有权规则。在我们学习下文中的说明示例时，请牢记这些规则：

* Each value in Rust has a variable that’s called its owner.
* Rust 中所有的值都有一个被称为其所有者的变量。
* There can only be one owner at a time.
* 每一时刻该值只能有一个所有者。
* When the owner goes out of scope, the value will be dropped.
* 如果所有者离开作用域，这个值就会被丢弃。

## Variable Scope
## 变量作用域

We’ve walked through an example of a Rust program already in Chapter 2. Now that we’re past basic syntax, we won’t include all the `fn main() {` code in examples, so if you’re following along, you’ll have to put the following examples inside a `main` function manually. As a result, our examples will be a bit more concise, letting us focus on the actual details rather than boilerplate code.
在第二章中，我们已经学习过一个 Rust 程序示例。现在我们也已经学过了基础语法，那么在下面的示例中，将不再包含 `fn main() {` 代码，因此如果您想要仿照示例练习，请记得将代码放入 `main` 函数中。这样代码示例将更加简洁，我们就能更加专注于代码细节，而不是模版代码。

As a first example of ownership, we’ll look at the scope of some variables. A scope is the range within a program for which an item is valid. Let’s say we have a variable that looks like this:
在所有权学习的第一个例子中，我们先了解变量作用域。作用域是指元素在程序中有效的范围。假设有这样一个变量：

```rs
let s = "hello";
```
The variable `s` refers to a string literal, where the value of the string is hardcoded into the text of our program. The variable is valid from the point at which it’s declared until the end of the current scope. Listing 4-1 has comments annotating where the variable `s` is valid.
变量 `s` 绑定了一个字符串字面量，字符串的值硬编码在程序文本中。从变量声明到当前作用域止，其间变量是有效的。代码示例 4-1 的注释中指出了变量 `s` 有效的范围。

```rs
{                      // 这里 s 无效，此时 s 还未声明
    let s = "hello";   // s 从这里开始有效

    // 可以对 s 进行操作
}                      // 作用域结束，s 无效
```
Listing 4-1: A variable and the scope in which it is valid
代码示例 4-1：变量及其作用域

In other words, there are two important points in time here:
换言之，两个非常重要的时间点：

* When `s` comes into scope, it is valid.
* 当 `s` 进入作用域，它就是有效的。
* It remains valid until it goes out of scope.
* 有效的状态持续到离开作用域为止。

At this point, the relationship between scopes and when variables are valid is similar to that in other programming languages. Now we’ll build on top of this understanding by introducing the `String` type.
在这一点，作用域和变量是否有效的关系与其他语言是类似的。在理解了这一点的基础上，我们继续介绍 `String` 类型。

## The `String` Type

To illustrate the rules of ownership, we need a data type that is more complex than the ones we covered in the “Data Types” section of Chapter 3. The types covered previously are all stored on the stack and popped off the stack when their scope is over, but we want to look at data that is stored on the heap and explore how Rust knows when to clean up that data.

We’ll use `String` as the example here and concentrate on the parts of `String` that relate to ownership. These aspects also apply to other complex data types, whether they are provided by the standard library or created by you. We’ll discuss `String` in more depth in Chapter 8.

We’ve already seen string literals, where a string value is hardcoded into our program. String literals are convenient, but they aren’t suitable for every situation in which we may want to use text. One reason is that they’re immutable. Another is that not every string value can be known when we write our code: for example, what if we want to take user input and store it? For these situations, Rust has a second string type, `String`. This type is allocated on the heap and as such is able to store an amount of text that is unknown to us at compile time. You can create a `String` from a string literal using the from function, like so:

```rs
let s = String::from("hello");
```

The double colon (`::`) is an operator that allows us to namespace this particular `from` function under the `String` type rather than using some sort of name like `string_from`. We’ll discuss this syntax more in the “Method Syntax” section of Chapter 5 and when we talk about namespacing with modules in “Paths for Referring to an Item in the Module Tree” in Chapter 7.

This kind of string can be mutated:

```rs
let mut s = String::from("hello");

s.push_str(", world!"); // push_str() appends a literal to a String

println!("{}", s); // This will print `hello, world!`
```

So, what’s the difference here? Why can `String` be mutated but literals cannot? The difference is how these two types deal with memory.

## Memory and Allocation

In the case of a string literal, we know the contents at compile time, so the text is hardcoded directly into the final executable. This is why string literals are fast and efficient. But these properties only come from the string literal’s immutability. Unfortunately, we can’t put a blob of memory into the binary for each piece of text whose size is unknown at compile time and whose size might change while running the program.

With the `String` type, in order to support a mutable, growable piece of text, we need to allocate an amount of memory on the heap, unknown at compile time, to hold the contents. This means:

* The memory must be requested from the operating system at runtime.
* We need a way of returning this memory to the operating system when we’re done with our `String`.

That first part is done by us: when we call `String::from`, its implementation requests the memory it needs. This is pretty much universal in programming languages.

However, the second part is different. In languages with a garbage collector (GC), the GC keeps track and cleans up memory that isn’t being used anymore, and we don’t need to think about it. Without a GC, it’s our responsibility to identify when memory is no longer being used and call code to explicitly return it, just as we did to request it. Doing this correctly has historically been a difficult programming problem. If we forget, we’ll waste memory. If we do it too early, we’ll have an invalid variable. If we do it twice, that’s a bug too. We need to pair exactly one `allocate` with exactly one `free`.

Rust takes a different path: the memory is automatically returned once the variable that owns it goes out of scope. Here’s a version of our scope example from Listing 4-1 using a String instead of a `string` literal:

```rs
{
    let s = String::from("hello"); // s is valid from this point forward

    // do stuff with s
}                                  // this scope is now over, and s is no
                                   // longer valid
```

There is a natural point at which we can return the memory our `String` needs to the operating system: when `s` goes out of scope. When a variable goes out of scope, Rust calls a special function for us. This function is called `drop`, and it’s where the author of `String` can put the code to return the memory. Rust calls `drop` automatically at the closing curly bracket.

> Note: In C++, this pattern of deallocating resources at the end of an item’s lifetime is sometimes called Resource Acquisition Is Initialization (RAII). The `drop` function in Rust will be familiar to you if you’ve used RAII patterns.

This pattern has a profound impact on the way Rust code is written. It may seem simple right now, but the behavior of code can be unexpected in more complicated situations when we want to have multiple variables use the data we’ve allocated on the heap. Let’s explore some of those situations now.

### Ways Variables and Data Interact: Move

Multiple variables can interact with the same data in different ways in Rust. Let’s look at an example using an integer in Listing 4-2.

```rs
let x = 5;
let y = x;
```

Listing 4-2: Assigning the integer value of variable `x` to `y`

We can probably guess what this is doing: “bind the value `5` to `x`; then make a copy of the value in `x` and bind it to `y`.” We now have two variables, `x` and `y`, and both equal `5`. This is indeed what is happening, because integers are simple values with a known, fixed size, and these two `5` values are pushed onto the stack.

Now let’s look at the `String` version:

```rs
let s1 = String::from("hello");
let s2 = s1;
```

This looks very similar to the previous code, so we might assume that the way it works would be the same: that is, the second line would make a copy of the value in `s1` and bind it to `s2`. But this isn’t quite what happens.

Take a look at Figure 4-1 to see what is happening to `String` under the covers. A `String` is made up of three parts, shown on the left: a pointer to the memory that holds the contents of the string, a length, and a capacity. This group of data is stored on the stack. On the right is the memory on the heap that holds the contents.

[image]()

Figure 4-1: Representation in memory of a `String` holding the value `"hello"` bound to `s1`

The length is how much memory, in bytes, the contents of the `String` is currently using. The capacity is the total amount of memory, in bytes, that the `String` has received from the operating system. The difference between length and capacity matters, but not in this context, so for now, it’s fine to ignore the capacity.

When we assign `s1` to `s2`, the `String` data is copied, meaning we copy the pointer, the length, and the capacity that are on the stack. We do not copy the data on the heap that the pointer refers to. In other words, the data representation in memory looks like Figure 4-2.

[image]()
Figure 4-2: Representation in memory of the variable `s2` that has a copy of the pointer, length, and capacity of `s1`

The representation does not look like Figure 4-3, which is what memory would look like if Rust instead copied the heap data as well. If Rust did this, the operation `s2 = s1` could be very expensive in terms of runtime performance if the data on the heap were large.

[image]()
Figure 4-3: Another possibility for what `s2 = s1` might do if Rust copied the heap data as well

Earlier, we said that when a variable goes out of scope, Rust automatically calls the `drop` function and cleans up the heap memory for that variable. But Figure 4-2 shows both data pointers pointing to the same location. This is a problem: when `s2` and `s1` go out of scope, they will both try to free the same memory. This is known as a double free error and is one of the memory safety bugs we mentioned previously. Freeing memory twice can lead to memory corruption, which can potentially lead to security vulnerabilities.

To ensure memory safety, there’s one more detail to what happens in this situation in Rust. Instead of trying to copy the allocated memory, Rust considers `s1` to no longer be valid and, therefore, Rust doesn’t need to free anything when `s1` goes out of scope. Check out what happens when you try to use `s1` after `s2` is created; it won’t work:

This code does not compile!
```rs
let s1 = String::from("hello");
let s2 = s1;

println!("{}, world!", s1);
```
You’ll get an error like this because Rust prevents you from using the invalidated reference:

```sh
error[E0382]: use of moved value: `s1`
 --> src/main.rs:5:28
  |
3 |     let s2 = s1;
  |         -- value moved here
4 |
5 |     println!("{}, world!", s1);
  |                            ^^ value used here after move
  |
  = note: move occurs because `s1` has type `std::string::String`, which does
  not implement the `Copy` trait
```

If you’ve heard the terms shallow copy and deep copy while working with other languages, the concept of copying the pointer, length, and capacity without copying the data probably sounds like making a shallow copy. But because Rust also invalidates the first variable, instead of being called a shallow copy, it’s known as a move. In this example, we would say that `s1` was moved into `s2`. So what actually happens is shown in Figure 4-4.

[image]()
Figure 4-4: Representation in memory after `s1` has been invalidated

That solves our problem! With only `s2` valid, when it goes out of scope, it alone will free the memory, and we’re done.

In addition, there’s a design choice that’s implied by this: Rust will never automatically create “deep” copies of your data. Therefore, any automatic copying can be assumed to be inexpensive in terms of runtime performance.

### Ways Variables and Data Interact: Clone

If we do want to deeply copy the heap data of the `String`, not just the stack data, we can use a common method called `clone`. We’ll discuss method syntax in Chapter 5, but because methods are a common feature in many programming languages, you’ve probably seen them before.

Here’s an example of the `clone` method in action:

```rs
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
```

This works just fine and explicitly produces the behavior shown in Figure 4-3, where the heap data does get copied.

When you see a call to `clone`, you know that some arbitrary code is being executed and that code may be expensive. It’s a visual indicator that something different is going on.

### Stack-Only Data: Copy

There’s another wrinkle we haven’t talked about yet. This code using integers, part of which was shown in Listing 4-2, works and is valid:

```rs
let x = 5;
let y = x;

println!("x = {}, y = {}", x, y);
```

But this code seems to contradict what we just learned: we don’t have a call to `clone`, but `x` is still valid and wasn’t moved into `y`.

The reason is that types such as integers that have a known size at compile time are stored entirely on the stack, so copies of the actual values are quick to make. That means there’s no reason we would want to prevent `x` from being valid after we create the variable `y`. In other words, there’s no difference between deep and shallow copying here, so calling `clone` wouldn’t do anything different from the usual shallow copying and we can leave it out.

Rust has a special annotation called the `Copy` trait that we can place on types like integers that are stored on the stack (we’ll talk more about traits in Chapter 10). If a type has the `Copy` trait, an older variable is still usable after assignment. Rust won’t let us annotate a type with the `Copy` trait if the type, or any of its parts, has implemented the `Drop` trait. If the type needs something special to happen when the value goes out of scope and we add the `Copy` annotation to that type, we’ll get a compile-time error. To learn about how to add the `Copy` annotation to your type, see “Derivable Traits” in Appendix C.

So what types are `Copy`? You can check the documentation for the given type to be sure, but as a general rule, any group of simple scalar values can be `Copy`, and nothing that requires allocation or is some form of resource is `Copy`. Here are some of the types that are `Copy`:

* All the integer types, such as `u32`.
* The Boolean type, `bool`, with values `true` and `false`.
* All the floating point types, such as `f64`.
* The character type, `char`.
* Tuples, if they only contain types that are also `Copy`. For example, `(i32, i32)` is `Copy`, but `(i32, String)` is not.

## Ownership and Functions

The semantics for passing a value to a function are similar to those for assigning a value to a variable. Passing a variable to a function will move or copy, just as assignment does. Listing 4-3 has an example with some annotations showing where variables go into and out of scope.

Filename: src/main.rs

```rs
fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // x would move into the function,
                                    // but i32 is Copy, so it’s okay to still
                                    // use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{}", some_integer);
} // Here, some_integer goes out of scope. Nothing special happens.
```

Listing 4-3: Functions with ownership and scope annotated

If we tried to use `s` after the call to `takes_ownership`, Rust would throw a compile-time error. These static checks protect us from mistakes. Try adding code to `main` that uses `s` and `x` to see where you can use them and where the ownership rules prevent you from doing so.

### Return Values and Scope

Returning values can also transfer ownership. Listing 4-4 is an example with similar annotations to those in Listing 4-3.

Filename: src/main.rs

```rs
fn main() {
    let s1 = gives_ownership();         // gives_ownership moves its return
                                        // value into s1

    let s2 = String::from("hello");     // s2 comes into scope

    let s3 = takes_and_gives_back(s2);  // s2 is moved into
                                        // takes_and_gives_back, which also
                                        // moves its return value into s3
} // Here, s3 goes out of scope and is dropped. s2 goes out of scope but was
  // moved, so nothing happens. s1 goes out of scope and is dropped.

fn gives_ownership() -> String {             // gives_ownership will move its
                                             // return value into the function
                                             // that calls it

    let some_string = String::from("hello"); // some_string comes into scope

    some_string                              // some_string is returned and
                                             // moves out to the calling
                                             // function
}

// takes_and_gives_back will take a String and return one
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope

    a_string  // a_string is returned and moves out to the calling function
}
```

Listing 4-4: Transferring ownership of return values

The ownership of a variable follows the same pattern every time: assigning a value to another variable moves it. When a variable that includes data on the heap goes out of scope, the value will be cleaned up by `drop` unless the data has been moved to be owned by another variable.

Taking ownership and then returning ownership with every function is a bit tedious. What if we want to let a function use a value but not take ownership? It’s quite annoying that anything we pass in also needs to be passed back if we want to use it again, in addition to any data resulting from the body of the function that we might want to return as well.

It’s possible to return multiple values using a tuple, as shown in Listing 4-5.

Filename: src/main.rs

```rs
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() returns the length of a String

    (s, length)
}
```
Listing 4-5: Returning ownership of parameters

But this is too much ceremony and a lot of work for a concept that should be common. Luckily for us, Rust has a feature for this concept, called references.
