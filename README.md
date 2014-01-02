[DRAFT] GC Web API Standard
====================

<span color='red'>
Working requirements for Government of Canada (GoC) Web Application Programming Interfaces (API)s.

Presently a draft from the TBS Web Interoperability Working Group with the intent to deliver a first working draft by the next Web Managers Council.  RFC to [WET - GC Web API Standards](https://github.com/wet-boew/wet-boew-api-standards).
</span>


* [This Document](#this-document)
    * [Goal](#goal)
    * [Structure](#structure)
    * [Style Guide](#style-guide)
* [Web API Implementation](#web-api-implementation)
    * [1. Minimum Delivery](#1-minimum-delivery)
        * [1.1 HTTP Header](#11-http-header)
            * [1.1.1 Media Type](#111-media-type)
            * [1.1.2 Language](#112-language)
        * [1.2 HTTP Methods](#12-http-methods)
        * [1.3 Output](#13-output)
            * [1.3.1 Metadata](#131-metadata)
            * [1.3.2 Minimum Formats](#132-minimum-formats)
            * [1.3.3 Official Languages](#133-official-languages)
        * [1.4 Documentation](#14-documentation)
        * [1.5 Registration](#15-registration)
    * [2. Optional Features](#2-optional-features)
        * [2.1 Dataset segmenting](#21-dataset-segmenting)
            * [2.1.1 Limits](#211-limits)
            * [2.1.2 Offsets](#212-offsets)
            * [2.1.3 Pages](#213-pages)
            * [2.1.4 Cursor](#214-cursor)
            * [2.1.5 Dataset segmenting metadata](#215-dataset-segmenting-metadata)
        * [2.2 Structured Error Handling](#22-structured-error-handling)
        * [2.3 URI argument filtering](#23-uri-argument-filtering)
        * [2.4 URI path filtering](#24-uri-path-filtering)
        * [2.5 Versioning](#25-versioning)
    * [3. Best Practices](#3-best-practices)
        * [3.1 URI Structure](#31-uri-structure)
            * [3.1.1 /api/](#311-api)
            * [3.1.2 /developer/](#312-developer)
            * [3.1.3 /data/](#313-data)
        * [3.2 Human readable intuitive keys](#32-human-readable-intuitive-keys)
        * [3.3 Responses](#33-responses)
            * [3.3.1 Values in Keys](#331-values-in-keys)
            * [3.3.2 Internal Specific Keys](#332-internal-specific-keys)
            * [3.3.3 Metadata is dataset properties](#333-metadata-is-dataset-properties)
        * [3.4 Caching](#34-caching)
        * [3.5 Client registration](#35-client-registration)
* [Examples](#examples)

## This document

### Goal
The goal of this document is to ensure API delivery across the Government of Canada (GoC) is consistent and up to the highest standards by defining a base level of delivery and only describing expansion when further comment is required.

The intent is to maintain a living document that adapts to changes in the landscape of Web API delivery but at the same time being mindful of established and mandated standards presently adopted inside and outside the GoC.

It's expected that anything not described in this document is to be implemented from best practices with an eye to interoperability, maintainability and future direction.  Gaps, errors or new best practices should be brought to the Web Standards Office (WSO) Web Interoperability Working Group (WIWG).

GoC APIs aim to balance a truly RESTful API interface with a positive developer experience (DX).

### Structure
This document describes API requirements by priority:

* [Minimum delivery](#1-minimum-delivery)
* [Optional features](#2-optional-features)
* [Best practices](#3-best-practices)

### Style Guide
For the remainder of this document code, arguments and other undefined technical statements will be `code fenced` as to be easily distinguishable from standard text.

Arguments text will be used as follows:
* Arguments stated as a header will be followed by a colon ':' as `argument:`
* Arguments stated as a URI argument will be followed by an the equals sign '=' as `argument=`
* Arguments stated as a URI path element will be bracketed with forward slashes '/' as `/argument/`
* Arguments stated as a file format extension will be prefaced with a period '.' as `.arg`

Draft comments are to be bracketed with the following.
`TODO:`, `NOTE:` and `ISSUE:`

## Web API Implementation
Web APIs in the GoC are to be RESTful as described by Roy Thomas Fielding's dissertation "Architectural Styles and the Design of Network-based Software Architectures" (2000) adapted to mandated minimums for the GoC and design choices in support of Web Interoperability.

The intent is not to limit development to the prescribed minimums but to ensure that GoC Web APIs behave consistently.  Any and all other requirements or options not described in this document may be implemented at the discretion of the API owner so long as the [minimum delivery](#minimum-delivery) and delivery standard of [optional features](#optional-features) are met.

### 1. Minimum delivery
Interoperability depends greatly on common implementation at the very least a minimum platform.  This his section describe mandatory elements in input, output and maintenance that must be found in a GoC API and behave as described.

#### 1.1 HTTP Header
Headers variables are part of the request and response cycle in the Hypertext Transfer Protocol (HTTP).  Although not explicitly prescribed by RESTful design header negotiation is a widely used method in defining state in format and/or language and as such need to be supported.  Supporting headers for format and language bridges, in part, a divide in the theory of proper implementation.

The minimum header variables to be supported are media type through `Accept:` and language through `Accept-Language:`.  On response `Content-type:` must return the appropriate media type.

Supplemental header variables in request (e.g.: `Accept-Charset:`, `Accept-Encoding:`) or response (e.g.: `Content-Language:`, `Content-Length:`) can aid in delivery and efficiency where appropriate but are not required.

Supplemental methods of specifying language or output formats such as [URI argument filtering](#23-uri-argument-filtering) or [URI path filtering](#24-uri-path-filtering) may override header variables when required.

##### 1.1.1 Media Type
Output format, commonly known as media type, from a Web API are historically described by Multipurpose Internet Mail Extensions (MIME) types registered with the Internet Assigned Numbers Authority (IANA)'s media type catalogue.  For most standard file types IANA's media type catalogue will provide the appropriate type definition.

The `Accept:` header is described by W3C RFC2616 Section 14.1 (http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.1)

The media types are defined by by IANA.org (http://www.iana.org/assignments/media-types/media-types.xhtml)

##### 1.1.2 Language
The `Accept-Language:` header is described by W3C RFC2616 Section 14.4 (http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4)

Languages are to be defined by the W3C recommended BCP-47 (http://www.w3.org/International/core/langtags/#bcp47).

#### 1.2 HTTP Methods
HTTP Methods are often described in APIs as verbs.  Verbs refer to standard actions taken on data, those standard verbs are (POST, GET, PUT and DELETE).

Assuming dogs is a list of all dogs, dog id 1234 is an instance of dog named "Bogart" and instance 4321 is "Shreddies"

| HTTP METHOD | POST            | GET            | PUT              | DELETE           |
| ----------- | --------------- | -------------- | ---------------- | ---------------- |
| CRUD OP     | CREATE          | READ           | UPDATE           | DELETE           |
| /dogs       | Create new dogs | List dogs      | Bulk update      | Delete all dogs  |
| /dogs/1234  | Error           | Show Bogart    | update Bogart    | Delete Bogart    |
| /dogs/4321  | Error           | Show Shreddies | update Shreddies | Delete Shreddies |

HTTP Methods are described by W3C RFC2616 Sections 9.3, 9.4, 9.6 and 9.7 (http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)

### 1.3 Output

#### 1.3.1 Metadata

Representing data about the returned dataset will be required for proper operation of certain features and describing the data provided.

To avoid collision with the data and general interoperability metadata is to be described in a variable in the response.

Two fields are required in the metadata variable.

* The creation date-time of the response in ISO 8601 date-time format with timezone.
* The licenses relevant to the dataset presented.

```JSON
{
    "metadata":
    {
        "request":{ "dateCreated": "2014-01-01T00:00:00+00:00" },
        "licenses": 
        [
            "http://example.gc.ca/license-eng.php",
            "http://data.gc.ca/eng/open-government-licence-canada"
        ]
    }
}
```

Where possible describe the dataset validity, additional licenses and publishers with Schema.org as the namespace.  This is consistent and interoperable with RDFa's usage in the Standard on Web Interoperability Appendix E: HTML data requirements (http://www.tbs-sct.gc.ca/pol/doc-eng.aspx?id=25875&section=text)

```JSON
{
    "metadata":{
        "request":{ "dateCreated":"2014-01-01T00:00:00+00:00" },

        "name":"Earthquakes >4 for 1992",
        "description":"Earthquakes magnitude 4 or greater, 1992 calendar year",
        "keywords":"earthquake data",
        "publisher": "Government of Canada",
        "dateCreated":"1867-07-01T00:00:01+00:00",
        "dateModified":"1982-03-29T00:00:01+00:00"
    }
}
```

In the previous example we show how dateCreated for the request does not collide with the dateCreated describing the data.

Elements used in this description:

* http://schema.org/name
* http://schema.org/description
* http://schema.org/keywords
* http://schema.org/dateModified
* http://schema.org/publisher
* http://schema.org/dateCreated

Mandated use of ISO 8601 in the TBS Standard on Metadata (http://www.tbs-sct.gc.ca/pol/doc-eng.aspx?id=18909&section=text#sec9.4)

Use of timezones in ISO 8601 (http://en.wikipedia.org/wiki/ISO_8601#Time_zone_designators)

#### 1.3.2 Minimum Formats

Leading API frameworks and present day implementation of APIs delivery two output formats, JSON and XML.  Trends may be leading to JSON only APIs but development tools and applications may still support only one of the two options.  To maximize the client base a GoC API must output both JSON and XML at a minimum.

https://www.google.com/trends/explore?q=xml+api#q=xml%20api%2C%20json%20api&cmpt=q

#### 1.3.3 Official Languages

All English or French content returned as data are to be nested with BCP-47 language codes used as keys.  If the content is unilingual offer the alternate language in metadata.

Bilingual
```JSON
{
    "size": {
         "fr": "Gros",
         "en": "Large"
    },
    "type": {
         "fr": "Chien",
         "en": "Dog"
    }
}
```

French Only
```JSON
{
    "metadata": { "en": "http://example.gc.ca/api/dogs/1234" },
    "size": {
         "fr": "Gros"
    },
    "type": {
         "fr": "Chien"
    }
}
```

English Only
```JSON
{
    "metadata": { "fr": "http://exemple.gc.ca/api/chiens/1234" },
    "size": {
         "en": "Large"
    },
    "type": {
         "en": "Dog"
    }
}
```

Bilingual, English and French endpoints must all offer bilingual, French or English results if requested by `Accept-Language:`, `query-parameter=` or other means.

The calculated language (e.g.: `Accept-Language:` or query parameters) should be overridden if the media-type is HTML and it conflicts with the official language of the public web page served from the API.

```
Accept: text/html
Accept-Language: en
URL: http://exemple.gc.ca/api/chiens
```

The scenario above could return English content on the French `http://exemple.gc.ca/api/chiens` page.

### 1.4 Documentation

`TODO: Known gap, to be complete early Jan`

### 1.5 Registration

`TODO: Known gap, to be complete early Jan`

## 2. Optional features

Beyond the base delivery of an API features can help clients better access and use the data.  Elements in this section describe mandatory implementation of the the optional elements.

### 2.1 Dataset segmenting

Limits are nearly always mandatory, only limited size datasets are safe to to implement without a default and maximum limit.  These elements are strongly recommended where possible and relevant.  A second, but no less important, consideration for the client creates requirements such as mobile device limitations and dataset size.

#### 2.1.1 Limits

* If no limit is specified, return results with a default limit.
    * Sanity check to a reasonable return size
    * Sanity check to a reasonable execution time
    * For small datasets limits may exceed row count

* Limits are row / object limits
    * The organization decides what is to be in a row or object
    * Limits apply to the topmost logical row or object

Limits are to be defined as the singular `limit=` followed by an integer.

#### 2.1.2 Offsets

Offsets apply to the structured data returned from the API distinct from internal indexing in the data.  For the purpose of explanation we'll assume rows/objects return are numbered 1, 2, 3, 4, 5 to the limit.

The general logic is to shift to what would be the subsequent entry by the offset amount.  For a query that returns 1,2,3 an offset of 1 should return 2,3,4.

Offsets are to be defined as the singular `offset=` followed by an integer.

Example use:

* http://example.gc.ca/api/dogs?limit=25&offset=75
    * For row is base 1 rows 76 through 100 should be returned

#### 2.1.3 Pages

Much like offsets defined above `page=` is an offset incremented by the `limit=` argument.  If `limit=` is set to 25 and `page=` is set to 2 the total offset is 50, if the `page=` is set to 3 the total offset is 150.

Example use:

* http://example.gc.ca/api/dogs?limit=25&page=3
    * For row is base 1 rows 76 through 100 should be returned

#### 2.1.4 Cursor

`cursor=` is an offset with a value based on the sort order of results returned.

Use `cursor=` to reliably iterate over results without risk of skipping or receiving duplicate rows/objects due to insertions/deletions.

The string value use with `cursor=` is returned in the metadata of each response when any rows/objects are returned.  Typically it is a single value copied from the last row/object, and could be a date, name, internal id or any other sortable type.

Example use:
* http://example.gc.ca/api/dogs?limit=25&cursor=20130101.010101
    * For 25 rows following the row containing the sort order value "20130101.010101"

#### 2.1.5 Dataset segmenting metadata

Information relevant to record limits, offsets and cursors should also be included as described in the example response as a nested element.  Only relevant elements ("count", "limit", "offset", "page" and "cursor") are required.

```JSON
{
    "metadata": {
        "request": {
            "count": 25,
            "limit": 25,
            "page": 3
        }
    }
}
```

```JSON
{
    "metadata": {
        "request": {
            "count": 25,
            "limit": 25,
            "offset": 75
        }
    }
}
```

```JSON
{
    "metadata": {
        "request": {
            "count": 18,
            "limit": 25,
            "cursor": "20130101.010101"
        }
    }
}
```

### 2.2 Structured Error Handling

Although errors can be represented by HTTP status code alone structured error handling improves the ability to resolve issues for both consumer and maintainer.

Common practice is to provide useful error messaging such as included in the example below:

```JSON
{
    "metadata":{
        "request":{
            "status" : 400,
            "errorMessage" : "Verbose, plain language description of the problem. Provide developers suggestions about how to solve their problems here",
            "errorCode" : "444444",
            "moreInfo" : "http://example.gc.ca/developer/path/to/help/for/444444",
        }
    }
}
```

The three base states to recognize are success, improper request (client error) and internal server error (API error).  These better defined by the following HTTP Status codes:

* 200 - OK
* 400 - Bad Request
* 500 - Internal Server Error

Where possible the use the appropriate HTTP status code such as the following:

* "304 Not Modified" when the resource has not changed since the last reload or will not change
* "404 Not found" when a requested entity does not exist

### 2.3 URI argument filtering

Although not mandatory arguments are likely in dynamic and large datasets and follow standard `query=` logic.

Where required for compatibility or to satisfy client requirements query arguments can be used to override header variables such as `Accept:` or `Accept-Language:`.

URL arguments are defined by IETF RFC2396 Section 3 defined, through the document, as "query" (http://www.ietf.org/rfc/rfc2396.txt)

### 2.4 URI path filtering

URI based filtering, or Clean URLs, can be used to specify arguments to the API in the resource path.  Generally an aesthetic feature Clean URIs aid in caching and general adoption of an API.

Caching is best served when arguments, or URIs in general, are consistently represented.  When encoding arguments in the URI be consistent in the ordering and value representation preferably in data logical order.

Formats in Clean URL are defined as extensions to the virtual file as one would on a traditional file in a filesystem.  To define an XML or JSON file would be defined as /path/file.xml and /path/file.json.

An API defined with traditional URL arguments would be converted from:

`http://example.gc.ca/api/collection?argument_one=vale_one&argument_two=value_two&data_layout=list&format=json`

To:

`http://example.gc.ca/api/collection/value_one/value_two/list.json`

Clean URLs can be achieved by various means such as scripting language or http server redirections.

### 2.5 Versioning

Versioning, from a RESTful approach, is an anti-pattern but often necessary by development practices.  The only requirement in versioning, if implemented, is the inclusion of the version in the output metadata to any relevant versioning format.

```JSON
{
    "metadata": {
        "version": "3.1.23a"
    }
}
```

If an API is to be versioned interoperability and consistency is greatly aided by the following:

* Limit endpoint changes unless necessary
* Create endpoint for needs by the need over numerical where appropriate (e.g.: hardware dependance, client needs)
* Versions should be integers not decimal numbers to avoid galloping endpoints and prefixed with ‘v’ for intuitive identification
    * Good: `v1, v2, v3`
    * Bad: `v-1.1, v1.2, 1.3`
* If numerical major version numbers are required if a change can produce changes in logic
* If numerical maintain at least one version back

## 3. Best Practices

Elements in this section describe preferred implementation to be consistent with existing implementation or better methods of implementation.

### 3.1 URI Structure

#### 3.1.1 /api/

For consistency with norms APIs should be identified separately and consistently.  There are two prevailing methods of distinguishing an api from standard content.

The first is to contain API endpoints in a set directory at the root of the resource identifier (RI).  This lowers the chances the API endpoint moves due to later needs for the same root RI.

* http://example.gc.ca/api/dogs
* http://example.gc.ca/api/cats

The second method is to define a DNS record for the API and defining API services after root.  To be consistent with RI structure the use of 'api' would be advised.

* http://api.example.gc.ca/dogs
* http://api.example.gc.ca/cats

To aid in API discovery, navigating to the root of the API container should prove to be a useful endpoint. This means that should a user access `http://api.example.gc.ca` or `http://example.gc.ca/api` directly, an HTTP status code of 200 should be returned along with a landing page (or requested media type if available). This landing page should serve as a starting point for information on what APIs are available and on how to get started. This could be as simple as a list of available APIs and links to their respective `/developer/` sections for more information. In addition, search engine crawlers could benefit from links that provide basic queries to data that would be useful if indexed in search engines.

#### 3.1.2 /developer/

For consistency API documentation should be segregated into a common location.  Common practice is to maintain a developer's resource, although no fixed standard exist `/developer/` is a consistent term.

Use one of the following as per the institution's inherent web delivery infrastructure.

* `/developer/`
* `/developpeur/`
* `/développeur/`
* `/developer-developpeur/`
* `/developer-développeur/`
* `/developpeur-developer/`
* `/développeur-developer/`

#### 3.1.3 /data/

The `/data/` directories are reserved for institutional use.  API development should no use `/data/` as the inherent home for APIs where `/data/api/` would be acceptable.

This is true for the following directories

* `/data/`
* `/donnees/`
* `/données/`
* `/data-donnees/`
* `/data-données/`
* `/donnees-data/`
* `/données-data/`

### 3.2 Human readable intuitive keys

The easier your data is to read and understand the more likely the data is to be used and correctly.

* Good: `{ "name" : "Bogart", "breed": "Bulldog" }`
* Bad: `{ "nm": "Bogart", "brd": "Bulldog" }`

### 3.3 Responses

Response design is heavily dictated by data structure but there are better practices and more pitfalls to avoid.

#### 3.3.1 Values in Keys

* Good: `{ "name" : "Bogart", "breed": "Bulldog" }`
* Bad: `{ "Bogart": "bulldog" }`

#### 3.3.2 Internal Specific Keys

Avoid "node" and "taxonomy term" in your data.

* Good: `{ "dog_id": 12345 }`
* Bad: `{ "did": 12345 }`

#### 3.3.3 Metadata is dataset properties

Metadata should only contain direct properties of the response set, not properties of the members of the response set

* Good: `"metadata": { "count": 3, "nextDog": 1237 }`
* Bad: `"metadata": { "count": 3, "dogs": "1234,1235,1236", "breeds": "bulldog,mixed,poodle" }`

#### 3.4 Caching

Considering the clients and easy of use for APIs current HTTP delivery models may not be sufficient.  Local caching servers (e.g.: Squid, Varnish) or hosted Content delivery network (CND) can protect a network from bandwidth depending on request patterns.

How the API is structured, how expiry times are calculated and contract minimum delivery are all vital to proper caching.

#### 3.5 Client registration

To ease future tracking for metrics or to identify issues requiring/requesting an api key may be of use.  The request for a key should not request private information (name, email, phone number, etc...)

## Examples

`TODO: Insert examples when the the document is sorted to this point`
