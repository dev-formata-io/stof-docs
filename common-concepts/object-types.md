---
description: Introduction and overview of Stof object types.
---

# Object Types

Stof adds types and structure to data so that it can be more easily worked with. A major part of this is adding object types.

The default and most common object type is `obj`, which we've already seen many times on the [objects.md](objects.md "mention")page and in examples. This type is implied on object declarations that are not given an explicit type.

However, for more structured scenarios, Stof users can define a type for their objects, adding functions and expected fields with optional default values.

```rust
type Point2D {
    x: float = 0
    y: float = 0
    
    fn len(): float {
        return Number.sqrt(self.x.pow(2) + self.y.pow(2));
    }
}

type Point extends Point2D {
    z: float = 0
    
    fn len(): float {
        return Number.sqrt(self.x.pow(2) + self.y.pow(2) + self.z.pow(2));
    }
}

Point point: {
    x: +2cm
    y: +1cm
    z: +3cm
}

point2: {
    x: 2cm
    y: 1cm
    z: 3cm
}

#[main]
fn run() {
    let point = self.point;
    pln(point.Point2D::len().round(2));
    pln(point.len().round(2));
    
    point = self.point2 as Point;
    pln(point.Point2D::len().round(2));
    pln(point.len().round(2));
    
    point = new Point {
        x: 2cm
        y: 1cm
        z: 3cm
    };
    pln(point.Point2D::len().round(2));
    pln(point.len().round(2));
}
```

```
> stof run example.stof
2.24cm
3.74cm
2.24cm
3.74cm
2.24cm
3.74cm
```

## Scoped Types

Just like functions, types are associated with an object in which they are defined. By default, Stof uses the closest type with a certain name.

{% hint style="info" %}
Partial paths can also be used to reference types. In this example, one could use `new first.Hello {}` instead of `new self.first.Hello {}`. Stof will start its search with the first object it finds, matching the name with the start of the path.

A real-world use case for this is the `Formata.Http.Request` interface. If you know it's the only `Http` scope you have loaded into the document, you can use `new Http.Request {}` or `new Request {}` instead of `new Formata.Http.Request {}`.
{% endhint %}

```rust
first: {
    type Hello {
        fn hello(): str {
            return "first";
        }
    }
}
second: {
    type Hello {
        fn hello(): str {
            return "second";
        }
    }
}

#[main]
fn run() {
    let first = new self.first.Hello {};
    let second = new self.second.Hello {};

    pln(first.hello());
    pln(second.hello());
}
```

```
> stof run example.stof
first
second
```

## Casting

### Function Signature Casting

```rust
type Base {
    fn isBase(): bool {
        return true;
    }
}

obj empty: {}
fn create_base(): Base {
    return self.empty; // casts self.empty to Base because of return type
}

#[test]
fn cast_on_return() {
    let base = self.create_base();
    assertEq(typename base, 'Base');
    assert(base.isBase());
}
```

### Using the 'as' Keyword

```rust
type Base {
    fn isBase(): bool {
        return true;
    }
}

obj as_cast: {}

#[test]
fn cast_on_as() {
    let as_cast = self.as_cast as Base;
    assertEq(typename as_cast, 'Base');
    assert(as_cast.instanceOf('Base'));
    assert(as_cast.isBase());
}
```

### Polymorphic Behavior

```rust
type Base {
    fn isBase(): bool {
        return true;
    }
}
type Extends extends Base {
    fn isBase(): bool {
        return false;
    }
}

Extends extends_obj: {}

fn get_base(object: obj): Base {
    return object;
}

#[test]
fn instance_of_catch() {
    assertEq(typename self.extends_obj, 'Extends');

    let object = self.get_base(self.extends_obj);
    assertEq(typename object, 'Extends');
    assert(object.instanceOf('Extends'));
    assert(object.instanceOf('Base'));

    assertNot(object.isBase());
    assert(object.Base::isBase());
}
```

### Upcasting

```rust
type Base {
    fn isBase(): bool {
        return true;
    }
}
type Extends extends Base {
    fn isBase(): bool {
        return false;
    }
}

obj empty: {}

#[test]
fn upcast_extends() {
    let object = self.empty as Extends;
    assertNot(object.isBase());
    assertEq(typename object, 'Extends');
    assert(object.instanceOf('Extends'));
    assert(object.instanceOf('Base'));

    object = object as Base; // Already of type Base...
    assertNot(object.isBase());
    assertEq(typename object, 'Extends');
    assert(object.instanceOf('Extends'));
    assert(object.instanceOf('Base'));

    assert(object.upcast());
    assert(object.isBase());
    assertEq(typename object, 'Base');
    assert(object.instanceOf('Base'));
    assertNot(object.instanceOf('Extends'));

    assertNot(object.upcast()); // Nowhere to upcast to...
}
```

### Default Values

```rust
type Defaults {
    x: float = 20cm;
    y: float = 2m;
    z: float = 10mm;
}

#[test]
fn create_defaults() {
    let inst = new Defaults {};
    assertEq(inst.x, 20cm);
    assertEq(inst.y, 2m);
    assertEq(inst.z, 10mm);
}
```

### Default Value Errors

```rust
type NoDefaults {
    x: int;
    y: int;
}

#[test]
#[errors]
fn no_defaults() {
    let inst = new NoDefualts {}; // Does not provide an x an y
}

#[test]
#[errors]
fn wrong_types() {
    let inst = new NoDefaults {
        x: 'hi'   // wrong type and fails cast to an int
        y: 5
    };
}
```
