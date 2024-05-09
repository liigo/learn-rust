初步掌握RUST基础应用开发
庄晓立，2024年3月。

----

# Rust简介

> A language empowering everyone to build reliable and efficient software.

Rust编程语言赋能所有人开发高可靠且高性能的软件。

## 性能

 > Rust is blazingly fast and memory-efficient: with no runtime or garbage collector, it can power performance-critical services, run on embedded devices, and easily integrate with other languages.

Rust能够编译出高性能运行且低内存占用的软件，没有运行时或垃圾收集器，适用于高性能服务、嵌入式设备，方便与其他语言集成。

## 可靠

> Rust’s rich type system and ownership model guarantee memory-safety and thread-safety — enabling you to eliminate many classes of bugs at compile-time.

Rust拥有丰富的类型系统、所有者模型，保证内存安全和线程安全，让你在编译时消除许多类型的BUG。

## 开发效率

> Rust has great documentation, a friendly compiler with useful error messages, and top-notch tooling — an integrated package manager and build tool, smart multi-editor support with auto-completion and type inspections, an auto-formatter, and more.

Rust有完善的文档，有友好的编译器输出实用的错误信息，有一流的工具：集成包管理器和编译工具，有多种编辑器且支持自动完成和类型审视，有代码格式化工具，等等。

# 安装Rust

推荐使用rustup安装Rust。

首先访问网站 https://rustup.rs/ ，下载 rustup-init.exe，然后按照提示逐步操作，即可完成安装Rust。

注意：在Windows操作系统下Rust需要依赖MSVC Build Tools（另有其他版本依赖gcc或clang）。

MSVC Build Tools可以在MS Visual Studio安装包内选装，确保选中模块“使用C++的桌面开发”（英文版“Desktop development With C++”）即可。

建议先安装MSVC Build Tools，然后再运行 rustup-init.exe 安装Rust，以便Rust自动找到MSVC链接器link.exe。

# 配置Rust开发和调试环境

针对VS Code编辑器，推荐使用两个插件：rust-analyzer，CodeLLDB。前者用于语法高亮和代码提示，后者用于调试。

安装方法：在VS Code的extensions界面搜索插件名称，点击install按钮即可。

# Rust基础开发知识

## Hello world

源文件hi.rs:
```rust
fn main() {
    println!("Hello world!");
}
```

编译：`rustc hi.rs`

运行：`./hi`

## Playground

The Rust Playground, https://play.rust-lang.org/ , 用于在线编译和运行测试代码。


## 输出

宏 [`println!`](https://doc.rust-lang.org/nightly/std/macro.println.html) 用于向stdout输出文本并换行。其首参数为格式文本(format string)，其中`{}`和`{var}`作为占位符。
```rust
let name = "liigo";
println!("hello {name}"); // 输出：hello liigo
let a = 123;
let b = "str";
println!("a={a} b={b}"); // 输出：a=123 b=str
println!("a={} b={}", a, b); // 同上
```

## 注释

```rust
// 单行注释
代码 // 行尾注释

/*
  多行注释
  多行注释
*/
代码 /* 行中或跨行注释 */ 代码

/// 外置文档注释（用于生成API文档），首行用一句话简要介绍。
/// 
/// 空一行后，详细说明其功能、参数、返回值、示例代码、Unsafe、Panic等。
/// 注释位于被注释的项目上方

//! 内置文档注释（用于生成API文档），首行用一句话简要介绍。
//!
//! 空一行后，是详细说明。
//！这类文档注释主要用在模块源文件首部，把文档和代码放在一个文件内，方便维护。
```

下述两处位置的文档注释是等效的（区别仅位置不同，outer-doc-comments VS inner-doc-comments）

```rust
/// 外置
mod x {
    //! 内置
}
```

需要内置文档注释的场景，通常是文件模块顶部文档和根模块顶部文档，参见下文。

## 程序结构

Rust程序源码由一个或多个源文件(.rs)组成，但只有一个根源文件（通常命名`main.rs`或`lib.rs`）。

编译时只需给编译器(rustc)传入根源文件（无需传入其他源文件），编译器根据源码内部模块结构自动识别并载入其他源文件。

假设 main.rs 内有代码`mod web;`，当你使用命令行`rustc main.rs`编译时，编译器会尝试在如下位置寻找web模块的源文件：
- `#[path = "..."]` 指定的目录和/或源文件（如果有的话）
- web.rs
- web/mod.rs

`main.rs`或`lib.rs`默认定义了根模块，其内部的`mod web;`定义了子模块web。子模块也可以有子模块，共同组成模块树，此模块树上每一个模块（包括根模块）内部都可以定义类型、函数等Items。

模块树定义了程序内部抽象结构，源文件树也能较好的体现模块结构。二者还可以灵活结合，实现定制的目录结构（主要依靠`#[path = "..."]`）和模块结构（主要依靠`pub use`）。

## 定义模块

方式1：内联模块
```rust

/// 文档
mod mod1 {
    // 代码
}

```

方式2：文件模块

源文件main.rs:
```rust
mod mod2;
```

源文件mod2.rs:
```rust
//! 文档

// 代码
```

以上定义的mod1和mod2均为当前模块的子模块。

## 数据类型

基础数据类型：

- 逻辑/字符/文本：bool, char, str
- 有符号整数：i8, i16, i32, i64, i128
- 无符号整数：u8, u16, u32, u64, u128
- 浮点数：f32, f64
- Never: !

其他数据类型：

- 序列相关：Tuple, Array, Slice
- 自定义相关：Struct, Enum, Union
- 函数相关：Functions, Closures
- 指针相关：References, Raw pointers, Function pointers
- 接口相关：Trait objects, Impl trait

参考：https://doc.rust-lang.org/stable/reference/types.html

## 定义变量

```rust
let a = 123;
let b = 123i8;
let c: i32 = 123;
let d = Some(123); // d: Option<i32>
let Some(x) = Some(123); // x: i32
let mut y = 0; // mut变量
y = 123; // 仅mut变量可以被赋值或被改变
```

## 定义类型

```rust
struct Counter {
    n: i32,
}

impl Counter {
    pub fn new() -> Counter {
        Counter {
            n: 0,
        }
    }

    pub fn get(&self) -> i32 {
        self.n
    }

    pub fn add(&mut self, x: i32) {
        self.n += x;
    }
}

#[test]
fn test1() {
    let mut counter = Counter::new();
    assert_eq!(counter.get(), 0);
    counter.add(1);
    assert_eq!(counter.get(), 1);
    counter.add(-2);
    assert_eq!(counter.get(), -1);
}
```

## 定义和实现接口

```rust
trait Inc {
    fn inc(&mut self);
}

impl Inc for Counter {
    fn inc(&mut self) {
        self.add(1);
    }
}

impl Clone for Counter {
    fn clone(&self) -> Self {
        Counter {
            n: self.get(),
        }
    }
}

#[test]
fn test2() {
    let mut counter = Counter::new();
    counter.inc();
    counter.inc();
    let counter2 = counter.clone();
    assert_eq!(counter2.get(), 2);
}
```

## 流程控制

```rust
let a = 1;
if a == 1 {
    println!("a == 1");
} else {
    println!("a = {a}");
}

enum Kinds { K1, K2, K3 }
let kind = Kinds::K1;
match kind {
    Kinds::K1 => { println!("K1"); }
    Kinds::K2 => { println!("K2"); }
    Kinds::K3 => { println!("K3"); }
}

let nums = [1, 2, 3, 4, 5, 6, 7];
for n in nums {
    print!("{n} ");
}

while cond {
    // code
}

loop {
    // code
    if cond {
        break;
    }
}
```

## 使用标准库

Rust程序默认静态链接标准库std，里面包含了很多常用模块/类型/接口/函数，可按需使用。

标准库在线文档：https://doc.rust-lang.org/stable/std/index.html

```rust
use std::fs::File;
use std::io::{BufRead, BufReader};

fn main() {
    let file = File::open("./2023-09-15.log").unwrap();
    for line in BufReader::new(file).lines() {
        println!("{}", line.unwrap());
    }
}
```

## 使用Cargo和第三方库

使用Cargo创建项目：`cargo init my_project` （生成项目目录，含`Cargo.toml` `src\main.rs`等）

使用Cargo编译项目：`cargo build` （在项目目录内执行，下同）

使用Cargo运行项目：`cargo run`

添加第三方库依赖项：`cargo add chrono` （参见 [chrono](https://crates.io/crates/chrono) [docs](https://docs.rs/chrono)）

src\main.rs:
```rust
use chrono::Local;

fn main() {
    println!("Hello, world! {}", Local::now());
}
```

Cargo.toml:
```toml
[package]
name = "my_project"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
chrono = "0.4.35"
```

其中最后一行是执行`cargo add chrono`时自动写入的。

## 测试

用`#[cfg(test)] mod tests`定义测试模块，用`#[test] fn`定义测试函数，用`cargo test`启动测试。

```rust
pub fn add(left: usize, right: usize) -> usize {
    left + right
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_works() {
        let result = add(2, 2);
        assert_eq!(result, 4);
    }
}
```

## 坑

```rust
fn do_something() -> std::io::Result<()> {
    std::fs::File::open("./nosuchfile")?; // 因为某个错误提前返回
    println!("the main work");
    Ok(())
}

fn main() {
    // 此处故意无视do_something()返回值，后果很严重：
    // 程序无故中途退出，无错误信息输出，也没法看调用栈，难以定位问题所在。
    // 正确的写法是`do_something().unwrap();`，`do_something().expect("...");`，或`do_something()?;`
    let _r = do_something();

    // 另一种无视返回值的写法是：`do_something().ok();`
    // 现实中一定要杜绝这类错误写法，因为后果真的很严重。

    // 为什么程序中途退出：因为代码控制流程提前返回了（?运算符具有提前返回的语义）
    // 为什么没有错误输出和调用栈：因为没有执行到panic!（unwrap/expect内部执行到panic!才会有相关输出）
}
```
