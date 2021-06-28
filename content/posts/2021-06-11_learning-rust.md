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

* [X] project creation
* [X] compile and run (--release)
* [X] variable bindings
* [X] control flow and loops
* [X] match
* [X] functions
* [X] structs
* [ ] mutability / references / &ref / *deref
* [ ] traits
* [ ] IO
* [ ] Threads
* [ ] `Mutex` and `Arc`
* [ ] lifetimes
* [ ] Using the api docs
* [ ] Vectors, Arrays and Slices

## Plan

### Project creation and running:

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

### Variable bindings:

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


### Control flow:

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

### Match:

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

### Functions:

A function

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

### Structs:

A struct is a way to organise data

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

### References and mutability

Talk about:
* Can't change a value unless it's mutable*
* Passing a reference to a value is done with `&`
* Passing a mutable ref is done with `&mut`
* `*` will dereference

```rust
let mut byte = 0u8;

change(&mut byte);

fn change(b: &mut u8) {
    *b = 1; // deref `b` and give it a new value
}
```
