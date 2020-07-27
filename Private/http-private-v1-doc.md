# Private HTTP API V1

## Private endpoints V1

* [Trading balance by currency](#trading-balance-by-currency)
* [Trading balances](#trading-balances)
* [Create limit order](#create-limit-order)
* [Cancel order](#cancel-order)
    
Base URL is https://whitebit.com

Endpoint example: https://whitebit.com/api/v1/{endpoint}

All endpoints return time in Unix-time format.

All endpoints return either a __JSON__ object or array.

For receiving responses from API calls please use http method __POST__

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

`Limit order` - to place this order, you need to fill the 'Price' and 'Amount' fields. If this order finds a corresponding order on the opposite side, it will be executed. Otherwise it will be placed into the orderbook.

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

<details>
<summary><b>Errors:</b></summary>

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
</details>
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

```json5
{
    "code": 0,
    "errors": {
        "amount": [
            "Not enough balance"
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
            "Given amount is less than min amount - 0.001",
            "Min amount step = 0.000001"
        ]
    },
    "message": "Validation failed"
}

```
</details>

___

### Cancel order

```
[POST] /api/v1/order/cancel
```
Cancel existing order

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
orderId | Int | **Yes** | Order Id. Example: 4180284841

**Request BODY raw:**
```json5
{
    "market": "BTC_USDT",
    "orderId": 4180284841,
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
        "market": [
            "The market field is required."
        ],
        "orderId": [
            "The order id field is required."
        ]
    },
    "message": "Validation failed"
}
```

```json5
{
    "code": 2,
    "errors": {
        "order_id": [
            "Unexecuted order was not found."
        ]
    },
    "message": "Inner validation failed"
}
```

```json5
{
    "code": 0,
    "errors": {
        "market": [
            "Market is not available"
        ]
    },
    "message": "Validation failed"
}
```

```json5
{
    "code": 0,
    "errors": {
        "orderId": [
            "The order id must be an integer."
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
            "The market must be a string.",
            "The market format is invalid.",
            "Market is not available"
        ]
    },
    "message": "Validation failed"
}
```

</details>

___

### Query unexecuted orders

```
[POST] /api/v1/orders
```
Returns unexecuted(active) orders

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
limit | Int | **No** | LIMIT is a special clause used to limit records a particular query can return. Default: 100, Min: 1, Max: 100
offset | Int | **No** | If you want the query to return entries starting from a particular line, you can use OFFSET clause to tell it where it should start. Default: 0, Min: 0, Max: 10000

**Request BODY raw:**
```json5
{
    "market": "BTC_USDT",
    "offset": 0,
    "limit": 100,
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**
```json5
[
    {
        "amount": "2.241379",             // active order amount
        "dealFee": "0",                   // executed fee by deal
        "dealMoney": "0",                 // executed amount in money
        "dealStock": "0",                 // executed amount in stock
        "left": "2.241379",               // unexecuted amount in stock
        "makerFee": "0.001",              // maker fee ratio. If the number less than 0.0001 - its rounded to zero    
        "market": "BTC_USDT",             // currency market
        "orderId": 3686033640,            // unexecuted order ID
        "price": "7900",                  // unexecuted order price
        "side": "buy",                    // type of order
        "takerFee": "0.001",              // taker fee ratio. If the number less than 0.0001 - its rounded to zero    
        "timestamp": 1594605801.49815,    // current timestamp of unexecuted order
        "type": "limit"                   // unexecuted order type
    },
    {...}
]

```
<details>
<summary><b>Errors:</b></summary>

```json5
{
    "code": 0,
    "errors": {
        "market": [
            "The market field is required."
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
            "Market is not available"
        ]
    },
    "message": "Validation failed"
}
```

```json5
{
    "code": 0,
    "errors": {
        "limit": [
            "The limit must be an integer."
        ],
        "offset": [
            "The offset must be an integer."
        ]
    },
    "message": "Validation failed"
}
```

```json5
{
    "code": 0,
    "errors": {
        "limit": [
            "The limit may not be greater than 100."
        ],
        "offset": [
            "The offset may not be greater than 10000."
        ]
    },
    "message": "Validation failed"
}
```

```json5
{
    "code": 0,
    "errors": {
        "limit": [
            "The limit must be at least 1."
        ],
        "offset": [
            "The offset must be at least 0."
        ]
    },
    "message": "Validation failed"
}
```

</details>

___