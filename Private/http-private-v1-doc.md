# Private HTTP API V1

## Private endpoints V1

* [Trading balance by currency](#trading-balance-by-currency)
* [Trading balances](#trading-balances)
* [Create limit order](#create-limit-order)
* [Kline](#kline)
* [Symbols](#symbols)
* [Order depth](#order-depth)
* [Trade history](#trade-history)
    
Base URL is https://whitebit.com

Endpoint example: https://whitebit.com/api/v1/{endpoint}

All endpoints return time in Unix-time format.

All endpoints return either a __JSON__ object or array.

For receiving responses from API calls please use http method __POST__

If an endpoint requires parameters you should send them as `query string`

#### Error messages V1 format:
___
```json5
{
    "result": [],
    "message": "ERROR MESSAGE",
    "success": false
}
```
___
### Terminology

#### Pair:

`Stock` - currency that you want to buy or sell

`Money` - currency that you are using to buy or sell something

`Maker` - person who puts an order and waiting till this order will be finished

`Taker` - person who finishes existing order

`Precision` - is the number of digits to the right of the decimal point

`Bid` - buy order

`Ask` - sell order

`Limit order` - order that has price, amount fields required for fill. If this order will find other order with opposite side - he will finish him. If not - he gets into orderbook.

___
### Trading balance by currency

```
[POST] /api/v1/account/balance
```
Get trade balance by currency ticker.

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
currency | String | **Yes** | Currency's ticker. Example: BTC

**Request BODY raw:**
```json5
{
    "currency": "BTC",
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**
```json5
{
    "message": "",
    "result": {
        "BTC": {
            "available": "0.2",    // Available balance of currency for trading
            "freeze": "1.02"       // Balance of currency on orders
        }
    },
    "success": true
}
```

**Errors:**
```json5
{
    "message": [
        [
            "Currency not found"
        ]
    ],
    "result": [],
    "success": false
}
```

```json5
{
    "message": {
        "currency": [
            "The currency field is required."
        ]
    },
    "result": [],
    "success": false
}
```
___

### Trading balances

```
[POST] /api/v1/account/balances
```
Get all balances for trading.

**Parameters:**
NONE

**Request BODY raw:**
```json5
{
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**
```json5
{
    "message": "",
    "result": {
        "BAT": {
            "available": "0",
            "freeze": "0"
        },
        "BCH": {
            "available": "0.00096586",
            "freeze": "0"
        },
        "BNB": {
            "available": "0",
            "freeze": "0"
        },
        "BSV": {
            "available": "0",
            "freeze": "0"
        },
        "BTC": {
            "available": "0.2",
            "freeze": "1.02"
        },
        "BTG": {
            "available": "0",
            "freeze": "0"
        },
        "BTT": {
            "available": "0",
            "freeze": "0"
        },
        "DASH": {
            "available": "0",
            "freeze": "0"
        },
        "DBTC": {
            "available": "0.47",
            "freeze": "0"
        },
        "ETH": {
            "available": "0.0000059",
            "freeze": "0"
        },
        "EUR": {
            "available": "0.00155901",
            "freeze": "0"
        },
        {...}
  }
}
```
___

### Create limit order

```
[POST] /api/v1/order/new
```
Creates limit trading order

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
side | String | **Yes** | Order type. Variables: 'buy' / 'sell' Example: 'buy'
amount | String | **Yes** | Amount of stock currency to buy or sell. Example: '0.001'
price | String | **Yes** | Price in money currency. Example: '9800'

**Request BODY raw:**
```json5
{
    "market": "BTC_USDT",
    "side": "buy",
    "amount": "0.01",
    "price": "9800",
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**
```json5
{
    "amount": "0.001",                 // amount
    "dealFee": "0",                    // fee in money that you pay if order is finished
    "dealMoney": "0",                  // if order finished - amount in money currency that finished
    "dealStock": "0",                  // if order finished - amount in stock currency that finished
    "left": "0.001",                   // if order not finished - rest of amount that must be finished
    "makerFee": "0.001",               // maker fee ratio. If the number less than 0.0001 - its rounded to zero    
    "market": "BTC_USDT",              // deal market
    "orderId": 4180284841,             // order id
    "price": "9800",                   // price
    "side": "buy",                     // order type
    "takerFee": "0.001",               // maker fee ratio. If the number less than 0.0001 - its rounded to zero
    "timestamp": 1595792396.165973,    // current timestamp
    "type": "limit"                    // order type
}
```
<details>
<summary><b>Errors:</b></summary>

```json5
{
    "code": 0,
    "errors": {
        "amount": [
            "The amount field is required."
        ],
        "market": [
            "The market field is required."
        ],
        "price": [
            "The price field is required."
        ],
        "side": [
            "The side field is required."
        ]
    },
    "message": "Validation failed"
}
```

```json5
{
    "code": 0,
    "errors": {
        "side": [
            "The selected side is invalid."
        ]
    },
    "message": "Validation failed"
}
```

```json5
{
    "code": 0,
    "errors": {
        "amount": [
            "The amount must be a number."
        ]
    },
    "message": "Validation failed"
}
```

```json5
{
    "code": 0,
    "errors": {
        "price": [
            "The price must be a number."
        ]
    },
    "message": "Validation failed"
}
```

```json5
{
    "code": 0,
    "errors": {
        "market": [
            "Unknown market."
        ]
    },
    "message": "Validation failed"
}
```
</details>
___

### Kline

```
[GET] /api/v1/public/ticker?market=BTC_USDT&interval=1h
```
This endpoint retrieves information about market kline.

**Response is cached for:**
_1 second_

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
start | Timestamp | **No** | Start time in seconds, default value is current start day. Example: 1596848400
end | Timestamp | **No** | End time in seconds, default value is current time. Example: 1596927600
interval | String | **Yes** | Possible values - 1m, 3m, 5m, 15m, 30m, 1h, 2h, 4h, 6h, 8h, 12h, 1d, 3d, 1w, 1M

**Response:**
```json5
{
  "success": true,
  "message": "",
  "result": [
    [
        594166400,             // Time in seconds
        "9257.4",              // Open
        "9243.19",             // Close
        "9265.14",             // High
        "9231.32",             // Low
        "817.535991",          // Volume stock
        "7558389.54233595"     // Volume money
    ],
    [...]
  ]
}
```
___

### Symbols

```
[GET] /api/v1/public/symbols
```
This endpoint retrieves information about all available markets for trading.

**Response is cached for:**
_1 second_

**Parameters:**
NONE

**Response:**
```json5
{
  "success": true,
  "message": "",
  "result": [
    "BTC_USDT",      // Name of market pair
    "ETH_BTC",       // Name of market pair
    "ETH_USDT",      // Name of market pair
    ...
  ]
}
```
___

### Order depth

```
[GET] /api/v1/public/depth/result?market=BTC_USDT
```
This endpoint retrieves the current order book as two arrays (bids / asks)

**Response is cached for:**
_1 second_

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
limit | int | **No** | Limit of results. Default: 50 Example: 100


**Response:**
```json5
{
  "asks": [
    [
      "9431.9",            // Price of lowest ask
      "0.705088"           // Amount of lowest ask
    ],
    [
      "9433.67",           // Price of next ask
      "0.324509"           // Amount of next ask
    ],
    [...]
  ],
  "bids": [
    [
      "9427.65",           // Price of highest bid
      "0.547909"           // Amount of highest bid
    ],
    [
      "9427.3",            // Price of next bid
      "0.669249"           // Amount of next bid
    ],
    [...]
  ]
}
```
___

### Trade History

```
[GET] /api/v1/public/history?market=BTC_USDT&lastId=1
```
This endpoint retrieves trades that have been executed for the requested market.

**Response is cached for:**
_1 second_

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
lastId | int | **Yes** | Largest id of last returned result. Example: 1
limit | int | **No** | Limit of results. Default: 50 Example: 100


**Response:**
```json5
{
  "success": true,
  "message": "",
  "result": [
    {
      "id": 156720314,              // Deal id
      "type": "sell",               // Deal type (buy or sell)
      "time": 1594240477.849703,    // Deal time in seconds
      "amount": "0.002784",         // Deal amount
      "price": "9429.66"            // Deal price
    },
    {
      "id": 156720309,
      "type": "sell",
      "time": 1594240476.832347,
      "amount": "0.002455",
      "price": "9429.66"
    },
    {...}
  ]
}
```
___
