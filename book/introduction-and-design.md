---
description: Understand how Stof works.
---

# Introduction & Design

At the heart of Stof is not a data format or language, but rather a tried and tested strategy for organizing and combining large amounts of diverse data types and relationships.

Stof is a special kind of [DAG](https://en.wikipedia.org/wiki/Directed_acyclic_graph) with an [Entity Component System](https://en.wikipedia.org/wiki/Entity_component_system) built into it. Data in Stof can be thought of as components, and the nodes of the DAG are entities. Each node is capable of housing an unknown amount of diverse data types.

If those words don't make a whole lot of sense, think about Stof as a fancy version of your file explorer - directories (nodes) organize different types of files (data).

The purpose of this page is to provide you with a mental model of Stof, which will help immensely in getting started and in understanding what Stof can do.

## Mental Model

<figure><img src="../.gitbook/assets/stof_simple_dag.png" alt=""><figcaption></figcaption></figure>

This structure is not new - it's one of the most common ways to store data. Every data format that exists is a DAG (or can be expressed as/within one), which is how Stof can be so universal.

### Concrete Example

The language around Stof is just a set of data components that know how to manipulate and describe the document that contains them. As such, we can express the following Stof document in terms of our mental model.

```rust
person: {
    name: {
        first: "Bob"
        last: "Jones"
    }
    fn get_name() -> str { return self.name.first + " " + self.name.last; }
}

#[main]
fn main() {
    pln(self.person.get_name());
}
```

<figure><img src="../.gitbook/assets/simple_concrete.png" alt=""><figcaption></figcaption></figure>

## Actual Model

So far, we have a convenient and human-friendly way to think about Stof. For using Stof, that's all you need to get started!

However, Stof, in reality, is a bit more complicated, so it can be more efficient and model more complex relationships.

<figure><img src="../.gitbook/assets/actual_simple.png" alt=""><figcaption></figcaption></figure>

This strategy allows Stof to structure data in many ways simultaneously and more than once, without any copies, while preserving the simplicity of our mental model (behaviorally).

Data is kept as a flat collection, completely separate from their relationships and any structure Stof introduces.

Stof's DAGs are also kept completely separate and flat, offering a lightweight and flexible way to model data. Each node can be thought of as its own DAG, but Stof also has the concept of having multiple roots. These roots can be traversed with the language and used to compartmentalize different sections of unified data entirely.

{% hint style="info" %}
Importing and parsing data into documents offers a flexible way to dynamically determine where you'd like data to be stored in relation to the other data in the document.
{% endhint %}

### Concrete Example

For consistency, let's use the same example as before:

```rust
person: {
    name: {
        first: "Bob"
        last: "Jones"
    }
    fn get_name() -> str { return self.name.first + " " + self.name.last; }
}

#[main]
fn main() {
    pln(self.person.get_name());
}
```

<div data-full-width="false"><figure><img src="../.gitbook/assets/actual_concrete.png" alt=""><figcaption></figcaption></figure></div>

Although still a bit simplistic, this is how Stof represents data and relationships. By now, you've probably put together a lot about the language - how paths are just a dot-separated way to traverse nodes by name, or maybe how objects are just fields pointing to other nodes that contain general buckets of data (not just fields and functions).
