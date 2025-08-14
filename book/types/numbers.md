---
description: Units & Casting
---

# Numbers

There are 3 types of numbers in Stof: integer, float, and units. In the [above section](./), we saw a brief example of the integer and floating-point number types. However, there is a variant of "float" that helps immensely in Stof - unit types.

{% hint style="info" %}
Take a look at the [number library (Num)](../../libraries/type-libraries/number-library-num.md) too for additional operations.
{% endhint %}

## Number Operations

```rust
#[main]
fn main() {
    let res = 10 + 10; // addition
    res = 10 - 5;      // subtraction
    res = 10 * 10;     // multiplication
    res = 10 / 2;      // division
    res = 10 % 2;      // modulo/remainder
    
    res = 0b0101 & 0b0100; // bit and
    res = 0b0101 | 0b0110; // bit or
    res = 0b0101 ^ 0b0100; // bit xor
    res = 0b0011 << 2;     // bit shift left
    res = 0b1100 >> 2;     // bit shift right
    
    res += 10; // res = res + 10
    res -= 10; // res = res - 10;
    res *= 10; // res = res * 10;
    res /= 10; // res = res / 10;
    res %= 10; // res = res % 10;
    
    res &= 0b0100; // res = res & 0b0100;
    res |= 0b0110; // res = res | 0b0110;
    res ^= 0b0100; // res = res ^ 0b0100;
    res >>= 2;     // res = res >> 2;
    res <<= 2;     // res = res << 2;
}
```

## Casting

Casting (and conversions) are done with the "as" keyword.

```rust
#[main]
fn main() {
    let floating = 45 as float;
    let another: float = 22; // also can just define the type
}
```

Casting also automatically occurs with function calls (parameters and return type).

```rust
fn add_hour(time: seconds) -> milliseconds {
    // time is in units of seconds
    time + 1hr
}

#[main]
fn main() {
    const time = self.add_hour(5hr);
    // time is now in units of milliseconds
    assert_eq(time, 6hr);
}
```

## Units

In Stof, units are additional number types that can be used in place of "float". The "float" type will match all unit types (so you can have a float parameter that accepts any units); however, the unit types will not match each other and will perform conversions when cast to other unit types.

All logical operators (greater than, less than, equals, etc.) and number operations consider units, performing conversions automatically when necessary (and able). Units of the same category will always have a common unit that is the larger of the two (Ex. m + mm -> m). For angles, it will always be radians if the types are mixed. Units of "undefined" are assigned if the numbers have incompatible units (Ex. m + kg).

### Examples

```rust
#[main]
fn main() {
    const distance = 234m;
    
    const func = (dist: km): ft => dist; // m -> km -> ft
    let res: m = func(distance); // ft -> m
    
    assert_eq(res, distance);
    assert_eq(str(res), "234m");
    assert_eq(str(res as km), "0.234km");
}
```

```rust
// Can define fields with unit types
m height: 135cm + 5ft + 123in

#[main]
fn main() {
    pln(self.height); // meters
}
```

### Time

One of the most useful applications of Stof units is with time, which is all over the place in configurations and intersystem communications.

As an example, the Time.now() function returns "ms" instead of an integer. This allows users to subtract days, for example, and have Stof handle the complexity.

```rust
#[main]
fn main() {
    const day_time: days = 1day;           // "day" or "days"
    const hour_time: hours = 23hr;         // "hr" or "hours"
    const min_time: minutes = 45min;       // "min" or "minutes"
    const sec_time: seconds = 60s;         // "s" or "seconds"
    const mil_time: milliseconds = 1000ms; // "ms" or "milliseconds"
    const mic_time: microseconds = 200us;  // "us" or "microseconds"
    const nan_time: nanoseconds = 79_23ns; // "ns" or "nanoseconds"
}
```

### Angles

There are 4 angle types in Stof: radians (rad), degrees (deg), positive radians (prad), and positive degrees (pdeg).

Positive variations ensure the angle is (and remains) positive (-90deg as pdeg == 270deg) and are used for comparisons.

Radians (rad) and degrees (deg) clamp the angle between \[0, +-360deg]. The positive variations clamp the angle between \[0, 360deg) (transforming +-360deg into 0 for comparisons). If you don't like the casting and clamping, use a normal float and do it yourself.

```rust
#[main]
fn main() {
    const rad_angle: radians = 0rad;       // "rad" or "radians"
    const deg_angle: degrees = 90deg;      // "deg" or "degrees"
    const prad_angle: pradians = 3.14prad; // "prad" or "pradians"
    const pdeg_angle: pdegrees = -45pdeg;  // "pdeg" or "pdegrees"

    let res: pdeg = 90deg - 270deg; // cast to pdeg always returns [0, 360)
    assert_eq(res, 180deg);
    pln(res as radians); // cast using the "as" keyword like normal
}
```

### Length

```rust
#[main]
fn main() {
    const kilo: kilometers = 3km;     // "km" or "kilometers"
    const hecto: hectometers = 4hm;   // "hm" or "hectometers"
    const deca: decameters = 5dcm;    // "dcm" or "decameters"
    const met: meters = 6m;           // "m" or "meters"
    const deci: decimeters = 45dm;    // "dm" or "decimeters"
    const centi: centimeters = 67cm;  // "cm" or "centimeters"
    const milli: millimeters = 500mm; // "mm" or "millimeters"
    const micro: micrometers = 567um; // "um" or "micrometers"
    const nano: nanometers = 67nm;    // "nm" or "nanometers"
    
    const mil: miles = 1mi;   // "mi" or "miles"
    const yar: yards = 5yd;   // "yd" or "yards"
    const fee: feet = 55ft;   // "ft" or "feet"
    const inc: inches = 79in; // "in" or "inches"
}
```

### Temperature

```rust
#[main]
fn main() {
    const kel: kelvin = 273K;    // "K" or "kelvin"
    const cel: celsius = 10C;    // "C" or "celsius"
    const far: fahrenheit = 72F; // "F" or "fahrenheit"
    
    // conversions are then pretty fun
    const res = cel as F; // celsius -> fahrenheit
    const exp: K = cel;   // can just define the type too
}
```

### Mass

```rust
#[main]
fn main() {
    const giga: gigatonnes = 10Gt; // "Gt" or "gigatonnes"
    const mega: megatonnes = 10Mt; // "Mt" or "megatonnes"
    const tonn: tonnes = 56t;      // "t" or "tonnes"
    const kilo: kilograms = 78kg;  // "kg" or "kilograms"
    const gram: grams = 78g;       // "g" or "grams"
    const mill: milligrams = 67mg; // "mg" or "milligrams"
    const micr: micrograms = 34ug; // "ug" or "micrograms"
    const nano: nanograms = 78ng;  // "ng" or "nanograms"
    const pico: picograms = 89pg;  // "pg" or "picograms"
    
    const ton: tons = 55Ton;  // "Ton" or "tons"
    const pou: lbs = 12lb;    // "lb" or "lbs" for imperial pounds
    const ozz: ounces = 77oz; // "oz" or "ounces"
}
```
