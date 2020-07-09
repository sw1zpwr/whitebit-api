# Official Documentation of WhiteBIT rest API

## Info

1. The API docks supports `private` and `public` methods
2. API versions: `V1`, `V2`, `V3`, `V4`
3. For public endpoints please use this information:
    1. Public endpoints are cached. (Time of caching you can see in the doc)
    2. Use HTTP method `GET` when making a request
    3. Use `query string` if you need to send additional data.
4. Private endpoints:
    1. For making a private API calls:
        1. Please goto your personal cabinet on exchange
        2. Click on API keys tab
        3. Choose what kind of keys do you need to generate for different API calls
        4. Generate keys
    2. How to make auth request:
        1. All requests must contain a nonce - number that will never be repeated and must increase between requests. ( This is to prevent an attacker who has captured a previous request from simply replaying that request. We recommend using a timestamp at millisecond or higher precision. )
        2. Payload: 

___

## Public api

[Public API V1 Documentation](/Public/http-public-v1-doc.md)

[Public API V2 Documentation](/Public/http-public-v1-doc.md)

[Public API V3 Documentation](/Public/http-public-v1-doc.md)

[Public API V4 Documentation](/Public/http-public-v1-doc.md)

___

## Private api

[Private API V1 Documentation](/Public/http-public-v1-doc.md)

[Private API V2 Documentation](/Public/http-public-v1-doc.md)

[Private API V3 Documentation](/Public/http-public-v1-doc.md)

[Private API V4 Documentation](/Public/http-public-v1-doc.md)

    