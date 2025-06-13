---
description: Stof time library ("Time").
icon: clock
---

# Time Library

The time library is a part of Stof, however, is separately organized because it is system-dependent and cannot be broadly implemented for all embedded environments.

### <mark style="color:purple;">Time</mark><mark style="color:orange;">.now</mark>(): <mark style="color:green;">ms</mark>

Returns the current time (in milliseconds) since the [Unix epoch](https://en.wikipedia.org/wiki/Unix_time).

{% hint style="info" %}
See [units.md](../../../common-concepts/units.md "mention") and [primitive-types.md](../../../common-concepts/primitive-types.md "mention") to better understand Stof unit types.
{% endhint %}

### <mark style="color:purple;">Time</mark><mark style="color:orange;">.nowNano</mark>(): <mark style="color:green;">ns</mark>

Returns the current time (in nanoseconds) since the Unix epoch. This is helpful for situations that require more granularity than milliseconds.

### <mark style="color:purple;">Time</mark><mark style="color:orange;">.diff</mark>(prev: <mark style="color:green;">ms</mark>): <mark style="color:green;">ms</mark>

Returns the current time (in milliseconds) minus a previous timestamp, or the elapsed time since a call to "Time.now()".

The argument may be in alternative time units to milliseconds (will get converted), however, if the value does not have units, it will be assumed to be milliseconds (float or int).

```rust
fn example() {
    let ts = Time.now();
    
    // do something cool...
    
    let elapsed = Time.diff(ts);
    pln("This took: ", elapsed); // "This took: 1ms"
}
```

### <mark style="color:purple;">Time</mark><mark style="color:orange;">.diffNano</mark>(prev: <mark style="color:green;">ns</mark>): <mark style="color:green;">ns</mark>

Returns the current time (in nanoseconds) minus a previous timestamp, or the elapsed time since a call to "Time.now()" or "Time.nowNano()".

The argument may be in alternative time units to nanoseconds (will get converted), however, if the value does not have units, it will be assumed to be nanoseconds (float or int).

```rust
fn example() {
    let ts = Time.nowNano(); // more accuracy
    
    // do something cool...
    
    let elapsed = Time.diffNano(ts);
    pln("This took: ", elapsed); // "This took: 12345ns"
}
```

### <mark style="color:purple;">Time</mark><mark style="color:orange;">.sleep</mark>(duration: <mark style="color:green;">ms</mark>): <mark style="color:green;">void</mark>

Block the current thread for a duration (assumed to be milliseconds if no units).

```rust
#[test]
fn example() {
    let ts = Time.now();
    Time.sleep(2ms);
    assert(Time.diff(ts) > 2ms);
}
```

### <mark style="color:purple;">Time</mark><mark style="color:orange;">.sleepNano</mark>(duration: <mark style="color:green;">ns</mark>): <mark style="color:green;">void</mark>

Block the current thread for a duration (assumed to be nanoseconds if no units). Use this function instead of "sleep" if you need a higher granularity than milliseconds.

```rust
#[test]
fn example() {
    let ts = Time.nowNano();
    Time.sleepNano(1000us); // microseconds
    assert(Time.diffNano(ts) > 1000us);
}
```
