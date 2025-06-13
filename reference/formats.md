# 🪧 Formats

Formats enable Stof to import and export many different forms of data. Just like libraries, they do not travel with Stof documents and it is up to the host environment to add, replace, or remove the available formats loaded into the document.

{% hint style="info" %}
See [standard-library.md](libraries/standard-library.md "mention") parse, blobify, and stringify as well as [imports.md](../common-concepts/imports.md "mention") to see how Stof uses its formats to help users interact with many forms of data.
{% endhint %}

Formats are referenced by ID. At any time, one can see what format IDs are loaded with the "std.formats()" function. However, these are the formats Stof provides by default:

* text - loads a string into a field named "text" on the object referenced.
* bytes - loads a blob into a field named "bytes" on the object referenced.
  * This format is the fallback format for "header\_import" which is an important document functionality used in "parse" for parsing binary streams into a Stof document.
* Stof - the format we are documenting currently, the language and everything in these docs.
  * It is treated as a format just like all of the others, can be parsed, blobified, etc.
* JSON - we all know and love this one - used in a lot of places, the JavaScript Object Notation is completely supported in Stof. One can import, stringify, blobify, and parse JSON at any time.
* TOML - complete support for importing, stringify, blobify, etc. for TOML.
* YAML - support for importing, stringify, blobify, etc. for YAML.
* XML - support for importing, stringify, blobify, etc. for XML.
  * XML has a lot of specializations that Stof does its best with, however, creating a custom XML format if needed is pretty easy - you can always add or replace these formats.
* urlencoded - support for URL-encoded messages.
* bstof - a binary Stof document that has already been created/parsed.
  * Helpful for transferring documents and interfaces (fields, types, etc...) over the wire or saving them for later use.
