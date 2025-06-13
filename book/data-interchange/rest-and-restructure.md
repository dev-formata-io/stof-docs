---
description: Making a REST request and restructuring the response.
---

# REST and Restructure

In this example, we are going to make a REST request to the free and public [Countries API](https://restcountries.com/#endpoints-region) using the [HTTP library](https://github.com/dev-formata-io/stof-http) that the [Stof CLI](../../reference/cli/) has access to. We're then going to restructure the response, returning country information for the most dense and least dense countries in Europe.

<figure><img src="../../.gitbook/assets/rest_restructure (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Stof is sandboxed by default, requiring the host environment to explicitly enable network access. The CLI allows this with the flag "-a http", instructing the document to load the default [HTTP library](https://github.com/dev-formata-io/stof-http).
{% endhint %}

## Making the HTTP Request

First, we'll need to make an HTTP request to get the data for every European country. Using this [endpoint](https://restcountries.com/#endpoints-region), and passing in "europe" for the region will do the trick.

{% code title="countries.stof" %}
```rust
/// Makes a REST call and parses the response into an object in this document
/// at the location "self.countries" or "root.countries".
/// Will create the object if needed and drop (delete) it if it already exists.
fn populateCountries() {
    // delete the countries object if it already exists and then create a new one
    drop self.countries;
    self.countries = new {};
    
    // make the HTTP request, parsing the response (based on headers) into our object
    HTTP.get("https://restcountries.com/v3.1/region/europe", self.countries);
    
    // next, we need to restructure each country in the response
    self.structureCountries();
}

/// Restructure the countries we receive back from the API call.
fn structureCountries() {
    // TODO
}

#[main]
fn main() {
    self.populateCountries();
    pln(stringify(self.countries, "toml")); // this will be rather large right now
}
```
{% endcode %}

### Running the Document

Before we move on to restructuring the response, let's use the CLI to test that the HTTP request is working. The TOML output is quite long, so we won't paste it here. However, you will see a lot of data per country and about 53 countries included in the response.

```
> stof run -a http ./countries.stof
... a bunch of TOML ...
```

## Structuring Each Country

Now that we have an array of countries, let's restructure each one. We want to do the following for each country:

* We only want to keep a subset of fields per country: "name", "capital", "altSpellings", "continents", "area", "maps", and "population".
* Instead of the name "altSpellings", it is better as "spellings", so rename it.
* Ultimately, we want to filter by density, but that information wasn't provided, so create it.

```rust
/// Makes a REST call and parses the response into "self.countries"
fn populateCountries() {
    drop self.countries;
    self.countries = new {};
    HTTP.get("https://restcountries.com/v3.1/region/europe", self.countries);
    self.structureCountries();
}

/// Restructure the countries we receive back from the API call.
fn structureCountries() {
    let keep = set("name", "capital", "altSpellings", "continents", "area", "maps", "population");
    let rename = map(("altSpellings", "spellings"));
    for (country in self.countries.field) {
        for (field in country.keys()) {
            if (!keep.contains(field)) {
                // remove this field from the country (and object if it's an object)
                country.removeField(field, true);
            } else {
                // rename this field if needed
                if (rename.contains(field)) country.renameField(field, rename.get(field));
            }
        }

        // add a population density field to this country, defined as population/area
        country.density = Number.round(country.population / country.area, 2);
    }
}

#[main]
fn main() {
    self.populateCountries();
    pln(stringify(self.countries, "toml")); // this will still be somewhat long
}
```

### Running the Document

Before we move on to getting the most dense and least dense countries, try running the document. You'll still see all of the countries, but they will have the structure we've given them.

```
> stof run -a http ./countries.stof
... a bunch of TOML ...
```

## Most and Least Dense

Now that we've added a density field to each country, we can look for the most dense and least dense European countries.

We're also creating a new object and combining the two countries' data for the final response.

```rust
/// Makes a REST call and parses the response into "self.countries"
fn populateCountries() {
    drop self.countries;
    self.countries = new {};
    HTTP.get("https://restcountries.com/v3.1/region/europe", self.countries);
    self.box("countries.field");
    self.structureCountries();
}

/// Restructure the countries we receive back from the API call.
fn structureCountries() {
    let keep = set("name", "capital", "altSpellings", "continents", "area", "maps", "population");
    let rename = map(("altSpellings", "spellings"));
    for (country in self.countries.field) {
        for (field in country.keys()) {
            if (!keep.contains(field)) country.removeField(field, true);
            else if (rename.contains(field)) country.renameField(field, rename.get(field));
        }
        country.density = Number.round(country.population / country.area, 2);
    }
}

/// Return the densest country.
fn mostDenseCountry(): obj {
    let density = 0;
    let current = null;
    for (country in self.countries.field) {
        if (country.density > density) {
            current = country;
            density = country.density;
        }
    }
    return current;
}

/// Return the least dense country.
fn leastDenseCountry(): obj {
    let density = null;
    let current = null;
    for (country in self.countries.field) {
        if (density == null || country.density < density) {
            current = country;
            density = country.density;
        }
    }
    return current;
}

#[main]
fn main() {
    self.populateCountries();
    
    let most = self.mostDenseCountry();
    let least = self.leastDenseCountry();

    let together = new {
        most_dense: most,
        least_dense: least,
    };

    pln(stringify(together, "toml"));
}
```

### Running the Document

Now when we run the document, we'll get the response we expect - both the least dense and most dense European countries, with the fields of our choice.

```
> stof run -a http ./countries.stof
[least_dense]
area = 61399.0
capital = ["Longyearbyen"]
continents = ["Europe"]
density = 0.04
population = 2562
spellings = ["SJ", "Svalbard and Jan Mayen Islands"]

[least_dense.maps]
googleMaps = "https://goo.gl/maps/L2wyyn3cQ16PzQ5J8"
openStreetMaps = "https://www.openstreetmap.org/relation/1337397"

[least_dense.name]
common = "Svalbard and Jan Mayen"
official = "Svalbard og Jan Mayen"

[least_dense.name.nativeName.nor]
common = "Svalbard og Jan Mayen"
official = "Svalbard og Jan Mayen"

[most_dense]
area = 2.02
capital = ["Monaco"]
continents = ["Europe"]
density = 19427.72
population = 39244
spellings = ["MC", "Principality of Monaco", "Principauté de Monaco"]

[most_dense.maps]
googleMaps = "https://goo.gl/maps/DGpndDot28bYdXYn7"
openStreetMaps = "https://www.openstreetmap.org/relation/1124039"

[most_dense.name]
common = "Monaco"
official = "Principality of Monaco"

[most_dense.name.nativeName.fra]
common = "Monaco"
official = "Principauté de Monaco"
```

### More Concise

The interface we currently have is a more general one, but it could be more concise:

```rust
/// Makes a REST call and parses the response into "self.countries"
fn populateCountries() {
    drop self.countries;
    self.countries = new {};
    HTTP.get("https://restcountries.com/v3.1/region/europe", self.countries);
    self.structureCountries();
}

/// Restructure the countries we receive back from the API call.
fn structureCountries() {
    let keep = set("name", "capital", "altSpellings", "continents", "area", "maps", "population");
    let rename = map(("altSpellings", "spellings"));

    let high_density = 0;
    let high_country = null;
    let low_density = null;
    let low_country = null;
    let density = null;
    for (country in self.countries.field) {
        for (field in country.keys()) {
            if (!keep.contains(field)) country.removeField(field, true);
            else if (rename.contains(field)) country.renameField(field, rename.get(field));
        }

        density = Number.round(country.population / country.area, 2);
        country.density = density;

        if (density > high_density) {
            high_country = country;
            high_density = density;
        }
        if (low_density == null || density < low_density) {
            low_country = country;
            low_density = density;
        }
    }
    self.countries.densest = high_country;
    self.countries.least_dense = low_country;
}

#[main]
fn main() {
    self.populateCountries();

    let together = new {
        most_dense: super.countries.densest,
        least_dense: super.countries.least_dense,
    };

    pln(stringify(together, "toml"));
}
```

In this version, we're storing two additional references on our "root.countries" object, pointing to the least dense and densest country objects in the response array.

### Using a Schema + Schemafy

In this variation, we will build off our more concise version and use a Stof schema (any object in Stof can be treated as a schema). The advantage here is that schemas are easy to read, efficient, and can be better kept independently from the dataset one is working with.

{% hint style="success" %}
Stof can introduce meta-code to your data, helping you describe, transform, and govern data in new, more efficient ways.
{% endhint %}

{% hint style="info" %}
Take a look at the [Object Library](../../reference/libraries/object-library.md) for more information on "schemafy".
{% endhint %}

```typescript
/// Schema for each country returned by the countries API
country_schema: {
    name: ''
    capital: ''
    continents: []

    #[schema]
    maps: {
        googleMaps: ''
    }

    #[schema((value: int): bool => value >= 0)]
    population: 0

    #[schema((value: float): bool => value >= 0)]
    area: 0

    #[schema((target: obj, value: Box<unknown>): unknown => {
        if (value == null) {
            let search = target.search('altSpellings');
            if (search) return search.at(0);
        }
        return value;
    })]
    spellings: []

    #[schema((target: obj, value: Box<unknown>): unknown => {
        if (value == null) return Number.round(target.population / target.area, 2);
        return value;
    })]
    density: 0
}

/// Makes a REST call and parses the response into "self.countries"
fn populateCountries() {
    drop self.countries;
    self.countries = new {};
    HTTP.get("https://restcountries.com/v3.1/region/europe", self.countries);
    self.structureCountries();
}

/// Restructure the countries we receive back from the API call.
fn structureCountries() {
    let high_density = 0;
    let high_country = null;
    let low_density = null;
    let low_country = null;
    let density = null;
    for (country in self.countries.field) {
        // Apply our country schema to this country, removing invalid
        // fields and fields that are not defined in our schema.
        self.country_schema.schemafy(country, true, true);
        
        density = country.density;
        if (density > high_density) { high_country = country; high_density = density; }
        if (low_density == null || density < low_density) { low_country = country; low_density = density; }
    }
    self.countries.densest = high_country;
    self.countries.least_dense = low_country;
}

#[main]
fn main() {
    self.populateCountries();
    let together = new {
        most_dense: super.countries.densest,
        least_dense: super.countries.least_dense,
    };
    pln(stringify(together, 'toml'));
}
```

Using "schemafy" is much cleaner, as you can see. The output from running this is the same as above, however, in our schema, we only define "googleMaps" under the "maps" object, so "openStreetMaps" is not present. This demonstrates how easy (and fun) it is to define schemas that do quite a lot.

```
> stof run -a http ./countries.stof
[least_dense]
area = 61399.0
capital = ["Longyearbyen"]
continents = ["Europe"]
density = 0.04
population = 2562
spellings = ["SJ", "Svalbard and Jan Mayen Islands"]

[least_dense.maps]
googleMaps = "https://goo.gl/maps/L2wyyn3cQ16PzQ5J8"

[least_dense.name]
common = "Svalbard and Jan Mayen"
official = "Svalbard og Jan Mayen"

[least_dense.name.nativeName.nor]
common = "Svalbard og Jan Mayen"
official = "Svalbard og Jan Mayen"

[most_dense]
area = 2.02
capital = ["Monaco"]
continents = ["Europe"]
density = 19427.72
population = 39244
spellings = ["MC", "Principality of Monaco", "Principauté de Monaco"]

[most_dense.maps]
googleMaps = "https://goo.gl/maps/DGpndDot28bYdXYn7"

[most_dense.name]
common = "Monaco"
official = "Principality of Monaco"

[most_dense.name.nativeName.fra]
common = "Monaco"
official = "Principauté de Monaco"
```

### Object Exec + Schema

Stof objects have a [baked-in way](../../reference/libraries/object-library.md) to execute functions and sub-objects in order. This is for orchestration purposes, and when composed, can create a declarative programming interface that is extensible and customizable.

{% hint style="info" %}
See [orchestration.md](../orchestration.md "mention") for more information on object execution.
{% endhint %}

Here is one way to use this functionality with the above example:

```rust
#[run]
Countries: {
    region: "europe"
    
    country_schema: {
        // same schema as before...
    }

    #[run]
    fn populate() {
        drop self.countries;
        self.countries = new {};
        HTTP.get("https://restcountries.com/v3.1/region/" + self.region, self.countries);
    }

    #[run(1)]
    fn structure() {
        // same structure countries as above
    }
}

#[run(1)]
Together: {
    #[run]
    fn run() {
        self.densest = super.Countries.countries.densest;
        self.least_dense = super.Countries.countries.least_dense;
    }
}

#[main]
fn main() {
    self.Countries.region = "asia"; // change the region
    self.exec();
    pln(stringify(self.Together, "toml"));
}
```

```
> stof run -a http ./countries.stof
[densest]
area = 30.0
continents = ["Asia"]
density = 21644.73
population = 649342
spellings = ["MO", "澳门", "Macao", "Macao Special Administrative Region of the People's Republic of China", "中華人民共和國澳門特別行政區", "Região Administrativa Especial de Macau da República Popular da China"]

[densest.maps]
googleMaps = "https://goo.gl/maps/whymRdk3dZFfAAs4A"

[densest.name]
common = "Macau"
official = "Macao Special Administrative Region of the People's Republic of China"

[densest.name.nativeName.por]
common = "Macau"
official = "Região Administrativa Especial de Macau da República Popular da China"

[densest.name.nativeName.zho]
common = "澳门"
official = "中华人民共和国澳门特别行政区"

[least_dense]
area = 1564110.0
capital = ["Ulan Bator"]
continents = ["Asia"]
density = 2.1
population = 3278292
spellings = ["MN"]

[least_dense.maps]
googleMaps = "https://goo.gl/maps/A1X7bMCKThBDNjzH6"

[least_dense.name]
common = "Mongolia"
official = "Mongolia"

[least_dense.name.nativeName.mon]
common = "Монгол улс"
official = "Монгол улс"
```
