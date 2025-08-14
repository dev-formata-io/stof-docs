---
description: Object prototypes.
---

# Prototypes

Prototypes are an important feature of Stof, but are very easy to use and do not introduce any additional syntax (they use attributes). This is important not only for developer experience, but also to reduce complexity with format import/export.

Any object can be used as a prototype, but the graph also maps type names with objects via the #\[type] attribute. The type name will be the object field's name, or give a name to use within the attribute as well (Ex. `#[type("MyType")]`).

## Type and Extends

```rust
#[type]
Point2D: {
    float x: 0;
    float y: 0;

    #[constructor] // all of these will get called with "new"
    fn is_a_point() {
        self.isapoint = true;
    }

    fn length() -> float {
        Num.sqrt(self.x.pow(2) + self.y.pow(2))
    }

    fn add(x: float, y: float) -> float {
        x + y
    }
}

#[type]
//#[extends(self.Point2D)] // works too
#[extends('Point2D')]
Point: {
    float z: 0;

    #[constructor] // optional
    fn init() {
        self.initialized = true;
    }

    fn length() -> float {
        Num.sqrt(self.x.pow(2) + self.y.pow(2) + self.z.pow(2))
    }

    #[dropped]
    fn dropped() {
        super.point_dropped = true;
    }
}

#[test]
fn point_2d() {
    const point = new Point2D { x: 2, y: 2 };
    assert_eq(point.length().round(2), 2.83);
    assert_not(point.initialized);
    assert(point.isapoint);
}

#[test]
fn point_3d() {
    const point = new Point { x: 2, y: 2, z: 2 };
    assert_eq(point.length().round(2), 3.46);
    assert_eq(point.length<Point2D>().round(2), 2.83);
    assert(point.initialized);
    assert(point.isapoint);
    
    assert_eq(typeof point, "obj");
    assert_eq(typename point, "Point");
    
    drop(point); // will remove the point from the graph and call all #[dropped]
    assert(self.point_dropped);
}

#[test]
fn static_add() {
    assert_eq(<Point2D>.add(12.5, 11.1), 23.6);
    assert_eq(<Point>.add(14.4, 10), 24.4);
    assert_eq(<self.Point2D>.add(25, 42), 67);
}
```

## Type Name Collisions

You might be wondering about type name collisions? When a type name is converted into an object ref at runtime, it will choose the closest object with that type name. You can use a path as well to control the "closest" lookup behavior.

```rust
Geometry: {
    #[type]
    Point: {
        x: 0
        y: 0
    }
}
Another: {
    #[type]
    Point: {
        x: 3
        y: 3
    }
}

#[test]
fn scoped_points() {
    const first = new self.Geometry.Point {};
    const second = new self.Another.Point {};
    
    assert_eq(first.x, 0);
    assert_eq(first.y, 0);
    assert_eq(second.x, 3);
    assert_eq(second.y, 3);
}
```

## Type Names as Types

Type names can then be used as normal types, dynamically mapped to the object they are associated with at runtime.

```rust
#[type]
Point: { x: 0, y: 0 }

/// Notice the "self.Point" - object types can always be paths
fn add(first: Point, second: self.Point) -> Point {
    new Point {
        x: first.x + second.x;
        y: first.y + second.y;
    }
}

#[test]
fn pass_points() {
    const first = new Point { x: 2, y: 2 };
    const res = self.add(first, first);
    assert_eq(res.x, 4);
    assert_eq(res.y, 4);
}
```
