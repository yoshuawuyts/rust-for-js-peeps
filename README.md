# Rust for JavaScript peeps
Rust for JavaScript peeps

## Introduction

People seem to like Rust a lot! But if you're coming from JavaScript, not
everything may make a lot of sense at first. But no problem; this guide is for
you!

Because I think Rust and JavaScript are really similar in many ways; to the
point that if you know JS it's mostly a matter of getting the hang of some of
the nuances before you can more or less get the hang of Rust.

## Quick Start

Alright. So you want to write Rust? Step one is to get yourself a working
environment. This means installing tools. Here's an overview of what you need
(more or less in-order):

## Installing Rust
[rustup](https://rustup.rs/): this is like `nvm` for Node, but officially
supported and really well done! It helps you install and manage Rust compiler
versions.

Installing Rustup also installs a valid compiler toolchain, which gives you 3
new commands in total

- `$ rustup`: runs "rustup" to manage your compiler toolchain
- `$ rustc`: which is the Rust compiler. You'll never need to call this because of:
- `$ cargo`: `cargo` is to Rust, what `npm` is to Node. It builds, compiles,
  installs and more. Getting to know `cargo` well is usually time well-spent!

## Installing packages

[`cargo-edit`](https://github.com/killercup/cargo-edit) provides essential
extensions to `cargo`. In particular: it allows you to run `cargo add` which
works similar to `npm install`. In rust-lingo, "packages" are called "crates".

`cargo install` works similar to `npm install -g`. And when you run
`cargo add` only your `Cargo.toml` file (Rust's `package.json` file) is updated
with the right versions. Run `cargo build` or `cargo check` to actually download
and compile dependencies.

You can install `cargo-edit` by running:
```sh
$ cargo install cargo-edit
```

## Formatting code

[`rustfmt`](https://github.com/rust-lang/rustfmt) is Rust's version of
[`prettier`](https://github.com/prettier/prettier). Everyone uses it, and even
if the default config might take some getting used to, it's what everyone uses.

It's a binary component that hooks into the compiler, so it needs to be
installed with `rustup`:

```sh
$ rustup component add rustfmt
```

This should take a few seconds on a fast connection. Whenever you update your
rust version, `rustfmt` will also be updated.

Important commands are:

```sh
$ cargo fmt                    # runs rustfmt to format your code
$ cargo fmt -- --check         # do a dry-run, outputting a diff of changes that would be made
$ cargo fmt -- --edition=2018  # pass this flag if you're doing stuff with async/await
```

## linting code

[clippy](https://github.com/rust-lang/rust-clippy) is a "linting" tool for Rust,
similar to [`standard`](https://github.com/standard/standard)'s style lints.
`cargo fmt` takes care of formatting. `rustc` takes care of correctness. But
`clippy` is in charge of helping you write "idiomatic" Rust.

This doesn't mean every lint in Clippy is perfect. But when you're getting
started it can be suuuper helpful to run!

## Editors and IDE support

Rust has a [language-server](https://langserver.org/) implementation in the form of
[`rust-analyzer`](https://github.com/rust-analyzer/rust-analyzer). This provides
IDE support for just about any editor. If you're not sure which editor to start with:
consider using VSCode! - It has really good integration with language servers, and should
make it easy to get started writing Rust.

## Testing

Cargo ships with a `cargo test` command, which will run both doctests and files
under `test/`. The Rust book has [a whole chapter dedicated to
testing](https://doc.rust-lang.org/1.30.0/book/second-edition/ch11-00-testing.html)
you should read on this. But to get you started, you can copy this boilerplate into
any file into your `src/` directory, and `cargo test` will pick it up:


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

This creates a module within your file which only compiles when `cargo test` is
run. It imports all functions and types from the outer scope to the `mod test`
scope, and then defines a single test `smoke_test` which is executed. You can
add more test by writing more `#[test]` functions.

## Creating new projects

You can create new projects using `cargo new` or `cargo init`. `new` creates a
new directory, `init` outputs files in the current directory. It's pretty basic,
but it's useful to get started with. If you want to write a library you can pass
either command the `--lib` flag. By default you'll create binaries (applications
with a `main` function that can be run).

There's also the newer
[`cargo-generate`](https://github.com/ashleygwilliams/cargo-generate) project.
This is a more powerful version of the built-in `cargo` commands, and allows you
to pick from templates. You may not need this if you're just messing around, but
it's probably good to be aware of.

## Publishing

`cargo publish` works like `npm publish`. The central repository is called
[crates.io](https://crates.io) and is very similar to NPM. Importantly it's not
owned by a scummy for-profit company, but is instead part of the Rust project.

If you've built something nice in Rust, consider going ahead and publishing it
to Crates.io. All that's needed is a GitHub account to sign up, and you're good
to go!

## Documentation

Most docs in JS seem to either be written in a README.md, or as part of some
special website. In Rust documentation is generated automatically using `rustdoc`.

You can run `rustdoc` through `$ cargo doc`. Every package on crates.io also has
documentation generated for you on [docs.rs](https://docs.rs). It's even
versioned, so you can check out older documentation too. For example: you can
find `async-std`'s docs under [docs.rs/async-std](https://docs.rs/async-std).

Writing docs in Rust is by using "doc comments" (`///` instead of the regular
`//` comments). You'll see a bunch in the rest of this guide. Important
documentation commands are:

```sh
$ cargo doc            # generate docs
$ cargo doc --open     # generate docs and then open them
$ rustup doc --std     # open the stdlib docs offline
$ rustup doc --book    # open the "Rust Programming Language" offline
```

## watching projects

It can sometimes be tedious to run `cargo check` after every change. Which is
why [`cargo-watch`](https://github.com/passcod/cargo-watch) exists. You can
install it by running:

```sh
$ cargo install cargo-watch
```

Important `cargo-watch` commands are:

```sh
$ cargo watch              # Run "cargo check" on every change
$ cargo watch -x "test"    # Run "cargo test" on every change
```

## Terminology

Before we continue, let's establish some quick terminology:

- __Struct:__ like an "object" in JS. It can both be a data-only type. But can
    also work like a "class" with methods (both inherent and static).
- __Vec:__ like a JS "array".
- __Slice:__ like a ["view" into a `TypedArray` in
    JS](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays).
    You can't grow them, but you can create a "view of a view"
    (slice of a slice).
- __Traits:__ essentially the answer to the question: "what if a class could
    inherit from multiple other classes". Traits are also referred to as
    "mixins" in other languages. They don't allocate any data, but only provide
    methods and type definitions related to those methods.

## Thinking in Rust

Aside of the obvious type system stuff, I think there are a few core differences
between Rust and JS:

## Object-Oriented everything

In Rust *everything* is object-oriented. Imports are always done through
namespaces, and namespaces kind of behave like structs.

```rust
// println comes from the "std" namespace -- print something to the screen.
std::println!("hello world");

// Call the "new" method from the `HashMap` type from the `std::collections`
// namespace
let hashmap = std::collections::HashMap::new();
```

## Classes & Structs

Structs don't have "constructor" methods the way JS do; instead you define a
method that returns `Self` (which is a shorthand for the name of the struct).

```js
/// How to instantiate:
/// ```js
/// let rect = new Rectangle(5, 10);
/// ```
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}
```

```rust
/// How to instantiate:
/// ```rs
/// let rect = Rectangle::new(5, 10);
/// ```
pub struct Rectangle {
    height: usize,
    width: usize,
}

impl Rectangle {
    /// Create a new instance.
    pub fn new(height: usize, width: usize) -> Self {
        Self { height, width }
    }
}
```

## Expressions!
Everything is an expression. Which is to say: blocks suddenly have a lot more
meaning, and you can do some fun substitutions.

These are all equivalent:
```rust
let y = 1 + 1;
let x = y * y;
if x == 4 {
    println!("hello world");
}
```

```rust
// If we omit the `;` from a statement, it becomes the return value of the
// block it's in.
let x = {
    let y = 1 + 1;
    y * y
};
if x == 4 {
    println!("hello world");
}
```


```rust
// Expressions means that you can inline almost anything. Don't actually do this
// please.
if {
    let y = 1 + 1;
    y * y
} == 4 {
    println!("hello world");
}
```

## Know your `self`
There are 3 kinds of self: `self`, `&self`, `&mut self`.

```rust
pub struct Rectangle {
    height: usize,
    width: usize,
}

impl Rectangle {
    pub fn new(height: usize, width: usize) -> Self {
        Self { height, width }
    }

    /// Get the height
    ///
    /// We want to reference the value as read-only,
    /// so we use `&self` for shared access.
    pub fn height(&self) -> usize {
        self.height
    }

    /// Set the height
    ///
    /// We want to reference the value as "writable", so we use
    /// `&mut self` for exclusive access.
    pub fn set_height(&mut self, height: usize) -> usize {
        self.height = height
    }

    /// Get the height + width as a tuple.
    ///
    /// We want to "consume" the current struct, and return its internal parts.
    /// So instead of taking a reference, we take an owned value `self` after
    /// which the struct can no longer be used, and return a tuple (anonymous
    /// struct) containing its internals.
    pub fn parts(self) -> (usize, usize) {
        (self.height, self.width)
    }
}
```

This is the core of everything around the borrow checker. If you have exclusive
access to a variable, nobody else can have access to that variable too and you
can mutate it. If you have shared access to a variable, others may too, but
you're not allowed to update the value. That's how data races are prevented!

There's some escape hatches using `RefCell`, `Mutex` and other things to get
around this; but they apply clever tricks internally to uphold the same
guarantees at runtime rather than compile-time. Less efficient, but same rules!

That's it! Everything else is basically an application of these rules.

## Handling null values

In JavaScript you can use `null` to show that a value hasn't been initialized
yet. Rust doesn't have `null`, instead you need to manually mark values which
can be uninitialized by using the `Option` type. This is generally how you do it
in JavaScript:

```js
let cat = {
    name: "chashu",
    favorite_food: null // we can initialize a key as "null"
}
cat.favorite_food = "tuna"; // ... and then later assign values to them.
```

In Rust we need to go through the `Option` type for this.

```rust
// Define our the shape of our type.
struct Cat {
    name: String,
    favorite_food: Option<String>,
}

// Create a new instance of our type. Note that it needs to be mutable
// so we can change values on it later. 
let mut cat = Cat {
    name: "Chashu".to_string(),
    favorite_food: None // this is short for `Option::None`
};

// ... and then we assign a value here.
cat.favorite_food = Some("tuna".to_string()); // this is short for `Option::Some`
```

This is our first look at _enums_: types which encapsulate a piece of state. Here
we see the enum `Option`, which can either be `None` to mark no value has been
set, or `Some` which contains an inner value.

## Switch cases and if/else blocks

Rust not only has `if/else` for control flow, it also has a concept of `match`. This is somewhat similar to JavaScript's `switch` statement. Let's translate some JavaScript control flow to Rust:

```js
let num = 1
switch (num) {
    case 0:
        break; // handle case 0
    case 1:
        break; // handle case 1
    default:
        throw new Error('oops')
}
```

And converting this to Rust we could write it as:

```rust
let num = 1;
match num {
    0 => todo!("handle case 0"),
    1 => todo!("handle case 1"),
    _ => panic!("oops"),
}
```

This creates a case for `0`, for `1`, and provides a fallback case for all other
numbers. If we didn't add the fallback case the compiler would not let us compile!

Match blocks are useful to quickly match on patterns. We can compare numbers to
other numbers, but also strings with each other, and importantly: compare enum
variants. Say we wanted to check whether the `Option` we defined earlier was a
`Some` variant, or a `None` variant we could write it like this:

```rust
struct Cat {
    name: String,
    favorite_food: Option<String>,
}

fn has_favorite_food(cat: Cat) -> bool {
    match cat.favorite_food {
        Some(_) => true, // return `true` if our cat has a favorite food
        None => false,   // return `false` if no favorite food has been provided
    }
}
```

This looks whether our `Option` is `Some` or `None`, and returns a different
bool depending on the case. We're not using the `return` keyword for this
because `match` statements are expressions too, so simply by omitting the
semicolon the booleans becomes the return values for our function.

Say we wanted to do something with the string within `Some`, we could give it a
variable name. We could for example print different messages depending on the
inner value.

```rust
match cat.favorite_food {
    Some(s) => println!("our favorite food is: {}", s),
    None => println!("we have no favorite food yet :("),
}
```

`match` statements are really common in Rust (more common than `switch`
statements in JavaScript), so it's worth playing around with them to see what
you can do. Can you make a match statement work for different string values?
What about defining enums? What if an enum contains another enum, can you match
on that?

## State machines (enums)

What we saw with Rust's `Option` is that we can have two variants: `Some` and
`None`. This is kind of like a small state machine. Probably the simplest example
of a state machine in Rust is if we write our own version for `bool`. We know
this can be in one of two states: `true` and `false`. We could write it as an enum
like so:

```rust
// our bool enum, we're using uppercase names because the lowercase
// names are keywords
enum Bool {
    True,
    False,
}
```

Now booleans in Rust are a bit special, and not _actually_ implemented like an
enum. But `Option` is, and the definition of it is this:

```rust
enum Option<T> {
    None,
    Some(T),
}
```

There's some new syntax here `<T>`, which means this type is _generic_. But all
you need to know about this now is that we can store a different type (like
`String`) inside of it. `Bool` doesn't store any values, so it doesn't need any
_generics_.

## Handling errors

If `Option` is Rust's enum to handle `null`, then `Result` is Rust's enum to
handle `Error`. Instead of `Some` and `None`, it returns `Ok` and `Error`. The
definition for `Result` is:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

This might look pretty overwhelming: we now have _two_ generic arguments! But
you don't need to worry too much about this yet. For now all you need to
remember is that `Ok` can return a value, and `Err` can return a different
value. For a practical example, let's read a file using Node.js:

```js
const fs = require('fs')

// try to read a file
try {
    let file = fs.readFileSync("./README.md")
    console.log(`read ${file.length} chars`)
} catch(e) {
    console.error(e) // handle error
}
```

And translating this to Rust we can write it using [`fs::read_to_string`](https://doc.rust-lang.org/std/fs/fn.read_to_string.html):

```rust
use std::fs;

fn main() {
    match fs::read_to_string("./README.md") {
        Ok(file) => println!("read {} chars", file.len()),
        Err(e) => eprintln!("{}", e), // handle error
    };
}
```

Overall error handling with Rust's `match` can feel pretty similar to
JavaScript's `try...catch`. The main difference you'll notice is that functions
which can error in Rust will always return `Result` in their signature.

## Re-throwing errors

In JavaScript we can catch errors using `try...catch`. And from within catch
blocks we can `throw` errors again:

```js
let file
try {
    file = readFileSync(path)
} catch (e) {
    throw e
}
// use `file` here
```

If we don't wrap our functions in `try...catch` blocks, errors will be
automatically re-thrown from the calling function until we hit another
`try...catch` block or the root of the program.

```js
function inner(path) {
    let buf = fs.readFileSync(path)
    buf.length
}

function outer() {
    try {
        inner("README.md")
        inner("uh-oh")
    } catch (e) {
        console.error(e)
    }
}

outer()
```

Re-throwing errors is pretty common in JavaScript: you (or the framework you
use) usually has some top-level `try...catch` block that catches all errors from
within the framework. It's enough to use `throw` somewhere within your user
code, and it'll be picked up by the framework.

As we mentioned: in Rust functions which can error must return `Result`, and we
can handle `Result` using `match`. We can write a re-throw in Rust like this:

```rust
let file = match fs::read_to_string(path) {
    Ok(file) => file,
    Err(e) => return Err(e),
};
```

This will either assign the `Ok` value to the variable `file` (of type `String`)
so we can continue to use it in our function. Or if we have an `Err` we
immediately `return` from our function using `Err`. However if we had to write
this everytime we wanted to re-throw an error, it'd be a lot of code to write. So
for that Rust has the "try" operator: `?`. With it we can replace the `match {
return }` block with a call to `?` like so:

```rust
let file = fs::read_to_string(path)?;
```

This isn't quite as easy to write as JavaScript's automatic rethrowing. But it can
be helpful in debugging and reading code, as every call which could throw an error
is neatly marked with `?`.

## async/await

You may be aware that Rust has support for `async/.await` much like JavaScript
has. Since this is a guide for folks coming from JavaScript who are new to Rust,
my advice is this: __become comfortable with the basics of non-async Rust before
using async Rust__. Async Rust is very much a work-in-progress. A lot of things
don't work yet, or can yield errors which require contextual knowledge. We've
come a long way, but we have a long way to go still.

With that warning out of the way, let's quickly cover some of the basics!

In JavaScript `async/await` is used to write async code which reads much like
synchronous code. For example we can rewrite our file reading example from
synchronous to asynchronous like so:

```js
const fs = require('fs/promises')

(async function () {
    try {
        let file = await fs.readFile("./README.md")
        console.log(`read ${file.length} chars`)
    } catch(e) {
        console.error(e)
    }
})()
```

Here we see we can rewrite our synchronous call to be asynchronous by importing
the "promise" version of `readFile`. And then we can call it from within an
async function by adding the `await` call in front of it.

Unlike Node.js, Rust doesn't have asynchronous IO bindings built-in yet. So we
have to import a third-party library from crates.io which provides those
bindings for us. The [`async-std`](https://docs.rs/async-std/1.9.0/async_std/)
library was designed as a drop-in asynchronous replacement for the stdlib (I'm a
co-author), so let's use that for our example:

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

Much like JavaScript, we import an async version of `fs`, wrap our code in an
`async` function, and then call it with `.await`. Just like the try operator
(`?`), so too does `await` go at the end of the call.

Internally JavaScript's `async/await`, and Rust's `async/.await` are implemented
fairly similarly: JavaScript's `async` desugars to "Promises" and "generators".
Rust's `async` desugars to "Futures" and "generators". The main difference
between the two systems though is that JavaScript's "Promises" start executing
the moment they're created, while Rust's Futures only start when they're
`.await`ed.

In that sense a Rust Future is more similar to a [JavaScript
"thenable"](https://masteringjs.io/tutorials/fundamentals/thenable). While a
JavaScript Promise is more similar to a Rust
[Task](https://docs.rs/async-std/1.9.0/async_std/task/index.html#the-task-model).

## Passing configuration and options

Instead of using `opts` or default values, most things use builders instead.
Kind of the way [`superagent`](https://github.com/visionmedia/superagent) works:

```js
let opts = {
  method: 'GET',
  headers: {
    'X-API-Key': 'foobar',
    'accept': 'json'
  }
};

try {
  let res = await fetch('/api/pet', opts);
} catch(err) {
  throw err
}
```

```js
superagent.post('/api/pet')
  .set('X-API-Key', 'foobar')
  .set('accept', 'json')
  .end((err, res) => {
    // Calling the end function will send the request
  });

```

We can do the same in Rust. Here is an example using the
[`surf`](https://docs.rs/surf) HTTP client:
```rust
let res = surf::post("/api/pet")
    .set_header("X-API-Key", "foobar")
    .set_header("accept", "json")
    .await?; // Calling .await will send the request
```

Internally builders generally take `self`, and return `self` as the output so
you can chain the methods together.

## Outro

Hopefully this is somewhat useful for JS peeps looking at Rust. There's a lot
more that should be written here, but hopefully this is somewhat helpful!

## License
[MIT](./LICENSE-MIT) OR [Apache-2.0](./LICENSE-APACHE)
