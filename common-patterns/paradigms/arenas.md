---
description: Arena memory management.
---

# Arenas

[Arena (Region-based) memory management](https://en.wikipedia.org/wiki/Region-based_memory_management) is not a new concept. However, it's worth pointing out as a pattern for Stof because it can be beneficial for controlling object memory within a Stof document.

Every time an [object](https://docs.stof.dev/book/types#objects) is created in Stof, a node is created within the document. This is a [general bucket](../../core-concepts/design.md) for fields, functions, and other complex data.

A common situation that arises when creating objects functionally is where in the document should that object exist so that it can be dropped later on?

This is where a pre-allocated object "arena" can be very helpful.

The idea is that by passing an object around as a designated parent (document region/arena), it can be managed later on very easily.

## Example

In this example, there is a Prompt type with a function "new" for creating new prompts. By passing an optional arena for the new prompt object to be created within, all of the created prompts can be cleaned up at once.

{% hint style="info" %}
In real life, these functions may be nested, making the arena pattern even more beneficial.

Typically, this pattern arises when using "static" functions or calling functions on [prototypes](../../core-concepts/types/prototypes.md) directly.
{% endhint %}

<pre class="language-rust" data-line-numbers><code class="lang-rust">#[type] // definition of a prototype object named "Prompt"
Prompt: {
    str! text: ''
    list! blocks: []

    #[static] // optional, but recommended for human eyes
    fn new(text: str!, blocks: list = [], arena?: obj) -> Prompt {
<strong>        new Prompt { text, blocks } on arena
</strong>    }
    fn push(prompt: Prompt) {
        self.blocks.push_back(prompt);
    }
    fn out(level: int = 0) -> str {
        const out = self.text;
        const indent = '';
        for (let _ in level + 1) indent.push('\t');
        for (const prompt: Prompt in self.blocks) out.push(`\n${indent}${prompt.out(level + 1)}`);
        out
    }
}

#[main]
fn main() {
<strong>    const arena = new {};
</strong>    const top = &#x3C;Prompt>.new('Title', arena = arena);
    
    const mid = &#x3C;Prompt>.new('Middle', [
        &#x3C;Prompt>.new('First', arena = arena),
        &#x3C;Prompt>.new('Second', arena = arena),
    ], arena);
    top.push(mid);

    const bot = &#x3C;Prompt>.new('Bottom', [
        &#x3C;Prompt>.new('First', arena = arena),
    ], arena);
    top.push(bot);

    pln(top.out());

    // All of the objects allocated have been in the arena
    assert_eq(self.children().len(), 2); // Prompt type and arena obj
    assert_eq(arena.children().len(), 6);
<strong>    drop(arena);
</strong>
    assert_eq(self.children().len(), 1); // just the Prompt object
    assert_not(arena.exists()); // all prompts have been removed from the document
}
</code></pre>

### Output

```bash
> stof run arena.stof
Title
    Middle
        First
        Second
    Bottom
        First
```

### Summary

Because all of the prompts have been allocated within the arena object, the arena can be dropped (line 43) to clean up all prompt objects, regardless of where they were created (nested functions, APIs, etc.).

On line 8, the "new" syntax programmatically allocates a new node/object within the document. The "on" syntax will specify the parent for the new object (or "self" if null or not an object).

## Insights

Within a Stof document, there's a mix of persistent data that is meant to live for the length of the document, and data that is created temporarily for programmatic usage.

Any objects created for temporary use within a function need to be manually dropped.

{% hint style="info" %}
Dropping objects to manage document memory only matters when you store/re-use the entire document. An example would be exporting the document using the "bstf" format, which includes all objects, functions, etc.
{% endhint %}

The arena pattern is a good one for managing temporary document objects, typically when a static prototype function is involved in the creation of new objects.

The "on" syntax within the "new" object expression can always be used to control where the object is created in the document.
