---
description: A singular & portable AI workflow artifact
---

# Stof Workflows

AI workflows are currently a mess, being fragmented across:

* Langchain/LlamaIndex (Python libraries with tons of dependencies)
* N8N/Zapier (visual tools with vendor lock-in)
* Custom scripts that are scattered, fragile, and difficult to maintain
* YAML, TOML, JSON, etc., configs that need separate execution engines

{% hint style="success" %}
No current solution offers a combination of portability + programmability + safety for fully describing and executing AI workflows.
{% endhint %}

## Write Once, Run Anywhere

Stof documents are:

* **Portable:** Send the entire workflow artifact over an API or store it in a database
* **Self-describing:** The data, validation, and execution logic travel together
* **Versionable:** Track workflow evolution like code
* **Inspectable:** See exactly what a workflow does without executing it
* **Cloud-agnostic:** Run on AWS/GCP/Azure/on-prem

## Not Another Library

Stof is a standard rather than a library. Instead of "yet another config format", Stof is a portable, self-contained workflow artifact that can run anywhere.

What really differentiates Stof in this landscape is that Stof workflows are **transmissible artifacts**. You can:

* Email a workflow
* Store workflows in Postgres
* Version workflows in Git like they're data (because they are)
* Compose workflows by importing them
* Execute untrusted workflows safely (sandboxing)
