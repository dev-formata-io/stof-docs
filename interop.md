---
description: Stof was created for interoperability and extensibility.
---

# Interop

Reads and writes JSON, YAML, TOML, and more. Drop it into your existing stack.

***

## Example: YAML in, JSON out

Start with a server config you already have:

```yaml
# server.yaml
server:
  port: 4000
  address: "my-server.com"
  memory: 3000
  ttl: 5000
```

Create our server Stof type with validation meta-logic:

```rust
// types.stof

#[type]
Server: {
    #[schema((target_val: int): bool => target_val > 1024 && target_val <= 65536)]
    int port: 8080

    #[schema((target_val: str): bool => target_val != "")]
    str! address: "localhost"

    /// 3000 from YAML becomes 3000MB — auto-converted to GiB for display
    #[schema((target_val: MiB): bool => target_val >= 512MB)]
    MiB memory: 1GiB

    /// 5000 from YAML becomes 5000ms — compared against 1s automatically  
    #[schema((target_val: ms): bool => target_val >= 1s)]
    ms ttl: 30s

    fn url() -> str {
        `https://${self.address}:${self.port}`
    }
}
```

Parse it into Stof, validate it, and export as JSON:

```typescript
import { StofDoc } from "@formata/stof";

// Create a new doc sandbox
const doc = await StofDoc.new();

// Parse types and yaml
doc.parse('// types.stof');
doc.parse('# server.yaml', 'yaml');

// Validate & transform
doc.parse(`
fn validate() {
    const server = self.server as Server;
    assert(<Server>.schemafy(server));
    
    self.url = server.url();
    drop(self.Server);
}`);
await doc.call('validate');

// Export as JS Object
console.log(doc.record());
```

Run it:

```bash
> bun run example.ts
{
  url: "https://my-server.com:4000",
  server: {
    port: 4000,
    address: "my-server.com",
    memory: 3000,
    ttl: 5000,
  },
}
```

The YAML had no idea what `512MB` or `1s` meant. Stof did — and validated against them automatically.

***

## Supported formats

| Format                                       | Read/Parse     | Write/Export |
| -------------------------------------------- | -------------- | ------------ |
| JSON                                         | ✓              | ✓            |
| YAML                                         | ✓              | ✓            |
| TOML                                         | ✓              | ✓            |
| Markdown                                     | ✓              | ✓            |
| URL-Encoded                                  | ✓              | ✓            |
| Bytes                                        | ✓              | ✓            |
| BSTF                                         | ✓              | ✓            |
| Stof                                         | ✓              | ✓            |
| DOCX                                         | ✓  (not in TS) | no           |
| Image (png, jpeg, gif, webp, tiff, bmp, ico) | ✓  (not in TS) | no           |
| PDF                                          | ✓  (not in TS) | no           |
| pkg (stof packages)                          | ✓              | ✓            |
| TXT                                          | ✓              | ✓            |

{% hint style="info" %}
Stof formats are extensible — adding or changing one is a simple trait implementation in Rust.
{% endhint %}

***

## What's next?

* [Formats](/broken/pages/qBnXxtv7vRe1h4I8UK9I) — full format reference
* [Schemas](common-patterns/schemas.md) — validation in depth
* [Types](core-concepts/types/) — prototypes and casting
