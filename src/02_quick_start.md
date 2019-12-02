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
$ cargo fmt                   # runs rustfmt to format your code
$ cargo fmt -- --check        # do a dry-run, outputting a diff of changes that would be made
$ cargo fmt -- --edition=2018 # pass this flag if you're doing stuff with async/await
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
$ cargo doc           # generate docs
$ cargo doc --open    # generate docs and then open them
$ rustup doc --std    # open the stdlib docs offline
$ rustup doc --book   # open the "Rust Programming Language" offline
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
$ cargo watch             # Run "cargo check" on every change
$ cargo watch -x "test"   # Run "cargo test" on every change
```

