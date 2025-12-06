# Stof vs. WebAssembly

## **TL;DR**

* **WebAssembly (wasm)** is a portable binary instruction format for executing compiled code at near-native speeds
* **Stof** is a text-based executable data format designed for sending logic + data together over APIs

**Key difference:** Wasm is optimized for performance. Stof is optimized for portability, flexibility, inspectability, and lightweight transmission.

{% hint style="success" %}
Stof is written in Rust and compiles to WebAssembly so that it can be embedded where you work.
{% endhint %}

## Comparison

Both let you:

* Execute code in a sandbox
* Run the same logic across different platforms
* Provide security boundaries for untrusted code
* Embed executable logic in different environments

| Feature               | WebAssembly                                   | Stof                                                                                  |
| --------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------- |
| Primary use case      | High-performance computation                  | Executable data interchange                                                           |
| Format                | Binary (.wasm)                                | Text (.stof) or Binary (.bstf)                                                        |
| Typical size          | 100KB - 10MB+                                 | 1KB - 100KB                                                                           |
| Over-the-wire         | :x: Too large for most APIs                   | :white\_check\_mark: Designed for it                                                  |
| Inspectability        | :x: Lots of additional tooling                | :white\_check\_mark: Human-readable/insepectable                                      |
| Data + logic together | :x: Separate                                  | :white\_check\_mark: Unified document                                                 |
| Execution speed       | :white\_check\_mark: Near-native              | :yellow\_circle: Interpretted (slower)                                                |
| Startup time          | :yellow\_circle: Instantiation overhead       | :white\_check\_mark: Fast                                                             |
| Browser support       | :white\_check\_mark: Native                   | :white\_check\_mark: Runtime as Wasm                                                  |
| Tooling maturaty      | :white\_check\_mark: Mature (lots of tooling) | :yellow\_circle: Growing                                                              |
| Best for              | Entire apps, games, crypto                    | Configs, workflows, APIs, distributed systems, data interchange, open protocols, etc. |

### **The Core Use Case Difference**

**WebAssembly:**

> "Compile performance-critical code once, run it anywhere fast."

Use when you need computation speed (games, image processing, cryptography).

**Stof:**

> "Send lightweight executable data that describes both what and how."

Use when you need to transmit logic + data together (APIs, configs, workflows).

***

### **Size Comparison: The Big Difference**

This is where the distinction becomes critical.

**WebAssembly:**

```rust
// Compiled from Rust
fn hello() -> String {
    "Hello, World!".to_string()
}
```

**Binary size:** \~1.8MB (including Rust std)\
**Stripped binary:** \~150KB\
**With wasm-opt:** \~20KB

***

**Stof:**

```rust
fn hello() -> str {
    "Hello, World!"
}
```

**Size:** **67 bytes**

| Task                | Wasm Size | Stof Size | Ratio        |
| ------------------- | --------- | --------- | ------------ |
| Hello World         | 20KB      | 67 bytes  | 300x smaller |
| Simple math         | 25KB      | 150 bytes | 170x smaller |
| String manipulation | 180KB     | 400 bytes | 450x smaller |
| JSON transformation | 500KB     | 2KB       | 250x smaller |
| API validation      | 1.2MB     | 5KB       | 240x smaller |
| Complex workflow    | 2.5MB     | 15KB      | 170x smaller |

**Average:** Stof is **200-300x smaller** for typical API/config use cases

## Summary

#### **Use WebAssembly when:**

* You need maximum computation speed (games, video, crypto)
* You're processing large amounts of data locally
* Binary size doesn't matter (desktop apps, embedded systems)
* You need access to existing C/C++/Rust libraries
* Performance is more important than inspectability

#### **Use Stof when:**

* You need to send logic over APIs (size matters)
* You want human-readable, inspectable code
* You need data + logic in one document
* Fast cold starts matter (serverless, edge)
* You're building configs, workflows, or data pipelines
* Inspectability and security auditing are important

#### **Use both when:**

* You need Stof for orchestration + Wasm for heavy computation
* You want lightweight transmission (Stof) with performance-critical sections (Wasm)
