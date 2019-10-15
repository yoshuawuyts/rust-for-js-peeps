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

### Rustup
[rustup](https://rustup.rs/): this is like `nvm` for Node, but officially
supported and really well done! It helps you install and manage Rust compiler
versions.

Installing Rustup also installs a valid compiler toolchain, which gives you 3
new commands in total

- `$ rustup`: runs "rustup" to manage your compiler toolchain
- `$ rustc`: which is the Rust compiler. You'll never need to call this because of:
- `$ cargo`: `cargo` is to Rust, what `npm` is to Node. It builds, compiles,
  installs and more. Getting to know `cargo` well is usually time well-spent!

### Cargo-edit

[`cargo-edit`](https://github.com/killercup/cargo-edit) provides essential
extensions to `cargo`. In particular: it allows you to run `cargo add` which
works somewhat similar to `npm install`.

Importantly: `cargo install` works similar to `npm install -g`. And when you run
`cargo add` only your `Cargo.toml` file (Rust's `package.json` file) is updated
with the right versions. Run `cargo build` or `cargo check` to actually download
and compile dependencies.

You can install `cargo-edit` by running:
```sh
$ cargo install cargo-edit
```

### Rust fmt

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
$ cargo fmt                   // runs rustfmt to format your code
$ cargo fmt -- --check        // do a dry-run, outputting a diff of changes that would be made
$ cargo fmt -- --edition=2018 // pass this flag if you're doing stuff with async/await
```

### Clippy

[clippy](https://github.com/rust-lang/rust-clippy) is a "linting" tool for Rust,
similar to [`standard`](https://github.com/standard/standard)'s style lints.
`cargo fmt` takes care of formatting. `rustc` takes care of correctness. But
`clippy` is in charge of helping you write "idiomatic" Rust.

This doesn't mean every lint in Clippy is perfect. But when you're getting
started it can be suuuper helpful to run!

### Editors

Rust has two language-server implementations:
[`rls`](https://github.com/rust-lang/rls) and
[`rust-analyzer`](https://github.com/rust-analyzer/rust-analyzer). Try using
`rust-analyzer` first, as it's a far superior experience. But if it doesn't work
for your setup, it's good to be aware of (the much older, kind of unmaintained)
`rls`.

You'll have to figure out for yourself how you want to set this up. But if
you're undecided, I've heard VSCode is generally straight forward.

### Testing

Cargo ships with a `cargo test` command, which will run both doctests and files
under `test/`. The Rust book has [a whole chapter dedicated to
testing](https://doc.rust-lang.org/1.30.0/book/second-edition/ch11-00-testing.html)
you should read on this. But it's good to know this is provided for you out of
the box!

### Creating new projects

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

### Publishing

`cargo publish` works like `npm publish`. The central repository is called
[crates.io](https://crates.io) and is very similar to NPM. Importantly it's not
owned by a scummy for-profit company, but is instead part of the Rust project.

If you've built something nice in Rust, consider going ahead and publishing it
to Crates.io. All that's needed is a GitHub account to sign up, and you're good
to go!

### Documentation

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
$ cargo doc           // generate docs
$ cargo doc --open    // generate docs and then open them
$ rustup doc --std    // open the stdlib docs offline
$ rustup doc --book   // open the "Rust Programming Language" offline
```

### Cargo-watch

It can sometimes be tedious to run `cargo check` after every change. Which is
why [`cargo-watch`](https://github.com/passcod/cargo-watch) exists. You can
install it by running:

```sh
$ cargo install cargo-watch
```

Important `cargo-watch` commands are:

```sh
$ cargo watch             // Run "cargo check" on every change
$ cargo watch -x "test"   // Run "cargo test" on every change
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

### Object-Oriented everything
In Rust *everything* is object-oriented. Imports are always done through
namespaces, and namespaces kind of behave like structs.

```rust
// println comes from the "std" namespace -- print something to the screen.
std::println!("hello world");

// Call the "new" method from the `HashMap` type from the `std::collections`
// namespace
let hashmap = std::collections::HashMap::new();
```

### Classes & Structs
Structs don't have "constructor" methods the way JS do; instead you define a
method that returns `Self` (which is a shorthand for the name of the struct).

```js
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}
```

```rust
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

### Expressions!
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

### Know your `self`
There are 3 kinds of self: `self`, `&self`, `&mut self`.

```rust
pub struct Rectangle {
    height: usize,
    width: usize,
}

impl Rectangle {
    pub fn new(height: usize, width: usize) -> Self {
        Self { heigh, width }
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

### Options
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

```rust
let res = surf::post("/api/pet")
    .set_header("X-API-Key", "foobar")
    .set_header("accept", "json")
    .await?; // Calling .await will send the request
```

Internally builders generally take `self`, and return `self` as the output so
you can chain the methods together.

### `?` and `.await`

In Rust:
- `?` is for error handling; it says: "if it's an error, return the error, if
    it's not an error, return the value".
- `.await` is like `await` but you can chain it just like any other property.

Speaking of `.await` though. Do yourself a big favor and start with
_synchronous_ Rust first. Starting with async Rust is going full throttle on
hard mode, and I can guarantee it's going to be confusing.

Start easy. Do synchronous Rust for a bit. Skip HTTP. Don't do timeouts. Try,
uhh, other things first. Loops, and printing stuff to the console. Maybe even
borrowing if you're feeling cheeky.

## Outro

Hopefully this is somewhat useful for JS peeps looking at Rust. There's a lot
more that should be written here, but hopefully this is somewhat helpful!

## License
[MIT](./LICENSE-MIT) OR [Apache-2.0](./LICENSE-APACHE)
