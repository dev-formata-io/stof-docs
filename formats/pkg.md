---
description: Stof package format ("pkg").
---

# Pkg

The package format enables one to work with directories of data instead of individual files, either zipping them up into a ".pkg" file, importing a ".pkg" file, importing a directory with a "pkg.stof" file, or using a ".stof" file to define imports (like a config).

## Importing a Directory/Project

Any directory with a "pkg.stof" file can be imported with the "pkg" format, with imports relative to this directory specified in the "import" field within the "pkg.stof" Stof file.

### Example Project Directory

Imagine these 2 files placed in a "stof/geo" directory (relative to the current working dir):

{% hint style="info" %}
Every import path in the "import" field (can be a list of them) is relative to the directory where the pkg.stof file is defined.
{% endhint %}

{% tabs %}
{% tab title="pkg.stof" %}
```rust
import: "point"
```
{% endtab %}

{% tab title="point.stof" %}
```rust
#[type]
GeoPoint: {
    float x: 0;
    float y: 0;

    fn length() -> float {
        Num.sqrt(self.x.pow(2) + self.y.pow(2))
    }
}
```
{% endtab %}
{% endtabs %}

### Importing the Example Directory

And then a main Stof file is placed in the current working directory that imports the package:

{% hint style="info" %}
Note: remember "@" gets converted into "stof/" by the parser ([imports.md](../learn-stof/imports.md "mention")).
{% endhint %}

```rust
import pkg "@geo"; // looks at "stof/geo/pkg.stof" and imports "import" field path

#[main]
fn main() {
    const point = new GeoPoint { x: 2, y: 2 };
    // work with point
}
```

### Importing the Pkg File

It also works to import a Stof file using this format, which will also look for an "import" field:

```rust
import pkg "stof/geo/pkg.stof"; // this does the same thing as the last example
```

## Creating a Pkg File

It's also a common need to pass many Stof files around as a single file. This can be done with the ".pkg" file (a zip variant).

To create a package file, use the Stof CLI tool:

```
stof pkg <DIR PATH> <OPTIONAL OUT PATH>
```

{% hint style="info" %}
The default output path is "\<DIR PATH>/out.pkg".
{% endhint %}

### Importing a Pkg File

Importing a ".pkg" file is quite easy since the file extension already references the correct format:

```rust
import "out.pkg"; // unpacks and uses "pkg.stof" file contained to import
```
