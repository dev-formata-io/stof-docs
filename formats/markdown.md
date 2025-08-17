---
description: Markdown format (text/markdown).
---

# Markdown

Just like the text format, the "md" format imports/exports plain text to/from an "md" field.

{% tabs %}
{% tab title="test.md" %}
```markdown

# Title
This is an example.

```
{% endtab %}

{% tab title="test.stof" %}
```rust
import './test.md'; // has both "md" and "markdown" as identifiers

#[main]
fn main() {
    assert_eq(self.md, r#"
# Title
This is an example.
"#);
}
```
{% endtab %}
{% endtabs %}
