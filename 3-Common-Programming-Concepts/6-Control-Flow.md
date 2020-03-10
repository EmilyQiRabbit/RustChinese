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

### Handling Multiple Conditions with `else if`
### 使用 `else if` 处理多条件逻辑

You can have multiple conditions by combining `if` and `else` in an `else if` expression. For example:

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

This program has four possible paths it can take. After running it, you should see the following output:

```sh
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/branches`
number is divisible by 3
```

When this program executes, it checks each `if` expression in turn and executes the first body for which the condition holds true. Note that even though 6 is divisible by 2, we don’t see the output `number is divisible by 2`, nor do we see the `number is not divisible by 4, 3, or 2` text from the `else` block. That’s because Rust only executes the block for the first true condition, and once it finds one, it doesn’t even check the rest.

Using too many `else if` expressions can clutter your code, so if you have more than one, you might want to refactor your code. Chapter 6 describes a powerful Rust branching construct called `match` for these cases.

### Using `if` in a `let` Statement

Because `if` is an expression, we can use it on the right side of a `let` statement, as in Listing 3-2.

Filename: src/main.rs
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

Listing 3-2: Assigning the result of an if expression to a variable

The `number` variable will be bound to a value based on the outcome of the `if` expression. Run this code to see what happens:

```rs
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/branches`
The value of number is: 5
```

Remember that blocks of code evaluate to the last expression in them, and numbers by themselves are also expressions. In this case, the value of the whole `if` expression depends on which block of code executes. This means the values that have the potential to be results from each arm of the `if` must be the same type; in Listing 3-2, the results of both the `if` arm and the `else` arm were `i32` integers. If the types are mismatched, as in the following example, we’ll get an error:

Filename: src/main.rs
文件名：src/main.rs

This code does not compile!
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

When we try to compile this code, we’ll get an error. The `if` and `else` arms have value types that are incompatible, and Rust indicates exactly where to find the problem in the program:

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

The expression in the `if` block evaluates to an integer, and the expression in the `else` block evaluates to a string. This won’t work because variables must have a single type. Rust needs to know at compile time what type the `number` variable is, definitively, so it can verify at compile time that its type is valid everywhere we use `number`. Rust wouldn’t be able to do that if the type of `number` was only determined at runtime; the compiler would be more complex and would make fewer guarantees about the code if it had to keep track of multiple hypothetical types for any variable.

## Repetition with Loops

It’s often useful to execute a block of code more than once. For this task, Rust provides several loops. A loop runs through the code inside the loop body to the end and then starts immediately back at the beginning. To experiment with loops, let’s make a new project called loops.

Rust has three kinds of loops: `loop`, `while`, and `for`. Let’s try each one.

### Repeating Code with loop

The `loop` keyword tells Rust to execute a block of code over and over again forever or until you explicitly tell it to stop.

As an example, change the src/main.rs file in your loops directory to look like this:

Filename: src/main.rs
文件名：src/main.rs

```rs
fn main() {
    loop {
        println!("again!");
    }
}
```

When we run this program, we’ll see `again`! printed over and over continuously until we stop the program manually. Most terminals support a keyboard shortcut, ctrl-c, to interrupt a program that is stuck in a continual loop. Give it a try:

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

The symbol `^C` represents where you pressed ctrl-c . You may or may not see the word `again!` printed after the `^C`, depending on where the code was in the loop when it received the interrupt signal.

Fortunately, Rust provides another, more reliable way to break out of a loop. You can place the `break` keyword within the loop to tell the program when to stop executing the loop. Recall that we did this in the guessing game in the “Quitting After a Correct Guess” section of Chapter 2 to exit the program when the user won the game by guessing the correct number.

### Returning Values from Loops

One of the uses of a `loop` is to retry an operation you know might fail, such as checking whether a thread has completed its job. However, you might need to pass the result of that operation to the rest of your code. To do this, you can add the value you want returned after the `break` expression you use to stop the loop; that value will be returned out of the loop so you can use it, as shown here:

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

Before the loop, we declare a variable named `counter` and initialize it to `0`. Then we declare a variable named `result` to hold the value returned from the loop. On every iteration of the loop, we add `1` to the `counter` variable, and then check whether the counter is equal to `10`. When it is, we use the `break` keyword with the value `counter * 2`. After the loop, we use a semicolon to end the statement that assigns the value to `result`. Finally, we print the value in `result`, which in this case is 20.

### Conditional Loops with `while`

It’s often useful for a program to evaluate a condition within a loop. While the condition is true, the loop runs. When the condition ceases to be true, the program calls `break`, stopping the loop. This loop type could be implemented using a combination of `loop`, `if`, `else`, and `break`; you could try that now in a program, if you’d like.

However, this pattern is so common that Rust has a built-in language construct for it, called a `while` loop. Listing 3-3 uses `while`: the program loops three times, counting down each time, and then, after the loop, it prints another message and exits.

Filename: src/main.rs
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
Listing 3-3: Using a `while` loop to run code while a condition holds true

This construct eliminates a lot of nesting that would be necessary if you used `loop`, `if`, `else`, and `break`, and it’s clearer. While a condition holds true, the code runs; otherwise, it exits the loop.

### Looping Through a Collection with `for`

You could use the `while` construct to loop over the elements of a collection, such as an array. For example, let’s look at Listing 3-4.

Filename: src/main.rs
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
Listing 3-4: Looping through each element of a collection using a `while` loop

Here, the code counts up through the elements in the array. It starts at index 0, and then loops until it reaches the final index in the array (that is, when index < 5 is no longer true). Running this code will print every element in the array:

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

All five array values appear in the terminal, as expected. Even though `index` will reach a value of `5` at some point, the loop stops executing before trying to fetch a sixth value from the array.

But this approach is error prone; we could cause the program to panic if the index length is incorrect. It’s also slow, because the compiler adds runtime code to perform the conditional check on every element on every iteration through the loop.

As a more concise alternative, you can use a `for` loop and execute some code for each item in a collection. A `for` loop looks like the code in Listing 3-5.

Filename: src/main.rs
文件名：src/main.rs

```rs
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a.iter() {
        println!("the value is: {}", element);
    }
}
```
Listing 3-5: Looping through each element of a collection using a `for` loop

When we run this code, we’ll see the same output as in Listing 3-4. More importantly, we’ve now increased the safety of the code and eliminated the chance of bugs that might result from going beyond the end of the array or not going far enough and missing some items.

For example, in the code in Listing 3-4, if you removed an item from the `a` array but forgot to update the condition to `while index < 4`, the code would panic. Using the `for` loop, you wouldn’t need to remember to change any other code if you changed the number of values in the array.

The safety and conciseness of `for` loops make them the most commonly used loop construct in Rust. Even in situations in which you want to run some code a certain number of times, as in the countdown example that used a `while` loop in Listing 3-3, most Rustaceans would use a `for` loop. The way to do that would be to use a `Range`, which is a type provided by the standard library that generates all numbers in sequence starting from one number and ending before another number.

Here’s what the countdown would look like using a `for` loop and another method we’ve not yet talked about, `rev`, to reverse the range:

Filename: src/main.rs
文件名：src/main.rs

```rs
fn main() {
    for number in (1..4).rev() {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}
```

This code is a bit nicer, isn’t it?

## 本章小结

You made it! That was a sizable chapter: you learned about variables, scalar and compound data types, functions, comments, `if` expressions, and loops! If you want to practice with the concepts discussed in this chapter, try building programs to do the following:

* Convert temperatures between Fahrenheit and Celsius.
* Generate the nth Fibonacci number.
* Print the lyrics to the Christmas carol “The Twelve Days of Christmas,” taking advantage of the repetition in the song.

When you’re ready to move on, we’ll talk about a concept in Rust that doesn’t commonly exist in other programming languages: ownership.
