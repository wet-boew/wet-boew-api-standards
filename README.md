GC Web API Standards
======================

Working requirements for Government of Canada APIs based on the [White House Web API Standards](https://github.com/WhiteHouse/api-standards)

Presently a Draft from the TBS Web Interoperability Working Group without a set deliverable date.  RFC to [WET - GC Web API Standards](https://github.com/wet-boew/wet-boew-api-standards).

* [Style guide](#style-guide)
* [Standards](#standards)
* [Registration](#registration)
* [Hypertext constraint] (#hypertext-constraint)
* [RESTful URLs](#restful-urls)
* [HTTP Verbs](#http-verbs)
* [Responses](#responses)
* [Error handling](#error-handling)
* [Official Languages](#official-languages)
* [Versions](#versions)
* [Record limits and offsets](#record-limits)
* [Request & Response Examples](#request-response-examples)

## Style guide

For the remainder of this document code, arguments and other undefiend technical statements will be `code fenced` as to be easily distinguishable from standard text.

Arguments text will be used as follows:
* Arguments stated in the URL will be followed by an the equals sign '=' as `argument=`
* Arguments stated as a header will be followed by a colon ':' as `argument:`

## Standards

This document provides a standard along with examples for Government of Canada Web APIs, encouraging consistency, maintainability, and best practices across applications. Government of Canada APIs aim to balance a truly RESTful API interface with a positive developer experience (DX).

This document borrows heavily from:
* [The White House Web API Standards] (https://github.com/WhiteHouse/api-standards)
* [Fieldings Dissertation on REST](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)
* [Designing HTTP Interfaces and RESTful Web Services](http://munich2012.drupal.org/program/sessions/designing-http-interfaces-and-restful-web-services)
* API Facade Pattern, by Brian Mulloy, Apigee
* Web API Design, by Brian Mulloy, Apigee
* Google Maps multiple language support

## Registration

( must be described. )

## Hypertext constraint

* The HTTP Header `accept:` is to be used to negotiate the API version and format:
   * `Accept: vnd.company.myapp.customer-v3+xml`
   * `Accept: vnd.company.myapp.customer-v3+json`

### Reverse DNS for Hypertext format

Pending communication with IANA regarding root registration of Hypertext definition the following is strongly recomended as the likely standard.

* For GC Hypertext the root is `ca.canada`
* Further organizational ownership, parent through children.  `nrcan.gsc.ess.chis`
* The api noun `earthquakes`
* The version `-v1`
* The format `+json`

For the examples listed above the complete Hypertext format would be

`ca.canada.nrcan.gsc.ess.chis.earthquakes-v1+json`

## RESTful URLs

### Standards for RESTful URLs
* A URL identifies a resource.
* URLs should include nouns, not verbs.  Nouns have properties, verbs do not.
* Nouns should be plural as the resource to multiple instances of that noun.
* You shouldn’t need to go deeper than resource/identifier/resource.
* As a whole APIs should define complexity with multiple noun end points.
* URL v. header:
    * If it changes the logic you write to handle the response, put it in the URL.
    * If it doesn’t change the logic for each response, like OAuth info, put it in the header.
* Specify optional fields in a comma separated list.
* Formats should be in the form of api/resource/{id}

### Good URL examples
* List of magazines:
    * http://example.gc.ca/api/magazines/
* Filtering is a query:
    * http://example.gc.ca/api/magazines?year=2011&sort=desc
    * http://example.gc.ca/api/magazines?topic=economy&year=2011
* All articles in (or belonging to) this magazine:
    * http://example.gc.ca/api/magazines/1234/articles
* Specify optional fields in a comma separated list:
    * http://example.gc.ca/api/magazines/1234?fields=title,subtitle,date
* Add a new article to a particular magazine:
    * POST http://example.gc.ca/api/magazines/1234/articles

### Bad URL examples
* Non-plural noun:
    * http://example.gc.ca/magazine
    * http://example.gc.ca/magazine/1234
    * http://example.gc.ca/publisher/magazine/1234
* Verb in URL:
    * http://example.gc.ca/magazine/1234/create
* Filter outside of query string
    * http://example.gc.ca/magazines/2011/desc

## HTTP Verbs

Assuming dogs is a list of all dogs, dog id 1234 is an instance of dog named "Bogart"

| HTTP METHOD | POST            | GET       | PUT         | DELETE |
| ----------- | --------------- | --------- | ----------- | ------ |
| CRUD OP     | CREATE          | READ      | UPDATE      | DELETE |
| /dogs       | Create new dogs | List dogs | Bulk update | Delete all dogs |
| /dogs/1234  | Error           | Show Bogart | If exists, update Bogart; If not, error | Delete Bogart |

## Responses
* No values in keys
    * Good, `{ "name" : "Bogart", "breed": "Bulldog" }`
    * Bad, `{ "Bogart": "bulldog" }`
* No internal-specific names (e.g. "node" and "taxonomy term")
    * Good `{ "dog_id": 12345 }`
    * Bad `{ "did": 12345 }`
* Metadata should only contain direct properties of the response set, not properties of the members of the response set
    * Good, `metadata: { "count": 3, "next_dog": 1237 }`
    * Bad, `metadata: { "count": 3, "dogs": "1234,1235,1236", "breeds": "bulldog,mixed,poodle" }`

## Error handling

Error responses should include a common HTTP status code, message for the developer, message for the end-user (when appropriate), internal error code (corresponding to some specific internally determined error number) and links where developers can find more info.

    {
        "status" : "400",
        "developerMessage" : "Verbose, plain language description of the problem. Provide developers
        suggestions about how to solve their problems here",
        "userMessage" : "This is a message that can be passed along to end-users, if needed.",
        "errorCode" : "444444",
        "more info" : "http://example.gc.ca/developer/path/to/help/for/444444,
        http://drupal.org/node/444444",
    }

General response codes are preferable where a clear code is not available.  General codes should serve for automated recognition of general state and propper error handling offer a path to resolution.

The three base states to recognize are success, improper request ( client error ) and internal server error ( API error ).  These better defined by the following HTTP Status codes:

* 200 - OK
* 400 - Bad Request
* 500 - Internal Server Error

## Official Languages

Best practice: Implement one or both of the following methods for
including multilingual data in responses.

### Single endpoint

Recommended for writable APIs, for APIs returning many non-language fields
and for APIs supporting more than the two official languages.

All languages are returned in a nested manner with BCP-47 language codes used as keys.

    {
        "title": {
             "fr": "Levé LiDAR aux environs du Réserve de biosphere",
             "en": "Biosphere Reserve LiDAR Survey"
        },
        "resource_count": 5,
        "state": "active",
        ...
    }

Fields with values chosen from a limited set, such as "state" above, are represented with a single value.

### Multiple language endpoints

Recommended for APIs returning many fields containing language content.

Create two APIs with the language included in the API URL.

Example: the same resource with language fields returned in only a single
language is available from:

* http://example.gc.ca/en/api/resource/[id]
* http://example.gc.ca/fr/api/resource/[id]

The domain and API URLs must match exactly so that callers can retrieve
the corresponding results in the other language easily.  If French and
English content is served from separate domains then both APIs must be
available on both domains.

Language fields are returned as objects with their language as the only key:

    {
        "title": {
             "fr": "Levé LiDAR aux environs du Réserve de biosphere",
        },
        "resource_count": 5,
        "state": "active",
        ...
    }

Non-language fields must not be different when the same resource is retrieved
in both languages.


## Versions

* APIs should always have a version number to maintain relation 
* Versions should be integers, not decimal numbers, prefixed with ‘v’
    * Good: v1, v2, v3
    * Bad: v-1.1, v1.2, 1.3
* Major version numbers are required if a change can produce changes in logic
* APIs should be maintained at least one version back
* The HTTP Header "Accept" must be defined and accepted to negotiate the API version 
    * "Accept: application/vnd.company.myapp.customer-v3+xml"

## Record limits, offsets, cursors and metadata

These elements are to be added where possible and relevant.  Consideration for the client creates requierments such as moblie device limitations and dataset size.

### Limits

* If no limit is specified, return results with a default limit.
    * Sanity check to a reasonable return size
    * Sanity check to a reaonsable execution time
    * For small datasets limits may exceed row count

* Limits are row / object limits
    * The organization decides what is to be in a row or object
    * Limits apply to the topmost logical row or object

Limits are to be defined as the singular `limit=` followed by an integer.

### Offsets

Offsets apply to the structured data returned from the API distinct from internal indexing in the data.  For the purpose of explanation we'll assume rows/objects return are numbered 1, 2, 3, 4, 5 to the limit.

The general logic is to shift to what would be the subsequent entry by the offset ammount.  For a query that returns 1,2,3 an offset of 1 should return 2,3,4.

Offsets are to be defined as the singular `offset=` followed by an integer.

Example use:

* http://example.gc.ca/api/dataset?limit=25&offset=75
    * For row is base 1 rows 76 through 100 should be returned

### Pages

Much like offets defined above `page=` is an offset incremtented by the `limit=` argument.  If `limit=` is set to 25 and `page=` is set to 2 the total offset is 50, if the `page=` is set to 3 the total offset is 150.

Example use:

* http://example.gc.ca/api/dataset?limit=25&page=3
    * For row is base 1 rows 76 through 100 should be returned

### Cursor

`cursor=` is an offset with a value based on the sort order of results returned.

Use `cursor=` to reliably iterate over all results without risk of skipping or receiving duplicate rows/objects due to insertions/deletions happening at the same time.

The string value use with `cursor=` is returned in the metadata of each response when any rows/objects are returned.
Typically it is a single value copied from the last row/object, and could be a date, name, internal id or any other sortable type.

Example use:
* http://example.gc.ca/api/dataset?limit=25&cursor=20130101.010101
    * For 25 rows following the row containing the sort order value "20130101.010101"

### Metadata

Information relevant to record limits, offsets and indexes should also be included as described in the example resonse as a nested element.  Only relevant elements ( "count", "limit", "offset", "page" and "continueFrom" ) are required.

    {
        "metadata": {
            "resultset": {
                "count": 25,
                "limit": 25,
                "page": 3,
                "offset": 75,
                "cursor": "sam100890032"
            }
        },
        "results": [...]
    }

    {
        "metadata": {
            "resultset": {
                "count": 100,
                "limit": 100,
                "page": 2,
                "offset": 200,
                "cursor": "Smith, John"
            }
        },
        "results": [...]
    }

    {
        "metadata": {
            "resultset": {
                "count": 18,
                "limit": 25,
                "cursor": "20130101.010101"
            }
        },
        "results": [...]
    }

## Documentation minimums

( must be described. )

## Callbacks

( must be described. )

## Request & Response Examples

### API Resources

  - [GET /magazines](#get-magazines)
  - [GET /magazines/[id]](#get-magazinesid)
  - [POST /magazines/[id]/articles](#post-magazinesidarticles)

### GET /magazines

Example: http://example.gc.ca/api/v1/magazines

    {
        "metadata": {
            "resultset": {
                "count": 123,
                "offset": 0,
                "limit": 10
            }
        },
        "results": [
            {
                "id": "1234",
                "type": "magazine",
                "title": "Public Water Systems",
                "tags": [
                    {"id": "125", "name": "Environment"},
                    {"id": "834", "name": "Water Quality"}
                ],
                "created": "1231621302"
            },
            {
                "id": 2351,
                "type": "magazine",
                "title": "Public Schools",
                "tags": [
                    {"id": "125", "name": "Elementary"},
                    {"id": "834", "name": "Charter Schools"}
                ],
                "created": "126251302"
            }
            {
                "id": 2351,
                "type": "magazine",
                "title": "Public Schools",
                "tags": [
                    {"id": "125", "name": "Pre-school"},
                ],
                "created": "126251302"
            }
        ]
    }

### GET /magazines/[id]

Example: http://example.gc.ca/api/v1/magazines/[id]

    {
        "id": "1234",
        "type": "magazine",
        "title": "Public Water Systems",
        "tags": [
            {"id": "125", "name": "Environment"},
            {"id": "834", "name": "Water Quality"}
        ],
        "created": "1231621302"
    }

### POST /magazines/[id]/articles

Example: Create – POST  http://example.gc.ca/api/v1/magazines/[id]/articles

    {
        "title": "Raising Revenue",
        "author_first_name": "Jane",
        "author_last_name": "Smith",
        "author_email": "jane.smith@example.gc.ca",
        "year": "2012"
        "month": "August"
        "day": "18"
        "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam eget ante ut augue scelerisque ornare. Aliquam tempus rhoncus quam vel luctus. Sed scelerisque fermentum fringilla. Suspendisse tincidunt nisl a metus feugiat vitae vestibulum enim vulputate. Quisque vehicula dictum elit, vitae cursus libero auctor sed. Vestibulum fermentum elementum nunc. Proin aliquam erat in turpis vehicula sit amet tristique lorem blandit. Nam augue est, bibendum et ultrices non, interdum in est. Quisque gravida orci lobortis... "

    }
