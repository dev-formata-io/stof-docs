---
description: CLI HTTP library ("HTTP").
icon: globe
---

# HTTP Library

The [HTTP library](https://github.com/dev-formata-io/stof-http) that the CLI uses is separate from Stof. It is required that you [give the option](../run.md) to add this library when invoking the CLI. Otherwise, the CLI will not add it and you will not have network access while running your Stof document.

{% hint style="info" %}
An example using this library can be found on the page [rest-and-restructure.md](../../../book/data-interchange/rest-and-restructure.md "mention").
{% endhint %}

### <mark style="color:purple;">HTTP</mark><mark style="color:orange;">.get</mark>(url: <mark style="color:green;">str</mark>, headers?: <mark style="color:green;">map</mark>, body?: <mark style="color:green;">str</mark> | <mark style="color:green;">blob</mark>, timeout?: <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, response?: <mark style="color:green;">obj</mark>): <mark style="color:green;">(</mark>content\_type: <mark style="color:green;">str</mark>, response\_headers: <mark style="color:green;">map</mark>, body: <mark style="color:green;">blob)</mark>

HTTP GET request.

The URL argument is required, but everything else is optional. You may skip providing arguments that you do not need. For example "HTTP.get(url, timeout)" would be acceptable. However, if you need all arguments, they are required to be in this order.

The request header map must have string keys and string values (if provided).

Timeout defaults to 5 seconds, but you may use [Stof time units](../../../common-concepts/units.md) as needed.

If a response object is provided, Stof will use the [formats](../../formats.md) it has access to and the Content-Type header in the response headers to parse the response body into the provided object.

{% hint style="info" %}
If adding a request body, it is helpful to use the [Standard Library](../../libraries/standard-library.md) stringify and blobify functions, creating a body in the format of your choice.
{% endhint %}

The return type is a tuple, giving you easy access to the response content type, headers, and body. You can then use these to [parse](../../libraries/standard-library.md) the body into the document, or further orchestrate additional requests.

### <mark style="color:purple;">HTTP</mark><mark style="color:orange;">.post</mark>(url: <mark style="color:green;">str</mark>, headers?: <mark style="color:green;">map</mark>, body?: <mark style="color:green;">str</mark> | <mark style="color:green;">blob</mark>, timeout?: <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, response?: <mark style="color:green;">obj</mark>): <mark style="color:green;">(</mark>content\_type: <mark style="color:green;">str</mark>, response\_headers: <mark style="color:green;">map</mark>, body: <mark style="color:green;">blob)</mark>

HTTP POST request. Identical in implementation to [GET](http-library.md#http.get-url-str-headers-map-body-str-or-blob-timeout-float-or-units-response-obj-content_type-str-r), but uses the POST request method.

### <mark style="color:purple;">HTTP</mark><mark style="color:orange;">.put</mark>(url: <mark style="color:green;">str</mark>, headers?: <mark style="color:green;">map</mark>, body?: <mark style="color:green;">str</mark> | <mark style="color:green;">blob</mark>, timeout?: <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, response?: <mark style="color:green;">obj</mark>): <mark style="color:green;">(</mark>content\_type: <mark style="color:green;">str</mark>, response\_headers: <mark style="color:green;">map</mark>, body: <mark style="color:green;">blob)</mark>

HTTP PUT request. Identical in implementation to [GET](http-library.md#http.get-url-str-headers-map-body-str-or-blob-timeout-float-or-units-response-obj-content_type-str-r), but uses the PUT request method.

### <mark style="color:purple;">HTTP</mark><mark style="color:orange;">.delete</mark>(url: <mark style="color:green;">str</mark>, headers?: <mark style="color:green;">map</mark>, body?: <mark style="color:green;">str</mark> | <mark style="color:green;">blob</mark>, timeout?: <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, response?: <mark style="color:green;">obj</mark>): <mark style="color:green;">(</mark>content\_type: <mark style="color:green;">str</mark>, response\_headers: <mark style="color:green;">map</mark>, body: <mark style="color:green;">blob)</mark>

HTTP DELETE request. Identical in implementation to [GET](http-library.md#http.get-url-str-headers-map-body-str-or-blob-timeout-float-or-units-response-obj-content_type-str-r), but uses the DELETE request method.

### <mark style="color:purple;">HTTP</mark><mark style="color:orange;">.patch</mark>(url: <mark style="color:green;">str</mark>, headers?: <mark style="color:green;">map</mark>, body?: <mark style="color:green;">str</mark> | <mark style="color:green;">blob</mark>, timeout?: <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, response?: <mark style="color:green;">obj</mark>): <mark style="color:green;">(</mark>content\_type: <mark style="color:green;">str</mark>, response\_headers: <mark style="color:green;">map</mark>, body: <mark style="color:green;">blob)</mark>

HTTP PATCH request. Identical in implementation to [GET](http-library.md#http.get-url-str-headers-map-body-str-or-blob-timeout-float-or-units-response-obj-content_type-str-r), but uses the PATCH request method.

### <mark style="color:purple;">HTTP</mark><mark style="color:orange;">.head</mark>(url: <mark style="color:green;">str</mark>, headers?: <mark style="color:green;">map</mark>, body?: <mark style="color:green;">str</mark> | <mark style="color:green;">blob</mark>, timeout?: <mark style="color:green;">float</mark> | <mark style="color:green;">units</mark>, response?: <mark style="color:green;">obj</mark>): <mark style="color:green;">(</mark>content\_type: <mark style="color:green;">str</mark>, response\_headers: <mark style="color:green;">map</mark>, body: <mark style="color:green;">blob)</mark>

HTTP HEAD request. Identical in implementation to [GET](http-library.md#http.get-url-str-headers-map-body-str-or-blob-timeout-float-or-units-response-obj-content_type-str-r), but uses the HEAD request method.
