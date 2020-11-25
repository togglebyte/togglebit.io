+++
title = "Zig notes"
date = 2020-11-25
+++

# Zig notes:


## Missing:
* Unions
* Tagged unions (which is way more fun)

`*val` is a pointer to `val`.
`val.*` is dereferencing `val`;

Print formatting: 
* `"{}"` will print string value.
* `"{x}"` will print hex value.
* `"{b}"` will print binary value.

`@<identifier>` is a built-in.

`@compileLog(@TypeOf(val))` will output the type name of `val`.

`!` is result (error_type!success_type) where !success_type alone infers the
error.
If a function doesn't (possibly) return an error then the `!` is redundant.

To run project: `zig make run`, to build: `zig make build`

`_` will dispose of the value (like `let _` binding in Rust)`_` will dispose of
the value (like `let _` binding in Rust)

`switch` can only match on values known at compile time
`|n|` is called a capture

Example also contains enum.
```zig
const MyEnum = enum {
    A,
    B,
    C,
}

pub fn main() !void {
    const val = MyEnum.A;
    
    switch (val) {
        MyEnum.A => {},
        MyEnum.B => {},
        else => |probably_c| {
        }
    }
   
}
```

`comptime_int`: example `const value: comptime_int = 5;`
The main use case of `comptime_int` is type inference.
- This has to be known at compile time.
- Has to be `const`

## Errors: 
Defining your own error type is similar to an enum.
```zig
const MyError = error {
    ErrorVariantA,
    ErrorVariantB,
}
```

Using errors:
```zig
fn throw() MyError!void {
    return error.ErrorVariantB;
}
```

Dealing with errors:
```zig
function_that_generates_errors() catch |err| switch (err) { 
    error.ErrorVariantA => {},
    error.ErrorVariantB => {}
}
```

## Enums:
Can be represented as a numerical value using `@enumToInt` as long the as 
enum is defined as `enum(some_int_type)`.

Assigning a numerical value to a variant means the next variant will have 
the numerical value + 1.
It is not necessary to assign a value higher than the previous value of the 
previous variant.
```zig
const EnumA = enum {
    A,
    B,
}

// We can also represent enums as u8

const EnumB = enum(u8) {
    A, // <- 0
    B // <- 1
}

const EnumC = enum(u8) {
    A = 100,// <- 100
    B,      // <- 101
    C,      // <- 102
    D = 1,  // <- 1
    E,      // <- 2
}

var value: EnumB = .B;
var byte = @enumToInt(value); // u8 is inferred 
```

## Optionals
Prefix a type with `?` makes it into an optional.
```zig
var number: ?usize = null;
```

Using `orelse` we can do an early return or assign a defaut value;

Default value:
```zig
var nullable: ?u8 = null;
var not_null = nullable orelse 0;
```

Early return:
```zig
var nullable: ?u8 = null;
var not_null = nullable orelse return;
```

## Strings
String literals = `const [N;0]u8`. 
String literals are pointers to arrays. String literals live in ro memory.

```zig
const string = "hello"; // *const [5:0]u8;
```
String literals can coerce into `[]const u8` (from `*const [L:0]u8`).
Note: `[Len;0]` where zero means null terminated.

String literal to byte array is done via coercion: 
```zig
const string = "hello";
const byte_arr: []const u8 = string;
```

## Arrays and Slices

```zig
var arr = [_]u8{1, 2, 3}; 
var slice: []const u8 = &arr;

var alt_array: [2]u8 = .{1, 2};
```


## Structs


```zig
const AStruct = struct {
    val: u32,
}

var inst = AStruct { val = 213 };
```

Anonymous structs:
```zig
const variable_name = .{ field_name: u32 = 123 };

const nameless = .{4,8};
var value = nameless.@"0"; // = 4
var other_value = nameless[1]; // = 8
```

## Packed structs
TODO: needs more info
```zig
const PackyStruct = packed struct {
    a: u2,
    b: u2,
    c: u2,
    d: u2,
};
```


## Unions and Tagged unions
A union has to be tagged (`union(enum)` as opposed to just `union`) 
to be able to use with a switch statement.

```zig
// Only one value
const SomeUnion = union {
    A: u16,
    B: [2]u8,
}

// Can switch on an instance of this
const Taggy = union(enum) {
    A: u16
    B: [2]u8
}

```
