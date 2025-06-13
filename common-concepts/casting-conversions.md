# Casting/Conversions

Unit conversions for a number take place when that number is cast to another unit. This is because units are types in Stof, just like an "int" or a "float".

### Casting With 'as' Keyword

```rust
#[main]
fn conversion() {
    let i = 33m;
    pln(i as cm);
}
```

```
> stof run example.stof
3300cm
```

### Function Signature Casting

```rust
fn testFunc(i: cm): m {
    pln(i);
    return i;
}

#[main]
fn conversion() {
    let i = 330mm;
    i = self.testFunc(i);
    pln(i);
}
```

```
> stof run example.stof
33cm
0.33m
```

### Incompatible Units

Compatible units are units in the same category. For example, any length unit can be cast to any other length unit, but cannot be cast to a temperature.

If operating with or casting to units of an incompatible type, the units become "undefined" and the value is treated as unitless.

```rust
#[main]
fn conversion() {
    let i = 33cm;
    i = i as kg;
    
    pln(i.units());
    pln(i);
    
    i = i.removeUnits();
    pln(i);
}
```

```
> stof run example.stof
undefined
33undefined
33
```

### Common Base Unit

When units are used in operations with compatible, but different units, Stof will pick a common base unit to present the returned value as. In a lot of cases, this is just the larger of the two units, which feels the most intuitive in practice.

```rust
#[main]
fn conversion() {
    pln(10cm + 1000mm + 2m + 440000um - 2000000000nm);
}
```

```
> stof run example.stof
1.54m
```
