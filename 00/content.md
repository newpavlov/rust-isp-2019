# Lecture 01: Hello, Rust! #

![](img/rust.png)
Based on: [CIS 198 slides](https://github.com/cis198-2016s/slides)

Artyom Pavlov, 2019.

---
## Overview ##


"Rust is a systems programming language that runs blazingly fast, prevents
nearly all segfaults, and guarantees thread safety." &ndash;
[prev.rust-lang.org](https://prev.rust-lang.org/)

"Empowering everyone to build reliable and efficient software. " &ndash;
[rust-lang.org](https://www.rust-lang.org/)

---
### What _is_ Rust? ###

Rust is:
- Fast
- Safe
- Functional
- Zero-cost
- Excellent tooling

---
### Fast ###

- Rust compiles to native code
- Rust has no garbage collector
- Most abstractions have zero cost
- Fine-grained control over lots of things
- Pay for exactly what you need...
- ...and pay for most of it at compile time

---
### Safe ###

- No null
- No uninitialized memory
- No dangling pointers
- No double free errors
- No manual memory management!

---
### Functional ###

- First-class functions
- Trait-based generics
- Algebraic datatypes
- Pattern matching

---
### Zero-Cost 100% Safe Abstractions ###

- Rust's defining feature
- Strict compile-time checks remove need for runtime
- Big concept: Ownership

---
### Tooling ###

- `cargo`: one of the best package managers/build systems in class. Say no to dependency hell and makefiles!
- Built-in benchmarks, tests and docs generator (`rustdoc`).
- `clippy`: A LOT of additionall lints.
- `rustfmt`: code formatting utility.
- Rust Language Server (RLS) for improving IDE experience.
- Many C/C++ tools are usable with Rust: GDB, LLDB, Valgrind, perf, gprof, etc.

---
### Release Model

- Rust has a new stable release every six weeks
- Additionally Rust has 3-year edition releases
- Nightly builds are available, well, nightly
- Current stable: Rust 1.31 (2018 edition)
- Train model:

Date | Stable | Beta | Nightly
--- | --- | --- | ---
2018-09-13 | 🚂 1.29 | 🚆 1.30 | 🚝 1.31
2016-10-25 | 🚆 1.30 | 🚝 1.31 | 🚈 1.32
2016-12-06 | 🚝 1.31 | 🚈 1.32 | 🚅 1.33

---
### Development

- Rust is led by the Rust Team, mostly at Mozilla Research.
- Very active community involvement - on GitHub, Reddit, irc.
    - [Rust Source](https://github.com/rust-lang/rust/)
    - [Rust Internals Forum](https://internals.rust-lang.org/)
    - [/r/rust](http://www.reddit.com/r/rust)

---
### Who Uses Rust?

![](img/rust-in-production.png)

<p align="center">[Friends of Rust](https://prev.rust-lang.org/en-US/friends.html)</p>

---
### Big Rust Projects

- [Servo](https://github.com/servo/servo)
- [Piston](https://github.com/PistonDevelopers/piston)
- [MaidSafe](https://maidsafe.net/)
- [MIO](https://github.com/carllerche/mio) and [tokio](https://github.com/tokio-rs/tokio)
- [Redox](https://www.redox-os.org/)
- [ripgrep](https://github.com/BurntSushi/ripgrep)
- [lalrpop](https://github.com/nikomatsakis/lalrpop)
- [cargo](https://github.com/rust-lang/cargo)
- [Rust itself!](https://github.com/rust-lang/rust/)

---
## Administrivia

- 5 lectures with practical exercises.
- Completing exercises will be given as a homework.
- Final project can be group (up to 5 people) or individual.
- Pass/fail mark will be decided based on a final project.
- You can ask questions in the Canvas chat or personally (ISR Lab, room 151).
- Class source material generally hosted on [GitHub](https://github.com/newpavlov/rust-isp-2019).
    - Corrections are welcomed via pull request/issue!
- Course is in development - give us feedback!

---
### Helpful Links ##

- [Official Rust Docs](https://doc.rust-lang.org/stable/std/)
- [The Rust Book (our course textbook)](https://doc.rust-lang.org/stable/book/)
- [Rust By Example](http://rustbyexample.com/)
- [Rust Playpen](https://play.rust-lang.org/)
    - Online editing and execution!
- [rust.godbolt.org](https://rust.godbolt.org/)
    - Examine generated assembly!

---
## Let's Dive In! ##

Hello, Rust!

```rust
fn main() {
    println!("Hello, world!");
}
```
- All code blocks have links to the Rust playpen so you can run them!

---
# Basic Rust Syntax #

---
## Variable Bindings ###
- Variables are bound with `let`:
```rust
let x = 17;
```

- Bindings are implicitly-typed: the compiler infers based on context.
- The compiler can't always determine the type of a variable, so sometimes you
  have to add type annotations.
```rust
let x: i16 = 17;
```

- Variables are inherently immutable:
```rust
let x = 5;
x += 1; // error: re-assignment of immutable variable x
let mut y = 5;
y += 1; // OK!
```

---
## Variable Bindings ###
- Bindings may be shadowed:
```rust
let x = 17;
let y = 53;
let x = "Shadowed!";
// x is not mutable, but we're able to re-bind it
```

- The shadowed binding for `x` above lasts until it goes out of scope.
- Above, we've effectively lost the first binding, since both `x`s are in the same scope.

- Patterns may also be used to declare variables:
```rust
let (a, b) = ("foo", 12);
let [c, d] = [1, 2];
```

---
## Expressions

- (Almost!) everything is an expression: something which returns a value.
    - Exception: variable bindings are not expressions.
- The "nothing" type is called "unit", which is written `()`.
    - The _type_ `()` has only one value: `()`.
    - `()` is the default return type.
- Discard an expression's value by appending a semicolon. Now it returns `()`.
    - Hence, if a function ends in a semicolon, it returns `()`.

```rust
fn foo() -> i32 { 5 }
fn bar() -> () { () }
fn baz() -> () { 5; }
fn qux()       { 5; }
```

---
## Expressions ###
- Because everything is an expression, we can bind many things to variable names:
```rust
let x = -5;
let y = if x > 0 { "greater" } else { "less" };
println!("x = {} is {} than zero", x, y);
```

- Side note: `"{}"` is Rust's (most basic) string interpolation operator
    - Similar to Python, Ruby, C#, and others; like `printf`'s `"%s"` in C/C++.
    - `"{:?}"` can be used for debug formating
    - More information: [doc.rust-lang.org/std/fmt/](https://doc.rust-lang.org/std/fmt/)

---
## Comments

```rust
//! Comments like this are for module/crate documentation.

/// Triple-slash comments are docstring comments.
///
/// `rustdoc` uses docstring comments to generate
/// documentation, and supports **Markdown** formatting.
fn foo() {
    // Double-slash comments are normal.

    /* Block comments
    also exist /* and can be nested! */
     */
}
```

---
## Types

---
### Primitive Types ###

- `bool`: spelled `true` and `false`.
- `char`: spelled like `'c'` or `'😺'` (`char`s are Unicode code-points, i.e. 4 bytes long!).

- Numerics: specify the signedness and size.
    - `i8`, `i16`, `i32`, `i64`, `isize`
    - `u8`, `u16`, `u32`, `u64`, `usize`
    - `f32`, `f64`
    - `isize` & `usize` are the size of pointers (and therefore have
        machine-dependent size)
    - Literals are spelled like `10i8`, `10u16`, `10.0f32`, `10usize`.
    - Type inference for non-specific literals default to `i32` or `f64`:
      - e.g. `10` defaults to `i32`, `10.0` defaults to `f64`.

- Arrays, slices, `str`, tuples.
- Functions.

---
### Arrays ###
- Arrays are generically of type `[T; N]`.
    - N is a compile-time _constant_. Arrays cannot be resized.
    - Array access is bounds-checked at runtime.
    - No const generics yet. (planned to be added in 2019)
- Arrays are indexed with `[]` like most other languages:
    - `arr[3]` gives you the 4th element of `arr`

```rust
let arr1 = [1, 2, 3]; // (array of 3 elements)
let arr2 = [2; 32];   // (array of 32 `2`s)
```

---
### Slices ###
- Generically of type `&[T]`
- A "view" into an array (or heap allocated data) by reference
- Not created directly, but are borrowed from other variables
- Mutable `&mut [T]` or immutable `&[T]`
- How do you know when a slice is still valid? Coming soon...

```rust
let arr = [0, 1, 2, 3, 4, 5];
let total_slice = &arr;         // Slice all of `arr`
let total_slice = &arr[..];     // Same, but more explicit
let partial_slice = &arr[2..5]; // [2, 3, 4]
```

---
### Strings ###
- Two types of Rust strings: `String` and `&str`.
- `String` is a heap-allocated, growable vector of characters.
- `&str` is a type&sup1; that's used to slice into `String`s.
- String literals like `"foo"` are of type `&str`.
- Strings are guaranteed to be encoded using UTF-8

```rust
let s: &str = "galaxy";
let s2: String = "galaxy".to_string();
let s3: String = String::from("galaxy");
let s4: &str = &s3;
```

&sup1;`str` is an unsized type, which doesn't have a compile-time known size,
and therefore cannot exist by itself.

---
### Tuples ###
- Fixed-size, ordered, heterogeneous lists
- Index into tuples with `foo.0`, `foo.1`, etc.
- Can be destructured in `let` bindings

```rust
let foo: (i32, char, f64) = (72, 'H', 5.1);
let (x, y, z) = (72, 'H', 5.1);
let (a, b, c) = foo; // a = 72, b = 'H', c = 5.1
```

---
### Casting ###

- Cast between types with `as`:

```rust
let x: i32 = 100;
let y: u32 = x as u32;
```

- Naturally, you can only cast between types that are safe to cast between.
    - No casting `[i16; 4]` to `char`! (This is called a "non-scalar" cast)
    - There are unsafe mechanisms to overcome this, if you know what you're doing.

---
### `Vec<T>` ###

- A standard library type: you don't need to import anything.
- A `Vec` (read "vector") is a heap-allocated growable array.
    - (cf. Java's `ArrayList`, C++'s `std::vector`, etc.)
- `<T>` denotes a generic type.
    - The type of a `Vec` of `i32`s is `Vec<i32>`.
- Create `Vec`s with `Vec::new()` or the `vec!` macro.
    - `Vec::new()` is an example of namespacing. `new` is a function defined for
      the `Vec` struct.

---
### `Vec<T>` ###
```rust
// Explicit typing
let v0: Vec<i32> = Vec::new();

// v1 and v2 are equal
let mut v1 = Vec::new();
v1.push(1);
v1.push(2);
v1.push(3);

let v2 = vec![1, 2, 3];
```

```rust
// v3 and v4 are equal
let v3 = vec![0; 4];
let v4 = vec![0, 0, 0, 0];
```

---
### `Vec<T>` ###

```rust
let v2 = vec![1, 2, 3];
let x = v2[2]; // 3
```

- Like arrays, vectors can be indexed with `[]`.
    - You can't index a vector with an i32/i64/etc.
    - You must use a `usize` because `usize` is guaranteed to be the same size as a pointer.
    - Other integers can be cast to `usize`:
      ```rust
      let i: i8 = 2;
      let y = v2[i as usize];
      ```

- Vectors has an extensive stdlib method list, which can be found at the
  [offical Rust documentation](https://doc.rust-lang.org/stable/std/vec/).

---
### References ###

- Reference *types* are written with an `&`: `&i32`.
- References can be taken with `&` (like C/C++).
- References can be _dereferenced_ with `*` (like C/C++).
- References are guaranteed to be valid.
    - Validity is enforced through compile-time checks!
- These are *not* the same as pointers!
- Reference lifetimes are pretty complex, as we'll explore later on in the course.

```rust
let x = 12;
let ref_x = &x;
println!("{}", *ref_x); // 12
```

---
## Control Flow ##

---
### If Statements ###

```rust
if x > 0 {
    10
} else if x == 0 {
    0
} else {
    println!("Not greater than zero!");
    -10
}
```
- No parens necessary.
- Entire if statement evaluates to one expression, so every arm must end with
  an expression of the same type.
    - That type can be unit `()`:

```rust
if x <= 0 {
    println!("Too small!");
}
```

---
### Loops ###
- Loops come in three flavors: `while`, `loop`, and `for`.
    - `break` and `continue` exist just like in most languages

- `while` works just like you'd expect:

```rust
let mut x = 0;
while x < 100 {
    x += 1;
    println!("x: {}", x);
}
```

---
### Loops ###
- `loop` is equivalent to `while true`, a common pattern.
    - Plus, the compiler can make optimizations knowing that it's infinite.

```rust
let mut x = 0;
loop {
    x += 1;
    println!("x: {}", x);
}
```

---
### Loops ###
- `for` is the most different from most C-like languages
     - `for` loops use an _iterator expression_:
     - `n..m` creates an iterator from n to m (exclusive).
     - Some data structures can be used as iterators, like arrays and `Vec`s.

```rust
// Loops from 0 to 9.
for x in 0..10 {
    println!("{}", x);
}

let xs = [0, 1, 2, 3, 4];
// Loop through elements in a slice of `xs`.
for x in &xs {
    println!("{}", x);
}
```

---
## Functions ##

```rust
fn foo(x: T, y: U, z: V) -> T {
    // ...
}
```

- `foo` is a function that takes three parameters:
    - `x` of type `T`
    - `y` of type `U`
    - `z` of type `V`
- `foo` returns a `T`.

- Must explicitly define argument and return types.
    - The compiler is actually smart enough to figure this out for you, but
      Rust's designers decided it was better practice to force explicit function
      typing.

---
### Functions

- The final expression in a function is its return value.
    - Use `return` for _early_ returns from a function.

```rust
fn square(n: i32) -> i32 {
    n * n
}

fn squareish(n: i32) -> i32 {
    if n < 5 { return n; }
    n * n
}

fn square_bad(n: i32) -> i32 {
    n * n;
}
```

- The last one won't even compile!
  - Why? It ends in a semicolon, so it evaluates to `()`.

---
### Function Objects ###

- Several things can be used as function objects:
    - Function pointers (a reference to a normal function)
    - Closures (covered later)
- Much more straightforward than C function pointers:

```rust
let x: fn(i32) -> i32 = square;
```

- Can be passed by reference:

```rust
fn apply_twice(f: &Fn(i32) -> i32, x: i32) -> i32 {
    f(f(x))
}

// ...

let y = apply_twice(&square, 5);
```

---
## Macros!

- Macros are like functions, but they're named with `!` at the end.
- Can do generally very powerful stuff.
    - They actually generate code at compile time!
- Call and use macros like functions.
- You can define your own with `macro_rules! macro_name` blocks.
    - These are *very* complicated (especially procedural macros).
    - We will not cover writing custom macros in this course.
- Because they're so powerful, a lot of common utilities are defined as macros.

---
### `print!` & `println!` ###
- Print stuff out. Yay.
- Use `{}` for general string interpolation, and `{:?}` for debug printing.
    - Some types can only be printed with `{:?}`, like arrays and `Vec`s.

```rust
print!("{}, {}, {}", "foo", 3, true);
// => foo, 3, true
println!("{:?}, {:?}", "foo", [1, 2, 3]);
// => "foo", [1, 2, 3]
```

---
### `format!` ###
- Uses `println!`-style string interpolation to create formatted `String`s.

```rust
let fmted = format!("{}, {:x}, {:?}", 12, 155, Some("Hello"));
// fmted == "12, 9b, Some("Hello")"
```

---
### `panic!(msg)`
- Exits current task with given message.
- Don't do this lightly! It is better to handle and report errors explicitly.

```rust
if x < 0 {
    panic!("Oh noes!");
}
```

---
### `assert!` & `assert_eq!`

- `assert!(condition)` panics if `condition` is `false`.
- `assert_eq!(left, right)` panics if `left != right`.
- `debug_assert!(condition)` and `debug_assert_eq!(left, right)` work in debug build, but omitted in release build.
- Useful for testing and catching illegal conditions.

```rust
#[test]
fn test_something() {
    let actual = 1 + 2;
    assert!(actual == 3);
    assert_eq!(3, actual);
}
```

---
### `unreachable!()`

- Used to indicate that some code should not be reached.
- `panic!`s when reached.
- Can be useful to track down unexpected bugs (e.g. optimization bugs).

```rust
if false {
    unreachable!();
}
```

---
### `unimplemented!()`

- Shorthand for `panic!("not yet implemented")`.

```rust
fn sum(x: Vec<i32>) -> i32 {
    // TODO
    unimplemented!();
}
```

---
## Match statements ###
- `switch` on steroids.

```rust
let x = 3;

match x {
    1 => println!("one fish"),  // <- comma required
    2 => {
        println!("two fish");
        println!("two fish");
    },  // <- comma optional when using braces
    3 | 4 => println!("three or four, dunno"), // we can use several patterns
    _ => println!("no fish for you"), // "otherwise" case
}
```

- `match` takes an expression (`x`) and branches on a list of `value => expression` statements.
- The entire match evaluates to one expression.
    - Like `if`, all arms must evaluate to the same type.
- `_` is commonly used as a catch-all (cf. Haskell, OCaml).

---
## Match statements ###
```rust
let x = 3;
let y = -3;

match (x, y) {
    (1, 1) => println!("one"),
    (2, j) => println!("two, {}", j),
    (_, 3) => println!("three"),
    (i, j) if i > 5 && j < 0 => println!("On guard!"),
    (_, _) => println!(":<"),
}
```

- The matched expression can be any expression (l-value), including tuples and function calls.
    - Matches can bind variables. `_` is a throw-away variable name.
- You _must_ write an exhaustive match in order to compile.
- Use `if`-guards to constrain a match to certain conditions.
- Patterns can get very complex.

---
# Rust Environment & Tools

---
## Rustc ##

- Rust's compiler is `rustc`.
- Run `rustc your_program.rs` to compile into an executable `your_program`.
    - Things like warnings are enabled by default.
    - Read all of the output! It may be verbose but it is *very* useful.
- `rustc` doesn't need to be called once for each file like in C.
    - The build dependency tree is inferred from module declarations in the
      Rust code (starting at `main.rs` or `lib.rs`).
- Typically, you'll instead use `cargo`, Rust's package manager and build system.

---
## Cargo ##

- Rust's package manager & build tool
- Create a new project:
    - `cargo new project_name` (executable)
    - `cargo new project_name --lib` (library)
- Build your project: `cargo build`
- Run your project: `cargo run`
- Use `--release` flag to enable release build profile (longer compilation times, but often much faster binary)
- Typecheck code without building it: `cargo check` (much faster)
- Run your benchmarks if you have any: `cargo bench` (requires Nightly toolchain)
- Run your tests: `cargo test`
    - These get tedious to type, so shell alias to your heart's content,
      e.g., `cargob`/`cb` and `cargot`/`ct`
- Magic, right? How does this work?

---
### Cargo.toml ###

- Cargo uses the `Cargo.toml` file to declare and manage dependencies and
  project metadata.
    - TOML is a simple format similar to INI.
- More in your first homework assignments.

```toml
[package]
name = "my_cool_project"
version = "0.1.0"
authors = ["My name"]
edition = "2018"

[dependencies]
uuid = "0.1"
rand = "0.3"

[profile.release]
opt-level = 3
debug = false
```

---
### `cargo test`

- A test is any function annotated with `#[test]`.
- `cargo test` will run all annotated functions in your project.
- Any function which executes without crashing (`panic!`ing) succeeds.
- Use `assert!` (or `assert_eq!`) to check conditions (and `panic!` on failure)

```rust
#[test]
fn it_works() {
    // ...
}
```


---
## Ownership & Borrowing

- Explicit ownership is the biggest new feature that Rust brings to the table!
- Ownership is all&sup1; checked at compile time!
- Newcomers to Rust often find themselves "fighting with the borrow checker"
   trying to get their code to compile

&sup1;*mostly*

???

- The ownership model is the biggest thing that Rust brings to the table, its
  claim to fame.
- Ownership is something that's checked at compile time and has as little
  runtime cost as possible.
- So it's zero (or very little) runtime cost, but you pay for it with a longer
  compilation time and learning curve. Which is where the phrase "fighitng with
  the borrow checker" comes from, when you have to work around the compiler's
  restrictions to figure out how to do what you want.

---
## Ownership

- A variable binding _takes ownership_ of its data.
    - A piece of data can only have one owner at a time.
- When a binding goes out of scope, the bound data is released automatically.
    - For heap-allocated data, this means de-allocation.
- Data _must be guaranteed_ to outlive its references.

```rust
fn foo() {
    // Creates a Vec object.
    // Gives ownership of the Vec object to v1.
    let mut v1 = vec![1, 2, 3];

    v1.pop();
    v1.push(4);

    // At the end of the scope, v1 goes out of scope.
    // v1 still owns the Vec object, so it can be cleaned up.
}
```

???

So here are the basics.
- When you introduce a variable binding, it takes ownership of its data. And a
  piece of data can only have one owner at a time.
- When a variable binding goes out of scope, nothing has access to the data
  anymore, so it can be released. Which means, if it's on the heap, it can be
  de-allocated.
- And data must be guaranteed to outlive its references. Or, all references are
  guaranteed to be valid.

---
## Move Semantics

```rust
let v1 = vec![1, 2, 3];

// Ownership of the Vec object moves to v2.
let v2 = v1;

println!("{}", v1[2]); // error: use of moved value `v1`
```

- `let v2 = v1;`
    - We don't want to copy the data, since that's expensive.
    - The data cannot have multiple owners.
    - Solution: move the Vec's ownership into `v2`, and declare `v1` invalid.
- `println!("{}", v1[2]);`
    - We know that `v1` is no longer a valid variable binding, so this is an error.
- Rust can reason about this at compile time, so it throws a compiler error.

???

Here's another example:
- Line 1: declare a vector v1.
- Line 2: let v2 = v1. Ownership of the vector is moved from v1 to v2.
  - we don't want to move or copy the data, that's expensive and causes other
    bugs
  - we already know the data can't have multiple owners
- Line 3: try to print v1.
  - but since the vector has been moved out of v1, it is no longer a valid
    variable binding
- all of this happens at compile time.

---
## Move Semantics

- Moving ownership is a compile-time semantic; it doesn't involve moving data
  during your program.
- Moves are automatic (via assignments); no need to use something like C++'s
  `std::move`.
    - However, there are functions like `std::mem::replace` in Rust to provide
      advanced ownership management.

???

- Moving ownership is an impliict operation done at compile time. No data is
  moved or copied around when your program is being run.
- The movement of data is automatic, you don't need to call anything like
  std::move (as in C++).
- But you can do more fine-grained ownership or memory movement with a number of
  standrard library functions, like std::mem::replace.

---
## Ownership

- Ownership does not always have to be moved.
- What would happen if it did? Rust would get very tedious to write:

```rust
fn vector_length(v: Vec<i32>) -> Vec<i32> {
    // Do whatever here,
    // then return ownership of `v` back to the caller
}
```
- You could imagine that this does not scale well either.
    - The more variables you had to hand back, the longer your return type would be!
    - Imagine having to pass ownership around for 5+ variables at a time :(

???

- Ownership doesn't have to be moved.
- If it did, you would also have to return ownership at the end of every
  function, or have all of your variables constantly going out of scope.
- This gets absurd very quickly, imagine having to return all of your function
  arguments as return values just to make sure they don't go out of scope.

---
## Borrowing

- Obviously, this is not the case.
- Instead of transferring ownership, we can _borrow_ data.
- A variable's data can be borrowed by taking a reference to the variable;
  ownership doesn't change.
    - When a reference goes out of scope, the borrow is over.
    - The original variable retains ownership throughout.

```rust
let v = vec![1, 2, 3];

// v_ref is a reference to v.
let v_ref = &v;

// use v_ref to access the data in the vector v.
assert_eq!(v[1], v_ref[1]);
```

???

- Obviously, this is not the case in Rust, otherwise the language would be
  impossible to use.
- Instead, we can temporarily transfer ownership by borrowing data.
- The way that borrowing works is: you can take a reference to the original
  variable and use it to access the data.
- When a reference goes out of scope, the borrow is over.
- However, the original variable retains ownership during the borrow and
  afterwards.

---
## Borrowing

- Caveat: this adds restrictions to the original variable.
- Ownership cannot be transferred from a variable while references to it exist.
    - That would invalidate the reference.

```rust
let v = vec![1, 2, 3];

// v_ref is a reference to v.
let v_ref = &v;

// Moving ownership to v_new would invalidate v_ref.
// error: cannot move out of `v` because it is borrowed
let v_new = v;
```

???

- This adds a caveat: ownership cannot be ransferred *from* a variable that is
  currently being borrowed, because that would invalidate the reference.

---
## Borrowing

```rust
/// `length` only needs `vector` temporarily, so it is borrowed.
fn length(vec_ref: &Vec<i32>) -> usize {
    // vec_ref is auto-dereferenced when you call methods on it.
    vec_ref.len()
    // you can also explicitly dereference.
    // (*vec_ref).len()
}

fn main() {
    let vector = vec![];
    length(&vector);
    println!("{:?}", vector); // this is fine
}
```
- Note the type of `length`: `vec_ref` is passed by reference, so it's now an `&Vec<i32>`.
- References, like bindings, are *immutable* by default.
- The borrow is over after the reference goes out of scope (at the end of `length`).

---
## Borrowing

```rust
/// `push` needs to modify `vector` so it is borrowed mutably.
fn push(vec_ref: &mut Vec<i32>, x: i32) {
    vec_ref.push(x);
}

fn main() {
    let mut vector: Vec<i32> = vec![];
    let vector_ref: &mut Vec<i32> = &mut vector;
    push(vector_ref, 4);
}
```
- Variables can be borrowed by _mutable_ reference: `&mut vec_ref`.
    - `vec_ref` is a reference to a mutable `Vec`.
    - The type is `&mut Vec<i32>`, not `&Vec<i32>`.
- Different from a reference which is variable.

---
## Borrowing

```rust
/// `push` needs to modify `vector` so it is borrowed mutably.
fn push2(vec_ref: &mut Vec<i32>, x: i32) {
    // error: cannot move out of borrowed content.
    let vector = *vec_ref;
    vector.push(x);
}

fn main() {
    let mut vector = vec![];
    push2(&mut vector, 4);
}
```
- Error! You can't dereference `vec_ref` into a variable binding because that
  would change the ownership of the data.

---
## Borrowing

- Rust will auto-dereference variables...
    - When making method calls on a reference.
    - When passing a reference as a function argument.

```rust
/// `length` only needs `vector` temporarily, so it is borrowed.
fn length(vec_ref: &&Vec<i32>) -> usize {
    // vec_ref is auto-dereferenced when you call methods on it.
    vec_ref.len()
}

fn main() {
    let vector = vec![];
    length(&&&&&&&&&&&&vector);
}
```

---
## Borrowing

- You will have to dereference variables...
    - When writing into them.
    - And other times that usage may be ambiguous.

```rust
let mut a = 5;
let ref_a = &mut a;
*ref_a = 4;
println!("{}", *ref_a + 4);
// ==> 8
```

---
## `ref`

```rust
let mut vector = vec![0];

{
      // These are equivalent
      let ref1 = &vector;
      let ref ref2 = vector;
      assert_eq!(ref1, ref2);
}

let ref mut ref3 = vector;
ref3.push(1);
```

- When binding a variable, `ref` can be applied to make the variable a reference to the assigned value.
    - Take a mutable reference with `ref mut`.
- This is most useful in `match` statements when destructuring patterns.

---
## `ref`

```rust
let mut vectors = (vec![0], vec![1]);
match vectors {
    (ref v1, ref mut v2) => {
        v1.len();
        v2.push(2);
    }
}
```
- Use `ref` and `ref mut` when binding variables inside match statements.

---
## `Copy` Types

- Rust defines a trait&sup1; named `Copy` that signifies that a type may be
    copied instead whenever it would be moved.
- Most primitive types are `Copy` (`i32`, `f64`, `char`, `bool`, etc.)
- Types that contain references may not be `Copy` (e.g. `Vec`, `String`).
```rust
let x: i32 = 12;
let y = x; // `i32` is `Copy`, so it's not moved :D
println!("x still works: {}, and so does y: {}", x, y);
```

&sup1; Like a Java interface or Haskell typeclass

???

This is why we've been using Vectors as examples in this slide set.

---
## Borrowing Rules
##### _The Holy Grail of Rust_
Learn these rules, and they will serve you well.

- You can't keep borrowing something after it stops existing.
- One object may have many immutable references to it (`&T`).
- **OR** _exactly one_ mutable reference (`&mut T`) (not both).
- That's it!

![](img/holy-grail.jpg)

---
### Borrowing Prevents...

- Iterator invalidation due to mutating a collection you're iterating over.
- This pattern can be written in C, C++, Java, Python, Javascript...
    - But may result in, e.g, `ConcurrentModificationException` (at runtime!)

```rust
let mut vs = vec![1,2,3,4];
for v in &vs {
    vs.pop();
    // ERROR: cannot borrow `vs` as mutable because
    // it is also borrowed as immutable
}
```

- `pop` needs to borrow `vs` as mutable in order to modify the data.
- But `vs` is being borrowed as immutable by the loop!

---
### Borrowing Prevents...

- Use-after-free
- Valid in C, C++...

```rust
let y: &i32;
{
    let x = 5;
    y = &x; // error: `x` does not live long enough
}
println!("{}", *y);
```

- The full error message:

```
error: `x` does not live long enough
note: reference must be valid for the block suffix following statement
    0 at 1:16
...but borrowed value is only valid for the block suffix
    following statement 0 at 4:18
```

- This eliminates a _huge_ number of memory safety bugs _at compile time_.

???

As a side note, this technique of creating a block to limit the scope of a
variable (in this case x) is pretty useful.

---
## Example: Vectors

- You can iterate over `Vec`s in three different ways:

```rust
let mut vs = vec![0,1,2,3,4,5,6];

// Borrow immutably
for v in &vs { // Can also write `for v in vs.iter()`
    println!("I'm borrowing {}.", v);
}

// Borrow mutably
for v in &mut vs { // Can also write `for v in vs.iter_mut()`
    *v = *v + 1;
    println!("I'm mutably borrowing {}.", v);
}

// Take ownership of the whole vector
for v in vs { // Can also write `for v in vs.into_iter()`
    println!("I now own {}! AHAHAHAHA!", v);
}

// `vs` is no longer valid
```
