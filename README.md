# Official Documentation of WhiteBIT rest API

## Info

1. The API docks supports `private` and `public` methods
2. API versions: `V1`, `V2`, `V4`
3. For public endpoints please use this information:
    1. Public endpoints are cached. (Time of caching you can see in the doc)
    2. Use HTTP method `GET` when making a request
    3. Use `query string` if you need to send additional data.
4. Private endpoints:
    1. For making a private API calls:
        1. Please goto your personal cabinet on exchange
        2. Click on API keys tab
        3. Choose what kind of keys do you need to generate for different API calls
        4. Generate keys and turn them on
    2. Auth request must send with `POST` method and should include:
        1. Body data - **JSON** that includes:
            1. **'request'** - request path without domain name. Example: `'/api/v4/trade-account/balance'`
            2. **'nonce'** - is a number that is always **higher** than the previous request nonce number. Example: `'1594297865'` or you can use `time()` function of your language that returns current timestamp.
            3. **params of request** - Example: `'ticker': 'BTC'`
        2. Headers:
            1. `'Content-type': 'application/json'`
            2. `'X-TXC-APIKEY': api_key` - where api_key is your public WhiteBit API key
            3. `'X-TXC-PAYLOAD': payload'` - where payload is base64-encoded body data
            4. `'X-TXC-SIGNATURE': signature` - where signature is `hex(HMAC_SHA512(base64(payload), key=api_secret))`
    3. For creating your first requests - you can use [API Quick start helper](https://github.com/whitebit-exchange/api-quickstart) library. It supports such languages like:
        1. ``Go``
        2. ``NodeJS``
        3. ``Python``
        4. ``PHP``

___

## Public api

[Public API V1 Documentation](/Public/http-public-v1-doc.md) - General endpoints

[Public API V2 Documentation](/Public/http-public-v1-doc.md) - Endpoints for cmc

[Public API V4 Documentation](/Public/http-public-v1-doc.md) - Additional endpoints

___

## Private api

[Private API V1 Documentation](/Public/http-public-v1-doc.md)

[Private API V2 Documentation](/Public/http-public-v1-doc.md)

[Private API V4 Documentation](/Public/http-public-v1-doc.md)

    