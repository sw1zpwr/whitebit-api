# Official Documentation of WhiteBIT rest API

## Info

1. WhiteBIT API supports `private` and `public` methods.
2. Available API versions: `V1`, `V2`, `V4`.
3. Using **Public endpoints**:
    1. Public endpoints are cached. You can find specific cache refresh interval for each particular request in API documentation.
    2. Use HTTP method `GET` method when making a request.
    3. Use `query string` if you need to send additional data.
4. Using **Private endpoints**:
    1. To make private API calls:
        1. Go to your account on whitebit.com.
        2. Click on the API keys tab.
        3. Select the appropriate configuration tab for your API keys. Different API keys allow access to different API calls.
        4. Generate API keys and toggle the activation switcher to "Activated".
    2. Auth request should be using `POST` method and should include:
        1. Body data - **JSON** that includes:
            1. **'request'** - a request path without the domain name. Example: `'/api/v4/trade-account/balance'`.
            2. **'nonce'** - a number that is always **larger** than the previous request’s nonce number. Example: `'1594297865'`. A good method of creating a **nonce** is to use the unix timestamp in milliseconds. This way you'll always get an incrementing number, but make sure not to send two API calls at the same time, otherwise their nonce will be identical.
            3. **params of request** - Example: `'ticker': 'BTC'`
        2. Headers:
            1. `'Content-type': 'application/json'`
            2. `'X-TXC-APIKEY': api_key` - where api_key is your public WhiteBit API key
            3. `'X-TXC-PAYLOAD': payload'` - where payload is base64-encoded body data
            4. `'X-TXC-SIGNATURE': signature` - where signature is `hex(HMAC_SHA512(base64(payload), key=api_secret))`
        3. Errors:
            
            **"Too many requests."** - this error means that the **“nonce”** in your current request is equal or is smaller than the one in the previous request.
            ___
            ```json5
            {
                "message": [
                    [
                        "Too many requests."
                    ]
                ],
                "result": [],
                "success": false
            }
            ```
            ___
            **"Invalid payload"** - that means that the data which you provided in the body of the  request didn't match the **base64-decoded** payload.
            ___
            ```json5
            {
                 "message": [
                     [
                         "Invalid payload."
                     ]
                 ],
                 "result": [],
                 "success": false
            }
            ```
            ___
            **"Unauthorized request."** - request signed incorrectly.
            ___
            ```json5
            {
                "message": [
                    [
                        "Unauthorized request."
                    ]
                ],
                "result": [],
                "success": false
            }
            ```
            ___ 
            **"Nonce not provided."** - you need to provide a **"nonce"** in the body request.
            ___
            ```json5
            {
                "message": [
                    [
                        "Nonce not provided."
                    ]
                ],
                "result": [],
                "success": false
            }
            ```
            ___ 
            **"Request not provided."** - you need to provide a **"request"** in the body request
            ___
            ```json5
            {
                "message": [
                    [
                        "Request not provided."
                    ]
                ],
                "result": [],
                "success": false
            }
            ```
            ___ 
    3. To help you get started with our API, we've created the [API Quick start helper](https://github.com/whitebit-exchange/api-quickstart) library. It supports the following languages:
        1. ``Go``
        2. ``NodeJS``
        3. ``Python``
        4. ``PHP``
        4. ``Java``
        4. ``Kotlin``

___

## Public api

[Public API V1 Documentation](/Public/http-public-v1-doc.md) - General endpoints

[Public API V2 Documentation](/Public/http-public-v2-doc.md) - Endpoints for cmc

[Public API V4 Documentation](/Public/http-public-v4-doc.md) - Additional endpoints

___

## Private api

[Private API V1 Documentation](/Private/http-private-v1-doc.md) - Private api documentation of trading possibilities

[Private API V4 Documentation](/Public/http-private-v4-doc.md)