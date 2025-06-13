---
description: Stof filesystem library ("fs").
icon: cabinet-filing
---

# Filesystem Library

The filesystem library is a part of Stof, however, is separately organized because it is system-dependent and cannot (and should not) be broadly implemented for all embedded environments.

All Stof filesystem calls go through this library though, so if this library is replicated or added in other environments, those features of Stof will work (primarily import statements). For example, in TypeScript, server environments (like Deno or Node.js) have filesystem calls that can be used to give Stof access to filesystem resources, but must do so explicitly (Stof is meant to be in the dark by default).

### <mark style="color:purple;">fs</mark><mark style="color:orange;">.write</mark>(path: <mark style="color:green;">str</mark>, contents: <mark style="color:green;">str</mark>): <mark style="color:green;">void</mark>

Will create a text file at the given path with the provided contents if it does not exist and will entirely replace its contents if it does.

### <mark style="color:purple;">fs</mark><mark style="color:orange;">.write\_blob</mark>(path: <mark style="color:green;">str</mark>, contents: <mark style="color:green;">blob</mark>): <mark style="color:green;">void</mark>

Will create a binary file at the given path with the provided blob content if it does not exist and will entirely replace its contents if it does.

### <mark style="color:purple;">fs</mark><mark style="color:orange;">.read</mark>(path: <mark style="color:green;">str</mark>): <mark style="color:green;">str</mark>

Reads a text file into a string. Will throw an error if the file does not exist.

{% hint style="info" %}
Used in [format](../../formats.md) implementations to read text files (up to the format to decide). Implementing this library function will enable Stof to import files of any format that uses it.
{% endhint %}

{% hint style="info" %}
For anyone implementing a custom format, the helper function "fs\_read\_string" exists on the Rust Stof document to make this easy, ensuring other embedded environments have access.
{% endhint %}

### <mark style="color:purple;">fs</mark><mark style="color:orange;">.read\_blob</mark>(path: <mark style="color:green;">str</mark>): <mark style="color:green;">blob</mark>

Reads a binary file into a blob. Will throw an error if the file does not exist.

{% hint style="info" %}
Used in [format](../../formats.md) implementations to read binary files (up to the format to decide). Implementing this library function will enable Stof to import files of any format that uses it.
{% endhint %}

{% hint style="info" %}
For anyone implementing a custom format, the helper function "fs\_read\_blob" exists on the Rust Stof document to make this easy, ensuring other embedded environments have access.
{% endhint %}
