# Rust for JavaScript peeps
Rust for JavaScript peeps

## Introduction
People seem to like Rust a lot! But if you're coming from JavaScript, not
everything may make a lot of sense at first. But no problem; this guide is for
you!

Because I think Rust and JavaScript are really similar in many ways; to the
point that if you know JS it's mostly a matter of getting the hang of some of
the nuances before you can more or less get the hang of Rust.

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
        Self { heigh, width }
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
