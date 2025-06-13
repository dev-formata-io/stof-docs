---
description: Stof's standard number library ("Number").
icon: input-numeric
---

# Number Library

{% hint style="info" %}
See [primitive-types.md](../../common-concepts/primitive-types.md "mention"), [units.md](../../common-concepts/units.md "mention"), and [casting-conversions.md](../../common-concepts/casting-conversions.md "mention") for more information on number types in Stof.
{% endhint %}

## Common Value Functions

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.toString</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">str</mark>

```rust
#[test]
fn test() {
    let a = 100.toString();
    let b = Number.toString(100);
    assertEq(a, b);
    assertEq(a, "100");
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.or</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
#[test]
fn test() {
    let val = 5;
    assertEq(val.or(2), 5);
    
    val = null;
    assertEq(val.or(3), 3);
}
```

## Parse Strings to Numbers

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.parse</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>

Parse a string into a number using the Stof parser. This includes integers, floating-point numbers, hexadecimal, octal, and binary values. It also includes the readability characters Stof supports: "+" prefix, "-" prefix (of course), and ignores "\_" characters. This function also parses Stof units.

```rust
fn example() {
    let val = Number.parse("+1.24_45e4"); // 12445
    let hex = Number.parse("0x35_Fa_60"); // must use 0x
    let oct = Number.parse("0o55");       // must use 0o
    let bin = Number.parse("0b11");       // must use 0b
    let units = Number.parse("12kg");     // units of kilograms
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.parseHex</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">int</mark>

Works exactly like Number.parse(...), however, if the value does not start with "`0x`", this function will add it as a prefix before parsing.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.parseOct</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">int</mark>

Works exactly like Number.parse(...), however, if the value does not start with "`0o`", this function will add it as a prefix before parsing.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.parseBin</mark>(val: <mark style="color:green;">str</mark>): <mark style="color:green;">int</mark>

Works exactly like Number.parse(...), however, if the value does not start with "`0b`", this function will add it as a prefix before parsing.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.toHexString</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">str</mark>

Converts the integer portion of this value to a hexadecimal string. The resulting string is always capital hex characters (A-F) and does not start with "`0x`".

```rust
fn example() {
    assertEq(0xff.toHexString(), "FF");
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.toOctString</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">str</mark>

Converts the integer portion of this value into an octal string. The resulting string does not start with "`0o`".

```rust
fn example() {
    assertEq(0o55.toOctString(), "55");
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.toBinString</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">str</mark>

Converts the integer portion of this value into a binary string. The resulting string does not start with "`0b`".

```rust
fn example() {
    assertEq(0b11.toBinString(), "11");
}
```

## Number Functions

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.round</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, places?: <mark style="color:green;">int</mark>): <mark style="color:green;">float</mark>

Round this val to the nearest integer. If value is half-way between two integers, rounds away from 0.0. If given a number of places to round to, move the decimal right that many places, round, then moves it back.

```rust
#[test]
fn round() {
    assertEq(12.23456.round(), 12);
    assertEq(12.23456.round(2), 12.23);
    assertEq(12.23456.round(4), 12.2346);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.pow</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, power: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark>): <mark style="color:green;">float</mark>

Raises a number to a power.

```rust
#[test]
fn pow() {
    assertEq(2.pow(3), 8);
    assertEq(Number.pow(2, 3), 8);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.sqrt</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Return the square root of a number.

```rust
#[test]
fn sqrt() {
    assertEq(4.sqrt(), 2);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.cbrt</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Return the cube root of a number.

```rust
#[test]
fn cbrt() {
    assertEq(8.cbrt(), 2);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.abs</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Return the absolute value of a number.

```rust
#[test]
fn abs() {
    assertEq(-3.abs(), 3);
    assertEq(3.abs(), 3);
    assertEq(Number.abs(box(-3434)), 3434);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.floor</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Return the largest integer less than or equal to "val".

```rust
#[test]
fn floor() {
    assertEq(1.45.floor(), 1);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.ceil</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Return the smallest integer greater than or equal to "val".

```rust
#[test]
fn ceil() {
    assertEq(1.45.ceil(), 2);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.trunc</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Return the integer part of "val". Non-integer values are always truncated towards zero.

```rust
#[test]
fn test() {
    assertEq(12.7.trunc(), 12);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.fract</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Return the fractional part of "val".

```rust
#[test]
fn fract() {
    assertEq(12.5.fract(), 0.5);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.signum</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Returns a number representing the sign of "val".

* 1,  the number is positive
* -1, the number is negative

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.exp</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Returns the exponential function `e^(val)` .

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.exp2</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Returns `2^(val)`.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.ln</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Returns the natural log of val: `ln(val)`.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.log</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, base: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark>): <mark style="color:green;">float</mark>

Returns the logarithm of the number with respect to an arbitrary base.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.sin</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Computes the sine of a number (in radians).

```rust
#[test]
fn angles_trig() {
    assertEq(90deg.sin(), 1);
    assertEq(270deg.sin(), -1);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.cos</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Computes the cosine of a number (in radians).

```rust
#[test]
fn angles_trig() {
    assertEq(0deg.cos(), 1);
    assertEq(180deg.cos(), -1);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.tan</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Computes the tangent of a number (in radians).

```rust
#[test]
fn angles_trig() {
    assertEq(0deg.tan(), 0);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.asin</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">rad</mark>

Computes the arcsine of a number. Return value is in radians in the range -pi/2, pi/2 or null if the number is outside the range -1, 1.

```rust
#[test]
fn test() {
    assertEq(-1.asin(), 270deg);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.acos</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">rad</mark>

Computes the arccosine of a number. Return value is in radians in the range 0, pi or null if the number is outside the range -1, 1.

```rust
#[test]
fn test() {
    assertEq(-1.acos(), 180deg);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.atan</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">rad</mark>

Computes the arctangent of a number. Return value is in radians in the range -pi/2, pi/2.

```rust
#[test]
fn test() {
    assertEq(0.atan(), 0deg);
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.atan2</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, other: <mark style="color:green;">float</mark>): <mark style="color:green;">rad</mark>

Computes the four quadrant arctangent of "val" and "other" in radians.

* `x = 0`, `y = 0`: `0`&#x20;
* `x >= 0`: `arctan(y/x)` -> `[-pi/2, pi/2]`
* `y >= 0`: `arctan(y/x) + pi` -> `(pi/2, pi]`
* `y < 0`: `arctan(y/x) - pi` -> `(-pi, -pi/2)`&#x20;

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.sinh</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Hyperbolic sine function.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.cosh</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Hyperbolic cosine function.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.tanh</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Hyperbolic tangent function.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.asinh</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Inverse hyperbolic sine function.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.acosh</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Inverse hyperbolic cosine function.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.atanh</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Inverse hyperbolic tangent function.

## Iteration

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.len</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">int</mark>

Returns the integer part of "val". Non-integer values are truncated towards zero.

This function is provided to support iteration of numbers.

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.at</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, index: <mark style="color:green;">int</mark>): <mark style="color:green;">int</mark>

Returns the integer part of "index" if index is less than the integer part of "val", otherwise, returns the integer part of "val". Non-integer values for both "val" and "index" are truncated towards zero.

```rust
#[test]
fn test() {
    // For loops use "len" to get the length and "at" to get each value
    for (i in 10) {
        pln(i); // will print 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
    }
}
```

## Units

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.units</mark>(val: <mark style="color:green;">units</mark>): <mark style="color:green;">str</mark> | <mark style="color:green;">null</mark>

Returns the units of this number or null if no units are defined.

```rust
#[test]
fn test() {
    assertEq(19km.units(), "km");
    assertNull(19.units());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.removeUnits</mark>(val: <mark style="color:green;">units</mark>): <mark style="color:green;">float</mark>

Remove the units of this number if any units are defined. Otherwise, returns a clone of "val".

```rust
#[test]
fn test() {
    let val = 20s;
    assertEq(val.units(), "s"); // seconds
    
    val = val.removeUnits();
    assertNull(val.units());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.hasUnits</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has units defined for it.

```rust
#[test]
fn test() {
    assert(10F.hasUnits());
    assertNot(10.hasUnits());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.isAngle</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has angle units defined for it.

```rust
#[test]
fn test() {
    assert(10deg.isAngle());
    assertNot(10F.isAngle());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.isDegrees</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has angle units of Degrees.

```rust
#[test]
fn test() {
    assert(10deg.isDegrees());
    assertNot(10rad.isDegrees());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.isRadians</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has angle units of Radians.

```rust
#[test]
fn test() {
    assert(10rad.isRadians());
    assertNot(10deg.isRadians());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.isPositiveDegrees</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has angle units of Positive Degrees. See [#units](number-library.md#units "mention") for more information on positive degrees.

```rust
#[test]
fn test() {
    assert(10pdeg.isPositiveDegrees());
    assertNot(10deg.isPositiveDegrees());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.isPositiveRadians</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has angle units of Positive Radians. See [#units](number-library.md#units "mention") for more information on positive radians.

```rust
#[test]
fn test() {
    assert(10prad.isPositiveRadians());
    assertNot(10rad.isPositiveRadians());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.isTemperature</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has units of temperature defined for it.

```rust
#[test]
fn test() {
    assert(10C.isTemperature());
    assertNot(10km.isTemperature());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.isLength</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has units of length defined for it.

```rust
#[test]
fn test() {
    assert(10m.isLength());
    assertNot(10ms.isLength());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.isTime</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has units of time defined for it.

```rust
#[test]
fn test() {
    assert(10ns.isTime());
    assertNot(10m.isTime());
}
```

### <mark style="color:purple;">Number</mark><mark style="color:orange;">.isMass</mark>(val: <mark style="color:green;">int</mark> | <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>): <mark style="color:green;">bool</mark>

Return true if this number has units of mass defined for it.

```rust
#[test]
fn test() {
    assert(10g.isMass());
    assertNot(10m.isMass());
}
```
