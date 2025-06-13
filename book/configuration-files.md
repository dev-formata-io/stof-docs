---
description: Stof for configuration files.
---

# Configuration Files

Because Stof is extremely friendly for both humans and machines, it can be a good choice for your configuration files. And because Stof works seamlessly with other formats and standards, you can be more flexible (keep using JSON, YAML, etc. if already in place).

<figure><img src="../.gitbook/assets/config.png" alt=""><figcaption></figcaption></figure>

In addition to being more user-friendly, this might be particularly helpful for:

* Cloud infrastructure & deployment
* Merging, diffing, or bridging environments
* Organizing any additional data required for initialization/usage
* Validation, governance, and control

Advantages:

* Flexibility (multiple formats + portable interfaces)
* Types, schemas, and structure
* Functional interface for multiple host environments
* Not just metadata, but metacode, attached directly to your fields & data

### Example

{% hint style="info" %}
Keep in mind that for defining data, Stof is a superset of JSON, and valid JSON is valid Stof.
{% endhint %}

This example shows off some unique features of Stof. One could make this much cleaner and more readable (outside of docs) by separating the types, functions, or metadata and combining them as needed.

* Field declarations are expressions that get evaluated when parsed.
* Types and interfaces for objects, but also types and units for all other values, with conversions.
* Attributes can hold any Stof value, including functions, objects, etc. (default is null).
* Import and export data in other formats (create/send/unify configs in JSON, YAML, etc.).

```rust
/**
 * Some example metadata.
 */
publisher: {
    name: "Bob Jones"
    title: "Head of Something"
}
organization: {
    name: "Jupiter, Inc"
    goals: ["more rings", "get closer to the sun"]
    str alive_for: Number.round(234days + 20hrs + 30min + 30s, 2);
}


/**
 * Service type and example interface.
 */
type Service {
    name: str = "unknown"
    providers: vec = [];

    fn number_of_providers(): int {
        return self.providers.len();
    }
}
fn validate_service(service: Service): bool {
    return service.number_of_providers() > 0;
}


/**
 * Service configuration.
 */
#[service]
#[metadata([self.publisher, self.organization])]
#[validate(self.validate_service)]
Service service: {
    type Provider {
        name: str
        region: str
    }

    providers: [
        {
            name: "aws"
            region: "us-west-2"
            instance: {
                type: "t2.micro"
                seconds ttl: 3600000ms // types & units, finally
            }
        } as Provider,
        {
            name: "gcp"
            region: "us-central"
        } as Provider,
    ]
}


/**
 * Get all services defined in this file (objects with the attribute #[service]).
 * Creates new combined objects, validates them, and returns them.
 */
fn get_services(): vec {
    let validated_services = [];
    for (key in self.keys()) {
        let attributes = self.attributes(key);
        if (attributes.contains("service")) {
            // Create a new object in this document for the combined data
            let combined = new {
                service: super.at(key);
            };

            // Get the metadata (could do more with it of course...)
            let metadata = attributes.get("metadata");
            if (metadata && isArray(metadata)) {
                for (meta in metadata) meta.type = meta.name();
                combined.metadata = metadata;
            }

            // Validate the service if necessary
            let valid = true;
            let validate = attributes.get("validate");
            if (validate && isFunc(validate)) {
                valid = validate.call(combined.service);
            }

            if (valid) validated_services.push(combined);
        }
    }
    return validated_services;
}


/**
 * Main function for testing with the CLI.
 * Can call functions from app or add more dynamically (they're just data).
 */
#[main]
fn main() {
    let services = self.get_services();
    let record = new {
        services: services;
    };
    pln(stringify(record, "yaml"));
}
```

```
> stof run config.stof
services:
- metadata:
  - name: Bob Jones
    title: Head of Something
    type: publisher
  - alive_for: 234.85days
    goals:
    - more rings
    - get closer to the sun
    name: Jupiter, Inc
    type: organization
  service:
    name: unknown
    providers:
    - instance:
        ttl: 3600.0
        type: t2.micro
      name: aws
      region: us-west-2
    - name: gcp
      region: us-central
```
