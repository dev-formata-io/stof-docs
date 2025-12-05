---
description: Age Data Library ("age_encrypt" feature)
---

# Age Encryption (Age)

[Age](https://crates.io/crates/age) is a simple, modern, and secure file encryption library. Within Stof, it enables distributed & embedded Age encryption for sensitive data, in the form of a complete document or sections of a Stof document in the format of choice.

This is useful when you have a portion of a Stof document that you want to encrypt before sending to someone else (Ex. distributed systems, collaborative AI, etc.).

The API itself is a simple extension of the [standard library (Std)](standard-library-std.md) parse & blobify functions. To encrypt, use the `Age.blobify` function with Age public key(s) (recipients) as the first argument. To decrypt, use the `Age.parse` function with a `Data<Age>` component associated with one of the public keys used to encrypt as the first argument (generate one with `Age.generate` and get its public key via `Age.public`).

## Example

{% hint style="success" %}
The Age library is included in the JS bindings, so copy the below example into the [playground](https://play.stof.dev/) to see it work!
{% endhint %}

<pre class="language-rust"><code class="lang-rust">/// Receiver in real life will most likely be a remote system.
/// Public keys could come from DB, HTTP, etc.
Receiver: {
    #[private]
    passport: Age.generate()

    fn key() -> str {
        self.passport.public()
    }

    fn receive(encrypted: blob) -> str {
        const dest = new {};
<strong>        Age.parse(self.passport, encrypted, dest, "bstf");
</strong><strong>        ?dest.speak();
</strong>        dest.message ?? "none"
    }
}


#[main]
fn sending_sensitive() {
    // Create a binary blob of encrypted data that only the receiver can use
    const encrypted: blob = {
        // Create the payload, including Stof functions/APIs
        const payload = new { message: "hey there, this is secret" };
<strong>        parse(r#"fn speak() { pln('Speaking: ', self.message); }"#, payload, "stof");
</strong>
        // Can use one or more receivers Data&#x3C;Age> or str public keys (list for multiple)
<strong>        const res = Age.blobify(self.Receiver.key(), "bstf", payload);
</strong>        drop(payload);
        res
    };

    // Now have the receiver do what it wants with it
<strong>    const msg = self.Receiver.receive(encrypted);
</strong>    assert_eq(msg, "hey there, this is secret");
}
</code></pre>

#### Output

```
> stof run example.stof
Speaking: hey there, this is secret
```

## Age.blobify(recipients: str | list | Data, format: str = 'stof', context?: obj) -> blob

Std.blobify, but with age public-key recipients. The resulting blob can only be parsed by a recipient's private key.

## Age.generate(context: obj = self) -> Data

Generate a new Age Identity (Data) on the given context object (default is self).

## Age.parse(age: Data, bin: blob, context: obj = self, format: str = "stof") -> bool

Parse an age-encrypted binary. Similar to Std.parse, but requires an Age identity (secret private key).

## Age.public(age: Data) -> str

Get the public key for a given age identity.
