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

## Examples

### Clearing bits

#### Bit mask

```rust
let mask = 0b00001111;
let value = 0b1110101;
let result = mask & value;
assert_eq!(result, 0b00000101);
```

### Shifting twice

```rust
let value: u8 = 0b1110101;
let result = value << 4 >> 4;
assert_eq!(result, 0b00000101);
```

To clear the other half, change `<< 4 >> 4` to `>> 4 << 4`.
