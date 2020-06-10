# Defining and Instantiating Structs
# 结构体的定义和实例化

Structs are similar to tuples, which were discussed in Chapter 3. Like tuples, the pieces of a struct can be different types. Unlike with tuples, you’ll name each piece of data so it’s clear what the values mean. As a result of these names, structs are more flexible than tuples: you don’t have to rely on the order of the data to specify or access the values of an instance.
结构体和我们第三章讨论过的元组类似。和元组类似，结构体可以包含不同类型的数据。但不同的是，结构体中每个数据必须命名，以便指明其含义。因此就让结构体比元组更加灵活：我们不需要依靠数据的顺序（或者说索引）来指定或访问结构体实例中的值。

To define a struct, we enter the keyword `struct` and name the entire struct. A struct’s name should describe the significance of the pieces of data being grouped together. Then, inside curly brackets, we define the names and types of the pieces of data, which we call fields. For example, Listing 5-1 shows a struct that stores information about a user account.
我们可使用关键字 `struct` 来定义并命名结构体。结构体的名字应当能描述这一组数据的含义。然后在打括号中，定义每个数据的名字和类型，我们称其为字段（field）。例如在示例 5-1 中，我们使用结构体存储用户账号的信息。

```rs
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
```

Listing 5-1: A `User` struct definition
代码示例 5-1：定义 `User` 结构体

To use a struct after we’ve defined it, we create an instance of that struct by specifying concrete values for each of the fields. We create an instance by stating the name of the struct and then add curly brackets containing `key: value` pairs, where the keys are the names of the fields and the values are the data we want to store in those fields. We don’t have to specify the fields in the same order in which we declared them in the struct. In other words, the struct definition is like a general template for the type, and instances fill in that template with particular data to create values of the type. For example, we can declare a particular user as shown in Listing 5-2.
通过为每个字段指定具体的值，我们可以创建已定义的结构体实例。声明实例时，首先需要写明结构体名，然后在后面加一个包含 `key: value` 键值对的大括号，这里的键是字段名，值则是希望保存在字段中的数据。结构体实例中字段的顺序不需要和声明时的顺序一致。也就是说，结构体定义就像一个类型通用模版，而实例则使用特殊的数据填写模版来创建这个类型的值。例如，我们在代码实例 5-2 中声明了一个特定的用户。

```rs
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

Listing 5-2: Creating an instance of the `User` struct
代码示例 5-2：创建 `User` 结构体实例

To get a specific value from a struct, we can use dot notation. If we wanted just this user’s email address, we could use `user1.email` wherever we wanted to use this value. If the instance is mutable, we can change a value by using the dot notation and assigning into a particular field. Listing 5-3 shows how to change the value in the `email` field of a mutable `User` instance.
我们可以使用点号获取结构体中一个特殊的值。例如，我们如果想要获取刚才这个用户的邮箱地址，在需要的地方使用 `user1.email` 即可。如果实例是可变的，我们也可以使用点号为某一特殊字段赋值。代码示例 5-3 展示了如何修改可变 `User` 实例 `email` 字段的值。

```rs
let mut user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};

user1.email = String::from("anotheremail@example.com");
```

Listing 5-3: Changing the value in the `email` field of a `User` instance
代码示例 5-3：修改 `User` 实例中 `email` 字段的值

Note that the entire instance must be mutable; Rust doesn’t allow us to mark only certain fields as mutable. As with any expression, we can construct a new instance of the struct as the last expression in the function body to implicitly return that new instance.
注意，必须整个实例可变；Rust 不允许仅为某些字段标注为可变。和其他表达式一样，我们可以将创建新的结构体实例作为函数的最后一个表达式，来隐式的返回这个实例。

Listing 5-4 shows a `build_user` function that returns a `User` instance with the given email and username. The `active` field gets the value of `true`, and the `sign_in_count` gets a value of `1`.
代码示例 5-4 中的 `build_user` 函数返回了一个可以指定其邮箱和用户名的 `User` 实例。`active` 字段的值为 `true`，而 `sign_in_count` 字段的值为 `1`。

```rs
fn build_user(email: String, username: String) -> User {
    User {
        email: email,
        username: username,
        active: true,
        sign_in_count: 1,
    }
}
```

Listing 5-4: A `build_user` function that takes an email and username and returns a `User` instance
代码示例 5-4：`build_user` 函数接收邮箱和用户名参数，并返回 `User` 实例

It makes sense to name the function parameters with the same name as the struct fields, but having to repeat the `email` and `username` field names and variables is a bit tedious. If the struct had more fields, repeating each name would get even more annoying. Luckily, there’s a convenient shorthand!
我们将函数参数名和结构体字段名设定为相同的，这能说得通，但不得不重复写作为字段名和变量名的 `email` 和 `username` 就显得有些冗余。如果结构体字段比较多，重复写每个名字就更烦人了。好消息是，有简写的方式！

## Using the Field Init Shorthand when Variables and Fields Have the Same Name
## 当变量和字段重名，可使用字段初始化简写

Because the parameter names and the struct field names are exactly the same in Listing 5-4, we can use the field init shorthand syntax to rewrite `build_user` so that it behaves exactly the same but doesn’t have the repetition of `email` and `username`, as shown in Listing 5-5.
在代码示例 5-4 中，由于参数名和结构体字段同名，我们可以使用字段初始化简写语法来重写 `build_user` 函数，改写后函数行为不变但是却无需重复写 `email` 和 `username`，如下所示：

```rs
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

Listing 5-5: A `build_user` function that uses field init shorthand because the `email` and `username` parameters have the same name as struct fields
代码示例 5-5：使用字段初始化简写语法的 `build_user` 函数，能这样写是由于 `email` 和 `username` 参数和结构体字段同名

Here, we’re creating a new instance of the `User` struct, which has a field named `email`. We want to set the `email` field’s value to the value in the `email` parameter of the `build_user` function. Because the `email` field and the `email` parameter have the same name, we only need to write `email` rather than `email: email`.
这里我们创建了一个新的 `User` 结构体实例，它有一个字段的名字是 `email`。我们希望将 `email` 字段的值设置为 `build_user` 函数的 `email` 参数值。由于 `email` 字段和 `email` 参数同名，我们可以省略 `email: email`，直接写 `email`。

## Creating Instances From Other Instances With Struct Update Syntax
## 使用结构体更新语法从实例创建实例

It’s often useful to create a new instance of a struct that uses most of an old instance’s values but changes some. You’ll do this using struct update syntax.
使用旧实例中的大部分值同时替换掉某些值，以这种方式创建新的实例非常实用。我们可以实用结构体更新语法来完成。

First, Listing 5-6 shows how we create a new `User` instance in `user2` without the update syntax. We set new values for `email` and `username` but otherwise use the same values from `user1` that we created in Listing 5-2.
首先，代码示例 5-6 展示了不使用更新语法时如何创建新的 `User` 示例 `user2`。我们将 `email` 和 `username` 设定为了新的值，但是其他值则和代码示例 5-2 中创建的 `user1` 实例的值一样。

```rs
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    active: user1.active,
    sign_in_count: user1.sign_in_count,
};
```

Listing 5-6: Creating a new `User` instance using some of the values from `user1`
代码示例 5-6：创建和 `user1` 部分值相同的新的 `User` 实例

Using struct update syntax, we can achieve the same effect with less code, as shown in Listing 5-7. The syntax `..` specifies that the remaining fields not explicitly set should have the same value as the fields in the given instance.
而如果使用了结构体更新语法，我们就可以使用更简洁的代码实现相同的效果，如代码示例 5-7 所示。`..` 语法表示，剩余那些没有明确赋值的字段将使用和给定实例中相同的值。

```rs
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};
```

Listing 5-7: Using struct update syntax to set new `email` and `username` values for a `User` instance but use the rest of the values from the fields of the instance in the `user1` variable
代码示例 5-7：使用结构体更新语法为 `User` 实例的 `email` 和 `username` 设定了新的值，而其他字段的值则沿用了 `user1` 变量。

The code in Listing 5-7 also creates an instance in `user2` that has a different value for `email` and `username` but has the same values for the `active` and `sign_in_count` fields from `user1`.
示例 5-7 中的代码也创建了一个新的 `user2` 实例，它的 `email` 和 `username` 字段与 `user1` 不同，而 `active` 和 `sign_in_count` 的值则是相同的。

## Using Tuple Structs without Named Fields to Create Different Types
## 使用没有命名字段的元组结构体来创建不同的类型

You can also define structs that look similar to tuples, called tuple structs. Tuple structs have the added meaning the struct name provides but don’t have names associated with their fields; rather, they just have the types of the fields. Tuple structs are useful when you want to give the whole tuple a name and make the tuple be a different type from other tuples, and naming each field as in a regular struct would be verbose or redundant.
我们还可以定义和元组类似的结构体，其称为元组结构体。元组结构体能够提供结构体名称，但是无需为字段命名，只需提供字段类型即可。当你想要为整个元组命名以区别于其他元组类型，但是为每个字段命名又显得麻烦和多余的时候，元组结构体就是最佳选择了。

To define a tuple struct, start with the `struct` keyword and the struct name followed by the types in the tuple. For example, here are definitions and usages of two tuple structs named `Color` and `Point`:
使用关键字 `struct` 开头，后面跟上元组的类型定义，就可以定义一个元组结构体了。如下所示即是元组结构体 `Color` 和 `Point` 的定义和使用：

```rs
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
```

Note that the `black` and `origin` values are different types, because they’re instances of different tuple structs. Each struct you define is its own type, even though the fields within the struct have the same types. For example, a function that takes a parameter of type `Color` cannot take a `Point` as an argument, even though both types are made up of three `i32` values. Otherwise, tuple struct instances behave like tuples: you can destructure them into their individual pieces, you can use a `.` followed by the index to access an individual value, and so on.

## Unit-Like Structs Without Any Fields

You can also define structs that don’t have any fields! These are called unit-like structs because they behave similarly to `()`, the unit type. Unit-like structs can be useful in situations in which you need to implement a trait on some type but don’t have any data that you want to store in the type itself. We’ll discuss traits in Chapter 10.

## Ownership of Struct Data

In the `User` struct definition in Listing 5-1, we used the owned `String` type rather than the `&str` string slice type. This is a deliberate choice because we want instances of this struct to own all of its data and for that data to be valid for as long as the entire struct is valid.

It’s possible for structs to store references to data owned by something else, but to do so requires the use of lifetimes, a Rust feature that we’ll discuss in Chapter 10. Lifetimes ensure that the data referenced by a struct is valid for as long as the struct is. Let’s say you try to store a reference in a struct without specifying lifetimes, like this, which won’t work:

Filename: src/main.rs

This code does not compile!

```rs
struct User {
    username: &str,
    email: &str,
    sign_in_count: u64,
    active: bool,
}

fn main() {
    let user1 = User {
        email: "someone@example.com",
        username: "someusername123",
        active: true,
        sign_in_count: 1,
    };
}
```

The compiler will complain that it needs lifetime specifiers:

```sh
$ cargo run
   Compiling structs v0.1.0 (file:///projects/structs)
error[E0106]: missing lifetime specifier
 --> src/main.rs:2:15
  |
2 |     username: &str,
  |               ^ expected lifetime parameter

error[E0106]: missing lifetime specifier
 --> src/main.rs:3:12
  |
3 |     email: &str,
  |            ^ expected lifetime parameter

error: aborting due to 2 previous errors

For more information about this error, try `rustc --explain E0106`.
error: could not compile `structs`.

To learn more, run the command again with --verbose.
```

In Chapter 10, we’ll discuss how to fix these errors so you can store references in structs, but for now, we’ll fix errors like these using owned types like `String` instead of references like `&str`.
