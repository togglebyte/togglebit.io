+++
title = "Rust lifetime rules"
date = 2021-09-13
+++

Elision rules are as follows:

### 1. 
Each elided lifetime in input position becomes a distinct lifetime parameter.

```rust
pub fn do_something<'a: 'b, 'b>(input: &'a str, other_input: &'b str) -> &'a str {
    input
}
```

### 2. 
If there is exactly one input lifetime position (elided or not), that lifetime is assigned to all elided output lifetimes.

```rust
pub fn first_do_something(input: &str) -> (&str, &str) {
    (input, input)
}
```

### 3. 
If there are multiple input lifetime positions, but one of them is &self or &mut self, the lifetime of self is assigned to all elided output lifetimes.


```rust
struct Config { 
    name: String
}

impl Config {
    fn name(&self, other_useless_val: &str) -> &str {
        &self.name // <- same lifetime as &self
    }
}
```

### 4.
Otherwise, it is an error to elide an output lifetime.


* [Source](https://doc.rust-lang.org/nomicon/lifetime-elision.html)
* [Common lifetime misconceptions](https://github.com/pretzelhammer/rust-blog/blob/master/posts/common-rust-lifetime-misconceptions.md)
