# 控制流

由某条件是否为真来决定是否运行某段代码，或者当某条件为真就反复运行某段代码，这些都是大部分编程语言的基础构成。Rust 代码中最常见的控制执行流的结构体是 `if` 表达式和循环语句。

## `if` 表达式

`if` 表达式能够根据条件分支代码。由开发者提供条件并声明，“如果条件满足，则执行这部分代码。否则就不要执行。”

在 projects 目录下创建一个名为 branches 的新项目来学习 `if` 表达式。在 src/main.rs 文件中输入如下代码：

文件名：src/main.rs

```rs
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

所有 `if` 表达式都以关键字 `if` 开头，并在它后面跟一个条件。这个例子中，条件代码会判断 `number` 变量的值是否小于 5。我们希望 if 条件为真时执行的代码就放在条件句后面的花括号中。与 `if` 表达式中条件关联的代码块有时也被称为 arms，正如我们在第二章中讨论过的 `match` 表达式中的分支一样。

我们还可以选择在代码中加入 `else` 表达式，就像上面的代码中那样，这样当条件计算结果为假，程序就会换为执行这段代码。而如果我们不提供 `else` 表达式并且 if 条件为假，程序就会直接跳过 `if` 代码块，继续执行下面的代码。

试着运行代码；将会看到如下输出：

```sh
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/branches`
condition was true
```

现在我们修改 `number` 的值，让条件判断的结果变为 `false`，看看会发生什么：

```rs
let number = 7;
```

重新运行程序并查看输出：

```sh
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/branches`
condition was false
```

还需要指出代码中的条件结果必须是 `bool` 类型。如果不这样就会报错。例如，我们尝试运行如下代码：

文件名：src/main.rs

这段代码无法编译！
```rs
fn main() {
    let number = 3;

    if number {
        println!("number was three");
    }
}
```

`if` 条件句计算的结果是 `3`，Rust 此时抛出了错误：

```sh
error[E0308]: mismatched types
 --> src/main.rs:4:8
  |
4 |     if number {
  |        ^^^^^^ expected bool, found integer
  |
  = note: expected type `bool`
             found type `{integer}`
```

错误表明 Rust 希望此处结果为布尔（`bool`）类型，但却发现是整数（`integer`）类型。和 Ruby 或 JavaScript 不同，Rust 不会自动尝试将非布尔类型的值转化为布尔类型。我们必须严格地为 `if` 提供布尔类型的值作为其条件。例如，如果我们希望 `if` 后的代码块只在某值不为 `0` 的时候执行，那么可以将上面的代码修改为：

文件名：src/main.rs

```rs
fn main() {
    let number = 3;

    if number != 0 {
        println!("number was something other than zero");
    }
}
```

运行代码将会打印出 `number was something other than zero`。

### 使用 `else if` 处理多重条件

可以将 `else if` 表达式与 `if` 和 `else` 组合来实现多重条件。例如：

文件名：src/main.rs

```rs
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```

程序提供了四个可能的执行路径。运行程序后，我们应该能看到如下输出：

```sh
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/branches`
number is divisible by 3
```

当程序执行时，它将会逐个检查 `if` 表达式，并执行所遇到的第一个条件为真后面的代码块。注意，尽管 6 也可以被 2 整除，但没有输出 `number is divisible by 2`，也没有输出 `else` 后面的 `number is not divisible by 4, 3, or 2`。这是因为 Rust 只会执行第一个条件为真的代码块，一旦找到，后面的条件都不会被检查。

使用过多的 `else if` 会让代码看起来杂乱无章，所以当我们使用多重条件时，可能会希望重构代码以变得整洁。在第六章我们将会介绍一个黑科技：Rust 分支结构 `match`，以应对这种情况。

### 在 `let` 语句中使用 `if`

由于 `if` 是表达式，我们可以将其应用于 `let` 语句的右侧，如下代码示例 3-2 所示：

文件名：src/main.rs

```rs
fn main() {
    let condition = true;
    let number = if condition {
        5
    } else {
        6
    };

    println!("The value of number is: {}", number);
}
```

代码示例 3-2：将 `if` 表达式的结果赋值给变量

`if` 表达式返回的结果将会和 `number` 变量绑定。运行程序可以看到：

```sh
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/branches`
The value of number is: 5
```

记住，代码块返回的值是其中最后一个表达式的值，而数字本身就是表达式。这个例子中，整个 `if` 表达式的值取决于哪个代码块被执行。意味着 `if` 每个分支上可能的返回值一定是同一种类型；示例代码 3-2 中，`if` 分支和 `else` 分支的结果都是 `i32` 整型。而如果像下面这个例子，分支的类型不匹配，代码就会报错：

文件名：src/main.rs

这段代码无法编译！
```rs
fn main() {
    let condition = true;

    let number = if condition {
        5
    } else {
        "six"
    };

    println!("The value of number is: {}", number);
}
```

如果我们试图编译这段代码，编译器就会报错。`if` 和 `else` 分支最终值的类型不同，同时 Rust 会准确指出在程序中何处可以找到问题：

```sh
error[E0308]: if and else have incompatible types
 --> src/main.rs:4:18
  |
4 |       let number = if condition {
  |  __________________^
5 | |         5
6 | |     } else {
7 | |         "six"
8 | |     };
  | |_____^ expected integer, found &str
  |
  = note: expected type `{integer}`
             found type `&str`
```

`if` 代码块中的表达式返回了整数，而 `else` 代码块中表达式则返回了字符串。这行不通，因为变量的类型必须是唯一的。编译时 Rust 需要确切的知道 `number` 变量的类型，这样才能保证编译时每一处使用 `number` 的地方，它的类型都是有效的。而如果 `number` 的类型只能在运行时决定，Rust 就无法做到这一点；因为这样 Rust 就不得不追踪变量多种可能的类型，于是编译过程会更加复杂，并且难以为代码提供保障。

## 循环重复

我们经常需要重复执行某一段代码。为此，Rust 提供了多种循环方式。循环会将循环体中的代码从头到尾执行一遍然后立即从头再来。为了对循环进行试验，我们需要新建一个名为 loops 的项目。

Rust 有三种类型的循环：`loop`、`while` 和 `for`。下面我们逐个测试讲解。

### 使用 `loop` 重复代码

`loop` 关键字会让 Rust 不断的重复执行某段代码，直到开发者明确告诉它停止。

我们一起来看一个例子，将 loops 目录下的 src/main.rs 文件改为如下所示：

文件名：src/main.rs

```rs
fn main() {
    loop {
        println!("again!");
    }
}
```

运行这段程序，我们将会看到 `again!` 被重复打印出来，直到程序被手动停止。大部分终端都支持快捷键 ctrl-c 来终止陷入无限循环的程序。一起试一下：

```sh
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished dev [unoptimized + debuginfo] target(s) in 0.29 secs
     Running `target/debug/loops`
again!
again!
again!
again!
^Cagain!
```

`^C` 符号表明我们在这里按下了组合键 ctrl-c。`^C` 后面的这个 `again!` 可能有也可能没有，取决于收到终止信号时代码执行到了循环的何处。

幸运的是，Rust 提供了另一种更可靠的打断循环的方式。我们可以在循环中放置 `break` 关键字来告知程序何时停止循环。还记得吗，在第二章的“猜对后退出”章节中，就是使用它在用户猜到正确数字赢得游戏后退出程序的。

### 循环返回值

`loop` 的一种应用是重复尝试可能失败的操作，例如检查某线程是否完成了它的任务。我们有时可能需要将循环执行的结果传递给其他代码。此时我们需要在用来终止循环的 `break` 表达式后加上想要返回的值；该值将从循环中返回，我们就可以使用它了，如下所示：

```rs
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {}", result);
}
```

在循环之前，我们声明了一个名为 `counter` 的变量并将其初始化为 `0`。然后我们又声明了一个名为 `result` 的变量来接收循环返回的值。在每次循环迭代中，我们为 `counter` 变量加 `1`，然后检查它是否等于 `10`。而当条件达成，我们就使用 `break` 返回值 `counter * 2`。循环结束后，使用分号结束 `result` 赋值语句。最后，打印出 `result` 的值，也就是 20。

### `while` 条件循环

程序经常需要在循环中进行条件判断。条件为真则循环继续。反之，程序则调用 `break` 终止循环。这种类型的循环可以将 `loop`、`if`、`else` 和 `break` 结合而完成；如果你愿意，现在就可以在程序中试试看。

然而，这种模式非常常见，所以 Rust 为其提供了内建的语言结构，名为 `while` 循环。如下代码示例 3-3 就使用了 `while`：程序会循环三次，每次循环变量都会递减，循环结束后打印出信息然后退出。

文件名：src/main.rs

```rs
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}!", number);

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```
代码示例 3-3：使用 `while` 循环，当条件为真时执行代码

`while` 结构消除了使用 `loop`、`if`、`else` 和 `break` 时所必需的嵌套，代码也更清晰。当条件为真，代码继续运行；否则就会退出循环。

### 使用 `for` 循环遍历集合

可以使用 `while` 结构语句循环遍历集合（例如数组）的每个元素。我们来看代码示例 3-4：

文件名：src/main.rs

```rs
fn main() {
    let a = [10, 20, 30, 40, 50];
    let mut index = 0;

    while index < 5 {
        println!("the value is: {}", a[index]);

        index += 1;
    }
}
```
代码示例 3-4：使用 `while` 循环遍历集合的每个元素

代码由小到大依次为数组中的元素计数。从索引 0 开始，循环直到数组的最大索引（也就是当 `index < 5` 结果为假时）。运行代码将会打印出数组中所有元素：

```sh
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished dev [unoptimized + debuginfo] target(s) in 0.32 secs
     Running `target/debug/loops`
the value is: 10
the value is: 20
the value is: 30
the value is: 40
the value is: 50
```

数组中所有值都如期地被打印在终端。尽管 `index` 最终的值会到达 `5`，但循环会在尝试获取数组第六个元素前终止执行。

但是这种方式还是容易导致错误；如果索引长度错误，就可能导致程序 panic。同时程序的运行速度也慢，因为在每次循环迭代中，编译器都增加了运行时代码来检验元素对应的条件是否为真。

我们可以使用 `for` 循环作来对集合中每个元素执行某些代码，这是一种更简洁的替代方案。`for` 循环如代码示例 3-5 所示：

文件名：src/main.rs

```rs
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a.iter() {
        println!("the value is: {}", element);
    }
}
```
代码示例 3-5：使用 `for` 循环遍历集合中每个元素

运行这段代码，我们将看到和代码示例 3-4 一样的输出。更重要的是，这种方式提高了代码的安全性，消除了因为索引长度过大、或遍历长度不够因此没能访问到某些元素而导致问题的可能。

我们来举个例子，在代码示例 3-4 中，如果我们移除 `a` 数组中的一个元素，但是忘记将条件更新为 `while index < 4`，那么代码就会导致 panic。而如果使用 `for` 循环，修改数组元素个数后，就无需惦记着还要修改其他任何代码了。

`for` 循环的安全性和简洁性让它成为了 Rust 中最常用的循环结构。甚至是在那些想要运行某段代码特定次数的场景下，例如代码示例 3-3 中使用 `while` 循环的倒计时的例子，大部分开发者还是会选择使用 `for` 循环。实现的方法是使用 `Range`，它是标准库提供的类型，可以生成两个数字范围内所有数字的序列。

如下是我们使用 `for` 循环和 `rev` 方法（我们还未曾介绍过，它可以用来反转）实现倒计时的代码：

文件名：src/main.rs

```rs
fn main() {
    for number in (1..4).rev() {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}
```

这段代码更棒了，不是吗？

## 本章小结

你已经成功完成了本章的学习！这一章内容丰富：我们学习了变量、标量、复合数据类型、函数、注释、`if` 表达式还有循环！如果你想练习本章学习的概念，尝试构建程序完成如下任务：

* 转换摄氏与华氏温度。
* 生成 n 阶斐波那契数列。
* 打印圣诞颂歌 “The Twelve Days of Christmas” 的歌词，歌曲中有循环部分，好好利用。

如果你准备好继续学习了，那么下一章我们将会讨论一个在其他编程语言中通常不存在的概念：所有权（ownership）。
