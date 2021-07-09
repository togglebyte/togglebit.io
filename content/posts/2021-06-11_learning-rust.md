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

**Goal: Multi threaded server / client chat**
 
## Prerequisites 

* Rust installed
* A working text editor
* (Optional) Telnet or netcat (or we can't test the server before creating the client)

## Format

1. Go over some functionality from the list.
2. Implement functionality for the project
3. Answer questions in the process.

## Goal

To have a basic understanding of Rust and to be able to
create something more than just `hello world`.

### Topics to cover
* [X] Project creation
* [X] Compile and run (--release)

1.  [X] Variable bindings
2.  [X] Mutability / references / &ref / *deref
3.  [X] Functions and closures
4.  [X] Importing modules
5.  [X] Vectors, Arrays and Slices
6.  [X] Strings
7.  [X] Match
8.  [X] Control flow and loops
9.  [X] Structs and tuples
10. [X] Enums, Result and Option
11. [X] Traits
12. [X] Threads, `Mutex` and `Arc`
13. [X] Channels
14. [X] Modules
15. [X] Error handling and type alias
16. [X] IO
17. [X] Iterators
18. [X] Using the api docs
19. [ ] Lifetimes

## Plan

* Goal 1: cover 1 - 9
* Goal 2: cover 10 - 13
* Goal 3: cover 14 - 15
* Goal 4: cover 16 - ...

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

```sh
cargo new --lib common
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

### Functions and closures

A function

Talk about:
* A function name ending with an `!` means we are calling macro
* Implicit return
* Closures as callbacks

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

A closure

```rust
let cl = |arg1: u32| { 
    println!("{}", arg1);
};

let string = "Hello closure".to_string();
cl(string);
```

### Importing from std lib

Talk about:
* Importing from std library
* Same applies to third party crates

Importing a single path 

```rust
use std::net::TcpStream;
```

Importing multiple paths from the same module

```rust
use std::net::{TcpStream, TcpListener};
```

Importing a path under another name

```rust
use std::net::TcpStream as Stream;
```

### Vectors, arrays and slices

Talk about:
* Vectors are dynamically sized
* Arrays are fixed size
* Slices are a view into either a vector or array
* Going over the capacity of a vector will move all the data in the vector to a
  new memory location that can fit all the elements
* Pushing and popping
* Why we create slices rather than cloning / copying a `Vec<T>` or `[T]` (array):
  * Ranges
  * Cost

Creating vectors

```rust 
// Create a new vector
let mut a_vec: Vec<usize> = Vec::new();

// Create a new vector with a capacity of ten.
let mut a_vec: Vec<usize> = Vec::with_capacity(10);

// Create a new vector with a capacity of two and two elements
let mut a_vec: Vec<usize> = vec![1, 2];

// Create a new vector with a capacity of 200, and it's full of ones
let mut a_vec: Vec<usize> = vec![1; 200];
```

Creating an array

```rust
// An array with three bytes in it
let array_of_bytes = [0u8, 1, 2, 3];

// An array with 200 bytes in it, where every byte is zero
let array_of_bytes = [0u8; 200];
```

Creating a slice

```rust
let data = vec![1u8, 2, 3, 4];
// We specify the type here to prevent
let slice = &data[1..2]; // a slice containing 2, 3
```

Looping over all the values in a vector.
If we didn't use a reference (`&`) we would consume the vector

```rust
let v = vec![1, 2];

for value in &v {
    println!("{}", value);
}
```

Pushing and popping.
Popping returns an `Option<T>`

```rust
let mut v = vec![];

v.push(1); // v = vec![1]
v.push(2); // v = vec![1, 2]
v.pop();   // v = vec![1]
v.pop();   // v = vec![]
```

### String and &str (string slice)

Talk about:
* Why do we need more than one type for a string
* Strings are just byte vectors under the hood
* A `String` in Rust is always valid utf8
* The `len()` function of a string returns number of bytes (not number of chars)
* A `String` can deref to a `&str`
* Useful `String` methods: `trim`, `split_whitespace`, `push_str`
* Useful `str` methods: `starts_with`, `ends_with`, `contains`
* Briefly show the list of available functions for `String` and `&str`

```rust
let string = String::new();
let string_slice: &str = &string;
let string_slice = string.as_str();
let string_slice = &string[..];
```

### Structs

A struct is a way to organise data

Talk about:
* Methods vs associated functions
* Calling a method
* Calling a method like an associated function
* Tuple structs
* New type pattern is just a word for wrapping an existing type in a tuple
  struct. There is nothing magical about this.

Note: Structs can't contain fields that are of the same type as the struct,
as the compiler has to know the size of the struct at compile time.
This would create an infinitely sized struct.


```rust
struct Thing {
    field_1: u8,
    field_2: u8,
}
```

We can add methods to structs

```rust
impl Thing {
    fn do_thing(&self) {
        let sum = self.field_1 + self.field_2;
        println!("sum: {}", sum);
    }
}
```

Tuple struct

```rust
struct Pos(u32, u32);

let pos = Pos(12, 33);
let (x, y) = (pos.0, pos.1);
println!("x: {}, y: {}", x, y);
```

New type pattern

```rust
struct UserId(u32);

let user_id = UserId(123);
let val: u32 = user_id.0;
```

### Enum

Talk about:
* Unlike other languages enums can hold complex data structure
* Enums are a good way to represent state
* An enum can only be **one** of it's variants
* Result and Option

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

### Traits

Talk about:
* Value of traits
* Required traits (e.g `Read` and `Write` for IO)
* Trait objects (`&dyn Trait` or, if moved: `Box<dyn Trait>`)
* Can't make trait objects if a trait has an associated function
* `impl` Trait in arg pos
* `impl` Trait in return pos

```rust
trait Describe {
    fn describe(&self) -> String;
}
```

### Channels

Talk about:
* Passing data
* Sender is clonable

```rust
use std::sync::mpsc;
use std::thread;

let (sender, receiver) = mpsc::channel();

thread::spawn(move || {
    loop {
        if let Ok(val) = receiver.recv() {
            println!("Received: {}", val);
        }
    }
});

let mut counter = 0usize;

loop {
    sender.send(counter);
    counter += 1;
    thread::sleep_ms(1000);
}
```

### Threads

Talk about:
* Not everything can be sent between threads (only `Send` and `Sync`)
* `Mutex` and `Arc` combinations
* Can use `Arc` without `Mutex`, just has no mutability


Creating a thread

```rust
use std::thread;

thread::spawn(|| {
    printline!("Hello from another thread");
});
```

`Arc` and `Mutex` combo: modify a string in two different threads.

```rust
use std::sync::{Mutex, Arc};
use std::thread;

let s = String::new();
let s = Arc::new(Mutex::new(s));

// Thread 1
let s_clone = Arc::clone(&s);
thread::spawn(move ||  s_clone.lock().map(|mut s| s.push_str("A")));

// Thread 2
let s_clone = Arc::clone(&s);
thread::spawn(move ||  s_clone.lock().map(|mut s| s.push_str("B")));
```

### IO

Talk about:
* Reading in files (as bytes and text)
* Creating files

Reading bytes from a file.
A similar approach can be used to read bytes from a network socket (where
reading zero bytes most likely means connection closed)

```rust
use std::io::Read;
use std::fs::File;

let mut buf = [0u8; 1024];
let file = File::open("/tmp/somefile.txt").expect("Failed to open the file");
let bytes_read = file.read(&mut buf).unwrap();

let file_content: &[u8] = &buf[..bytes_read];
```

Read an entire file as a string

```rust
use std::fs::read_to_string;

let file_content = read_to_string("/tmp/somefile.txt").unwrap();
```

### Iterators
Talk about:
* Iterator adaptors
* Chaining adaptors
* Collecting
* `iter`, `into_iter`, `iter_mut`
* `map` is lazy and `for_each` can't return anything but a unit

Iterate and produce a new `Vec<T>` that has all the values squared

```rust
let mut v = vec![1, 2, 3];
let new_vec = v
    .iter()
    .map(|val| val * val)
    .collect::<Vec<usize>>();
```

Modify all elements in a `Vec<T>`

```rust
let mut v = vec![1, 2, 3];
v.iter_mut().for_each(|val| val *= val);
```

Combining adaptors

```rust
let v = vec![1, 2, 3, 4];

let new_values = v
    .iter()
    .enumerate() 
    .filter(|number| number > 2)
    .map(|number| number += 10)
    .collect::<Vec<_>>();
```

Flatten an iterator

```rust
let v = vec![
    vec![1, 2],
    vec![3, 4],
];

for val in v.into_iter().flatten() {
    eprintln!("{:?}", val);
}
```

### Modules

Talk about:
* Files as modules
* The structure of sub modules
* Multiple modules in one file
* Hierarchy of modules

```rust
// main.rs
mod parent {
    mod child {
    }
}

parent::child
```

File system layout for a module named "submod",
which has its own sub module named "misc"

```sh
project
  \_ submod \
     \_ mod.rs
     \_ misc.rs
  \_ main.rs
```


### Error handling

Talk about:
* Create our own error type
* Passing errors down the chain
* Using `thiserr` and `anyhow`

Implementing your own error type that works with the `?` operator.

```rust
use std::io::Error as IoErr;

type Result<T> = std::result::Result<T, OurErr>;

pub enum OurErr {
    Io(IoErr),
    Misc(String),
}

impl From<IoErr> for OurErr {
    fn from(e: IoErr) -> Self {
        Self::Io(e)
    }
}
```

### Using api docs

Talk about:
* Finding functions
* Looking at return types
* Looking at args

Open docs in default browser:

```sh
cargo doc --open
```

Local version of std lib api docs and "The Book":

Open docs

```sh
rustup doc
```

Go directly to std lib api

```sh
rustup doc --std
```

### Lifetimes

Talk about:
* Structs having references
* `'static`: As a trait bound, it means the type does not contain any non-static references.
* Read up on: https://doc.rust-lang.org/nomicon/lifetimes.html

```rust
struct Application<'a> {
    name: &'a str
}

struct Config<'a> {
    name: &'a str,
}

let app_name = "Blorp".to_string();

let app = Application { name: &app_name };
let config = Config { name: &app_name };
```

