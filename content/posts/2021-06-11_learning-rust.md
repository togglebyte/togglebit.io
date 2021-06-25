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
* [ ] variable bindings
* [X] control flow and loops
* [X] match
* [ ] functions
* [ ] structs
* [ ] mutability / references / &ref / *deref
* [ ] traits
* [ ] IO
* [ ] Threads
* [ ] `Mutex` and `Arc`
* [ ] lifetimes

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

```rust
let cond = true;

if cond {
    // body
}

loop {
}

while cond {
}

for i in vec![1, 2, 3] {
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
