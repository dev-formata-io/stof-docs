# Where Does Stof Fit?

Stof occupies a unique space in the software ecosystem: **executable data that's lightweight enough to send over APIs**. This page helps you understand how Stof compares to existing tools and when to choose it.

## **The Landscape**

Here's how Stof relates to major categories of tools:

```
Configuration Languages     Serialization Formats      Workflow Engines
(Jsonnet, CUE, Dhall)      (JSON, Protobuf, YAML)    (Temporal, Airflow)
        ↓                           ↓                          ↓
        └───────────────────────────┴──────────────────────────┘
                                    │
                              🎯 STOF 🎯
                    (Executable Data Format)
                                    │
        ┌───────────────────────────┴──────────────────────────┐
        ↓                           ↓                          ↓
   Validation Tools          Portable Runtimes          Query Languages
   (JSON Schema)             (WebAssembly)              (jq, JSONata)
```

Stof bridges these categories by treating **logic as first-class data**.

## **For Configuration**

#### **Stof vs. Jsonnet / CUE / Dhall**

**What they do:** Generate static configs with templating and type safety\
**What Stof does:** Configs that stay executable at runtime

**Key difference:**

Instead of a `Smart Config -> Compile -> Dumb JSON` workflow (Jsonnet, CUE, Dhall), Stof documents stay smart everywhere.

**Use Jsonnet/CUE/Dhall when:**

* You want immutable, auditable configs
* Compile-time validation is sufficient
* You're generating Kubernetes manifests
* You need zero runtime overhead

**Use Stof when:**

* Configs need runtime validation (check connectivity, schemas, types, etc.)
* Configs need to adapt based on environment
* You want logic to travel with the data
* You don't want a separate "compile" step

:book: Full comparison: [Stof vs. Configuration Languages](stof-vs.-configuration-languages.md)

## For Data Validation

#### Stof vs. JSON Schema

**What it does:** Declarative validation rules for JSON\
**What Stof does:** Self-validation with transformation

**Key difference:**

Instead of separate validation rules, Stof validation functions are embedded within it's own data and the document validates/modifies itself.

**Use JSON Schema when:**

* You need API documentation (OpenAPI/Swagger)
* Static validation is sufficient
* Maximum ecosystem compatibility matters
* Separation of schema and data is preferred

**Use Stof when:**

* Validation requires runtime checks (API calls, workflows, complex types & units, etc.)
* You need validation + transformation together
* Complex conditional logic is needed
* Validation logic (meta-logic & metadata) should travel with the data

:book: Full comparison: [Stof vs JSON Schema](stof-vs.-json-schema.md)

## For Performance

#### Stof vs. WebAssembly

**What it does:** Portable binary format for near-native speed\
**What Stof does:** Portable text/bin format for lightweight interchange

**Key difference:**

WebAssembly is optimized for speed and is well suited for full apps or performance critical code-paths. Stof is optimized for size and is used over networks (interchange), for data + logic (instead of just exec), and for flexible, inspectable, unified data documents.

**Use WebAssembly when:**

* Execution speed is critical (games, video, crypto)
* You're processing large amounts of data
* Binary size doesn't matter
* Performance > inspectability

**Use Stof when:**

* Sending logic over APIs (size matters)
* Inspectability is important
* Human-readable code + data is needed

**Size Comparison:**

**Simple Function:** WebAssembly - \~20KB (optimized), Stof - \~67 bytes (**\~300x smaller**)

**Typical API payload:** WebAssembly - \~500KB, Stof - \~100 bytes - 5KB (**\~300x smaller**)

:book: Full comparison: [Stof vs. WebAssembly](stof-vs.-webassembly.md)

## For Workflow Orchestration

#### Stof vs. Temporal / Airflow / Perfect

**What they do:** Orchestration platforms with servers, DBs, and UIs\
**What Stof does:** Workflows as open-source, portable documents (no infrastructure)

**Key difference:**

**Temporal/Airflow:** Deploy platform -> Deploy workflows -> Monitor

**Stof:** Write workflow -> Execute anywhere

**Use Temporal/Airflow/Prefect when:**

* Workflows run for days/weeks/months
* You need rich monitoring and debugging UIs
* Durable state across restarts is critical
* Team collaboration features matter
* You can afford infrastructure costs

**Use Stof when:**

* Workflows are lightweight (minutes/hours)
* You don't want to manage infrastructure
* Workflows need to run on edge/IoT devices
* Workflows travel as API payloads
* Cost and simplicity matter

:book: Full comparison: [Stof vs. Temporal/Airflow](stof-vs.-temporal-airflow.md)

## Use Cases & Tool Mapping

| Use Case                         | Traditional Tool           | Stof Advantage                  |
| -------------------------------- | -------------------------- | ------------------------------- |
| API request/responses with logic | REST + separate code       | Logic travels with data         |
| Validating config                | JSON + validation code     | One file, validates itself      |
| Edge computing workflow          | Compile & deploy code      | Send document, run anywhere     |
| User-defined transformations     | Sandboxed JS (eval)        | Safer, inspectable, lightweight |
| Data pipeline                    | Airflow DAG                | No infrastructure needed        |
| Smart contract                   | Solidity (blockchain)      | Lighter, more portable          |
| AI agent workflow                |  Langchain + code          | Workflow is data                |
| IoT data processing              | C/C++ firmware             | Update logic without refresh    |
| Serverless function              | AWS Lambda (code deploy)   | Function is a document          |
| Dynamic config                   | Jsonnet -> compile -> JSON | Skip compilation, stay dynamic  |

## **The Bottom Line**

**Stof is best when you need:**

1. **Data and logic together** (one portable document)
2. **Lightweight transmission** (APIs, edge, serverless)
3. **Inspectable code** (security, debugging)
4. **Fast iteration** (no compilation step)
5. **Portability** (runs anywhere)
6. **Open Source** (create your own solution)

**Stof is NOT ideal when you need:**

1. **Maximum speed** (use WebAssembly/native)
2. **Enterprise orchestration** (use Temporal/Airflow)
3. **Compile-time guarantees** (use CUE/Dhall)
4. **Rich monitoring UI** (use workflow platforms)
5. **Massive ecosystem** (use established tools)

## **Next Steps**

#### **New to Stof?**

1. Try the 5-minute [Quick Start](getting-started/quick-start-5-min.md)
2. [Play with examples in the online playground](https://play.stof.dev/)
3. Follow a [tutorial](getting-started/tutorial-stof-+-typescript-config.md)

#### **Ready to adopt?**

1. [See installation options](getting-started/installation.md)
2. [Explore the standard library](libraries/standard-library-std.md)
3. [Join the Discord community](https://discord.gg/Up5kxdeXZt)

#### **Want deeper comparisons?**

* [Stof vs. JSON Schema (validation)](stof-vs.-json-schema.md)
* [Stof vs. Jsonnet/CUE/Dhall (config generation)](stof-vs.-configuration-languages.md)
* [Stof vs. WebAssembly (portable code)](stof-vs.-webassembly.md)
* [Stof vs. Temporal/Airflow (workflows)](stof-vs.-temporal-airflow.md)

***

**Questions?** Join our [Discord](https://discord.gg/Up5kxdeXZt) or [open a discussion on GitHub](https://github.com/dev-formata-io/stof).
