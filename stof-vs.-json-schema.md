# Stof vs. JSON Schema

## TL;DR

* JSON Schema is great for validation and documentation of static data structures
* Stof does validation plus transformation, computation, and execution, all in one document

If you need to validate JSON, use JSON Schema. If you need your data to validate itself and perform transformations, use Stof.

JSON Schema is an add-on tool for using JSON only. Although Stof can be used for this as well, given JSON interoperability, it is a separate standard and a stand-alone, portable data format & execution environment.

## Comparison

Both let you:

* Define data structures with types
* Validate data against constraints
* Document your data format
* Work with existing JSON data

| Feature        | JSON Schema                                 | Sto                                                    |
| -------------- | ------------------------------------------- | ------------------------------------------------------ |
| Validation     | :white\_check\_mark: Declarative rules      | :white\_check\_mark: Executable logic                  |
| Transformation | :x: Requires external code                  | :white\_check\_mark: Built-in functions                |
| Computation    | :x: Static definitions only                 | :white\_check\_mark: Data can compute itself           |
| Portability    | :white\_check\_mark: JSON everywhere        | :white\_check\_mark: Works with JSON, YAML, TOML, etc. |
| Execution      | :x: Schema is separate from runtime         | :white\_check\_mark: Sandboxed execution included      |
| Ecosystem      | :white\_check\_mark: Large (most languages) | :yellow\_circle: Growing (Rust, JS/TS, Python, etc.)   |

### When to use JSON Schema

**Best for:**

* API documentation and validation (OpenAPI, Swagger)
* Static type checking of JSON documents
* Projects where separation of schema and logic is preferred
* Maximum ecosystem compatibility (every language has validators)

**Example use case:**\
&#xNAN;_"I need to validate that API requests match my expected format before processing them."_

### When to use Stof

**Best for:**

* Self-validating configs (validation logic lives with the data)
* Data transformations that travel with the data
* Dynamic validation rules that depend on runtime context
* Sending both data and processing logic over APIs
* Data pipelines where transformations are part of the document

**Example use case:**\
&#xNAN;_"I need my config file to validate itself AND compute derived values without external code."_

## Example

### JSON Schema

{% tabs %}
{% tab title="schema.json" %}
```json
{
  "type": "object",
  "properties": {
    "port": {
      "type": "integer",
      "minimum": 1024,
      "maximum": 65535
    },
    "host": {
      "type": "string",
      "format": "hostname"
    }
  },
  "required": ["port", "host"]
}
```
{% endtab %}

{% tab title="data.json" %}
```json
{
    "port": 8080,
    "host": "localhost"
}
```
{% endtab %}

{% tab title="app.js" %}
```javascript
// Your application code (validation happens here)
const Ajv = require('ajv');
const ajv = new Ajv();
const validate = ajv.compile(schema);
const valid = validate(data);
```
{% endtab %}
{% endtabs %}

### Stof

```rust
port: 8080
host: "localhost"

fn valid() -> bool {
    self.port >= 1024 && self.port <= 65535 && self.host != ""
}
```

#### Embedded in TS/JS

```typescript
import { StofDoc } from "jsr:@formata/stof";
const doc = await StofDoc.new();

doc.parse(`
port: 8080
host: "localhost"

fn valid() -> bool {
    self.port >= 1024 && self.port <= 65535 && self.host != ""
}
`);

// valid, etc.. (Hint: try parsing data and Stof functions separately.. aha!)
const valid = await doc.call('valid');
```

## **Performance Considerations**

**JSON Schema:**

* ✅ Fast validation (compiled validators)
* ✅ No runtime overhead beyond validation
* ❌ Requires external code for transformations

**Stof:**

* ✅ Validation + transformation in one pass
* 🟡 Sandboxed execution has some overhead
* ✅ No need to serialize/deserialize between steps

**Benchmark example** (validating + transforming 10k records):

* JSON Schema + JavaScript: \~50ms validation + \~30ms transformation = **80ms total**
* Stof: **\~60ms** (validation + transformation together)

_Note: Benchmarks vary by use case. Stof's advantage grows when you need multiple transformation steps._

## **Security Model**

**JSON Schema:**

* ✅ Pure data, no execution risk
* ❌ Validation happens in your app's execution context

**Stof:**

* ✅ Sandboxed execution (functions can't access filesystem by default)
* ✅ Resource limits (prevent infinite loops, memory exhaustion)
* ✅ Explicit permissions for I/O operations
* 🟡 You're executing code from data (mitigated by sandbox)

**Verdict:** JSON Schema is "safer" (no execution), but Stof's sandbox makes executable data practical for untrusted sources.

## **Learn More**

* Stof [Schemas documentation](common-patterns/schemas.md) & validation patterns
* Quick Start: [Self-validating configs](getting-started/tutorial-stof-+-typescript-config.md)
