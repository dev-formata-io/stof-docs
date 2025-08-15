---
description: Stof functions.
---

# Functions

A function is a type of data, just like a field. As such, they are referenced by path in the same way!

{% hint style="info" %}
Note: functions and field data in practice should often come from different places, only mixed at runtime in Stof for manipulation & usage (most export formats only consider fields anyways).

In reality, it is tricky to heavily mix both functions and fields in documents that are stored as versions/behavior inevitably changes (classic separation of concerns issues).

One strategy to combat this, however, is to put APIs in a separate root object (or known location). In this case, it's as easy to replace as swapping a file in a file system, without worry of missing data.

Keep in mind that this ability is very useful despite the potential learning curve - especially when sending API + data over the wire for another system to use in its own sandbox of Stof.
{% endhint %}

```rust
Coffee: {
    const bool decaf: false // never!!
    bool light_roast: false
    
    Customer: {
        name: "Stanly Yelnatz III"
        time: Time.now()
    }
    
    Shop: {
        str name: "Coffee Pot"
        str address: "42 Wallaby Way, Sydney"
        
        fn order() -> str {
            const order = `${self.name} on ${self.address}\n`;
            order.push(`1 ${super.light_roast ? 'light' : 'dark'} roast Coffee`);
            order
        }
    }
    
    fn customer_name() -> str {
        self.Customer.name
    }
}

#[main]
fn main() -> void {
    pln(self.Coffee.customer_name());
    pln(self.Coffee.Shop.order());
}
```

```bash
> stof run coffee.stof
Stanly Yelnatz III
Coffee Pot on 42 Wallaby Way, Sydney
1 dark roast Coffee
```

## Async

The Stof runtime supports async processes at its core. Functions can be async with an #\[async] attribute (or the async syntax that adds this attribute).

```rust
#[main] // each #[main] function is already a separate async process
fn main() {
    const looped = self.looped_sleep();
    const answers = self.also_async();
    pln("Answers: ", await answers);
    pln("Looped: ", await looped);
}

#[async]
fn also_async() -> int {
    sleep(10ms);
    42
}

/// syntax just adds an #[async] attribute
async fn looped_sleep() -> s {
    let total = 0ms;
    loop {
        let now = Time.now();
        sleep(100ms);
        total += Time.diff(now);
        
        pln(total);
        if (total > 1s) break;
    }
    total
}
```

```bash
> stof run
Answers: 42
100ms
200ms
300ms
400ms
500ms
600ms
700ms
800ms
900ms
1000ms
1101ms
Looped: 1.101s
```

### Flexible Async

Because async is so foundational in Stof, any function, even ones not marked as async can be called asynchronously.

```rust
#[main]
fn main() {
    const async_expr = await async self.doing_something();
    assert_eq(async_expr, 0);
    
    const async_block = await async {
        return self.doing_something();
    };
    assert_eq(async_block, 0);
    
    async {
        assert_eq(self.doing_something(), 0);
    }
}

fn doing_something() -> int {
    for (let _ = 0; i < 100; i += 1) {
        // doin something cool here with my loop
    }
    return 0;
}
```

## Attributes

Just like fields, functions can have custom attributes.

```rust
#[custom(new { x: 0, y: 0 })]
#[answers(42)]
fn my_function() {
    const func = self.my_function; // yup, getting cooler now, eh?
    const attr: map = func.attributes();
    assert_eq(attr.get("answers"), 42);
    
    // "this" is a reference to the current function on the call stack
    // this(); // would be infinitely recursive
    const attributes = this.attributes(); // same map as above
    assert_eq(attributes.get("custom").get("x"), 0);
}
```

## Arrow Functions

```rust
#[main]
fn main() {
    const func: fn = (a: float, b: float): float => a + b;
    assert_eq(func(5, 5), 10);
    
    const async_func = async () => {
        pln("from async arrow function");
    };
    async_func();
}
```

### As a Field

There are times when functions (or function pointers "fn") end up as a field. You can call them just like normal functions!

```rust
name: "Bob Smith"
message: (name: str = self.name): str => `Hi, ${name}`

#[main]
fn main() {
    pln(self.message()); // "Hi, Bob Smith"
}
```

## Return

A return statement is one without a semi-colon, returning the last value on the stack. Or, an explicit "return" statement within the function.

```rust
#[main]
fn main() -> int {
    for (const i in 100) {
        if (i > 50) return i; // return from the function with 51
    }
    -1                        // return with -1
}
```
