# Number representation
## Number systems
Numbers have different bases. We usually use a `Base10` system in our daily lives.

In computers, we need to represent numbers in a Binary system.
### Two's complement
Two's complement is a computers way of representing `-ve` numbers.<br>
A complement is like a `NOT` gate. The complement of `0` is `1`, and vice versa.

Two's complement is split into 2 steps.
1. Find the complement of a number. E.g. `0011` -> `1100`
2. Add one to the complement. E.g. `1100` -> `1101`

Hence, the two's complement of `0011` is `1101`. <br>
> [!info] Number representation
> In a n-bit signed[^1] number, two's complement is the `n-1`'s bit which is the MSB/Highest bit. <br>

[^1]: "Signed" means the number can take both `+ve` and `-ve` values. "Unsigned" means that there is no "sign" bit, and hence the number can only be `+ve`.(two's complement is not applied)

Two's complement has the following formula: `x = ~x + 1` (~ stands for `NOT`)

Two's complement is very useful.
1. Has a range from $-2^{n-1}$ to $2^{n-1}-1$.[^2]
2. We can subtract using addition. <br>

[^2]: This is one more then [One's complement](https://en.wikipedia.org/wiki/Ones%27_complement). This is as while One's complement has 2 representations of `0`, being `+0` and `-0`, Two's complement only has 1 `0`, being `0` itself.

> [!note]- Subtraction via addition example
> This is a 8-bit example, but you can extend the idea to n-bits.
> ```
> A = 0100 0011 //67, 0x43
> B = 0010 0101 //37, 0x25
> ```
> Lets take `A - B`
> ```
>   0100 0011
> - 0010 0101
> -------------
>   ???? ????
> -------------
> ```
> First, we apply two's complement to `B`. Resulting in
> ```
> B = ~(0010 0101) + 1
> ~0010 0101 = 1101 1010
> 1101 1010 + 1 = 1101 1011 //Two's complement of B
> ```
> Now, we add the two's complement to `A`.
> ```
>   0100 0011
> + 1101 1011 //We add the two's complement representation of B
> -------------
>   0001 1110 //NOTE: the value overflowed in the addition!
> -------------
> 0001 1110 = 30, 0x1E
> ```
> Hence, Using two's complement, we can subtract by adding!

### Range Extension
- Range extension is storing a n-bit number in m-bits, where $m \gt n$. <br>
- This is called Range extension, as the range of the numbers that can be represented($-2^{n-1}$ to $2^{n-1}-1$) is "extended" via an increase in bits used.
- For two's complement values, the sign bit moves to the left most bit, as the bits gets extended.
- For `+ve` numbers, fill in new bits with `0`s. For `-ve` numbers, fill in new bits with `1`s.

<sub>AUTHORS NOTE: We usually use 2^n amount of bits for "padding". in my experience, x86-64 usually pads to 8-bits. Even if you require less bits, it can(and will probably) be faster to use a "padded" amount of bits for speed purposes. </sub>

## Floating point numbers
All number storing systems used up to now is only capable of storing integers. <br>
We need a way to store more then just integers. Such numbers include
- Fractions. E.g. **3/4 (0.75)**
- Big numbers. E.g. **0.602x10^{24}**(one mole)
- Small numbers. E.g. **0.5x10^{-10}**(size of a hydrogen atom)

Expression, scientific notation: $F \times r^{E}$ where $F$ = fraction, $r$ = radix, and $E$ = exponent.<br>

Decimals use a radix `10`($F \times 10^{E}$). Binary uses `2`($F \times 2^{E}$).
- **floating-point numbers suffer from a loss of precision**
	- Impossible to represent infinite amount of numbers with finite amount of bits. Hence, loss of accuracy occurs.
- same number in floating-point can be stored in many different ways(floating point representation is not unique)
### IEEE 754 Format
- Most used format for representing floats.
#### format
##### Formula:
$$F \times r^{E}$$
- This specifies the 32-bit format
- MSB: sign bit. `0` for `+ve` numbers, `1` for `-ve` numbers.
- Next 8 bits: exponent(E).
- Rest of the 23 bits: Fraction/Mantissa(M)
```
32-bit
|1: Sign|8 : Exponent|23 : Mantissa(significand)|
64-bit
|1: Sign|11: Exponent|52 : Mantissa(significand)|
128-bit
|1: Sign|15: Exponent|112: Mantissa(significand)|
```
NOTE: mantissas always start with 1.\* in normalized form. We don't need to represent the leading 1, giving us an extra bit of accuracy.
##### Formula
$$x = (-1)^{s} \times (1 + M)\times 2^{E}$$ `x` = value, `s` = sign, `M` = mantissa, `E` = exponent
Example: `x = 18.45`

- First we split it to `18`, and `0.45`.
```
18 = 0001 0010
```
```
0.45*2 % 1 = 0.90 | R0
0.90*2 % 1 = 0.80 | R1
0.80*2 % 1 = 0.60 | R1 //SAME
0.60*2 % 1 = 0.20 | R1
0.20*2 % 1 = 0.40 | R0
0.40*2 % 1 = 0.80 | R0
0.80*2 % 1 = 0.60 | R1 //SAME
Represented by 011100[1100][1100][repeating]
```
- Decimal point: `18.45` = `0001 0010 . 0111 00[11 00][11 00][cont]`
- Decimal point in scientific notation: $1.845 \times 10$
- In binary case, it'll be `1.0010 0111 00[11 00][11 00][cont]x2^{4}`
- Hence, `Sign(s) = 0`, `Exponent(e) = 4[0b100] + 127`, `Mantissa(M) = 0010 0111 0011 0011 0011 001`
- **NOTE: `Exponent(e)` is being added with `127[0x7F]`. This is as we can then represent `+ve` and `-ve` exponents(e.g. `-10 + 127` = 117.). Hence, when finding the exponent, we need to subtract by `127`.**
- We end up with: `[0][100 0001 1][001 0011 1001 1001 1001 1001]`.
- Converting it back to decimal: `Sign(s) = 0`, `Exponent(e) = 131 - 127 = 4`. `Mantissa(M) = 0.15312492847442627` (calculated by converting back using $2^{-n}$)
- Formula: $$x = (-1)^{0} \times (1 + 0.15312492847442627)\times 2^{4} = 18.449[cont]$$ This shows that `18.45` cannot be represented precisely in binary.

<sub>AUTHORS NOTE: People have done some very interesting things with IEEE 754. For example, the infamous [Fast inverse square root](https://www.youtube.com/watch?v=p8u_k2LIZyo). This video also explains IEEE 754, so if you are still lost, consider checking it out! </sub>

# ASCII(American Standard Code for Information Interchange)
- Uses 8-bits. Originally used 7(last bit was parity).
- Values from `0x20`('` `') to `0x7E`('`~`') are printable.
- `0x00` to `0x1F` and `0x7F` are special characters. Some notable ones include:
 <!-- TODO: CHANGE TO BOX -->
| Value | Name | Representation(coding) |
| --- | --- | --- |
|`0x00`| NULL| NULL|
| `0x09` | TAB | '`\t`'|
| `0x0A` | NEWLINE, `LF`(Line-Feed) | '`\n`' |
| `0X0D` | Carriage-Return, `CR` | '`\r`' |
| `0xFF` | End-Of-File | -1[^3] | `EOF`|
- Not so used ones
- `0x08`: Backspace, '`\b`'
- `0x0B`: Vertical Feed

[^3]: Recall [two's complement](#Two%27s%20Complement), `-1` in a 8-bit integer will be `0xFF`!
 
- There is no standard for "newlines".
	- Unix/Mac uses `\n`(LF).
	- Windows uses `\r\n`(CRLF).

# Endianness
There are two types of Endian in computers.
1. Big Endian(BE)
2. Little Endian(LE)(modern computers usually use this)

Endian refers to how the bytes are stored<br>
- Big endian means the MSB is stored in the lowest memory address(biggest first)
- Little endian means the MSB is stored in the highest memory address(smallest first)

| Memory | Big endian | Little endian |
| --- | --- | --- |
| 0x03 | A2 | B0 |
| 0x02 | A4 | A8 |
| 0x01 | A8 | A4 |
| 0x00 | B0 | A2 |

This notes are based on SP's module: <br>
Author: [<code>@duckupus</code>](https://github.com/duckupus)