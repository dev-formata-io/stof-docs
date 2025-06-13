---
description: An overview of available unit types in Stof.
---

# Units

Stof has units as primitive types. Each unit type is a variant of a floating-point number, able to do unit conversions when used in operations or explicitly cast to another unit type.

See [primitive-types.md](primitive-types.md "mention") to learn more about the value types Stof has available.

Outlined on this page is an overview of all of the unit types Stof has available. Unit conversions can happen with units of the same category.

### Reference Table

| Category                                     | Unit                 | Stof Type Keywords                                                                                                                                                     |
| -------------------------------------------- | -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <mark style="color:orange;">Length</mark>    | Kilometers           | <mark style="color:purple;">km</mark>, <mark style="color:purple;">kilometer</mark>, <mark style="color:purple;">kilometers</mark>                                     |
| <mark style="color:orange;">Length</mark>    | Hectometers          | <mark style="color:purple;">hm</mark>, <mark style="color:purple;">hectometer</mark>, <mark style="color:purple;">hectometers</mark>                                   |
| <mark style="color:orange;">Length</mark>    | Decameters           | <mark style="color:purple;">dcm</mark>, <mark style="color:purple;">decameter</mark>, <mark style="color:purple;">decameters</mark>                                    |
| <mark style="color:orange;">Length</mark>    | Meters               | <mark style="color:purple;">m</mark>, <mark style="color:purple;">meter</mark>, <mark style="color:purple;">meters</mark>                                              |
| <mark style="color:orange;">Length</mark>    | Decimeters           | <mark style="color:purple;">dm</mark>, <mark style="color:purple;">decimeter</mark>, <mark style="color:purple;">decimeters</mark>                                     |
| <mark style="color:orange;">Length</mark>    | Centimeters          | <mark style="color:purple;">cm</mark>, <mark style="color:purple;">centimeter</mark>, <mark style="color:purple;">centimeters</mark>                                   |
| <mark style="color:orange;">Length</mark>    | Millimeters          | <mark style="color:purple;">mm,</mark> <mark style="color:purple;">millimeter</mark>, <mark style="color:purple;">millimeters</mark>                                   |
| <mark style="color:orange;">Length</mark>    | Micrometers          | <mark style="color:purple;">um</mark>, <mark style="color:purple;">micrometer</mark>, <mark style="color:purple;">micrometers</mark>                                   |
| <mark style="color:orange;">Length</mark>    | Nanometers           | <mark style="color:purple;">nm</mark>, <mark style="color:purple;">nanometer</mark>, <mark style="color:purple;">nanometers</mark>                                     |
| <mark style="color:orange;">Length</mark>    | Miles                | <mark style="color:purple;">mi</mark>, <mark style="color:purple;">mile</mark>, <mark style="color:purple;">miles</mark>                                               |
| <mark style="color:orange;">Length</mark>    | Yards                | <mark style="color:purple;">yd</mark>, <mark style="color:purple;">yard</mark>, <mark style="color:purple;">yards</mark>                                               |
| <mark style="color:orange;">Length</mark>    | Feet                 | <mark style="color:purple;">ft</mark>, <mark style="color:purple;">foot</mark>, <mark style="color:purple;">feet</mark>                                                |
| <mark style="color:orange;">Length</mark>    | Inches               | <mark style="color:purple;">in</mark>, <mark style="color:purple;">inch</mark>, <mark style="color:purple;">inches</mark>                                              |
| <mark style="color:green;">Time</mark>       | Days                 | <mark style="color:purple;">day</mark>, <mark style="color:purple;">days</mark>                                                                                        |
| <mark style="color:green;">Time</mark>       | Hours                | <mark style="color:purple;">hr</mark>, <mark style="color:purple;">hrs</mark>, <mark style="color:purple;">hour</mark>, <mark style="color:purple;">hours</mark>       |
| <mark style="color:green;">Time</mark>       | Minutes              | <mark style="color:purple;">min</mark>, <mark style="color:purple;">mins</mark>, <mark style="color:purple;">minute</mark>, <mark style="color:purple;">minutes</mark> |
| <mark style="color:green;">Time</mark>       | Seconds              | <mark style="color:purple;">s</mark>, <mark style="color:purple;">second</mark>, <mark style="color:purple;">seconds</mark>                                            |
| <mark style="color:green;">Time</mark>       | Milliseconds         | <mark style="color:purple;">ms</mark>, <mark style="color:purple;">millisecond</mark>, <mark style="color:purple;">milliseconds</mark>                                 |
| <mark style="color:green;">Time</mark>       | Microseconds         | <mark style="color:purple;">us</mark>, <mark style="color:purple;">microsecond</mark>, <mark style="color:purple;">microseconds</mark>                                 |
| <mark style="color:green;">Time</mark>       | Nanoseconds          | <mark style="color:purple;">ns</mark>, <mark style="color:purple;">nanosecond</mark>, <mark style="color:purple;">nanoseconds</mark>                                   |
| <mark style="color:blue;">Temperature</mark> | Kelvin               | <mark style="color:purple;">K</mark>, <mark style="color:purple;">kelvin</mark>, <mark style="color:purple;">Kelvin</mark>                                             |
| <mark style="color:blue;">Temperature</mark> | Celsius              | <mark style="color:purple;">C</mark>, <mark style="color:purple;">celsius</mark>, <mark style="color:purple;">Celsius</mark>                                           |
| <mark style="color:blue;">Temperature</mark> | Fahrenheit           | <mark style="color:purple;">F</mark>, <mark style="color:purple;">fahrenheit</mark>, <mark style="color:purple;">Fahrenheit</mark>                                     |
| <mark style="color:red;">Mass</mark>         | Gigatonnes           | <mark style="color:purple;">Gt</mark>, <mark style="color:purple;">gigatonne</mark>, <mark style="color:purple;">gigatonnes</mark>                                     |
| <mark style="color:red;">Mass</mark>         | Megatonnes           | <mark style="color:purple;">Mt</mark>, <mark style="color:purple;">megatonne</mark>, <mark style="color:purple;">megatonnes</mark>                                     |
| <mark style="color:red;">Mass</mark>         | Tonnes               | <mark style="color:purple;">t</mark>, <mark style="color:purple;">tonne</mark>, <mark style="color:purple;">tonnes</mark>                                              |
| <mark style="color:red;">Mass</mark>         | Kilograms            | <mark style="color:purple;">kg</mark>, <mark style="color:purple;">kilogram</mark>, <mark style="color:purple;">kilograms</mark>                                       |
| <mark style="color:red;">Mass</mark>         | Grams                | <mark style="color:purple;">g</mark>, <mark style="color:purple;">gram</mark>, <mark style="color:purple;">grams</mark>                                                |
| <mark style="color:red;">Mass</mark>         | Milligrams           | <mark style="color:purple;">mg</mark>, <mark style="color:purple;">milligram</mark>, <mark style="color:purple;">milligrams</mark>                                     |
| <mark style="color:red;">Mass</mark>         | Micrograms           | <mark style="color:purple;">ug</mark>, <mark style="color:purple;">microgram</mark>, <mark style="color:purple;">micrograms</mark>                                     |
| <mark style="color:red;">Mass</mark>         | Nanograms            | <mark style="color:purple;">ng</mark>, <mark style="color:purple;">nanogram</mark>, <mark style="color:purple;">nanograms</mark>                                       |
| <mark style="color:red;">Mass</mark>         | Picograms            | <mark style="color:purple;">pg</mark>, <mark style="color:purple;">picogram</mark>, <mark style="color:purple;">picograms</mark>                                       |
| <mark style="color:red;">Mass</mark>         | Tons (US imperial)   | <mark style="color:purple;">ton</mark>, <mark style="color:purple;">tons</mark>, <mark style="color:purple;">Ton</mark>, <mark style="color:purple;">Tons</mark>       |
| <mark style="color:red;">Mass</mark>         | Pounds (US imperial) | <mark style="color:purple;">lb</mark>, <mark style="color:purple;">lbs</mark>, <mark style="color:purple;">pound</mark>, <mark style="color:purple;">pounds</mark>     |
| <mark style="color:red;">Mass</mark>         | Ounces (US imperial) | <mark style="color:purple;">oz</mark>, <mark style="color:purple;">ounce</mark>, <mark style="color:purple;">ounces</mark>                                             |
| <mark style="color:green;">Angle</mark>      | Radians              | <mark style="color:purple;">rad</mark>, <mark style="color:purple;">radians</mark>                                                                                     |
| <mark style="color:green;">Angle</mark>      | Degrees              | <mark style="color:purple;">deg</mark>, <mark style="color:purple;">degrees</mark>                                                                                     |
| <mark style="color:green;">Angle</mark>      | Positive Radians     | <mark style="color:purple;">prad</mark>, <mark style="color:purple;">pradians</mark>                                                                                   |
| <mark style="color:green;">Angle</mark>      | Positive Degrees     | <mark style="color:purple;">pdeg</mark>, <mark style="color:purple;">pdegrees</mark>                                                                                   |

### Length Units

#### Kilometers

Stof type keywords:

* km
* kilometer
* kilometers

#### Hectometers

Stof type keywords:

* hm
* hectometer
* hectometers

#### Decameters

Stof type keywords:

* dcm
* decameter
* decameters

#### Meters

Stof type keywords:

* m
* meter
* meters

#### Decimeters

Stof type keywords:

* dm
* decimeter
* decimeters

#### Centimeters

Stof type keywords:

* cm
* centimeter
* centimeters

#### Millimeters

Stof type keywords:

* mm
* millimeter
* millimeters

#### Micrometers

Stof type keywords:

* um
* micrometer
* micrometers

#### Nanometers

Stof type keywords:

* nm
* nanometer
* nanometers

#### Miles

Stof type keywords:

* mi
* mile
* miles

#### Yards

Stof type keywords:

* yd
* yard
* yards

#### Feet

Stof type keywords:

* ft
* foot
* feet

#### Inches

Stof type keywords:

* in
* inch
* inches

### Time Units

#### Days

Stof type keywords:

* day
* days

#### Hours

Stof type keywords:

* hr
* hrs
* hour
* hours

#### Minutes

Stof type keywords:

* min
* mins
* minute
* minutes

#### Seconds

Stof type keywords:

* s
* second
* seconds

#### Milliseconds

Stof type keywords:

* ms
* millisecond
* milliseconds

#### Microseconds

Stof type keywords:

* us
* microsecond
* microseconds

#### Nanoseconds

Stof type keywords:

* ns
* nanosecond
* nanoseconds

### Temperature Units

#### Kelvin

Stof type keywords:

* K
* kelvin
* Kelvin

#### Celsius

Stof type keywords:

* C
* celsius
* Celsius

#### Fahrenheit

Stof type keywords:

* F
* fahrenheit
* Fahrenheit

### Mass Units

#### Gigatonnes

1e15 grams.

Stof type keywords:

* Gt
* gigatonne
* gigatonnes

#### Megatonnes

1e12 grams.

Stof type keywords:

* Mt
* megatonne
* megatonnes

#### Tonnes

1e6 grams.

Stof type keywords:

* t
* tonne
* tonnes

#### Kilograms

1e3 grams.

Stof type keywords:

* kg
* kilogram
* kilograms

#### Grams

Stof type keywords:

* g
* gram
* grams

#### Milligrams

1e-3 grams.

Stof type keywords:

* mg
* milligram
* milligrams

#### Micrograms

1e-6 grams.

Stof type keywords:

* ug
* microgram
* micrograms

#### Nanograms

1e-9 grams.

Stof type keywords:

* ng
* nanogram
* nanograms

#### Picograms

1e-12 grams.

Stof type keywords:

* pg
* picogram
* picograms

#### Tons

US imperial mass unit, defined as 0.907 tonnes.

Stof type keywords:

* ton
* tons
* Ton
* Tons

#### Pounds

US imperial mass unit, defined as 453.592 grams.

Stof type keywords:

* lb
* lbs
* pound
* pounds

#### Ounces

US imperial mass unit, defined as 28.3495 grams.

Stof type keywords:

* oz
* ounce
* ounces

### Angle Units

For operations, base units for angles are always radians. For comparisons and equality, angles get cast to positive radians, with a precision of 1e-6.

Angles are always clamped between 0 and 360 degrees. Radians and Degrees can be +-360deg, but Positive Radians and Positive Degrees treat 360deg as 0deg and always remain positive.

If you need angles expressed differently, you can always use a normal floating-point number.

#### Radians

Clamped between \[0, +-2pi].

Stof type keywords:

* rad
* radians

#### Degrees

Clamped between \[0, +-360].

Stof type keywords:

* deg
* degrees

#### Positive Radians

Clamped between \[0, +2pi).

Stof type keywords:

* prad
* pradians

#### Positive Degrees

Clamped between \[0, +360).

Stof type keywords:

* pdeg
* pdegrees
