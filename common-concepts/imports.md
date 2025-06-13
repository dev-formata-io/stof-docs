---
description: Overview of the import statement in Stof.
---

# Imports

In Stof, formats are dynamic and can be added and replaced to be used from a host environment, an import statement, or the Stof standard library (parse, stringify, etc.).

### Import Statement Syntax

```
import <FORMAT (OPTIONAL)> "<PATH>" as <STOF OBJECT PATH (OPTIONAL)>;
```

### Relative Paths

An absolute path resolves files from the directory Stof is being executed from.

```java
import "dir/file.stof"; // looks for "CWD/dir/file.stof"
```

```java
import "./file.toml"; // looks in parallel to the file containing this statement
```

### Stof Imports

When importing additional Stof documents, the ".stof" extension is optional. The following two import statements are equivalent:

```java
import "additional"
```

```java
import "additional.stof"
```

### Simple Import Example

```python
import "example.json";
```

This import statement is as basic as it gets. Here, we are importing a JSON file into the "root" object of this Stof document. Stof will see the format is "json" from the file extension and look for that format in its collection of known formats it can use. JSON happens to be a format that comes with Stof, so this will work out of the box.

### Explicit Format Specifier

```python
import text "example.json";
```

In this example, we are importing a JSON file, but we want it parsed as a text file, using the default "text" format that Stof provides. If a file specifier is provided, Stof will look for a format with that name in its known formats. For the "text" format, Stof will read the entire file as a string, placing the contents in a field named "text". In this case, that field will be placed on the "root" of this document.

### Specific Import Location

```python
import "example.json" as Import;
```

Import locations are paths, just like in other areas of Stof. In this example, we are importing the JSON document's fields and values into another root node named "Import".

After this statement, you'd be able to reference fields via the absolute path `Import.<field name>` from anywhere in the Stof document.

#### Relative Path Example

```python
import "example.json" as self.Import;
```

In this import statement, we are placing the contents of "example.json" into an object named "Import" as a child of the object containing the import statement.

After this statement, you'd be able to reference fields via the relative path `self.Import.<field name>` from the object containing the import.

#### Location Collisions

If the object does not exist (anywhere in the path), Stof will create it. If it already exists, Stof will merge the fields in that location as needed.

```python
import 'example.json' as self.myobj.specialobj.dude.import;
```

In this instance, if "myobj" already exists as a child of this object, it will be utilized. However, Stof will create any objects it needs to make this path valid.

### Full Example

file: <mark style="color:purple;">`import.json`</mark>

```json
{
    "name": "JSON Import",
    "purpose": "Some data that can be checked",
    "test": true,
    "number": 42
}
```

file: <mark style="color:purple;">`location.stof`</mark>

```rust
import "./import.json";
import text "./import.json";
import "./import.json" as self.JsonImport;
import "src/tests/imports/import.json" as TestRootImports; // absolute path

#[test]
fn basic_import() {
    assertEq(self.name, "JSON Import");
    assertEq(self.number, 42);
}

#[test]
fn root_import() {
    assertEq(TestRootImports.name, "JSON Import");
    assertEq(TestRootImports.number, 42);
}

#[test]
fn format_specifier() {
    assert(self.text.len() > 0);
}

#[test]
fn location_specifier() {
    assertEq(self.JsonImport.name, "JSON Import");
    assertEq(self.JsonImport.number, 42);
}

local_imports: {
    import json "src/tests/imports/import.json";
    import "src/tests/imports/import.json" as self.JsonImport;

    #[test]
    fn local_import() {
        assertEq(self.name, "JSON Import");
        assertEq(self.number, 42);
    }

    #[test]
    fn location_specifier() {
        assertEq(self.JsonImport.name, "JSON Import");
        assertEq(self.JsonImport.number, 42);
    }
}
```
