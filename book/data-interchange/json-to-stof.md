---
description: Turning a JSON document into a Stof document.
---

# JSON to Stof

{% hint style="info" %}
For defining data, Stof is a superset of JSON - valid JSON will always be valid Stof.
{% endhint %}

This is a kind of funny page to create since Stof can parse JSON. However, explicitly showing how Stof ties into an existing interchange format is helpful for later, when we add more complex custom interfaces and unify data.

{% tabs %}
{% tab title="start.json" %}
```json
{
    "resourceType" : "Patient",
    "id" : "example",
    "active" : true,
    "name" : [
        {
            "use" : "official",
            "family" : "Chalmers",
            "given" : ["Peter", "James"]
        },
        {
            "use" : "usual",
            "given" : ["Jim"]
        },
        {
            "use" : "maiden",
            "family" : "Windsor",
            "given" : ["Peter", "James"],
            "period" : {
                "end" : "2002"
            }
        }
    ]
}
```
{% endtab %}

{% tab title="remove braces" %}
To start, the outer braces are irrelevant in Stof, where there is always an object/scope. The outermost object is named "root" and is the main root of the Stof document.

```json
"resourceType" : "Patient",
"id" : "example",
"active" : true,
"name" : [
    {
        "use" : "official",
        "family" : "Chalmers",
        "given" : ["Peter", "James"]
    },
    {
        "use" : "usual",
        "given" : ["Jim"]
    },
    {
        "use" : "maiden",
        "family" : "Windsor",
        "given" : ["Peter", "James"],
        "period" : {
            "end" : "2002"
        }
    }
]
```
{% endtab %}

{% tab title="optional quotes" %}
Field declarations in Stof can optionally be double-quoted, single-quoted, or without quotes altogether.

Also, declarations can end with a comma, semi-colon, or some whitespace (space or newline).

```json5
resourceType: 'Patient', // comma
id: 'example';           // semi-colon
active: true             // whitespace
name: [
    {
        use: "official",
        family: "Chalmers",
        given: ["Peter", "James"]
    },
    {
        use: "usual",
        given: ["Jim"]
    },
    {
        use: "maiden",
        family: "Windsor",
        given: ["Peter", "James"],
        period: {
            end: "2002"
        }
    }
]
```
{% endtab %}

{% tab title="types" %}
```rust
str resourceType: "Patient"
str id: "example"
bool active: true

type Name {
    use: str; // semi-colon or comma optional
    family: str = "";
    given: vec = [];
    period: obj = null; // could add more complex types, etc.
}

vec name: [
    {
        use: "official",
        family: "Chalmers",
        given: ["Peter", "James"]
    } as Name, // would otherwise be "obj"
    {
        use: "usual",
        given: ["Jim"]
    } as Name,
    {
        use: "maiden",
        family: "Windsor",
        given: ["Peter", "James"],
        period: {
            end: "2002"
        }
    } as Name,
]
```
{% endtab %}

{% tab title="functions" %}
Now let us turn this example into an interface that can do something by adding a function. We'll add a #\[main] function and use the CLI to test that this is working as expected.

```rust
/**
 * Data definition.
 */
resourceType: "Patient"
id: "example"
active: true
name: [
    {
        use: "official",
        family: "Chalmers",
        given: ["Peter", "James"]
    },
    {
        use: "usual",
        given: ["Jim"]
    },
    {
        use: "maiden",
        family: "Windsor",
        given: ["Peter", "James"],
        period: {
            end: "2002"
        }
    }
]

/**
 * Interface definition.
 */
type Name {
    use: str; // semi-colon or comma optional
    family: str = "";
    given: vec = [];
    period: obj = null;

    fn name(): str {
        return `${self.given.join(" ")} ${self.family}`;
    }
}

#[main]
fn main() {
    for (Name name in self.name) {
        pln(name.name());
    }
}
```

Copy this code and place it in a file called "example.stof". Next, make sure to install the [Stof CLI](../../reference/cli/), and then invoke it using the "run" command. You will see the following printed to your console:

```
> stof run example.stof
Peter James Chalmers
Jim
Peter James Windsor
```

{% hint style="info" %}
The CLI is for testing and using Stof in a non-embedded capacity. This functionality also exists by simply parsing your Stof into TypeScript or wherever you like to work with data. Keep in mind though, that in different contexts, Stof might not have access to the system, network, or outside world in general unless the host environment allows/enables it.
{% endhint %}
{% endtab %}
{% endtabs %}
