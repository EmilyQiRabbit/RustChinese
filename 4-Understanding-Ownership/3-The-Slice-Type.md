# slice 类型

另一种没有所有权的类型是 slice。slice 可以引用集合中某个连续的集合序列，而非整个集合。

我们来看一道编程小题：要求写一个函数，该函数接收一个字符串，并返回该字符串中的第一个单词。而如果字符串中没有空格，就认为整个字符串就是一个单词，此时返回整个字符串即可。

首先我们先想想函数签名应该怎么写：

```rs
fn first_word(s: &String) -> ?
```

函数 `first_word` 参数为 `&String` 类型。我们并不需要所有权，这样写没问题。那么我们应该返回什么呢？我们还没有正式讨论过字符串片段，不过我们可以先返回第一个单词末尾的索引。试试看示例 4-7 的代码：

文件名：src/main.rs

```rs
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
}
```

代码示例 4-7：函数 `first_word` 会返回 `String` 参数中的某一字节的索引值

因为我们需要审查 `String` 中的每一个元素，并确认其是否为空格，因此我们使用 `as_bytes` 方法将 `String` 转化为一个字节数组：

```rs
let bytes = s.as_bytes();
```

下面我们使用 `iter` 方法在该字节数组上创建一个迭代器：

```rs
for (i, &item) in bytes.iter().enumerate() {
```

我们将会在第十三章详细讨论迭代器。现在我们只需要知道 `iter` 方法会返回一个包含所有元素的集合，`enumerate` 则会包装 `iter` 返回的结果，将每个元素包裹在元组中。元组中第一个元素是索引，第二个则是元素的引用。这比我们自己计算索引要方便的多。

我们可以使用模式解构 `enumerate` 方法返回得元组。在 `for` 循环中，定义模式中的 `i` 表示元组中的索引，`&item` 表示元组中的单个字节。而因为我们从 `.iter().enumerate()` 得到的是元素的引用，因而在模式中也要使用 `&` 符号。

在 `for` 循环中，我们使用字节字面值语法来搜索表示空格的字节。如果找到了，就返回空格的位置信息。否则就使用 `s.len()` 返回字符串的长度：

```rs
    if item == b' ' {
        return i;
    }
}

s.len()
```

现在我们有办法找到字符串中第一个单词结尾的索引了，但是仍旧存在一个问题。我们只返回了 `usize` 数据本身，但它仅在 `&String` 数据的上下文中才是个有意义的数字。也就是，由于这个数字和 `String` 是分离的，那么我们无法保证未来这个数字依旧有效。我们来看代码示例 4-8 的程序，它使用了 4-7 中的 `first_word` 函数。

文件名：src/main.rs

```rs
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s); // word 的值是 5

    s.clear(); // 这行代码将 String 清空了，现在字符串等于 ""

    // 而 word 的值依旧是 5，但是字符串已经不存在了
    // 也就没有我们可以有效应用 5 的地方了。word 现在已经完全无效了！
}
```

代码示例 4-8：存储 `first_word` 函数返回的结果然后修改了 `String` 的内容

程序编译没有任何问题，并且在调用 `s.clear()` 后我们依旧可以使用 `word`。因为 `word` 和 `s` 的状态没有任何关联，其值依旧是 `5`。我们也依旧可以使用 `5` 来尝试提取出 `s` 的第一个单词，但这就会导致问题了，因为 `s` 的内容已经在将 `5` 保存至 `word` 后改变了。

我们不得不担心 `word` 保存的索引和 `s` 的数据不同步是数据冗余且容易出错的！而如果我们再写一个 `second_word` 函数来管理这些索引的逻辑甚至更加脆弱。它的函数签名如下：

```rs
fn second_word(s: &String) -> (usize, usize) {
```

这次我们追踪的是单词起始和结束的索引，我们对于数据某个特殊状态计算得出的结果更多了，但它们都没有和状态绑定。现在我们有三个相互并不关联的变量，而我们需要让它们保持同步。

幸运的是，Rust 对此类问题提供了解决方案：字符串 slice。

## 字符串 slice

字符串 slice 是 `String` 的部分的引用，如下所示：

```rs
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
```

这种方式和获取完整 `String` 的引用类似，但却还带有额外的 `[0..5]` 部分。它获取得不是整个 `String` 的引用，而是部分的引用。

我们使用方括号 `[starting_index..ending_index]` 的形式创建 slices，`starting_index` 是 slice 的起始位置，`ending_index` 是 slice 终止位置的下一个索引。在引用内，slice 数据结构保存了起始位置和 slice 的长度，即 `ending_index` 减去 `starting_index` 的值。所以例如 `let world = &s[6..11];`，`world` 是一个包含了指向 `s` 第 7 个（从 1 开始计算）字节的指针，并且长度值是 5 的 slice。

如图 4-6 所示。

![world containing a pointer to the 6th byte of String s and a length 5](../images/slice.png)

图 4-6：引用部分 `String` 的字符串 slice

如果你想要以第 1 个索引（即 0 号索引）作为起始，那么可以将 `..` 语法中两点之前的数字省略。也就是说，下面这两种写法是一样的：

```rs
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```

同理，如果 slice 包含了 `String` 的最后一个字节，也可以将结尾的数字省略。即如下两种写法是一样的：

```rs
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```

而如果想获取整个字符串的 slice，可以将两个值都省略。因此如下的两种写法也是一样的：

```rs
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];
```

> 注：字符串 slice 范围索引必须位于有效的 UTF-8 字符边界内。如果你尝试在多字节字符中间创建字符串 slice，程序将会报错错误退出。为了便于介绍字符串 slice，我们在本章假设只使用 ASCII 字符集；第八章的 “使用字符串存储 UTF-8 编码的文本” 部分会更加全面的讨论 UTF-8 的处理问题。

记住所有这些知识后，我们来重写 `first_word`，让它返回一个 slice。“字符串 slice” 的类型声明写作 `&str`：

文件名：src/main.rs

```rs
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

和示例代码 4-7 方法相同，我们寻找第一个空格出现的位置然后将该索引做为第一个单词的结尾返回。当我们找到第一个空格时，我们将字符串开始和空格的索引做为字符串 slice 的开始和结束。

现在我们调用 `first_word` 就会得到一个和底层数据关联的值。这个值由指向字符串起始位置的引用和 slice 中元素的数量组成。

对于 `second_word` 函数，返回 slice 同样可以奏效：

```rs
fn second_word(s: &String) -> &str {
```

现在这个 API 非常直观且更不易出现问题，因为编译器将会确保指向 `String` 的引用保持有效。还记得代码示例 4-8 中程序的问题吗，我们记录下了第一个单词末尾的索引，但是清空了字符串，那么这个索引也就无效了。这段代码的逻辑已经不对了，但是却没有马上报错。而当你尝试使用索引在一个空字符串里寻找第一个单词时，问题就会浮现出来。slice 将会确保这种问题不可能发生，并且会提早让我们知道代码中的问题。使用 slice 的 `first_word` 将会报出如下的编译时错误：

文件名：src/main.rs

这段代码无法编译！

```rs
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // 错误！

    println!("the first word is: {}", word);
}
```

编译错误为：

```sh
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
  --> src/main.rs:18:5
   |
16 |     let word = first_word(&s);
   |                           -- immutable borrow occurs here
17 |
18 |     s.clear(); // error!
   |     ^^^^^^^^^ mutable borrow occurs here
19 |
20 |     println!("the first word is: {}", word);
   |                                       ---- immutable borrow later used here
```

回忆一下借用的规则，如果创建了某一变量的不可变引用，那么就不能再创建一个可变的。由于 `clear` 函数需要清空 `String`，它需要获取一个可变引用。这是 Rust 所不允许的，于是就会报出编译错误。

### 字符串字面量就是 slice

还记得我们之前讨论过，存储在二进制程序中的字符串字面量吗。我们现在知道了 slice，我们也许能更好的理解字符串字面量：

```rs
let s = "Hello, world!";
```

这里 `s` 的类型是 `&str`：是一个指向二进制程序特定位置的 slice。因此字符串字面量是不可以修改的；因为 `&str` 是不可修改引用。

### 做为参数的字符串 slice

现在我们知道，获取字面量和 `String` 的 slice，可以让我们对 `first_word` 作出优化，如下是它的函数签名：

```rs
fn first_word(s: &String) -> &str {
```

更有经验的开发者则会使用如下代码示例 4-9 中的签名，这样我们使用 `&String` 和 `&str` 类型的时候都可以使用这个函数。

```rs
fn first_word(s: &str) -> &str {
```

代码示例 4-9：使用字符串 slice 做为 `s` 参数的类型，优化 `first_word` 函数

如果我们的变量是字符串 slice，我们可以直接将其传入函数。而如果变量时 `String` 类型，我们可以将整个 `String` 的 slice 传入。将函数参数定义为 `string` slice 而不是 `String` 的引用可以让 API 通用性更强，同时也不会影响任何功能：

文件名：src/main.rs

```rs
fn main() {
    let my_string = String::from("hello world");

    // 参数是 `String` slice 的 first_word 函数
    let word = first_word(&my_string[..]);

    let my_string_literal = "hello world";

    // 参数是字符串字面量 slice 的 first_word 函数
    let word = first_word(&my_string_literal[..]);

    // 字符串字面量已经是 slice 了，
    // 不使用 slice 语法也同样可以工作！
    let word = first_word(my_string_literal);
}
```

## 其他类型的 slice

字符串 slice 是针对字符串的。当然也存在更加通用的类型 slice。考虑如下数组：

```rs
let a = [1, 2, 3, 4, 5];
```

正如我们想引用字符串的一部分，我们也可能想要引用数组的一部分。那么这样就可以了：

```ts
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];
```

slice 的类型是 `&[i32]`。它和字符串 slice 的工作方式相同，存储了是第一个元素的引用和一个长度值。你可以对其他所有集合使用这类 slice。我们将会在第八章讨论 vector 的时候详细讨论这些集合。

## 总结

所有权、借用和 slice 的概念保证了 Rust 程序在编译时的内存安全。Rust 语言和其他系统编程语言一样，给了你控制内存使用的能力，但它还有数据所有权的概念，当数据所有者离开作用域时将会自动清理数据，这意味着开发者不需要对此处的控制编写和调试多余的代码了。

所有权影响了 Rust 其他很多部分的工作方式，我们在余下的部分中将会更深入探讨这个概念。我们继续学习第五章吧，看看如何使用 `struct` 将多个数据片组合起来。
