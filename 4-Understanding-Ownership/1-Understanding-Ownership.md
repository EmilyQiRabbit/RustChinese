# 认识所有权

所有权（ownership）是 Rust 最为独特的特性，它能让 Rust 在无需垃圾收集的同时保证内存安全。因此对开发者而言，学习 Rust 所有权的运作原理非常重要。我们将在本章讨论所有权及几个相关的特性：借用、slice 以及 Rust 如何在内存中布局数据。