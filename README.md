# City of Miami REST API Standards [DRAFT]

Ver 0.11.0  
Last modified 03.26.20

# Contents


[API Categories](#api-categories)

[API Lifecycle](#api-lifecycle)

[API Design Standards](#api-design-guidelines)

[API Management](#api-management)

[API Security](#api-security)

[References](#_Toc66114527)

## Overview

This document provides REST API design guidelines for creating APIs at
the City of Miami. It aims to encourage consistency, reusability,
maintainability, and best practices across applications. *The goal is to
balance a truly RESTFUL API with a positive developer experience (DX)*

These standards are influenced by the [White House](https://github.com/WhiteHouse/api-standards)
and THE [18F API standards](https://github.com/18F/api-standards) as well as general API best practices from the private sector.

### What is a REST API?


A REST API is modeled as collections of individually addressable
resources (the nouns of the API). Resources are referenced with their
resource names and manipulated via a small set of methods (also known as
verbs or operations).

REST APIs are designed around resources, which are any kind of object,
data, or service that can be accessed by the client.

Source: <https://cloud.google.com/apis/design/resources>

## API Categories

**Private / Internal API** – used internally within the organization.
Connects to organizations backend data. (methods allowed GET/POST, etc.)
data isolation for public.

**Public / Open API** – Available to non-City of Miami applications and
users. available for use by developers and other users with relatively
few restrictions. They are usually backed by open data. Some may require
a subscription for API consumption by external users.

**Partner API** – used to facilitate communication and integration of
applications between companies and its business partners. iBuild,
Laserfiche, DocuSign and ProjectDox would be an example.

## API Lifecycle 

The five stages of the API lifecycle are: 

1.  **Planning and designing & mocking the API**: API planning and
    design involves mapping out the various, resources and operations,
    along with the business case scenarios before the API is fully
    implemented. Identify who the consumer of the API will be. **Create
    the specification**, mock and validation created from specification,
    validate requirements, if requirements are not validated return to
    analysis and requirements gathering. If requirements are validated
    proceed to step 2.

2.  **Developing the API**: The development phase of an API focuses on
    implementing the API based on the plan and design.

3.  **Testing the API:** APIs deserve the same first-class treatment
    that you would give to any application. This means that APIs should
    be thoroughly tested and monitored for performance issues. Once
    you’ve made your API available to other developers, you take on a
    responsibility to ensure that nothing affects the API’s quality and
    performance. API functional testing tools like SoapUI and load
    testing tools like LoadUI are essential for testing API
    functionality and performance in pre-production environments.

4.  **Deploying the API**: Deployment of the API to a secure environment
    where it will be discovered and consumed. It is also important to
    monitor the APIs in production.

5.  **Retiring the API**: Deprecation is a natural part of the API
    lifecycle. It is the phase where support for an API’s version, or in
    many cases, an entire API itself, is discontinued.

A roadmap of your API development should be published in advance and
should announce new versions, deprecations, and retirement of existing
APIs.


### REST API Specification

- [Open API Specification (Swagger)](https://swagger.io/specification/)
- [RAML Specification for MuleSoft APIs](https://www.mulesoft.com/resources/api/design-apis-easily-with-RAML)

### Naming Convention (URL Structure)

-   A URL identifies a resource

-   All lower case

-   Use plural nouns to identify the resources

    -   /permits

-   Use HTTP verbs to do CRUD operations on the collections and elements

    -   Non-CRUD actions can be verbs instead of nouns and define that
        action as part of the resource hierarchy

        -   /permits/search/

| **HTTP METHOD** | **POST**        | **GET**           | **PUT**                             | **DELETE**          |
|-----------------|-----------------|-------------------|-------------------------------------|---------------------|
| CRUD OP         | CREATE          | READ              | UPDATE                              | DELETE              |
| /dogs           | Create new dogs | List dogs         | Bulk update                         | Delete all dogs     |
| /dogs/1234      | Error           | Show specific dog | If exists, update Bo; if not, error | Delete specific dog |

(Example from Web API design by Brian Mulloy)

-   Resource relationships

    -   E.g orders have items `/orders/{id}`

    -   People have followers `/people/{id}/followers/{id}`

    -   Accounts have transactions `/accounts/{id}/transactions/{id}`

-   Avoid deep nesting with a maximum of 3 levels
    - `/resource/identifier/resource`  
    -   Use subqueries to avoid deep nesting
 -   Move away from using systeminterfaces as part of the API name

### Versioning 

Have a clear version roadmap for your API and communicate all changes
with API developers. All released APIs must have a version.

-   Specify the version with a ‘v’ prefix. Move it all the way to the
    left in the URL so that it has the highest scope

-   Use a simple ordinal number, do not use dot notation.

    -   (e.g /v1/dogs).

| Type of Change                              | Impact               | Action                                                      |
|---------------------------------------------|----------------------|-------------------------------------------------------------|
| Adding a new operation or resource          | Non-breaking / minor | Update documentation                                        |
| Change the HTTP verb or method              | Breaking/Major       | Create new version, update documentation, notify developers |
| Remove an operation                         | Breaking/Major       | Create new version, update documentation, notify developers |
| Adding operational parameters to properties | Non-breaking / minor | Update documentation                                        |

-   Maintain APIs at least one version back (support multiple versions,
    how far back will we go?)

    -   How long to support deprecated version (3mo, 6mo, 1 year?)

    -   When a new version comes out, the previous version gets marked
        as deprecated and it is not available for subscription by new
        users. Support is maintained for existing users for \[a period
        of time\].
        
### Support 

- FAQ to provide guidance to solve common problems
- GitHub Issue tracking
- Developer newsletter
- Social media
- SLAs
    - Support previous versions for 1 year after release of new API version
    - Issue track response time 3-4 business days 

See: <https://github.com/GSA/api-standards#5-provide-a-feedback-mechanism-that-is-clear-and-monitored>

### Filtering, Pagination and Sorting

-   Partial response allows you to give developers just the information
    they need.

    -   `/users?fields=id,name,address`

        -   Better performance, & optimized resource usage and bandwidth

        -   Support for multiple devices, use cases and form factors

        -   Make it easy for developers to paginate objects in database

            -   Offset based (most popular approach)

            -   Use limit and offset

                -   Involves use of query parameters
                    `business?offset=6&limit5` (starting row and number of
                    rows to receive)

                -   Default pagination is `limit=10 with offset = 0`

                -   You should design a web API to limit the amount of data
                    returned by any single request.

            -   Consider supporting query strings that specify the
                maximum number of items to retrieve and a starting
                offset into the collection.  
                  
                `orders?limit=25&offset=50`

            -  Mobile app return 10 rows vs. browser 50 rows

        
### Error Handling

Error responses should include a common HTTP status code, a message for
the developer, a message for the end user (when appropriate), an
internal error code (corresponding to some specific internally
determined ID), and links where developers can find more info.

```json {

"status" : 400,

"developerMessage" : "Verbose, plain language description of the
problem. Provide developers

suggestions about how to solve their problems here",

"userMessage" : "This is a message that can be passed along to
end-users, if needed.",

"errorCode" : "6000",

"moreInfo" : "http://www.example.gov/developer/path/to/help/for/444444,

http://drupal.org/node/6000",

} 
```

####  Supported HTTP Status Codes

| HTTP Status Code | Result                | When to use |
|------------------|-----------------------|-------------|
| 200              | OK                    |             |
| 201              | Created               |             |
| 304              | Not modified          |             |
| 400              | Bad Request           |             |
| 401              | Unauthorized          |             |
| 404              | Not found             |             |
| 405              | Method not allowed    |             |
| 413              | Payload too large     |             |
| 429              | Too many requests     |             |
| 500              | Internal Server Error |             |

#### COM Specific Status Codes

| Internal Code | Result            | When to use                                  |
|---------------|-------------------|----------------------------------------------|
| 5000          | Unknown error     |                                              |
| 6XXX          | Database errors   | i.e. duplicate key 6001                      |
| 7XXX          | Validation errors | i.e. 7002 Missing required field description |
|               |                   |                                              |


-   Asynchronicity (handling long operations)

    -   Sometimes POST,PUT,DELETE operations might require processing
        that takes some time to complete. In these situations consider
        making the operation asynchronous. Return HTTP status code 201
        (Accepted) to indicate the request was accepted for processing
        but is not completed. (Microsoft)

### Testing and Performance 

-   API Testing

-   Measuring, benchmarks and SLA

See: https://github.com/GSA/api-standards\#api-testing

### Data Handling Patterns

-   Client controls response data to allow for better performance, lower battery usage on mobile, optimal CPU, etc.

    -   Mobile device requests less data than a desktop browser

    -   Support partial responses

       -   Single query parameter holds expression that identifies the fields (projections) (linkedin)  

                `/people/me?fields=firstname,lastname`

### REST API Caching 

Caching improves performance and improves scalability and the throughput
of the API. Caching can take place at the client browser, proxy, app
server or database.

When architecting a new API ask yourself the following questions:

- Type of data your API is returning 
- Speed of change (answers how long to cache)
- Sensitivity of the data 
   - sensitive data should never be cached
   - Consider no-store and private for sensitive data
   - Cache-Control:”no-store,max-age=60”
   - Private data is meant for a single user
         - Cache-Control:”no-store,max-age=60”
         - Default is public, so it needs to be specified     
- For large payloads consider using ETag header to check if data has changed

    -   Check if ETag hash has changed ,if it hasn’t changed
        (304: Not Modified) don’t request data, if ETag has
        has changed (200, OK), then request new information.

    -   Use ETag response especially for large reponses

-   Time sensitivity
    -   max-age=60 means that cached data is valid for 60
                seconds

    -   set max-age based on the refresh speed of your data.
          

## Supported data formats

-   We currently support JSON & XML data formats
-   List data formats provided in documentation
-   Best practice is to make sure that you future proof for accepting other formats in the future
-   Client decides the format if multiple formats are available

    -   Specified in query parameter

       -   /news?output=json

       -   /news?output=csv
    

## API Management

The City of Miami currently uses MuleSoft as their API management
platform. API management platforms provide a developer portal, security,
documentation among other things.

### Developer portal

-   API documentation

    -   Use externalDocs URL to connect with additional external
        documentation

    -   Try it feature

    -   Provide code sample and sample data

-   Self service provisioning

    -   Clearly define role, responsible for the authorization of access
        to the API

    -   Setup API access policies for your API

       -   Who can access it, private vs. partner vs. public

    -   Do not publish internal APIs on the portal 
       - Publish internal / private APIs in internal exchange   

-   Provide specifications-based tooling

    -   APIs created by import of specifications

-   API Traffic Management

    -   Response time consistency

       -   Make sure that response times are reasonable

       -   API uptime

       -   Response time

       -   Server performance (CPU/ Memory)

   
## API Security

### HTTPS

Only provide HTTPS endpoints. This protects authentication credentials.

### API Keys

Public REST services without access control run the risk of being farmed
leading to excessive bills for bandwidth or compute cycles. API keys can
be used to mitigate this risk.

API keys can reduce the impact of denial-of-service attacks. However,
when they are issued to third-party clients, they are relatively easy to
compromise.

-   Require API keys for every request to the protected endpoint.

-   Return 429 Too Many Requests HTTP response code if requests are
    coming in too quickly.

-   Revoke the API key if the client violates the usage agreement.

-   Do not rely exclusively on API keys to protect sensitive, critical
    or high-value resources.
    
### OAuth 2.0 authorization framework 

  -   Best practice for authentication
  -   Tokens based (JWT)
  -   End user is in full control of their data
  -   Used for .Net Core APIs

### Restrict HTTP methods

-   Apply a whitelist of permitted HTTP Methods e.g. GET, POST, PUT.

-   Reject all requests not matching the whitelist with HTTP response
    code 405 Method not allowed.

-   Make sure the caller is authorised to use the incoming HTTP method
    on the resource collection, action, and record

### Input Validation

-   Do not trust input parameters/objects.

-   Validate input: length / range / format and type.

-   Achieve an implicit input validation by using strong types like
    numbers, booleans, dates, times or fixed data ranges in API
    parameters.

-   Constrain string inputs with regexps.

-   Reject unexpected/illegal content.

-   Make use of validation/sanitation libraries or frameworks in your
    specific language.

-   Define an appropriate request size limit and reject requests
    exceeding the limit with HTTP response status 413 Request Entity Too
    Large.

### CORS

-   Disable CORS headers if cross-domain calls are not
    supported/expected.

-   Be as specific as possible and as general as necessary when setting
    the origins of cross-domain calls.

<https://github.com/GSA/api-standards#enable-cors>

### Sensitive Information in HTTP Requests

RESTful web services should be careful to prevent leaking credentials.
Passwords, security tokens, and API keys should not appear in the URL,
as this can be captured in web server logs, which makes them
intrinsically valuable.

-   In POST/PUT requests sensitive data should be transferred in the
    request body or request headers.

-   In GET requests sensitive data should be transferred in an HTTP
    Header.

**OK:**

https://example.com/resourceCollection/\[ID\]/action

https://twitter.com/vanderaj/lists

**NOT OK:**

https://example.com/controller/123/action?apiKey=a53f435643de32 
API Key is inserted into the URL.

### Traffic management controls

-   Quota policy

-   Rate limiting policy

    -   5 / second, 2,500 per day 


> It is recommended that you review the OWASP REST Security Cheat Sheet
> for more information
> <https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html>

## Reference

What is API Lifecycle Management:
<https://swagger.io/blog/api-strategy/what-is-api-lifecycle-management/>

https://github.com/18F/api-standards

<https://github.com/WhiteHouse/api-standards>
<https://github.com/GSA/api-standards>

Open API Standard
[https://www.openapis.org/](https://www.openapis.org/faq)

<https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html>

<https://github.com/wet-boew/wet-boew-api-standards>

