---
description: Stof's standard object library ("Object").
icon: brackets-curly
---

# Object Library

## Common Value Functions

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.toString</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">str</mark>

The default implementation for turning an object into a string is to use the "json" format. If not loaded, it will fall back to the "toml" format. If both are not loaded in the document, this function will return a debug implementation, dumping the entire contents of the object as a string (just like "std.dbg(obj)").

```typescript
#[test]
fn test() {
    let obj = new {
        a: "a"
    };
    assertEq(obj.toString(), '{"a":"a"}');
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.or</mark>(object: <mark style="color:green;">obj</mark>, ...): <mark style="color:green;">unknown</mark>

Returns the first non-empty (null or void) argument, just like the [Standard Library](standard-library.md) "or" function.

```rust
#[test]
fn test() {
    let obj = self.dne.or(new { default: true });
    assert(obj.default);
}
```

## Fields & Functions

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.len</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">int</mark>

Because the default iteration of an object is over its fields, the length of an object is defined by the number of fields it has.

```rust
object: {
    a: "a"
    b: "b"
    c: "c"
    
    fn another() {}
}

#[test]
fn test() {
    assertEq(self.object.len(), 3);
    assertEq(Object.len(self.object), 3);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.at</mark>(object: <mark style="color:green;">obj</mark>, index: <mark style="color:green;">str</mark> | <mark style="color:green;">int</mark>): <mark style="color:green;">unknown</mark>

Will get a field or function on this object according to an index.

If the index is a string, this function will return the first field or function with a name that matches the index, or null if not found.

If the index is an integer, this function will return the field at the desired index in relation to all other fields on the object as a Tuple (name, value). This is only useful for iterating over an object's fields (without using the preferred "Object.fields(object)" function).

```rust
field: 42

#[test]
fn getValueByName() {
    assertEq(self.at("field"), 42);
    assertEq(self["field"], 42);
    
    assertEq(self["getFieldByIndex"], self.getFieldByIndex);
    
    assertNull(self.at("dne"));
}

#[test]
fn getFieldByIndex() {
    assertEq(self.at(0), ("field", 42));
    
    // use case - faster to use self.fields(), self.keys(), or self.values()
    for (field in self) {
        let name = field[0];
        let value = field[1];
        
        // do something cool...
    }
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.fields</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">map</mark>

Creates a map out of all fields on this object.

{% hint style="info" %}
This function differs from the Standard Library map initializer `std.map(obj)` with an object argument. This function will not create maps out of sub-objects like the standard library function will.
{% endhint %}

```rust
a: "a"
b: "b"

#[test]
fn fasterIteration() {
    for (field in self.fields()) {
        let name = field[0];
        let value = field[1];
        
        // do something cool...
    }
}

#[test]
fn more() {
    let fields = box(self.fields()); // loads all object fields into a map ref
    assert(fields.contains("a"));
    assertEq(fields.get("a"), "a");
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.keys</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">vec</mark>

Returns a vector of all field keys on this object.

```rust
a: "A"
b: "B"

#[test]
fn test() {
    let keys = self.keys() as set; // cast to set for order
    assertEq(keys, set("a", "b"));
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.values</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">vec</mark>

Returns a vector of all field values on this object.

```rust
a: "A"
b: "B"

#[test]
fn test() {
    let values = self.values() as set; // cast to set for order
    assertEq(values, set("A", "B"));
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.set</mark>(object: <mark style="color:green;">obj</mark>, name: <mark style="color:green;">str</mark>, value: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Sets a field value on/from this object. Will create a new field if one does not already exist.

The field name is really a field path, starting at this object - Stof will create new objects as needed to make the path valid.

```rust
field: 42

#[test]
fn test() {
    // Field on self
    assertEq(self.field, 42);
    assert(self.set("field", 100));    // just like "self.field = 100"
    assertEq(self.field, 100);
    
    // Create new objects (or set fields if already exists)
    self.set("self.subobj.field", 222); // creates a child object named "subobj"
    assertEq(self.subobj.field, 222);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.box</mark>(object: <mark style="color:green;">obj</mark>, name: <mark style="color:green;">str</mark>, value?: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Box a field on this object without an additional assignment statement, optionally accepting a new value to box and set.

{% hint style="info" %}
This function can be used like "set", but it makes sure that the value is boxed. If a field is not present, it will box a null value in preparation for an assignment.
{% endhint %}

```rust
value: 42

#[test]
fn box_field() {
    assert(self.box("value"));
    
    let v = self.value;
    v = 100;

    assertEq(self.value, 100);
}

#[test]
fn box_null_value() {
    assert(self.box("dude", "hi"));
    assertEq(self.dude, "hi");

    let v = self.dude;
    v = "hello";

    assert(isBoxed(self.dude));
    assertEq(self.dude, "hello");
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.unbox</mark>(object: <mark style="color:green;">obj</mark>, name: <mark style="color:green;">str</mark>, value?: <mark style="color:green;">unknown</mark>): <mark style="color:green;">bool</mark>

Unbox a field on this object without an additional assignment statement, optionally accepting a new value to unbox and set.

Unlike the function "box", if the field is not present and a value is not given as an argument, this function will not set a null value and will return false.

{% hint style="info" %}
This function can be used like "set", but it makes sure that the value is unboxed.
{% endhint %}

```rust
value: box(32)

#[test]
fn unbox_value() {
    let v = self.value;
    v = 10;
    assertEq(self.value, 10);

    assert(self.unbox("value"));
    v = 100;
    assertEq(self.value, 10);
}

#[test]
fn unbox_set() {
    assertNot(self.unbox("dne")); // won't set without a given value...
    assert(self.unbox("another", box(20)));
    assertEq(self.another, 20);
    
    let v = self.another;
    v = 100;
    assertEq(self.another, 20);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.removeField</mark>(object: <mark style="color:green;">obj</mark>, name: <mark style="color:green;">str</mark>, dropIfObject?: <mark style="color:green;">bool</mark>): <mark style="color:green;">bool</mark>

Remove a field by name/path, starting from this object. Path behavior is the same as for "set".

The parameter "dropIfObject" is only considered when the field being removed is an object. Objects in Stof are just references and can exist in multiple objects at once. This parameter gives more control over when those objects are removed from the document entirely. The default value is false, not dropping objects from the document. Unless you know what you're doing, it is recommended to leave it as false.

Returns whether or not the field was found and removed.

```rust
field: 42
another: 42

#[test]
fn test() {
    assertEq(self.field, 42);
    assert(self.removeField("field"));
    assertNull(self.field);
    assertNot(self.removeField("field"));
    
    // alternative if you know the name
    assertEq(self.another, 42);
    drop self.another;
    assertNull(self.another);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.moveField</mark>(object: <mark style="color:green;">obj</mark>, from: <mark style="color:green;">str</mark>, to: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Alias function for "renameField". Both functions have the same implementation.

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.renameField</mark>(object: <mark style="color:green;">obj</mark>, from: <mark style="color:green;">str</mark>, to: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Renames a field and/or moves it to a new location, starting from this object.

Both "from" and "to" are paths, which makes this function very useful.

```rust
saying: "hi there"

#[test]
fn test() {
    assertEq(self.saying, "hi there");
    self.renameField("saying", "message");
    assertNull(self.saying);
    assertEq(self.message, "hi there");

    self.moveField("message", "subobject.message");
    assertNull(self.message);
    assertEq(self.subobject.message, "hi there");
}

value: 10
collision: {
    dude: 100
}

#[test]
fn collides() {
    self.moveField("value", "collision.dude");
    // self.collision.moveField("super.value", "dude"); // does the same thing
    assertNull(self.value);
    assertEq(self.collision.dude, [100, 10]);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.mapFields</mark>(object: <mark style="color:green;">obj</mark>, mapping: <mark style="color:green;">map</mark>): <mark style="color:green;">map</mark>

Calls "moveField"/"renameField" for each key/value pair in a mapping, returning a map of all key/value pairs that were successfully moved/renamed.

{% hint style="info" %}
Take note that the paths start at the calling object. So if you wanted to call this function instead from "source" (`self.source.mapFields(mapping)`), the mapping from field "a" to field "first" would instead be `("a", "super.destination.first")`.
{% endhint %}

```rust
source: {
    a: "A"
    b: "B"
    c: "C"
}
destination: {}  // will be created if not present already...

#[test]
fn test() {
    let mapping = map(
        ("source.a", "destination.first"),
        ("source.b", "destination.second"),
        ("source.c", "destination.third"),
        ("source.d", "destination.fourth") // this one doesn't exist
    );
    
    let res = self.mapFields(mapping);
    
    assertEq(res.keys(), ["source.a", "source.b", "source.c"]);
    assertEq(self.destination.first, "A");
    assertEq(self.destination.second, "B");
    assertEq(self.destination.third, "C");
    
    assertNull(self.source.a);
    assertNull(self.source.b);
    assertNull(self.source.c);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.attributes</mark>(object: <mark style="color:green;">obj</mark>, name: <mark style="color:green;">str</mark>): <mark style="color:green;">map</mark>

Returns a map of attributes on a field or function that exists at the name/path, starting at this object, or null if a field or function cannot be found.

{% hint style="info" %}
See [fields.md](../../common-concepts/fields.md "mention") & [functions.md](../../common-concepts/functions.md "mention") for more information on attributes.
{% endhint %}

```rust
#[custom((): str => "hey there")] // attribute values get executed when parsed
field: 42;

#[test]  // default attribute value is null
fn test() {
    // gets the attributes from this function
    let testAttributes = self.attributes("test");
    assert(testAttributes.contains("test"));
    assertNull(testAttributes.get("test"));
    
    // gets the attributes from our field
    let fieldAttrs = self.attributes("field");
    assertEq(typeof fieldAttrs.get("custom"), "fn");
    assertEq(fieldAttrs.get("custom").call(), "hey there");
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.functions</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">map</mark>

Returns a map of all functions that exist on this object. Keys are function names and values are function pointers that can be called.

```rust
fn hello(): str { return "hello"; }
fn add(a: float, b: float): float { return a + b; }

#[test]
fn test() {
    let funcs = self.functions();
    assertEq(funcs.keys(), ["add", "hello", "test"]);
    assertEq(funcs.get("add").call(3, 4), 7);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.removeFunc</mark>(object: <mark style="color:green;">obj</mark>, name: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Remove a function from the document by name/path, starting at this object.

```rust
fn hello(): str { return "hello"; }

#[test]
fn test() {
    assertEq(self.hello(), "hello");
    
    assert(self.removeFunc("hello"));
    assertNull(self.hello);
    //assertEq(self.hello(), ""); // errors - func doesn't exist
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.reference</mark>(object: <mark style="color:green;">obj</mark>, path: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Add a reference to a field or function on this object from a path.

In Stof's terms, this function merely attaches the field or function to this object in addition to any other objects it is already attached to - nothing is created, copied, or moved.

{% hint style="warning" %}
This is a cool functionality in Stof, but use it wisely as it can make some interfaces hard to trace and use.
{% endhint %}

```rust
record: {
    nested: {
        name: {
            first: "Bob"
            last: "Jones"
            
            fn full(): str {
                return `${self.first} ${self.last}`;
            }
        }
    }
}

#[test]
fn test() {
    self.reference("self.record.nested.name");
    assertEq(self.name.first, self.record.nested.name.first);
    assertEq(self.name.full(), "Bob Jones");
    
    self.name.first = "Karen";
    assertEq(self.name.first, self.record.nested.name.first);
    assertEq(self.name.full(), "Karen Jones");
    assertEq(self.record.nested.name.full(), "Karen Jones");
    
    self.name.removeFunc("full"); // only one function ever existed.
    assertNull(self.name.full);
    assertNull(self.record.nested.name.full);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.search</mark>(object: <mark style="color:green;">obj</mark>, field: <mark style="color:green;">str</mark>, parentChildren: <mark style="color:green;">bool</mark> <mark style="color:red;">= true</mark>, ignore: <mark style="color:green;">vec</mark> <mark style="color:red;">= \[]</mark>): <mark style="color:blue;">(</mark><mark style="color:green;">unknown</mark><mark style="color:blue;">,</mark> <mark style="color:green;">int</mark><mark style="color:blue;">)</mark>

Search this object for a field by name. Will search both up (parents) and down (children), returning the closest found field. If a field was found in a parent and also found in a child and the distances are the same, this function will favor the child field (downwards).

Parameters:

* field - the name of the field to search for.
* parentChildren - allow searching through the children of parent nodes?
* ignore - a list of objects to skip searching through (will still search parents & children of these objects).

```rust
a: "A"
b: "B"
middle: {
    d: "D"
    down: {
        a: "AD"
        c: "C"
    }
}

#[test]
fn test() {
    assertEq(self.middle.search("d"), ("D", 0));
    
    assertEq(self.middle.search("a"), ("AD", 1));
    assertEq(self.middle.search("a", true, [self.middle.down]), ("A", 1));
    
    assertEq(self.middle.search("c"), ("C", 1));
    assertEq(self.middle.search("b"), ("B", 1));
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.searchUp</mark>(object: <mark style="color:green;">obj</mark>, field: <mark style="color:green;">str</mark>, parentChildren: <mark style="color:green;">bool</mark> <mark style="color:red;">= true</mark>, ignore: <mark style="color:green;">vec</mark> <mark style="color:red;">= \[]</mark>): <mark style="color:blue;">(</mark><mark style="color:green;">unknown</mark><mark style="color:blue;">,</mark> <mark style="color:green;">int</mark><mark style="color:blue;">)</mark>

Search this object for a field by name. Behaves just like "search", however, will only allow searching upwards, through this object's parents.

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.searchDown</mark>(object: <mark style="color:green;">obj</mark>, field: <mark style="color:green;">str</mark>, currDist: <mark style="color:green;">int</mark> <mark style="color:red;">= 0</mark>, ignore: <mark style="color:green;">vec</mark> <mark style="color:red;">= \[]</mark>): <mark style="color:blue;">(</mark><mark style="color:green;">unknown</mark><mark style="color:blue;">,</mark> <mark style="color:green;">int</mark><mark style="color:blue;">)</mark>

Search this object for a field by name. Behaves just like "search", however, will only allow searching downwards, through this object's children.

## Schemafy

Every object in Stof can be treated as a schema, capable of being applied to another object. We call this application "schemafy".

This is one of the most powerful and complex "standard" operations that Stof can perform, able to create new fields, remove lots of fields that are not defined, validate, control access, etc.

You might think that the interface is complex as well, but "schemafy" only looks at one singular attribute, <mark style="color:purple;">`#[schema(...)]`</mark> and returns a boolean, reflecting the validity of the object in question (we call this the "target" object) with respect to this "schema" object.

{% hint style="info" %}
See [rest-and-restructure.md](../../book/data-interchange/rest-and-restructure.md "mention") for a real-life example of "schemafy" (at the bottom).
{% endhint %}

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.schemafy</mark>(schema: <mark style="color:green;">obj</mark>, target: <mark style="color:green;">obj</mark>, removeInvalidFields: <mark style="color:green;">bool</mark> <mark style="color:red;">= true</mark>, removeUndefinedFields: <mark style="color:green;">bool</mark> <mark style="color:red;">= false</mark>): <mark style="color:green;">bool</mark>

* **schema** - The first argument (or caller) of this function is considered the "schema" object. Fields defined on this object that have a #\[schema] attribute will have that attribute applied to the "target" object's corresponding fields (by name).
* **target** - The object that will be manipulated/validated using this "schema".
* **removeInvalidFields** - If true, when a field is determined to be invalid (using the #\[schema(..)] attribute), that field will be removed from the target object (including object fields). If false, invalid fields will be left on the target object (but the return value will still be false if an invalid field is found).
* **removeUndefinedFields** - If true, after each schema field is applied to the target object, all fields on the target that are not defined on the schema object (#\[schema] attribute or not) will be removed from the target object. Ex. the target object should only contain these few fields.

```typescript
schema: {
    // If you can think it, you can do it with the #[schema] attribute...
    #[schema((value: int): bool => value >= 0 && value <= 90)]
    field: 0
}

target: {
    field: 90
}

#[test]
fn example() {
    assert(self.schema.schemafy(self.target)); // assert the target is valid

    self.target.field = 91;
    assertNot(self.schema.schemafy(self.target)); // no longer valid
    assertNull(self.target.field); // removeInvalidFields defaults to true
}
```

### Schema Attribute Values

&#x20;The <mark style="color:purple;">`#[schema(...)]`</mark> attribute for "schemafy" is extremely powerful, enabling one to create new fields, conditionally validate values, etc. If you can think it, you can do it.

#### Objects

If an object value is given in the schema attribute, it will be treated as an additional schema to apply to the current field (like a proxy).

```typescript
schema: {
    #[schema((value: unknown): bool => set('s', 'm', 'l', 'xl', 'xxl').contains(value))]
    field: ''
}

proxy: {
    #[schema(super.schema)]
    field: ''
}

#[test]
fn valid() {
    let record = new {
        field: 'xl';
    };
    assert(self.proxy.schemafy(record));
    assertEq(record.field, 'xl');
}

#[test]
fn invalid() {
    let record = new {
        field: 'small';
    };
    assertNot(self.proxy.schemafy(record));
    assertNull(record.field);
}
```

#### Null

A null attribute value will not do anything (just returns valid) unless the field is an object and the corresponding field on the target is also an object. In that case, the schema field object will be applied as the schema to the corresponding field object on the target.

```typescript
schema: {
    #[schema] // does nothing, just returns true (valid) for non-objects
    field: ''

    // This is the long way to do what #[schema] is doing below.
    // However, this might be helpful if you don't want to propagate the behavior
    // for "removeInvalidFields" & "removeUndefinedFields" to sub-objects.
    // #[schema((value: obj): bool => self.name.schemafy(value))]
    
    #[schema] // applies this object as the schema for the target "name" field
    name: {
        #[schema((value: str): bool => value.len() > 0)]
        first: ''
        #[schema((value: str): bool => value.len() > 0)]
        last: ''
    }
}

target: {
    name: {
        first: 'Bob'
        last: 'Smith'
    }
}

#[test]
fn example() {
    assert(self.schema.schemafy(self.target));
    self.target.name.first = '';
    assertNot(self.schema.schemafy(self.target));
    assertNull(self.target.name); // removeInvalidFields = true by default
}
```

#### Functions

The most common value contained within the attribute is a function. Schemafy functions can have up to 4 parameters and can either have a boolean return value (indicating validity), a null value (resulting in nothing being done), or anything other value, set as the new field value.

Parameters can be specified in any order, but are associated with arguments via name (and value type) at runtime.

* **target: obj** - if found in the function's parameters, the target object that the schema is being applied to will be given as this argument.
* **schema: obj** - if found in the function's parameters, the schema object being applied to the target will be given as this argument.
* **field: str** - if found in the function's parameters, the name of the field that is currently being validated/manipulated will be given as this argument. If the type is a **Box\<str>**, this will enable one to rename this field by setting this value in the function.
* **value: unknown** - if found in the function's parameters, the current value associated with this field on the target will be given as this argument. Any type will work here as long as the name is "value". If the type is a box type (Ex. **Box\<unknown>** instead of just **unknown**), this will box the field value and enable one to modify the field value by setting this value in the function (this is an alternative to returning a non-null and non-boolean value to be set as the new field value).

```typescript
schema: {
    // N.B. - marked as "invalid" if "value" cannot be cast to a string here or the function call fails...
    // Avoid this scenario by setting "value" type to "undefined" or "Box<undefined>" if you aren't sure.
    #[schema((target: obj, schema: obj, field: str, value: str): bool => true)] // return validity
    a: ''
    #[schema((schema: obj, target: obj, value: str, field: str): bool => true)] // ordering doesn't matter
    b: ''

    // If field names don't correlate with "target", "schema", "field", or "value", then they're set by type.
    // First object seen is the "target" ("a" in this case), then "schema" ("b" in this case).
    // First string seen is the "field" ("c" in this case).
    // Any other value (or after the others if conflicting type) is set as the "value" ("d" in this case).
    // Recommended to use the parameter names "target", "schema", "field", and "value" to avoid any issues.
    #[schema((a: obj, b: obj, c: str, d: unknown): unknown) => d]
    c: ''

    // Not all parameters have to be given (could even have none).
    #[schema((value: str): bool => value.len() > 0)]
    d: ''

    // If the return type is not "bool", then the return value is treated as the new field value (if not null).
    // You can still modify a field value with a boolean return type if the value type is boxed.
    #[schema((): str => 'always valid, and value always set (or created) to be this string')]
    e: ''
}
```

#### Arrays

An array of objects and functions can be given as the schema attribute value, indicating that each function and additional schema should be evaluated in order, ceasing execution if the field is evaluated to be invalid.

```typescript
schema: {
    #[private]
    hex_chars: {
        let chars = 0..10;
        for (char in chars) chars.set(index, char as str);
        chars = chars as set;
        chars.insert('a');
        chars.insert('b');
        chars.insert('c');
        chars.insert('d');
        chars.insert('e');
        chars.insert('f');
        return box(chars);
    };

    fn valid_hex_chars(hex: str): bool {
        hex = hex.toLower().substring(1);
        let valid = self.hex_chars;
        for (char in hex) if (!valid.contains(char)) return false;
        return true;
    }

    #[schema([
        (value: Box<unknown>): bool => value != null, // has to exist
        (value: Box<unknown>): bool => isString(value), // has to be a string
        (value: Box<str>): bool => value.startsWith('#'), // has to start with '#'
        (value: Box<str>): bool => value.len() == 4 || value.len() == 7, // has to be the right length
        (value: Box<str>): bool => self.valid_hex_chars(value), // have to be valid chars
    ])]
    hex: '#000'
}

#[test]
fn not_valid_char() {
    let record = new { hex: '#aaaaag' };
    assertNot(self.schema.schemafy(record));
    assertNull(record.hex);
}

#[test]
fn valid_three() {
    let record = new { hex: '#fff' };
    assert(self.schema.schemafy(record));
    assertEq(record.hex, '#fff');
}

#[test]
fn valid_hex() {
    let record = new { hex: '#08fa2e' };
    assert(self.schema.schemafy(record));
}
```

## Execute

The "exec" functionality is another powerful paradigm in Stof. It orchestrates many objects together as tasks.

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.exec</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">void</mark>

Calls all functions on the object with a #\[run] attribute. Also executes all object fields that also have a #\[run] attribute.

The #\[run] attribute value can optionally be a number, ordering the execution of sub-objects and functions together. By default, sub-objects have an order of -2 and functions have an order of -1 (sub-objects are executed first).

Functions that get executed should not have any parameters (or must have default values provided for all parameters).

```rust
Box<vec> context: []

#[run]
sub_task: {
    #[run]
    fn task() {
        super.context.push(0);
    }
}

#[run]
fn task() {
    self.context.push(1);
}

#[run(1)]
fn second_task() {
    self.context.push(2);
}

#[test]
fn exec_test() {
    self.exec();
    assertEq(self.context, [0, 1, 2]);
}
```

## Object Interface

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.name</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">str</mark>

Returns the name of this object. Typically, but not always the name of fields that reference this object.

```rust
record: {
    name: "Tom"
}

#[test]
fn test() {
    assertEq(self.name(), "root");
    assertEq(self.record.name(), "record");
    
    let array = [new { field: 42 }];
    let name = array.first().name(); // unique name - anonymous object in the doc
    let path = name + ".field";      // example only - referencing the obj by path
    assertEq(self.at(path), 42);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.id</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">str</mark>

Returns this object's ID. Helpful for some debugging instances.

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.parent</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">obj</mark>

Returns this object's parent object, or null if this object is a root. It is preferred to use the "super" keyword when working with paths (if able).

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.root</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">obj</mark>

Returns this object's root object in the document.

```rust
root Variables: {
    Key: {
        token: "abcdefg"
    }
}

tests: {
    #[test]
    fn test() {
        assertEq(self.name(), "tests");
        assertEq(self.root().name(), "root");
        
        let key = Variables.Key;
        assertEq(key.root().name(), "Variables");
    }
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.isRoot</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">bool</mark>

Returns true if this object is a root object in the document.

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.path</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">str</mark>

Returns the dot-separated path to this object from a root of this document.

```rust
root Variables: {
    Space: {
        Info: {
            name: {
                first: "Joe"
            }
            
            #[test]
            fn test() {
                assertEq(self.path(), "Variables.Space.Info");
            }
        }
    }
}

other: {
    #[test]
    fn test() {
        assertEq(self.path(), "root.other");
    }
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.children</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">vec</mark>

Returns a vector containing this object's child objects, or an empty vector if this object does not have any children.

```rust
a: {}  // fields with values that point to child objects
b: {}
c: {}

#[test]
fn test() {
    assertEq(self.children().len(), 3);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.typename</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">str</mark>

Returns the name of this object's type. If this object does not have a prototype, this will return "obj", the same as "typeof".

```rust
type CustomType {}

CustomType object: {}
regular: {}

#[test]
fn test() {
    assertEq(self.object.typename(), "CustomType");
    assertEq(self.regular.typename(), "obj");
    
    assertEq(typeof self.object, "obj");
    assertEq(typeof self.regular, "obj");
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.typestack</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">vec</mark>

Returns a vector containing the stack of type names associated with this object.

```rust
type CustomType {}
type SubType extends CustomType {}

SubType object: {}
regular: {}

#[test]
fn test() {
    assertEq(self.object.typestack(), ["SubType", "CustomType"]);
    assertEq(self.regular.typestack(), []);
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.instanceOf</mark>(object: <mark style="color:green;">obj</mark>, type: <mark style="color:green;">str</mark>): <mark style="color:green;">bool</mark>

Returns true if this object has the type name "type" in its type stack.

```rust
type CustomType {}
type SubType extends CustomType {}

SubType object: {}

#[test]
fn test() {
    assert(self.object.instanceOf("SubType"));
    assert(self.object.instanceOf("CustomType"));
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.prototype</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">obj</mark>

Return this object's prototype object if it has one. The prototype object will most likely be referenced by other objects, so use this function carefully.

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.setPrototype</mark>(object: <mark style="color:green;">obj</mark>, prototype: <mark style="color:green;">obj</mark>): <mark style="color:green;">void</mark>

Set this object's prototype to be the object "prototype".

```rust
type CustomType {
    fn hey(): str { return "hey"; }
}
CustomType typed: {}
untyped: {}

#[test]
fn get_set() {
    self.untyped.setPrototype(self.typed.prototype());
    assertEq(typename self.untyped, "CustomType");
    assertEq(self.untyped.hey(), "hey");
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.upcast</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">bool</mark>

Changes this object's prototype to be the prototype its current prototype extends.

```rust
type CustomType {}
type SubType extends CustomType {}

SubType object: {}

#[test]
fn test() {
    assert(self.object.instanceOf("SubType"));
    assert(self.object.upcast());
    
    assertNot(self.object.instanceOf("SubType"));
    assert(self.object.instanceOf("CustomType"));
    assertNot(self.object.upcast()); // prototype does not extend any other type
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.removePrototype</mark>(object: <mark style="color:green;">obj</mark>): <mark style="color:green;">bool</mark>

Removes this object's prototype completely (regardless of whether it extends another), returning true if this object had a prototype that was removed.

```rust
type CustomType {}
type SubType extends CustomType {}

SubType object: {}

#[test]
fn test() {
    assert(self.object.removePrototype());
    assertEq(self.object.typename(), "obj");
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.shallowCopy</mark>(object: <mark style="color:green;">obj</mark>, other: <mark style="color:green;">obj</mark>): <mark style="color:green;">void</mark>

Make "object" a shallow copy of "other" by attaching all of the data on "other" to it. Essentially, a "reference" call to each one of the fields on "other".

{% hint style="info" %}
This function does not just shallow copy fields, but all data (functions, prototype, etc...).
{% endhint %}

```rust
type Custom {
    fn hey(): str { return "hey"; }
}

Custom first: {
    a: "a"
    b: "b"
}
second: {}

#[test]
fn test() {
    self.second.shallowCopy(self.first);
    
    assertEq(self.second.a, "a");
    assertEq(self.second.a, self.first.a);

    self.second.a = "modified";
    assertEq(self.second.a, "modified");
    assertEq(self.first.a, "modified");

    assertEq(self.second.hey(), "hey");
    assertEq(self.first.hey(), "hey");
}
```

### <mark style="color:purple;">Object</mark><mark style="color:orange;">.deepCopyFields</mark>(object: <mark style="color:green;">obj</mark>, other: <mark style="color:green;">obj</mark>): <mark style="color:green;">void</mark>

Copies all fields from "other" and places them on this object. This function is also called for every field on "other" that is an object, recursively deep copying all referenced (by field) sub-objects.

Unlike shallow copy, this function only operates on fields, not functions or any other type of data.

The object "other" is unmodified by this function.

```rust
type Custom {
    fn hey(): str { return "hey"; }
}

Custom first: {
    a: "a"
    b: "b"
}
second: {}

#[test]
fn test() {
    self.second.deepCopyFields(self.first);
    
    assertEq(self.second.a, "a");
    assertEq(self.second.a, self.first.a);

    self.second.a = "modified";
    assertEq(self.second.a, "modified");
    assertEq(self.first.a, "a");

    assertNull(self.second.hey);
    assertEq(self.first.hey(), "hey");
}
```
