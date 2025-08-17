---
description: Embed Stof within Rust on your own terms.
---

# Custom Embedded

Stof is meant to be embeddable within the environment of your choice. Because it is written in Rust, we'll give a brief Rust overview here. Take a look at the [source code](https://github.com/dev-formata-io/stof) or [crate](https://crates.io/crates/stof) for more information.

{% hint style="info" %}
We're adding more embedded environments - if language interop is interesting to you, please get involved.
{% endhint %}

```rust
use stof::model::Graph;

/// This is Rust now, not Stof.
fn main() {
    let mut graph = Graph::default();
    
    graph.parse_stof_src(r#"
        message: "Hello, world!"
        
        #[main]
        fn main() {
            pln(self.message);
        }
    "#, None).expect("error parsing Stof");
    
    // this will run all #[main] functions
    // can always call functions individually with graph.call(..)
    match graph.run(None, true) {
        Ok(res) => println!("{res}"),
        Err(res) => println!("{res}"),
    }
}
```

## Sandboxing

Stof interfaces with the outside world through libraries and formats only. A library is just a map of LibFunc implementations, so you can partially remove, replace, extend, etc., any library of your choice. The same goes for formats, where you can import or export data as you wish, either from Stof or Rust.

```rust
use stof::model::{Graph, Val};

/// This is Rust now, not Stof.
fn main() {
    let mut graph = Graph::default();
    
    // remove file system lib (was only added if the "system" feature is enabled)
    // will remove all "fs" functions (read & write)
    // no longer possible to interface with the file system via Stof
    graph.remove_lib(&"fs".into());
    
    // remove Std.pln(..)
    // in real life, you might want to replace it with a different implementation
    // in this case, Std.pln(..) no longer exists and will throw a "not found" error
    graph.remove_libfunc(&"Std".into(), "pln");
    
    // remove TOML format
    // no longer possible to import/export TOML (parse, stringify, & blobify too)
    // in real life, you might want to replace it or add your own formats
    graph.remove_format("toml");
    
    // parse some JSON src into the graph
    graph.string_import("json", r#"
    {
        "message": "Hello, JSON!!"
    }
    "#, None).expect("error parsing JSON");
    
    // parse some Stof src into the graph
    // the "parse_stof_src" function (last example) just calls "string_import"
    graph.string_import("stof", r#"
        fn json_message() {
            // pln(self.message); // this will error, because we removed pln
            err(self.message); // error stream function still there
        }
    "#, None).expect("error parsing Stof");
    
    // call Stof function - printing our JSON message to the error stream
    let res = graph.call("json_message", None, vec![]).unwrap();
    assert_eq!(res, Val::Void); // function returns void
}
```
