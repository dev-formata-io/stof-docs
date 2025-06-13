---
description: Targeting systems that do not understand Stof (yet).
---

# Exporting Data

Just like a lot of data is not defined as Stof, a lot of systems are unaware of Stof, but aware of some other form of data.

Stof [formats](../../reference/formats.md) export data just as easily as they import data!

In this example, we'll import some data, mutate it a little, and then export it in another standard interchange format.

This simulates receiving data (either from a file, API, etc.), parsing it into a Stof interface as an interchange transform step, and then exporting it to a binary stream or string to use in another API call. The same transform step would be valid in preparing this document to be used directly by an application, however, we are demonstrating the "stringify" and "blobify" functions found in the [Standard Library](../../reference/libraries/standard-library.md).

{% tabs %}
{% tab title="Stof" %}
```rust
import "./record.toml" as Record; // creates a new root named "Record"

#[main]
fn main() {
    Record.renameField("resourceType", "type");
    drop Record.active;
    for (name in Record.name) {
        name.renameField("family", "last");
        name.renameField("use", "purpose");
    }

    let binary = blobify(Record, "json"); // Uint8Array or Vec<u8>
    parse(binary, "json", "self.Clone");  // parse the binary into location "self.Clone"

    let json = stringify(Record, "json");
    assertEq(json, stringify(self.Clone, "json"));

    pln(stringify(Record, "toml")); // lots of formats to choose from... or add your own!
}
```

Make sure to install the [Stof CLI](../../reference/cli/) and use the "run" command to test this function.

{% hint style="info" %}
Take a look at the [Object Library](../../reference/libraries/object-library.md) and the [Stof Language Reference](../../reference/stof-language.md) for more information on the "renameField" function and "drop" statement used in this example.
{% endhint %}

```
> stof run ./example.stof
id = "example"
type = "Patient"

[[name]]
given = ["Peter", "James"]
last = "Chalmers"
purpose = "official"

[[name]]
given = ["Jim"]
purpose = "usual"

[[name]]
given = ["Peter", "James"]
last = "Windsor"
purpose = "maiden"

[name.period]
end = "2002"
```
{% endtab %}

{% tab title="record.toml" %}
```toml
active = true
id = "example"
resourceType = "Patient"

[[name]]
family = "Chalmers"
given = ["Peter", "James"]
use = "official"

[[name]]
given = ["Jim"]
use = "usual"

[[name]]
family = "Windsor"
given = ["Peter", "James"]
use = "maiden"

[name.period]
end = "2002"
```
{% endtab %}
{% endtabs %}
