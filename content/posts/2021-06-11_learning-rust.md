+++
title = "Learning Rust by building a server client chat"
date = 2021-06-11
+++

## Overview

[So far this is just a draft, and requires more thought]

We will do this once or twice to test it out first before we commit to this.
 
This an exercise in learning Rust together on stream.
We will do this for one hour on [date tbc], and announce this on the Discord
server and in chat one hour in advance.

Goal suggestions: 
* Server / client chat (possibly a MUD if someone fancies that)
* Todo app
* Ini file parser
 
## Prerequisites 

* Rust installed.
* A working text editor
* Telnet or netcat (or we can't test the server before the client)

## Format

1. Go over some functionality.
2. Answer questions in the process.

## Goal

To have a basic understanding of Rust and to be able to
create something more than just `hello world`.

### Topics to cover

* [X] Project creation
* [X] Compile and run (--release)
* [X] Variable bindings
* [X] Control flow and loops
* [X] Match
* [X] Functions
* [X] Structs
* [X] Enums
* [X] Mutability / references / &ref / *deref
* [X] Traits
* [ ] IO
* [ ] Threads
* [ ] `Mutex` and `Arc`
* [ ] Lifetimes
* [ ] Using the api docs
* [ ] Vectors, Arrays and Slices

## Plan

### Project creation and running

```sh
cargo new helloworld
```

```sh
cargo run
```

```sh
cargo build
./target/debug/helloworld
```

### Variable bindings

Talk about:
* Signed vs unsigned int
* `usize` / `isize` (why do we have it)
* Specify type
* Type inference
* Const and static

```rust
let x = 1;
let mut y = 2;
```


### Control flow

Talk about:
* Assignment via `if`
* Assignment from a `loop`
* Breaking a loop

```rust
let cond = true;

if cond {
    // body
}

loop {
}

while cond {
}

for i in 0..100 {
}
```

### Match

* Curly braces only needed if there is a block
* Assignment via `match` also works for `loop` but not for `for` and `while`

```rust
let value = 1u8;

match value {
    0 => {}
    1 => {}
    5 => {}
    _ => { /* Wildcard */ }
}

let value = true;

let data = match value {
    true => 1,
    false => 2,
}; // semicolon is important
```

### Functions

A function

Talk about:
* A function name ending with an `!` means we are calling macro
* Implicit return

```rust
fn do_thing() {
}

```

A function with one or more arguments

```rust
fn do_thing(arg1: u32, arg2: u32) {
}
```

A function with a return value

```rust
fn do_thing() -> u32 {
    return 123;
}

fn do_thing_implicit_return() -> u32 {
    123
}
```

### Structs

A struct is a way to organise data

Talk about:
* Methods vs associated functions
* Calling a method
* Calling a method like an associated function

Note: Structs can't contain fields that are of the same type as the struct,
as the compiler has to know the size of the struct at compile time.
This would create an infinitely sized struct.


```rust
struct Thing {
    field_1: u8,
    field_2: u8,
}
```

We can add methods to structs:

```rust
impl Thing {
    fn do_thing(&self) {
        let sum = self.field_1 + self.field_2;
        println!("sum: {}", sum);
    }
}
```

### Enum

Talk about:
* Unlike other languages enums can hold complex data structure
* Enums are a good way to represent state
* An enum can only be **one** of it's variants

```rust
enum State {
    Stopped,
    Running(String),
    Paused { duration: u32, count: u32 }
    SubState(State)
}
```

Enums combined with `match` makes for happy times

```rust
enum Food {
    Hamburger,
    AvocadoToast,
}

let food = Food::Hamburger;
let mut burger_counter = 0;

match food {
    Food::Hamburger => {
        printlin!("I love hamburgers");
        burger_counter += 1;
    },
    Food::AvocadoToast => println!("Avocado toast sounds like fun"),
}
```

### References and mutability

Talk about:
* Can't change a value unless it's mutable*
* Passing a reference to a value is done with `&`
* Passing a mutable ref is done with `&mut`
* `*` will dereference
* When to pass a reference and when to move

```rust
let mut byte = 0u8;

change(&mut byte);

fn change(b: &mut u8) {
    *b = 1; // deref `b` and give it a new value
}
```

### Traits

Talk about:
* Value of traits
* Trait objects (`&dyn Trait` or, if moved: `Box<dyn Trait>`)
* Can't make trait objects if a trait has an associated function
* `impl` Trait in arg pos
* `impl` Trait in return pos

```rust
trait Describe {
    fn describe(&self) -> String;
}
```

