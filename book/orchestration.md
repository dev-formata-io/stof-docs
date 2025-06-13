---
description: Task based execution in Stof.
---

# Orchestration

Because Stof is both a declarative document and a functional interface, we can combine those worlds, executing objects as tasks rather than just the specific functions they contain.

This solves a common dilemma: declarative simplicity fails when specific flexibility and extensibility are needed. For example, maybe you have a declarative data connector but need to add just a line of logic to change something. Now, you can do it right in line with the declarative data.

{% hint style="info" %}
Take a look at the [Object Library](../reference/libraries/object-library.md) for more information on the "exec" function.
{% endhint %}

## Run Attribute

Object execution is controlled with a single #\[run] attribute that can be placed on the fields of sub-objects and functions.

```rust
#[run]
task: {
    #[run]
    fn doSomething() {
        self.something = true;
    }
}

#[test]
fn test() {
    self.exec();
    assert(self.task.something);
}
```

The value of the attribute (number) determines the order of execution. While functions and objects are sorted together, the default ordering dictates that sub-objects are executed before functions.

```rust
task: {
    Box<vec> context: []
    
    #[run] // default order for objects is -2
    subobj: {
        #[run]
        fn first() {
            super.context.push(0);
        }
        
        #[run(1)]  // order value has to be a number
        fn second() {
            super.context.push(1);
        }
    }
    
    #[run] // default order for functions is -1
    fn third() {
        self.context.push(2);
    }
}

#[test]
fn test() {
    self.task.exec();
    assertEq(self.task.context, [0, 1, 2]);
}
```

## Types as Tasks

Because attributes can be placed on prototype functions, these are also considered during execution. This can be extremely helpful for transforming and working with external/imported data, as that data can be assigned a type and executed accordingly.

{% hint style="info" %}
Object fields on prototypes are not considered in execution (only functions are). It is discouraged to place field data within prototype objects.
{% endhint %}

```rust
type Base {
    #[run]
    fn do_something() {
        self.shouldnt = true; // overridden, so shouldn't happen
    }

    #[run]
    fn do_base_things() {
        self.base = true;
    }
}

type MyTask extends Base {
    #[run]
    fn do_something() {
        self.did_something = true;
    }
}

MyTask task: {}

#[test]
fn test() {
    self.task.exec();
    assert(self.task.base);
    assert(self.task.did_something);
    assertNull(self.task.shouldnt);
}
```
