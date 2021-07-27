+++
title = "Bitwise cheat sheet for Rust"
date = 2021-07-06
+++

## Operators

```txt
AND         &
OR          |
NOT         !
XOR         ^
Left shift  <<
Right shift >>
```

### Get a bit

```rust
(i >> n) & 1
```

### Set a bit

#### Set to 1

```rust
i | (1 << n)
```

#### Set to 0

```rust
i & !(1 << n)
```

### Toggle a bit

```rust
i ^ (1 << n)
```

Source: [https://nicolwk.medium.com/bitwise-operations-cheat-sheet-743e09aec5b5](https://nicolwk.medium.com/bitwise-operations-cheat-sheet-743e09aec5b5)
