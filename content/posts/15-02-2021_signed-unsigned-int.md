+++
title = "Two's complement by Temporus"
date = 2021-02-15
+++

In reality all bytes in memory are unsigned.

The concept of signed values is just an encoding of the bits of those bytes.

It makes use of a property in CPU's that add in general will wrap around the value (and sets a carry flag)

So

```
0xFF + 0x01 = 0x00
```

What you may notice we can think of it as the other way around

```
0x01 + 0xFF = 0x00
```

Which is the same as

```
0x01 + -0x01 = 0x00
```

Or

```
0x01 - 0x01 = 0x00
```

You keep following it backwards and you realize it acts like going further into negatives:

```
0x01 + 0xFE = 0XFF
```

Which can be the same as

```
0x01 - 0x02 = -0x01
```

You then look at it in bits

```
00000001 + 11111111 = 00000000
0x01     +     0xFF = 0x00
0x01     -     0x01 = 0x00

00000001 + 11111110 = 11111111
0x01     +     0xFE = 0xFF
0x01     -     0x02 = -0x01
```

So you notice we can easily split negative numbers based on whether the highest bit is set

If we treat it in such a manner our byte goes from 0-255 to a -128-127 range.

So how do we turn 1 into -1 easily? Or

```
00000001 -> 11111111
```

we can invert all the bits and add 1

```
invert(00000001) + 1 = 11111110 + 1 = 11111111
```

We can do the same to reverse it

```
invert(11111111) + 1 = 00000000 + 1 = 00000001
```

So this is working in 8 bits, but the same process applies to any bit size 
if an unsigned value is 16 bits, with a value range of `0-((2^16) - 1)`, or `0-65535` 
making the highest bit the signed bit our range becomes `-(2^(16-1))` to `(2^(16-1) - 1)`, 
or `-32768` to `32767`.

