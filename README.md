GC Web API Standards
======================

Working requirements for Government of Canada APIs based on the [White House Web API Standards](https://github.com/WhiteHouse/api-standards)

Presently a Draft from the TBS Web Interoperability Working Group without a set deliverable date.  RFC to [WET - GC Web API Standards](https://github.com/wet-boew/wet-boew-api-standards).

* [Standards](#standards)
* [Response Format] (#response-format)
* [RESTful URLs](#restful-urls)
* [HTTP Verbs](#http-verbs)
* [Responses](#responses)
* [Error handling](#error-handling)
* [Official Languages](#official-languages)
* [Versions](#versions)
* [Record limits and offsets](#record-limits)
* [Mock Responses](#mock-responses)
* [JSONP](#jsonp)
* [Request & Response Examples](#request-response-examples)

## Standards

This document provides a standard along with examples for Government of Canada Web APIs, encouraging consistency, maintainability, and best practices across applications. Government of Canada APIs aim to balance a truly RESTful API interface with a positive developer experience (DX).

This document borrows heavily from:
* [The White House Web API Standards] (https://github.com/WhiteHouse/api-standards)
* [Fieldings Dissertation on REST](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)
* [Designing HTTP Interfaces and RESTful Web Services](http://munich2012.drupal.org/program/sessions/designing-http-interfaces-and-restful-web-services)
* API Facade Pattern, by Brian Mulloy, Apigee
* Web API Design, by Brian Mulloy, Apigee
* Google Maps multiple language support

## Response Format ( Hypertext contraint? )

* The HTTP Header "Accept" should be used to negotiate the API version 
   * "Accept: application/vnd.company.myapp.customer-v3+xml"
   * "Accept: application/vnd.company.myapp.customer-v3+json"

### Reverse DNS for Hypertext definition

( must be described. Chris Majewski. )

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
    * http://organizationName.ca/api/magazines/
* Filtering is a query:
    * http://organizationName.ca/api/magazines?year=2011&sort=desc
    * http://organizationName.ca/api/magazines?topic=economy&year=2011
* All articles in (or belonging to) this magazine:
    * http://organizationName.ca/api/magazines/1234/articles
* Specify optional fields in a comma separated list:
    * http://organizationName.ca/api/magazines/1234?fields=title,subtitle,date
* Add a new article to a particular magazine:
    * POST http://organizationName.ca/api/magazines/1234/articles

### Bad URL examples
* Non-plural noun:
    * http://organizationName.ca/magazine
    * http://organizationName.ca/magazine/1234
    * http://organizationName.ca/publisher/magazine/1234
* Verb in URL:
    * http://organizationName.ca/magazine/1234/create
* Filter outside of query string
    * http://organizationName.ca/magazines/2011/desc

## HTTP Verbs

Assuming dogs is a list of all dogs, dog id 1234 is an instance named "Bogart"

| HTTP METHOD | POST            | GET       | PUT         | DELETE |
| ----------- | --------------- | --------- | ----------- | ------ |
| CRUD OP     | CREATE          | READ      | UPDATE      | DELETE |
| /dogs       | Create new dogs | List dogs | Bulk update | Delete all dogs |
| /dogs/1234  | Error           | Show Bogart | If exists, update Bogart; If not, error | Delete Bogart |

## Responses
* No values in keys
    * Good, { name : "Bogart", breed: "Bulldog" }
    * Bad, { Bogart: "bulldog" }
* No internal-specific names (e.g. "node" and "taxonomy term")
    * Good { dog_id: 12345 }
    * Bad { did: 12345 }
* Metadata should only contain direct properties of the response set, not properties of the members of the response set
    * Good, metadata: { count: 3, next_dog: 1237 }
    * Bad, metadata: { count: 3, dogs: "1234,1235,1236", breeds: "bulldog,mixed,poodle" }

## Error handling

Error responses should include a common HTTP status code, message for the developer, message for the end-user (when appropriate), internal error code (corresponding to some specific internally determined error number) and links where developers can find more info.

    {
        "status" : "400",
        "developerMessage" : "Verbose, plain language description of the problem. Provide developers
        suggestions about how to solve their problems here",
        "userMessage" : "This is a message that can be passed along to end-users, if needed.",
        "errorCode" : "444444",
        "more info" : "http://organizationName.ca/developer/path/to/help/for/444444,
        http://drupal.org/node/444444",
    }

General response codes are preferable where a clear code is not available.  General codes should serve for automated recognition of general state and propper error handling offer a path to resolution.

The three base states to recognize are success, improper request ( client error ) and internal server error ( API error ).  These better defined by the following HTTP Status codes:

* 200 - OK
* 400 - Bad Request
* 500 - Internal Server Error

## Official Languages

### Caller-specified language

User selected language is prefered as it allows for a lighter footprint and a more common language independant framework.

This is best executed through a query parameter.  Language components are changed in place with the selected language code.

    * http://organizationName.ca/api/article/123-456/?language=en

    {
        "title": "Biosphere Reserve LiDAR Survey",
        ...
    }

    * http://organizationName.ca/api/article/123-456/?language=fr

    {
        "title": "Levé LiDAR aux environs du Réserve de biosphere",
        ...
    }

### All in one request

Content in all available languages should appear in separate fields at the same level.

Use a "_lang" suffix from BCP-47 on keys for both languages where possible or, at minimum, non-English languages.

    {
        "title_en": "Biosphere Reserve LiDAR Survey",
        "title_fr": "Levé LiDAR aux environs du Réserve de biosphere",
        ...
    }

or

    {
        "title": "Biosphere Reserve LiDAR Survey",
        "title_fr": "Levé LiDAR aux environs du Réserve de biosphere",
        ...
    }

## Versions

* APIs should always have a version number to maintain relation 
* Versions should be integers, not decimal numbers, prefixed with ‘v’
    * Good: v1, v2, v3
    * Bad: v-1.1, v1.2, 1.3
* Major version numbers are required if a change can produce changes in logic
* APIs should be maintained at least one version back
* The HTTP Header "Accept" must be defined and accepted to negotiate the API version 
    * "Accept: application/vnd.company.myapp.customer-v3+xml"

## Record limits and offsets

### Limits

* If no limit is specified, return results with a default limit.
    * Sanity check to a reasonable return size
    * Sanity check to a reaonsable execution time
    * For small datasets limits may exceed row count

* Limits are row / object limits
    * The organization decides what is to be in a row or object
    * Limits apply to the topmost logical row or object

### Offsets

Offsets apply to the structured data returned from the API distinct from internal indexing in the data.  For the purpose of explanation we'll assume rows/objects return are numbered 1, 2, 3, 4, 5 to the limit.

The general logic is to shift to what would be the subsequent entry by the offset ammount.  For a query that returns 1,2,3 an offset of 1 should return 2,3,4.  In a non-integer sets such as date.time ( eg: 20130101.010101, 20130101.010102, 20130101.010103 ) applying an offset of 1 would skip the first row and show the next in the series ( 20130101.010102, 20130101.010103, 20130101.010104 ).

Non integer row offsets are also possible, the same shifting logic still applies.  For a single integer without units it's assumed it's a row offset, if a unit is applied and the API is aware of such unit offets one can be applied by such, be it time ( y m d h m s ), distance ( m km ), temperature ( C F K ) or any other offset logical to the data.

### Pages

Much like offets defined ablove page= is an offset from the start of the larger dataset by the limit number.  If limit= is set to 25 and page= is set to 2 the total offset is 50, if the page= is set to 3 the total offset is 150.

Example use:
    * http://organizationName.ca/api/dataset?limit=25&offset=50
        * For row is base 1 rows 51 through 75 should be returned
    * http://organizationName.ca/api/dataset?limit=25&page=3
        * For row is base 1 rows 151 through 175 should be returned

### Metadata

Information about record limits should also be included in the Example resonse. Example:

    {
        "metadata": {
            "resultset": {
                "count": 25,
                "offset": 50,
                "limit": 25
            }
        },
        "results": [
            { .. }
        ]
    }

## Documentation minimums

( must be described. Chris Majewski. )

## Mock Responses

( Are mock responses standard?  The documentation should provide all the examples needed, real data should provide functional testing. I'd consider culling it.  Chris Majewski. )

It is suggested that each resource accept a 'mock' parameter on the testing server. Passing this parameter should return a mock data response (bypassing the backend).

Implementing this feature early in development ensures that the API will exhibit consistent behavior, supporting a test driven development methodology.

Note: If the mock parameter is included in a request to the production environment, an error should be raised.

## Callbacks

## JSONP

( Is JSONP a wise recomendation? It's a competing stanard to our Metadata definitons.  I'd consider culling it. Chris Majewski )

JSONP is easiest explained with an example. Here's a one from [StackOverflow](http://stackoverflow.com/questions/2067472/what-is-jsonp-all-about?answertab=votes#tab-top):

> Say you're on domain abc.com, and you want to make a request to domain xyz.com. To do so, you need to cross domain boundaries, a no-no in most of browserland.

> The one item that bypasses this limitation is `<script>` tags. When you use a script tag, the domain limitation is ignored, but under normal circumstances, you can't really DO anything with the results, the script just gets evaluated.

> Enter JSONP. When you make your request to a server that is JSONP enabled, you pass a special parameter that tells the server a little bit about your page. That way, the server is able to nicely wrap up its response in a way that your page can handle.

> For example, say the server expects a parameter called "callback" to enable its JSONP capabilities. Then your request would look like:

>         http://www.xyz.com/sample.aspx?callback=mycallback

> Without JSONP, this might return some basic javascript object, like so:

>         { foo: 'bar' }

> However, with JSONP, when the server receives the "callback" parameter, it wraps up the result a little differently, returning something like this:

>         mycallback({ foo: 'bar' });

> As you can see, it will now invoke the method you specified. So, in your page, you define the callback function:

>         mycallback = function(data){
>             alert(data.foo);
>         };

http://stackoverflow.com/questions/2067472/what-is-jsonp-all-about?answertab=votes#tab-top

## Request & Response Examples

### API Resources

  - [GET /magazines](#get-magazines)
  - [GET /magazines/[id]](#get-magazinesid)
  - [POST /magazines/[id]/articles](#post-magazinesidarticles)

### GET /magazines

Example: http://organizationName.ca/api/v1/magazines

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

Example: http://organizationName.ca/api/v1/magazines/[id]

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

Example: Create – POST  http://organizationName.ca/api/v1/magazines/[id]/articles

    {
        "title": "Raising Revenue",
        "author_first_name": "Jane",
        "author_last_name": "Smith",
        "author_email": "jane.smith@organizationName.ca",
        "year": "2012"
        "month": "August"
        "day": "18"
        "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam eget ante ut augue scelerisque ornare. Aliquam tempus rhoncus quam vel luctus. Sed scelerisque fermentum fringilla. Suspendisse tincidunt nisl a metus feugiat vitae vestibulum enim vulputate. Quisque vehicula dictum elit, vitae cursus libero auctor sed. Vestibulum fermentum elementum nunc. Proin aliquam erat in turpis vehicula sit amet tristique lorem blandit. Nam augue est, bibendum et ultrices non, interdum in est. Quisque gravida orci lobortis... "

    }
