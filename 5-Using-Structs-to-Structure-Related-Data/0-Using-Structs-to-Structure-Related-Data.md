# Using Structs to Structure Related Data
# 使用结构体（struct）组织相关联的数据

A struct, or structure, is a custom data type that lets you name and package together multiple related values that make up a meaningful group. If you’re familiar with an object-oriented language, a struct is like an object’s data attributes. In this chapter, we’ll compare and contrast tuples with structs, demonstrate how to use structs, and discuss how to define methods and associated functions to specify behavior associated with a struct’s data. Structs and enums (discussed in Chapter 6) are the building blocks for creating new types in your program’s domain to take full advantage of Rust’s compile time type checking.
struct，或称为 structure，它是一种自定义数据类型，开发者可以使用它将多个关联的数据命名并打打包在一起，形成一个有意义的组合。如果你熟悉面向对象的语言，可以认为结构体很像对象的数据属性。在这一章，我们将会对比元组和结构体的异同，说明如何使用结构体，讨论如何定义方法和关联函数以指定与结构数据关联的行为。在程序中使用结构体和枚举（我们将会在第六章讨论它）这样的构建模块来创建新的类型，能够充分发挥出 Rust 编译时类型检查的优势。
