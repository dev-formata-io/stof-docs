---
description: Executing objects as tasks.
---

# Running Objects

In addition to schemas, Stof's capabilities also present an interesting abstraction above the functional layer - executing entire objects as tasks.

This is accomplished with the `Obj.run(..)` function in the [object-library-obj.md](../libraries/type-libraries/object-library-obj.md "mention"), which executes all `#[run]` functions and fields, optionally in a user-specified order.

```rust
#[type]
Task: {
    ok: false
    result: null
    config: {
        endpoint: "https://myendpoint"
        schema: {
            // some schema to apply to the result
        }
    }
    
    #[run]
    fn exec() {
        const res = await Http.fetch(self.config.endpoint);
        
        const result = new {};
        parse(res.remove("text"), result, "json");
        
        self.ok = self.config.schema.schemafy(result);
        self.result = result;
    }
}

#[main]
fn main() {
    const task = new Task {
        config: new {
            endpoint: 'https://restcountries.com/v3.1/region/europe'
        }
    };
    task.run();

    assert(task.ok);
    for (const country in task.result.field) {
        pln(country.name);
    }
}
```

## Ordering

```rust
#[run]
Task: {
    out: []

    #[run(1)]
    fn first() {
        self.out.push_back(1);
    }

    #[run(2)]
    fn second() {
        self.out.push_back(2);
    }

    #[run(3)]
    fn third() {
        self.out.push_back(3);
    }
}

#[main]
fn main() {
    self.run();
    assert_eq(self.Task.out, [1, 2, 3]);
}
```

## Running Fields

```rust
#[run]
Task: {
    fn testing() {
        self.SubTask.out.push_back(55);
    }

    #[run(1)]
    pipeline: [
        {
            #[run]
            fn cool() {
                super.SubTask.out.push_back(42);
            }
        },
        self.testing,
    ]

    #[run(3)]
    field: self.testing

    #[run(2)]
    SubTask: {
        out: []

        #[run(1)]
        fn first() {
            self.out.push_back(1);
        }

        #[run(2)]
        fn second() {
            self.out.push_back(2);
        }

        #[run(3)]
        fn third() {
            self.out.push_back(3);
        }
    }
}

#[main]
fn main() {
    self.run();
    assert_eq(self.Task.SubTask.out, [42, 55, 1, 2, 3, 55]);
}
```
