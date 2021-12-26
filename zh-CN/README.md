# 从 JavaScript 窥探 Rust

从 JavaScript 窥探 Rust

## 引言

人们似乎非常喜欢 Rust！但是如果你来自 JavaScript，一开始并不是所有的东西都有意义。但没问题；本指南适合您！

可能第一个问题是：“什么是 Rust？” 您可能听说过它被描述为：“一种系统编程语言”或“C++ 的现代替代品”。虽然我不认为这些描述是错误的，但我也不认为它们讲述了完整的故事。

我认为 Rust 是一种具有广泛应用的语言。一方面，我们有一种带有包管理器的语言，它可以方便地编写 Web 应用程序、创建数据处理管道和创建用户界面。但另一方面，我们有一种语言可以精确地操纵内存布局、调用内核 API，甚至编写内联汇编 — 在 JavaScript 中，您需要编写[原生 C++ 扩展](https://nodejs.org/docs/latest-v15.x/api/n-api.html).

在本指南中，我们将关注与 JavaScript 最相似的部分：编写类、函数和控制流。我们的想法是，如果我们能让您到达一个您感到舒适的地方，您就可以以此为起点，深入研究 Rust，并逐渐了解更多关于该语言所提供的内容。

## 快速开始

好吧。所以你想写 Rust 吗？第一步是给自己一个工作环境。这意味着安装工具。这是您需要的概述（或多或少按顺序）：

## 安装 Rust

[rustup](https://rustup.rs/): 这就像 Node 中的 `nvm` 一样，但官方支持并且做得非常好！它可以帮助您安装和管理 Rust 编译器版本。

安装 Rustup 还会安装一个有效的编译器工具链，它总共为您提供 3 个新命令

- `$ rustup`: 运行“rustup”来管理你的编译器工具链
- `$ rustc`: 这是 Rust 编译器。您永远不需要调用它，因为：
- `$ cargo`: `cargo` 之于 Rust 相当于 `npm` 之于 Node。它负责构建、编译、安装等等。通常花时间深入了解`cargo` 是值得的！

## 安装 packages

[`cargo-edit`](https://github.com/killercup/cargo-edit) 为`cargo`提供基本的扩展。特别是：它允许您运行 `cargo add` 类似于 `npm install`。在 rust 行话中，“packages”被称为“crates”。

`cargo install` 工作类似于 `npm install -g`。当您运行 `cargo add` 时，只有您的 `Cargo.toml` 文件（Rust 的 `package.json` 文件）会更新为正确的版本。运行 `cargo build` 或 `cargo check` 以实际下载和编译依赖项。

您可以通过运行以下命令安装`cargo-edit`:

```sh
$ cargo install cargo-edit
```

## 格式化代码

[`rustfmt`](https://github.com/rust-lang/rustfmt) 是 Rust 版本的 [`prettier`](https://github.com/prettier/prettier). 每个人都使用它，即使默认配置可能需要一些时间来适应，这也是每个人都在使用的。

它是一个挂钩到编译器的二进制组件，因此需要使用`rustup`安装：

```sh
$ rustup component add rustfmt
```

在快速连接上，这应该需要几秒钟的时间。每当你更新你的 Rust 版本时，`rustfmt` 也会更新。

重要的命令是：

```sh
$ cargo fmt                    # 运行 rustfmt 来格式化你的代码
$ cargo fmt -- --check         # 做一个试运行，输出将要进行的更改的差异
$ cargo fmt -- --edition=2018  # 传递这个标志，如果你正在用 async/await 做事
```

## linting code

[clippy](https://github.com/rust-lang/rust-clippy) 是 Rust 的 “linting” 工具，类似于 [`standard`](https://github.com/standard/standard) 的样式 lints。 `cargo fmt` 负责格式化。`rustc` 照顾正确性。但是 `clippy` 负责帮助您编写 “惯用的” Rust。

这并不意味着 Clippy 中的每一个 lint 都是完美的。但是，当您开始使用时，运行起来会很有帮助！

## 编辑器和 IDE 支持

Rust 有一个 [language-server](https://langserver.org/) 实现，形式为 [`rust-analyzer`](https://github.com/rust-analyzer/rust-analyzer)。 这为几乎所有编辑器提供了 IDE 支持。这为几乎所有编辑器提供了 IDE 支持。如果您不确定从哪个编辑器开始：考虑使用 VSCode！- 它与语言服务器的集成非常好，应该可以轻松开始编写 Rust。

## 测试

Cargo 附带一个 `cargo test` 命令，它将运行 doctests 和 `test/` 下的文件。 The Rust book 有[一整章专门用于测试](https://doc.rust-lang.org/1.30.0/book/second-edition/ch11-00-testing.html)， 您应该阅读此内容。但是为了让您开始，您可以将此样板复制到您 `src/` 目录中的任何文件中，然后 `cargo test` 将其提取：

```rust
#[cfg(test)]
mod test {
    use super::*;
    #[test]
    fn smoke_test() {
        assert_eq!(1, 1);
    }
}
```

这会在您的文件中创建一个模块，该模块仅在运行 `cargo test` 时进行编译。它将所有函数和类型从外部作用域导入到 `mod test` 作用域，然后定义一个 `smoke_test` 执行的单个测试。您可以通过编写更多 `#[test]` 函数来添加更多测试。

## 创建新项目

您可以使用 `cargo new` 或 `cargo init` 创建新项目。`new` 创建一个新目录，`init` 输出当前目录中的文件。这是非常基础的，但它是有用的开始。如果你想写一个库，你可以为任一命令传递 `--lib` 标志。默认情况下，您将创建二进制文件（具有 `main` 可以运行的功能的应用程序）。

还有较新的 [`cargo-generate`](https://github.com/ashleygwilliams/cargo-generate) 项目。这是内置 `cargo` 命令的更强大版本，允许您从模板中进行选择。如果你只是在乱搞，你可能不需要这个，但意识到这一点可能很好。

## 发布

`cargo publish` 类似 `npm publish` 。 中央存储库称为 [crates.io](https://crates.io)，与 NPM 非常相似。重要的是，它不是由一家卑鄙的营利性公司所有，而是 Rust 项目的一部分。

如果您在 Rust 中构建了一些不错的东西，请考虑继续并将其发布到 Crates.io。只需要一个 GitHub 帐户即可注册，您就可以开始了！

## 文档

JS 中的大多数文档似乎要么写在 README.md 中，要么作为某些特殊网站的一部分。在 Rust 中，文档是使用`rustdoc`。

您可以通过 `$cargo doc` 运行 `rustdoc` 。crates.io 上的每个包都为您在 [docs.rs](https://docs.rs) 生成了文档。它甚至是版本化的，因此您也可以查看较旧的文档。例如：您可以在 [docs.rs/async-std](https://docs.rs/async-std) 下找到 `async-std` 的文档。

在 Rust 中编写文档是通过使用“文档注释”（///而不是常规 //注释）。您将在本指南的其余部分看到一堆内容。重要的文档命令是：

```sh
$ cargo doc            # 生成文档
$ cargo doc --open     # 生成文档然后打开它们
$ rustup doc --std     # 离线打开 stdlib 文档
$ rustup doc --book    # 离线打开“Rust Programming Language”
```

## 监视项目

有时在每次更改后运行 `cargo check` 可能会很乏味。这就是为什么 [`cargo-watch`](https://github.com/passcod/cargo-watch) 存在。您可以通过运行以下命令来安装它：

```sh
$ cargo install cargo-watch
```

重要的 `cargo-watch` 命令是：

```sh
$ cargo watch              # 在每次更改时运行“cargo check”
$ cargo watch -x "test"    # 在每次更改时运行“cargo test”
```

## 术语

在我们继续之前，让我们建立一些快速术语：

- **Struct:** 就像 JS 中的“对象”。它既可以是纯数据类型。但也可以像“类”一样使用方法（内部的和静态的）。
- **Vec:** 就像一个 JS “数组”。
- **Slice:** 就像[JS 中的 “视图” `TypedArray`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)。您无法扩大它们，但您可以创建“视图的视图”（切片的切片）。
- **Traits:** 本质上是问题“如果一个类可以从多个其他类继承会怎样？”的答案。Traits 在其他语言中也称为“mixin”。它们不分配任何数据，而只提供与这些方法相关的方法和类型定义。

## Rust 编程思想

除了明显的类型系统之外，我认为 Rust 和 JS 之间还有一些核心区别：

## 面向对象的一切

在 Rust 中，_一切_ 都是面向对象的。导入总是通过命名空间完成，命名空间的行为类似于结构。

```rust
// println 来自“std”命名空间 — 在屏幕上打印一些东西。
std::println!("hello world");

// 从`std::collections` 的`HashMap` 类型调用“new”方法
// 命名空间
let hashmap = std::collections::HashMap::new();
```

## 类与结构

Structs don't have "constructor" methods the way JS do; instead you define a
method that returns `Self` (which is a shorthand for the name of the struct).

结构没有像 JS 那样的 “constructor” 方法；相反，您定义了一个返回 `Self`（这是结构名称的简写）的方法。

````js
/// 如何实例化：
/// ```js
/// let rect = new Rectangle(5, 10);
/// ```
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}
````

````rust
/// 如何实例化：
/// ```rs
/// let rect = Rectangle::new(5, 10);
/// ```
pub struct Rectangle {
    height: usize,
    width: usize,
}

impl Rectangle {
    /// 创建一个新实例。
    pub fn new(height: usize, width: usize) -> Self {
        Self { height, width }
    }
}
````

## 表达式!

一切都是一种表达式。也就是说：块突然有了很多意义，你可以做一些有趣的替换。

这些都是等价的：

```rust
let y = 1 + 1;
let x = y * y;
if x == 4 {
    println!("hello world");
}
```

```rust
// 如果我们在语句中省略了`;`，它就会成为它所在块的返回值
let x = {
    let y = 1 + 1;
    y * y
};
if x == 4 {
    println!("hello world");
}
```

```rust
// 表达式意味着你几乎可以内联任何东西。请不要真的这样做。
if {
    let y = 1 + 1;
    y * y
} == 4 {
    println!("hello world");
}
```

## 了解你的 `self`

有 3 种 self：`self`, `&self`, `&mut self`。

```rust
pub struct Rectangle {
    height: usize,
    width: usize,
}

impl Rectangle {
    pub fn new(height: usize, width: usize) -> Self {
        Self { height, width }
    }

    /// 获取高度
    ///
    /// 我们希望将该值引用为只读，
    /// 所以我们使用 `&self` 进行共享访问。
    pub fn height(&self) -> usize {
        self.height
    }

    /// 设置高度
    ///
    /// 我们希望将值引用为“可写”，因此我们使用
    /// `&mut self` 进行独占访问。
    pub fn set_height(&mut self, height: usize) -> usize {
        self.height = height
    }

    /// 以元组形式获取高度+宽度。
    ///
    /// 我们希望“使用”当前结构，并返回其内部部分。
    /// 因此，我们不采用引用，而是采用`self`自有值
    /// 之后该结构不能再被使用，并返回一个包含其内部结构的元组（匿名结构）。
    pub fn parts(self) -> (usize, usize) {
        (self.height, self.width)
    }
}
```

这是借用检查器周围一切的核心。如果您对某个变量具有独占访问权，那么其他人也无法访问该变量，您可以对其进行 mutate。如果您拥有对变量的共享访问权限，其他人也可以共享，但您不能更新该值。这就是防止数据竞争的方式！

有一些逃生舱使用 `RefCell`，`Mutex` 以及其他方法来解决这个问题；但是他们在内部应用了巧妙的技巧来在运行时而不是编译时维护相同的保证。效率较低，但规则相同！

就这样！其他一切基本上都是这些规则的应用。

## 处理空值

在 JavaScript 中，您可以 `null` 用来显示值尚未初始化。Rust 没有 `null`，相反，您需要使用 `Option` 类型手动标记可以未初始化的值。这通常是你在 JavaScript 中的做法：

```js
let cat = {
  name: "chashu",
  favorite_food: null, // 我们可以将一个 key 初始化为 "null"
};
cat.favorite_food = "tuna"; // ... 然后给它们赋值。
```

在 Rust 中，我们需要通过`Option`类型来实现这一点。

```rust
// 定义我们的shape的类型。
struct Cat {
    name: String,
    favorite_food: Option<String>,
}

// 创建一个我们类型的新实例。请注意，它需要是可变的
// 以便我们稍后可以更改它的值。
let mut cat = Cat {
    name: "Chashu".to_string(),
    favorite_food: None // 这是 `Option::None` 的缩写
};

// ... 然后我们在这里赋值。
cat.favorite_food = Some("tuna".to_string()); // 这是`Option::Some`的缩写
```

这是我们第一次看到 _enums_ ：封装状态的类型。在这里我们看到 enum `Option`，它可以用 `None` 标记没有设置任何值，也可以用 `Some` 包含一个内部值。

## Switch cases 和 if/else 块

Rust 不仅有 `if/else` 控制流，它还有一个 `match`. 这有点类似于 JavaScript 的 `switch` 声明。让我们将一些 JavaScript 控制流转换为 Rust：

```js
let num = 1;
switch (num) {
  case 0:
    break; // 处理 case 0
  case 1:
    break; // 处理 case 1
  default:
    throw new Error("oops");
}
```

并将其转换为 Rust，我们可以将其写为：

```rust
let num = 1;
match num {
    0 => todo!("handle case 0"),
    1 => todo!("handle case 1"),
    _ => panic!("oops"),
}
```

这为 `0` 和 `1` 各创建了一个 case ，并为所有其他数字提供了一个后备 case。如果我们不添加回退情况，编译器将不会让我们编译！

Match 块对于快速匹配模式很有用。我们可以将数字与其他数字进行比较，也可以将字符串相互比较，重要的是：比较枚举变体。假设我们想检查 `Option` 我们之前定义的是否是一个 `Some` 变体，或者 `None` 我们可以这样写：

```rust
struct Cat {
    name: String,
    favorite_food: Option<String>,
}

fn has_favorite_food(cat: Cat) -> bool {
    match cat.favorite_food {
        Some(_) => true, // 如果我们的猫有最喜爱的食物则返回 `true`
        None => false,   // 如果没有提供最喜爱的食物，则返回 `false`
    }
}
```

这将查看我们的 `Option` 是 `Some` 还是 `None` ，并根据具体情况返回不同的 bool。我们不使用`return` 关键字，因为 `match` 语句也是表达式，所以只要省略分号，布尔值就成为我们函数的返回值。

假设我们想处理 `Some` 中的字符串，我们可以给它一个变量名。例如，我们可以根据内部值打印不同的消息。

```rust
match cat.favorite_food {
    Some(s) => println!("our favorite food is: {}", s),
    None => println!("we have no favorite food yet :("),
}
```

`match` 语句在 Rust 中真的很常见（比 JavaScript 中的 `switch` 语句更常见），所以值得尝试一下它们，看看你能做什么。您可以使匹配语句适用于不同的字符串值吗？定义枚举怎么样？如果一个枚举包含另一个枚举，你能匹配它吗？

## 状态机（枚举）

What we saw with Rust's `Option` is that we can have two variants: `Some` and
`None`. This is kind of like a small state machine. Probably the simplest example
of a state machine in Rust is if we write our own version for `bool`. We know
this can be in one of two states: `true` and `false`. We could write it as an enum
like so:

我们从 Rust 的 `Option` 中看到的是，我们可以有两个变体：`Some` 和 `None`。这有点像一个小型的状态机。Rust 中状态机最简单的例子可能是为 `bool` 编写自己的版本。我们知道这可以是两种状态之一：`true` 和 `false` 。我们可以将其编写为枚举，如下所示：

```rust
// 我们的 bool 枚举，我们使用大写名称，因为小写名称是关键字
enum Bool {
    True,
    False,
}
```

现在 Rust 中的布尔值有点特殊，_实际上_ 不像枚举那样实现。但是 `Option` 是，它的定义是这样的：

```rust
enum Option<T> {
    None,
    Some(T),
}
```

这里有一些新语法 `<T>` ，这意味着这种类型是 _泛型_ 。但是你现在需要知道的是，我们可以存储不同的类型（比如 `字符串`）在它的内部 `Bool` 不存储任何值，因此不需要任何 _泛型_ 。

## 处理错误

如果 `Option` 是 Rust 处理 `null` 的枚举，那么 `Result` 是 Rust 处理 `Error` 的枚举。它不返回 `Some` 和 `None` ，而是返回 `Ok` 和 `Error` 。`Result` 的定义是：

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

这可能看起来很难接受：我们现在有两个通用参数！但是你还不需要太担心这个。现在，您需要记住的是，`Ok`可以返回一个值，`Err` 可以返回另一个值。作为一个实际示例，让我们使用 Node 读取一个文件。

js:

```js
const fs = require("fs");

// 尝试读取文件
try {
  let file = fs.readFileSync("./README.md");
  console.log(`read ${file.length} chars`);
} catch (e) {
  console.error(e); // 处理错误
}
```

And translating this to Rust we can write it using [`fs::read_to_string`](https://doc.rust-lang.org/std/fs/fn.read_to_string.html):

并将其转换为 Rust，我们可以使用 [`fs::read_to_string`](https://doc.rust-lang.org/std/fs/fn.read_to_string.html) 编写它：

```rust
use std::fs;

fn main() {
    match fs::read_to_string("./README.md") {
        Ok(file) => println!("read {} chars", file.len()),
        Err(e) => eprintln!("{}", e), // 处理错误
    };
}
```

Rust `match` 的整体错误处理与 JavaScript 的 `try...catch` 非常相似。您会注意到的主要区别是，在 Rust 中可能出错的函数总是在其签名中返回 `Result` 。

## 重新抛出错误

在 JavaScript 中，我们可以使用 `try...catch` 捕捉错误。在 catch 块中，我们可以再次 `throw` 错误：

```js
let file;
try {
  file = readFileSync(path);
} catch (e) {
  throw e;
}
// 在这里使用 `file`
```

如果我们不把函数包在 `try...catch` 块中，调用函数将自动重新抛出错误，直到我们命中程序根目录的另一个 `try...catch` 块。

```js
function inner(path) {
  let buf = fs.readFileSync(path);
  buf.length;
}

function outer() {
  try {
    inner("README.md");
    inner("uh-oh");
  } catch (e) {
    console.error(e);
  }
}

outer();
```

重新抛出错误在 JavaScript 中非常常见：您（或您使用的框架）通常有一些顶级的 `try...catch` 捕获框架内所有错误的块。在您的用户代码中的某个地方使用 `throw` 就足够了，它将被框架捡起。

正如我们提到的：在 Rust 中，可能出错的函数必须返回 `Result` ，我们可以使用 `match` 处理 `Result` 。我们可以在 Rust 中写一个类似这样的 `re-throw` ：

```rust
let file = match fs::read_to_string(path) {
    Ok(file) => file,
    Err(e) => return Err(e),
};
```

这将为变量 `file`（类型为 `String` ）分配 `Ok` 值，以便我们可以在函数中继续使用它。或者，如果我们有一个 `Err` ，我们立即使用 `Err` 从函数 `return`。然而，如果每次我们想重新抛出一个错误时都要写这个，那么要写的代码就太多了。因此，Rust 有一个 `try` 操作符：`?` 。有了它，我们可以调用 `?` 来替换 `match{return}` 块, 像这样：

```rust
let file = fs::read_to_string(path)?;
```

这并不像 JavaScript 的自动 `rethrowing` 那么容易编写。但它在调试和读取代码时会很有帮助，因为每个可能引发错误的调用都会整齐地标记为 `?` 。

## async/await

您可能知道 Rust 像 JavaScript 一样支持 `async/.await` 。由于这是一个针对 JavaScript 新手的指南，我的建议是：**在使用异步 Rust 之前，先熟悉非异步 Rust 的基本知识**。异步 Rust 在很大程度上是一项正在进行的工作。很多事情还不起作用，或者可能产生错误，这需要上下文知识。我们已经走了很长的路，但我们还有很长的路要走。

有了这个警告，让我们快速介绍一些基本知识！

在 JavaScript 中，`async/await` 用于编写异步代码，其读取方式与同步代码非常相似。例如，我们可以将文件读取示例从同步重写为异步，如下所示：

```js
const fs = require("fs/promises")(async function () {
  try {
    let file = await fs.readFile("./README.md");
    console.log(`read ${file.length} chars`);
  } catch (e) {
    console.error(e);
  }
})();
```

在这里，我们可以通过导入 `readFile` 的 “promise” 版本将同步调用重写为异步调用。然后我们可以在异步函数中通过在前面添加 `await` 调用来调用它。

与 Node.js 不同。Rust 还没有内置异步 IO 绑定。因此，我们必须从板条箱中导入第三方库。io 为我们提供这些绑定。[`async std`](https://docs.rs/async-std/1.9.0/async_std/) 库被设计为 stdlib（我是合著者）的一个嵌入式异步替代品，因此让我们将其用于我们的示例：

```rust
use async_std::fs;

#[async_std::main]
async fn main() {
    match fs::read_to_string("./README.md").await {
        Ok(file) => println!("read {} chars", file.len()),
        Err(e) => eprintln!("{}", e),
    };
}
```

与 JavaScript 非常相似，我们导入异步版本的 `fs` ，将代码包装在
`async` 函数，然后调用 `.await` 。就像 try 操作符（`？`）一样，所以把 `await` 放在最后调用。

在内部 JavaScript 的 `async/await` ，以及 Rust 的 `async/.await` 的实现相当类似：JavaScript 的 `async` 将 “Promises” 和 “generators” 绑定到一起。
Rust 的 `async` 设计了 “Futures” 和 “generators” 。不过，这两个系统之间的主要区别在于 JavaScript 的 “Promises” 在创建时就开始执行，而 Rust 的 Futures 仅在它们被 `.await` 调用时才开始执行。

从这个意义上讲，Rust 的 `Future` 更类似于[JavaScript]
“thenable”](https://masteringjs.io/tutorials/fundamentals/thenable)。而JavaScript 的 Promise 更类似于 Rust 的 [Task](https://docs.rs/async-std/1.9.0/async_std/task/index.html#the-task-model）。

## 传递配置和选项

Instead of using `opts` or default values, most things use builders instead.
Kind of the way [`superagent`](https://github.com/visionmedia/superagent) works:

大多数东西都使用构建器，而不是使用 `opts` 或默认值。
['superagent`](https://github.com/visionmedia/superagent)的一种工作方式：

```js
let opts = {
  method: "GET",
  headers: {
    "X-API-Key": "foobar",
    Accept: "application/json",
  },
};

try {
  let res = await fetch("/api/pet", opts);
} catch (err) {
  throw err;
}
```

```js
superagent
  .post("/api/pet")
  .set("X-API-Key", "<secret>")
  .set("Accept", "application/json")
  .end((err, res) => {
    // 调用 end 函数将发送请求
  });
```

我们也可以在 Rust 上做同样的事情。下面是一个使用 [`surf`](https://docs.rs/surf) 的 HTTP 客户端例子：

```rust
let res = surf::post("/api/pet")
    .header("X-API-Key", "<secret>")
    .header("Accept", "application/json")
    .await?; // 调用 .await 将发送请求
```

内部构建器通常携带 `self` 并返回 `self` 作为输出，因此您可以将这些方法链接在一起。

## Outro

希望这对从 JS 窥探 Rust 的人来说有点有用。这里还有很多东西需要写，但希望这会有所帮助！

## License

[MIT](../LICENSE-MIT) OR [Apache-2.0](../LICENSE-APACHE)
