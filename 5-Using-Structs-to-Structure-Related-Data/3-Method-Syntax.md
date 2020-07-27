# Method Syntax
# 方法语法

Methods are similar to functions: they’re declared with the `fn` keyword and their name, they can have parameters and a return value, and they contain some code that is run when they’re called from somewhere else. However, methods are different from functions in that they’re defined within the context of a struct (or an enum or a trait object, which we cover in Chapters 6 and 17, respectively), and their first parameter is always `self`, which represents the instance of the struct the method is being called on.
方法和函数类似：都使用关键字 `fn` 和名字声明，具有参数和返回值，并且包含某些代码，这些代码会在被调用的时候执行。但是方法和函数也是有区别的，方法在结构体（或枚举类型以及 trait 对象中，我们将会在第六章和第十七章中分别介绍）上下文中定义，它们的第一个参数一定是 `self`，代表调用方法的结构体实例。

## 方法定义

Let’s change the `area` function that has a `Rectangle` instance as a parameter and instead make an `area` method defined on the `Rectangle` struct, as shown in Listing 5-13.
我们来将带有 `Rectangle` 实例参数的 `area` 函数改造为定义在 `Rectangle` 结构体中的 `area` 方法，如代码示例 5-13 所示：

Filename: src/main.rs
文件名：src/main.rs

```rs
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```

Listing 5-13: Defining an `area` method on the `Rectangle` struct
代码示例 5-13：在 `Rectangle` 结构体中定义 `area` 方法

To define the function within the context of `Rectangle`, we start an `impl` (implementation) block. Then we move the `area` function within the `impl` curly brackets and change the first (and in this case, only) parameter to be `self` in the signature and everywhere within the body. In `main`, where we called the `area` function and passed `rect1` as an argument, we can instead use method syntax to call the `area` method on our `Rectangle` instance. The method syntax goes after an instance: we add a dot followed by the method name, parentheses, and any arguments.
我们使用 `impl`（implementation）代码块在 `Rectangle` 上下文中定义函数。然后我们将 `area` 函数移动到 `impl` 打括号中，在本例中，我们还需要将函数签名的第一个参数和函数体对应位置改为 `self`。在 `area` 函数中，调用 `area` 函数需要将 `rect1` 作为参数传入，而现在使用方法语法只需要在 `Rectangle` 实例调用 `area` 方法。方法语法在实例后加上一个点号，然后再加上方法名、一对括号以及方法参数。

In the signature for `area`, we use `&self` instead of `rectangle: &Rectangle` because Rust knows the type of `self` is `Rectangle` due to this method’s being inside the `impl Rectangle` context. Note that we still need to use the `&` before `self`, just as we did in `&Rectangle`. Methods can take ownership of `self`, borrow `self` immutably as we’ve done here, or borrow `self` mutably, just as they can any other parameter.
我们在 `area` 的签名中使用了 `&self` 而不是 `rectangle: &Rectangle`，这是由于该方法位于 `impl Rectangle` 上下中，因此 Rust 知道 `self` 的类型是 `Rectangle`。注意，正如 `&Rectangle`，我们在 `self` 之前也需要使用 `&` 符号。方法可以选择拿走 `self` 所有权，也可以像这段代码，使用不可变引用，或者也可以像其他任何参数都可以的那样，使用可变引用。

We’ve chosen `&self` here for the same reason we used `&Rectangle` in the function version: we don’t want to take ownership, and we just want to read the data in the struct, not write to it. If we wanted to change the instance that we’ve called the method on as part of what the method does, we’d use `&mut self` as the first parameter. Having a method that takes ownership of the instance by using just `self` as the first parameter is rare; this technique is usually used when the method transforms `self` into something else and you want to prevent the caller from using the original instance after the transformation.
在这里我们选择了 `&self`，理由和在函数版本中选择 `&Rectangle` 一样：我们不想拿走所有权，并且只需要读取结构体数据，无需写入。而如果我们想要在实例调用的方法中修改该实例，我们就需要将 `&mut self` 作为第一个参数。仅使用 `self` 作为第一个参数方法很少见，这样的方法会把实例所有权拿走；一般只有当方法需要将 `self` 转换为其他类型，并且开发者想要防止调用者在转换后使用原实例的时候才会使用。

The main benefit of using methods instead of functions, in addition to using method syntax and not having to repeat the type of `self` in every method’s signature, is for organization. We’ve put all the things we can do with an instance of a type in one `impl` block rather than making future users of our code search for capabilities of `Rectangle` in various places in the library we provide.
使用方法代替函数，除了能无需在每个方法签名中重复 `self` 的类型，其最重要优势是能让代码组织结构更好。我们可以将所有处理类型实例的方法都放在一个 `impl` 块中，这样将来使用这段代码的用户就无需在我们提供仓库的不同位置搜索 `Rectangle` 具备的功能。

## Where’s the `->` Operator?
## 操作符 `->` 在哪里？

> In C and C++, two different operators are used for calling methods: you use `.` if you’re calling a method on the object directly and `->` if you’re calling the method on a pointer to the object and need to dereference the pointer first. In other words, if `object` is a pointer, `object->something()` is similar to `(*object).something()`.
> 在 C 和 C++ 中，有两种不同的操作符都可以用来调用方法：使用 `.` 直接在对象上调用方法，或使用 `->` 在对象指针上调用方法，但此时需要先将指针解引用（dereference）。换句话说，如果 `object` 是一个指针，那么 `object->something()` 和 `(*object).something()` 这两种写法是类似的。

> Rust doesn’t have an equivalent to the `->` operator; instead, Rust has a feature called automatic referencing and dereferencing. Calling methods is one of the few places in Rust that has this behavior.
> 而 Rust 没有和 `->` 等价的操作符；但 Rust 有一个名为自动引用和解引用的功能。调用方法就是 Rust 中为数不多的使用了这种功能的地方。

> Here’s how it works: when you call a method with `object.something()`, Rust automatically adds in `&`, `&mut`, or `*` so `object` matches the signature of the method. In other words, the following are the same:
> 它的工作原理是：当我们使用 `object.something()` 调用方法时，Rust 会自动加上 `&`、`&mut` 或者 `*`，这样 `object` 就可以和方法签名匹配。换句话说，下面两种写法是等价的：

```rs
p1.distance(&p2);
(&p1).distance(&p2);
```

> The first one looks much cleaner. This automatic referencing behavior works because methods have a clear receiver—the type of `self`. Given the receiver and name of a method, Rust can figure out definitively whether the method is reading (`&self`), mutating (`&mut self`), or consuming (`self`). The fact that Rust makes borrowing implicit for method receivers is a big part of making ownership ergonomic in practice.
> 第一种写法要简洁得多。这种自动引用的行为能够生效是因为方法有明确的接收者 —— `self` 的类型。只要给定接收者和方法名，Rust 完全可以推断出方法是读取、修改还是获取所有权。事实上，Rust 对方法接收者的隐式借用让所有权的实际使用对开发者更加友好。

## Methods with More Parameters
## 具有更多参数的方法

Let’s practice using methods by implementing a second method on the `Rectangle` struct. This time, we want an instance of `Rectangle` to take another instance of `Rectangle` and return `true` if the second `Rectangle` can fit completely within `self`; otherwise it should return `false`. That is, we want to be able to write the program shown in Listing 5-14, once we’ve defined the `can_hold` method.

Filename: src/main.rs
文件名：src/main.rs

```rs
fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };
    let rect2 = Rectangle {
        width: 10,
        height: 40,
    };
    let rect3 = Rectangle {
        width: 60,
        height: 45,
    };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}
```

Listing 5-14: Using the as-yet-unwritten `can_hold` method

And the expected output would look like the following, because both dimensions of `rect2` are smaller than the dimensions of `rect1` but `rect3` is wider than `rect1`:

```
Can rect1 hold rect2? true
Can rect1 hold rect3? false
```

We know we want to define a method, so it will be within the `impl Rectangle` block. The method name will be `can_hold`, and it will take an immutable borrow of another `Rectangle` as a parameter. We can tell what the type of the parameter will be by looking at the code that calls the method: `rect1.can_hold(&rect2)` passes in `&rect2`, which is an immutable borrow to `rect2`, an instance of `Rectangle`. This makes sense because we only need to read `rect2` (rather than write, which would mean we’d need a mutable borrow), and we want `main` to retain ownership of `rect2` so we can use it again after calling the `can_hold` method. The return value of `can_hold` will be a Boolean, and the implementation will check whether the width and height of `self` are both greater than the width and height of the other `Rectangle`, respectively. Let’s add the new `can_hold` method to the `impl` block from Listing 5-13, shown in Listing 5-15.

Filename: src/main.rs
文件名：src/main.rs

```rs
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```

Listing 5-15: Implementing the `can_hold` method on `Rectangle` that takes another `Rectangle` instance as a parameter

When we run this code with the `main` function in Listing 5-14, we’ll get our desired output. Methods can take multiple parameters that we add to the signature after the `self` parameter, and those parameters work just like parameters in functions.

## Associated Functions

Another useful feature of `impl` blocks is that we’re allowed to define functions within `impl` blocks that don’t take `self` as a parameter. These are called associated functions because they’re associated with the struct. They’re still functions, not methods, because they don’t have an instance of the struct to work with. You’ve already used the `String::from` associated function.

Associated functions are often used for constructors that will return a new instance of the struct. For example, we could provide an associated function that would have one dimension parameter and use that as both width and height, thus making it easier to create a square `Rectangle` rather than having to specify the same value twice:

Filename: src/main.rs
文件名：src/main.rs

```rs
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}
```

To call this associated function, we use the `::` syntax with the struct name; `let sq = Rectangle::square(3);` is an example. This function is namespaced by the struct: the `::` syntax is used for both associated functions and namespaces created by modules. We’ll discuss modules in Chapter 7.

## Multiple `impl` Blocks

Each struct is allowed to have multiple `impl` blocks. For example, Listing 5-15 is equivalent to the code shown in Listing 5-16, which has each method in its own `impl` block.

```rs
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```

Listing 5-16: Rewriting Listing 5-15 using multiple `impl` blocks

There’s no reason to separate these methods into multiple `impl` blocks here, but this is valid syntax. We’ll see a case in which multiple `impl` blocks are useful in Chapter 10, where we discuss generic types and traits.

## Summary
## 总结

Structs let you create custom types that are meaningful for your domain. By using structs, you can keep associated pieces of data connected to each other and name each piece to make your code clear. Methods let you specify the behavior that instances of your structs have, and associated functions let you namespace functionality that is particular to your struct without having an instance available.

But structs aren’t the only way you can create custom types: let’s turn to Rust’s enum feature to add another tool to your toolbox.
