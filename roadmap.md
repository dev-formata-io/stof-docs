---
description: Stof Roadmap.
---

# 🗓️ Roadmap

## Community

Please open an issue and/or discussion on our [GitHub](https://github.com/dev-formata-io/stof). Our community is early, but very active and inviting.

Hop into the [Discord server](https://discord.gg/Up5kxdeXZt) to see the latest updates, tech discussions, feature requests, and random Stof. This is the best way to get involved.

Contact me at cj@stof dot dev for anything else. Always happy to chat with fellow developers!

## :ship: MVP

* [x] DAG & ECS Foundation
* [x] [Field Data](book/fields.md)
* [x] [Function Data](book/functions.md)
* [x] Attribute System
* [x] Async Runtime & Instructions
  * [x] [Async functions](book/functions.md#async), blocks, & expressions
* [x] Variable & Field Const
* [x] [References](book/variables-and-references.md) & Loop Refs
* [x] Stof Format & Parser (w/native JSON syntax support)
* [x] [Prototypes](book/types/prototypes.md)
* [x] Canonical Format API (parse, stringify, & blobify)
* [x] Canonical Library API (extend & control Stof's host access/interface)
* [x] [Standard Library](libraries/standard-library-std.md) & [Type Libs](libraries/type-libraries/) (lists, maps, sets, asserts, time, etc.)
* [x] Basic Formats (JSON, YAML, TOML, text, md, bytes, etc.)
* [x] [Unit Types (ms, GiB, kg, etc.)](book/types/numbers.md)
* [x] Basic Language Features ([loops](book/loops.md), [null checks](book/null-and-initialization.md), [error handling](book/error-handling.md), etc.)
* [x] [Imports](book/imports.md)
* [x] Data Validate/Invalidate (background diffs & partial updates)

## :rocket: v1.0.0

* [x] [Schema](book/schemas.md) & [Schemafy](libraries/type-libraries/object-library-obj.md#obj.schemafy-schema-obj-target-obj-remove_invalid-bool-false-remove_undefined-bool-false-greater-tha)
* [x] [Object Run](book/object-run.md) & [Standard Pipelines](libraries/type-libraries/object-library-obj.md#obj.run-obj-obj-greater-than-void)
* [ ] [Time Library](libraries/time-library-time.md)
  * [x] Async [Sleep](libraries/time-library-time.md#time.sleep-time-float-1000ms-greater-than-void) & [Std Sleep](libraries/standard-library-std.md#std.sleep-time-ms-greater-than-void)
  * [x] Now & Diff
  * [x] [RFC-2822](libraries/time-library-time.md#time.to_rfc2822-time-float-greater-than-str) & [RFC-3339](libraries/time-library-time.md#time.to_rfc3339-time-float-greater-than-str)
  * [ ] Set Timeout & Intervals
* [x] [Image Library](libraries/image-library-image.md) w/Basics (flip, brighten, contrast, thumbnail, etc.)
* [x] [PDF Library](libraries/pdf-library-pdf.md) w/Basics (extract text & extract images)
* [x] [HTTP Library](libraries/http-network-library-http.md) with async background [fetch](libraries/http-network-library-http.md#async-http.fetch-url-str-method-str-get-body-str-or-blob-null-headers-map-null-timeout-seconds-null)
* [ ] Format Updates
  * [ ] Stof Format String Export
  * [ ] Optional Binary Export Encryption
  * [ ] XML & HTML
* [ ] WebAssembly
  * [ ] Embed Stof in JS/browser environments
  * [ ] WebAssembly native to Stof (lib & types)
* [ ] Language Support
  * [ ] Go, Python, C, etc. (hop into Discord to discuss)
* [ ] Examples
  * [x] [Generate Configs](https://github.com/dev-formata-io/stof/blob/main/project.stof) & Settings
  * [ ] Single Endpoint Server w/Stof API
  * [ ] AI workflow & orchestration
  * [ ] AI data pre-processing, validation, and tagging
  * [ ] Embedded Plugin
  * [ ] ETL pipeline w/processing & transform logic
* [ ] Web Playground (play.stof.dev)
* [ ] Hosted Packages & Manager (pkg.stof.dev)
* [ ] Complete Docs & Website
  * [x] Basic Book/Guide for Getting Started
  * [x] Simple Roadmap
  * [ ] Tutorial
  * [x] Basic Libraries
  * [x] Basic Formats
  * [ ] Sponsorship & Partners
  * [ ] Use Case Highlights
    * [ ] Scientific Computing
    * [ ] Artificial Intelligence
    * [ ] Extract-Transform-Load
    * [ ] Embedded & Distributed Systems
    * [ ] Configuration Management
    * [ ] API & Database Systems

## :computer: CLI

### Minimum Viable

* [x] Run (#\[main] attribute funcs by default, but customizable)
* [x] Test (#\[test] attribute functions)
* [x] Docs MVP (simple version of MD docs)
* [x] Pkg Format (zip file behavior to bundle Stof files)
* [x] Special "stof" directory mapped to "@" in [imports](book/imports.md#path-greater-than-stof)

### Planned

* [ ] Unpack PKG files
* [ ] Package Registry & Management
* [ ] Remote Execution
* [ ] Availability in CI/CD, cloud, etc.
