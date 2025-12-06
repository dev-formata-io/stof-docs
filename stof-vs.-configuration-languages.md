---
description: Stof vs. Jsonnet/CUE/Dhall
---

# Stof vs. Configuration Languages

## **TL;DR**

* **Jsonnet**, **CUE**, and **Dhall** are configuration languages that add templating, logic, and type safety to config generation
* **Stof** goes further: configs that don't just _generate_ data, but _execute logic at runtime_

**Key difference:** Jsonnet/CUE/Dhall compile to static JSON/YAML. Stof documents remain executable.

## Comparison

All four let you:

* Write configs with variables and functions
* Reduce duplication with reusable components
* Add type safety to configuration
* Generate JSON/YAML output

| Feature         | Jsonnet                                 | CUE                                          | Dhall                                    | Stof                                                      |
| --------------- | --------------------------------------- | -------------------------------------------- | ---------------------------------------- | --------------------------------------------------------- |
| Primary use     | Config templating                       | Config validation & generation               | Typed config generation                  | Executable data                                           |
| Output          | Static JSON/YAML                        | Static JSON/YAML                             | Static JSON/YAML                         | data + logic + any format                                 |
| Runtime         | :x: Compile-time only                   | :x: Compile-time only                        | :x: Compile-time only                    | :white\_check\_mark: Sandboxed & embeddable execution     |
| Functions       | :white\_check\_mark: Template functions | :yellow\_circle: Constraints (not functions) | :white\_check\_mark: Pure functions      | :white\_check\_mark: Full functions                       |
| Type system     | :yellow\_circle: Dynamic                | :white\_check\_mark: Structural types        | :white\_check\_mark: Strong static types | :white\_check\_mark: Full type system                     |
| Validation      | :yellow\_circle: Via assertions         | :white\_check\_mark: Core feature            | :white\_check\_mark: Via types           | :white\_check\_mark: Full functional validation + schemas |
| API Portability | :x: Must compile first                  | :x: Must compile first                       | :x: Must compile first                   | :white\_check\_mark: Send document + logic                |
| I/O operations  | :x: No                                  | :x: No                                       | :x: No                                   | :white\_check\_mark: Sandboxed                            |
| Maturity        | :white\_check\_mark: Mature (Google)    | :white\_check\_mark: Growing (CNCF)          | :yellow\_circle: Smaller                 | :yellow\_circle: Growing                                  |

### **The Core Philosophical Difference**

**Jsonnet, CUE, Dhall:**

> "Write smart code that generates dumb data."

Compile your config logic down to static JSON/YAML that your application consumes.

**Stof:**

> "Write data that stays smart."

The data carries its logic and can execute at runtime, wherever it goes.

## When to Use Each

{% hint style="info" %}
Many projects use different tools for different needs, using multiple together.
{% endhint %}

### **When Compilation is Better**

**Jsonnet/CUE/Dhall when:**

1. **You want immutable, auditable configs**
   * Compiled JSON can't change at runtime
   * Perfect for GitOps workflows
2. **You need zero runtime overhead**
   * Static JSON parsing is as fast as it gets
   * Critical for high-performance systems
3. **You don't trust the config source**
   * No execution = no risk
   * Safe for untrusted third-party configs (though Stof has sandboxing for this)
4. **Your tool ecosystem requires static configs**
   * kubectl, terraform, etc. expect JSON/YAML
   * (Though Stof can output these too)

### **When Runtime Execution is Better**

**Stof wins when:**

1. **Configs need to adapt to runtime conditions**
   * Check if a service is available
   * Adjust settings based on system resources
   * Compute values from current environment
2. **You need to send logic over APIs**
   * Client sends data + transformation to server
   * Server executes safely in sandbox
   * Enables distributed workflows
3. **Validation requires external checks**
   * Test database connectivity
   * Verify API keys are valid
   * Check file permissions
4. **You want one source of truth**
   * Data, validation, and transformation in one document
   * No separate "compile" and "runtime" versions

### **Use Jsonnet if**

* You need to generate Kubernetes manifests with heavy templating
* You want a mature, Google-backed tool
* Your configs are complex but ultimately static
* You're already in the Kubernetes ecosystem

**Sweet spot:** Kubernetes config generation, Grafana dashboards

### **Use CUE if**

* You need strong schema validation at compile time
* You want data definition and validation in one language
* You're okay with a steeper learning curve for powerful constraints
* You're building config-heavy cloud-native apps

**Sweet spot:** API schemas, Kubernetes operators, config validation

### **Use Dhall if**

* You want total type safety (like Haskell for configs)
* You need guaranteed termination (no infinite loops)
* You prefer functional programming paradigms
* You're okay with a smaller ecosystem

**Sweet spot:** Infrastructure as code with maximum safety guarantees

### **Use Stof if**

* Your data needs to validate _and transform_ at runtime
* You want to send executable logic over APIs
* You need dynamic behavior based on runtime context
* You're building data pipelines, AI workflows, or smart configs
* You want one format that works everywhere (embedded, CLI, browser)

**Sweet spot:** Self-modifying configs, data pipelines, distributed systems, AI agent workflows
