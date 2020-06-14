# 结构体的定义和实例化

结构体和我们第三章讨论过的元组类似。和元组类似，结构体可以包含不同类型的数据。但不同的是，结构体中每个数据必须命名，以便指明其含义。因此就让结构体比元组更加灵活：我们不需要依靠数据的顺序（或者说索引）来指定或访问结构体实例中的值。

我们可使用关键字 `struct` 来定义并命名结构体。结构体的名字应当能描述这一组数据的含义。然后在打括号中，定义每个数据的名字和类型，我们称其为字段（field）。例如在示例 5-1 中，我们使用结构体存储用户账号的信息。

```rs
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
```

代码示例 5-1：定义 `User` 结构体

通过为每个字段指定具体的值，我们可以创建已定义的结构体实例。声明实例时，首先需要写明结构体名，然后在后面加一个包含 `key: value` 键值对的大括号，这里的键是字段名，值则是希望保存在字段中的数据。结构体实例中字段的顺序不需要和声明时的顺序一致。也就是说，结构体定义就像一个类型通用模版，而实例则使用特殊的数据填写模版来创建这个类型的值。例如，我们在代码实例 5-2 中声明了一个特定的用户。

```rs
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

代码示例 5-2：创建 `User` 结构体实例

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

代码示例 5-3：修改 `User` 实例中 `email` 字段的值

注意，必须整个实例可变；Rust 不允许仅为某些字段标注为可变。和其他表达式一样，我们可以将创建新的结构体实例作为函数的最后一个表达式，来隐式的返回这个实例。

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

代码示例 5-4：`build_user` 函数接收邮箱和用户名参数，并返回 `User` 实例

我们将函数参数名和结构体字段名设定为相同的，这能说得通，但不得不重复写作为字段名和变量名的 `email` 和 `username` 就显得有些冗余。如果结构体字段比较多，重复写每个名字就更烦人了。好消息是，有简写的方式！

## 当变量和字段重名，可使用字段初始化简写

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

代码示例 5-5：使用字段初始化简写语法的 `build_user` 函数，能这样写是由于 `email` 和 `username` 参数和结构体字段同名

这里我们创建了一个新的 `User` 结构体实例，它有一个字段的名字是 `email`。我们希望将 `email` 字段的值设置为 `build_user` 函数的 `email` 参数值。由于 `email` 字段和 `email` 参数同名，我们可以省略 `email: email`，直接写 `email`。

## 使用结构体更新语法从实例创建实例

使用旧实例中的大部分值同时替换掉某些值，以这种方式创建新的实例非常实用。我们可以实用结构体更新语法来完成。

首先，代码示例 5-6 展示了不使用更新语法时如何创建新的 `User` 示例 `user2`。我们将 `email` 和 `username` 设定为了新的值，但是其他值则和代码示例 5-2 中创建的 `user1` 实例的值一样。

```rs
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    active: user1.active,
    sign_in_count: user1.sign_in_count,
};
```

代码示例 5-6：创建和 `user1` 部分值相同的新的 `User` 实例

而如果使用了结构体更新语法，我们就可以使用更简洁的代码实现相同的效果，如代码示例 5-7 所示。`..` 语法表示，剩余那些没有明确赋值的字段将使用和给定实例中相同的值。

```rs
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};
```

代码示例 5-7：使用结构体更新语法为 `User` 实例的 `email` 和 `username` 设定了新的值，而其他字段的值则沿用了 `user1` 变量。

示例 5-7 中的代码也创建了一个新的 `user2` 实例，它的 `email` 和 `username` 字段与 `user1` 不同，而 `active` 和 `sign_in_count` 的值则是相同的。

## 使用没有命名字段的元组结构体来创建不同的类型

我们还可以定义和元组类似的结构体，其称为元组结构体。元组结构体能够提供结构体名称，但是无需为字段命名，只需提供字段类型即可。当你想要为整个元组命名以区别于其他元组类型，但是为每个字段命名又显得麻烦和多余的时候，元组结构体就是最佳选择了。

使用关键字 `struct` 开头，后面跟上元组的类型定义，就可以定义一个元组结构体了。如下所示即是元组结构体 `Color` 和 `Point` 的定义和使用：

```rs
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
```

注意，`black` 和 `origin` 值是不同的类型，它们分别是不同的元组结构体的实例。你所定义的的每个结构体都是独立的类型，即使它们所包含的字段的类型都相同。例如，以 `Color` 作为参数的函数不可以接收 `Point` 类型的参数，尽管它们都是由三个 `i32` 类型的值组成的。而在其他方面，元组结构体实例和元组类似：可以将其解构为单独的部分，也可以使用 `.` 加索引来访问单独的值等等。

## 没有任何字段的类单元结构体

我们还可以定义没有任何字段的结构体！它们和单元类型 `()` 很类似，因此被称为类单元结构体。当你想要在某个类型上实现 trait，但却没有任何数据要保存在这个类型本身的时候，类单元结构体就很实用了。我们将会在第十章讨论 trait。

## 结构体数据的所有权

在代码示例 5-1 中定义的 `User` 结构体中，我们使用了自身拥有所有权的 `String` 类型而非 `&str` slice 类型。这是一个有意而为的选择，因为我们希望结构体实例能拥有其数据的所有权，因此在结构体有效的时候，其数据也必定有效。

使用结构体保存其他变量拥有的数据引用也是可以的，但是为了这样做，就必须使用生命周期，我们将在第十章讨论这个 Rust 特性。生命周期能够保证结构体引用的数据在结构体有效时保持有效。我们先来像如下这样尝试一次，在结构体中保存引用并且不指明生命周期，这段代码是无法正常工作的：

文件名：src/main.rs

这段代码无法编译！

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

编译器将会告知你，变量需要生命周期标识符：

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

我们将会在第十章讨论如何解决这类错误，到那时我们就可以在结构体中存储引用了，但是现在，我们只需要使用具有所有权的类型 `String` 来代替引用 `&str` 即可修正错误。
