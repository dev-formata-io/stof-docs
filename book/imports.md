---
description: Import data into Stof.
---

# Imports

Stof can import any format for which it has an implementation. By default, this includes other popular formats like JSON, YAML, TOML, etc. But this can also include more complex formats like PDF, Images, DocX, etc.

Formats are extensible and replaceable, so feel free to create your own if you'd like! Even better if you add it to Stof so that everyone can benefit.

{% hint style="info" %}
Any format can also be used with `Std.parse(..)` to import data, found in the [standard-library-std.md](../libraries/standard-library-std.md "mention").
{% endhint %}

## Import Syntax

```
import <OPTIONAL FORMAT> "PATH.extension" as <OPTIONAL LOCATION>
```

The most basic import statement is `import "file"` , where "file" will be assumed to have an extension of ".stof" (normal Stof file).

## Relative Paths

Import paths will always be relative to the current working directory (where stof CLI/parse was invoked). However, imports also support relative paths that start with "./", to support the navigation between directories while parsing.

Take the following directory structure for example:

```
- main.stof
- dir
    - mod.stof
    - file.stof
```

Running this example would be the command: `stof run main.stof`.

Inside main would be an import statement: `import "./dir/mod"`.

Inside mod would be an import statement: `import "./file"`. The relative path will now include the "dir" directory.

Remember the default import extension/format is "stof", so this directory structure with these import statements would work just fine.

## Import Format

Unless explicitly specified, the format used will always be the file extension. For example: `import "report.pdf"` will search for a format with an identifier of "pdf" and use it if found.

To ensure a specific format is used, place your desired format identifier like so: `import text "file.stof"`. This import statement will not import the Stof like normal; instead will use the "text" format, creating a "text" field with the (str) contents of the file at the current location (self).

## Import Location

Unless explicitly specified, the import location will always be the calling context (self). So if you have an import statement within an object, that object will be the destination for the import data.

```rust
Object: {
    import "./docs.md"; // relative paths start with "./"
    
    // Object.md field now contains the str contents of "docs.md"
    fn markdown() -> str { self.md }
}
```

To specify a location where you'd like the data imported, use the "as" keyword.

```rust
import "./docs.md" as self.Object; // will create self.Object if needed

// Object.md field now contains the str contents of "docs.md"
fn markdown() -> str { self.Object.md }
```

Keep in mind that an "as" path is always absolute. The following import would create a new document root called Object instead of a new Object on self:

```rust
import text "./docs.md" as Object; // will create a new root Object if needed

// Object.text field now contains the str contents of "docs.md"
fn markdown() -> str { Object.text }
```

## Path "@" -> "stof"

If an import path contains an "@", Stof will transform it into "stof/". This is helpful for defining packages and eventually a package manager.

{% hint style="info" %}
The "pkg" format works with directories containing a "pkg.stof" file that has an "import" field with file(s) (paths) to import (all relative to the directory that contains the "pkg.stof" file).

This, in combination with "@" can create a package import feel that is familiar coming from other languages.
{% endhint %}

```javascript
import "@formata";
import "stof/formata.stof"; // equivalent import

// using the pkg format as mentioned in the hint above
// uses an "import" field located in "stof/formata/pkg.stof"
// string path(s) at this field location are relative to "stof/formata"
import pkg "@formata";
```

## Stof Importing Stof

Because of how Stof is implemented as a format, you can parse and modify Stof with Stof functions, just like any other data format.

{% hint style="info" %}
You might be thinking that this is a security nightmare, but the host environment controls every aspect of how Stof can interact with it (libraries & formats).

These cannot be manipulated from within Stof. This keeps the integrity of the Stof sandbox, limiting any parsed functions from touching the underlying system (even if it's external, like from an HTTP request).
{% endhint %}

```rust
#[main]
fn main() {
    const stof = r#"
        fn my_function() {
            pln("Isn't this cool!");
        }
    "#;
    parse(stof, self, format = "stof");
    
    // can use it immediately
    self.my_function();
}
```
