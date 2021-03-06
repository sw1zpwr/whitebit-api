# Private HTTP API V4

## Private endpoints V4 for trading

* [Trading balance](#trading-balance)
* [Create limit order](#create-limit-order)
* [Create market order](#create-market-order)
* [Create stop-limit order](#create-stop-limit-order)
* [Create stop-market order](#create-stop-market-order)
* [Cancel order](#cancel-order)
* [Query unexecuted orders](#query-unexecuted-orders)
* [Query executed order history](#query-executed-order-history)
* [Query executed order deals](#query-executed-order-deals)
* [Query executed orders by market](#query-executed-orders-by-market)
    
Base URL is https://whitebit.com

Endpoint example: https://whitebit.com/api/v4/{endpoint}

All endpoints return time in Unix-time format.

All endpoints return either a __JSON__ object or array.

For receiving responses from API calls please use http method __POST__

#### Error messages V4 format:
___
```json5
{
    "message": "ERROR MESSAGE",
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
### Trading balance

```
[POST] /api/v4/trade-account/balance
```
Get trade balance by currency ticker or all balances.

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
ticker | String | **No** | Currency's ticker. Example: BTC

**Request BODY raw:**
```json5
{
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**


Available statuses:
* `Status 200`
* `Status 400 if request validation failed`

```json5
{
    "...": {...},
    "BTC": {
        "available": "0.123",      // Available balance of currency for trading
        "freeze": "1"              // Balance of currency on orders
    },
    "...": {...},
    "XMR": {
        "available": "3013",
        "freeze": "100"
    },
    "...": {...}
}
```

<details>
<summary><b>Errors:</b></summary>

```json5
{
    "message": "Currency not found"
}
```

</details>
___

### Create limit order

```
[POST] /api/v4/order/new
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
Available statuses:
* `Status 200`
* `Status 422 if inner validation failed`
* `Status 503 if service temporary unavailable`

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

Error codes:
* `1` - market disabled for trading
* `2` - incorrect amount (it is less than or equals zero or its precision is too big)
* `3` - incorrect price (it is less than or equals zero or its precision is too big)
* `4` - incorrect taker fee (it is less than zero or its precision is too big)
* `5` - incorrect maker fee (it is less than zero or its precision is too big)

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

### Create market order

```
[POST] /api/v4/order/market
```
Creates market trading order

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
side | String | **Yes** | Order type. Variables: 'buy' / 'sell' Example: 'buy'
amount | String | **Yes** | ⚠️Amount of **`money`** currency to **buy** or amount in **`stock`** currency to **sell**. Example: '0.01' for buy and '0.0001' for sell.

**Request BODY raw:**
```json5
{
    "market": "BTC_USDT",
    "side": "buy",
    "amount": "0.01",             // i want to buy 0.01 USDT
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

```json5
{
    "market": "BTC_USDT",
    "side": "sell",
    "amount": "0.01",              // i want to sell 0.01 BTC
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**
Available statuses:
* `Status 200`
* `Status 422 if inner validation failed`
* `Status 503 if service temporary unavailable`

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
    "side": "buy",                     // order type
    "takerFee": "0.001",               // maker fee ratio. If the number less than 0.0001 - its rounded to zero
    "timestamp": 1595792396.165973,    // current timestamp
    "type": "market"                   // order type
}
```
<details>
<summary><b>Errors:</b></summary>

Error codes:
* `1` - market disabled for trading
* `2` - incorrect amount (it is less than or equals zero or its precision is too big)
* `3` - incorrect price (it is less than or equals zero or its precision is too big)
* `4` - incorrect taker fee (it is less than zero or its precision is too big)
* `5` - incorrect maker fee (it is less than zero or its precision is too big)

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

### Create stop-limit order

```
[POST] /api/v4/order/stop_limit
```
Creates stop-limit trading order

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
side | String | **Yes** | Order type. Variables: 'buy' / 'sell' Example: 'buy'
amount | String | **Yes** | Amount of stock currency to buy or sell. Example: '0.001'
price | String | **Yes** | Price in money currency. Example: '9800'
activation_price | String | **Yes** | Activation price in money currency. Example: '10000'

**Request BODY raw:**
```json5
{
    "market": "BTC_USDT",
    "side": "buy",
    "amount": "0.01",
    "price": "9800",
    "activation_price": "9800",
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**
Available statuses:
* `Status 200`
* `Status 422 if inner validation failed`
* `Status 503 if service temporary unavailable`

```json5
{
    "activation_price": "11500",       // activation price
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
    "type": "stop limit"               // order type
}
```
<details>
<summary><b>Errors:</b></summary>

Error codes:
* `1` - market disabled for trading
* `2` - incorrect amount (it is less than or equals zero or its precision is too big)
* `3` - incorrect price (it is less than or equals zero or its precision is too big)
* `4` - incorrect taker fee (it is less than zero or its precision is too big)
* `5` - incorrect maker fee (it is less than zero or its precision is too big)

```json5
{
    "code": 0,
    "errors": {
        "activation_price": [
            "The activation price field is required."
        ],
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
        "activation_price": [
            "The activation price must be a number."
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

### Create stop-market order

```
[POST] /api/v4/order/stop_market
```
Creates stop-market trading order

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
side | String | **Yes** | Order type. Variables: 'buy' / 'sell' Example: 'buy'
amount | String | **Yes** | ⚠️Amount of **`money`** currency to **buy** or amount in **`stock`** currency to **sell**. Example: '0.01' for buy and '0.0001' for sell.
activation_price | String | **Yes** | Activation price in money currency. Example: '10000'

**Request BODY raw:**
```json5
{
    "market": "BTC_USDT",
    "side": "buy",
    "amount": "0.01",              // i want to buy 0.01 USDT
    "activation_price": "10000",
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```
```json5
{
    "market": "BTC_USDT",
    "side": "sell",
    "amount": "0.01",              // i want to sell 0.01 BTC
    "activation_price": "10000",
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**
Available statuses:
* `Status 200`
* `Status 422 if inner validation failed`
* `Status 503 if service temporary unavailable`

```json5
{
    "activation_price": "10000",       // activation price
    "amount": "0.001",                 // amount
    "dealFee": "0",                    // fee in money that you pay if order is finished
    "dealMoney": "0",                  // if order finished - amount in money currency that finished
    "dealStock": "0",                  // if order finished - amount in stock currency that finished
    "left": "0.001",                   // if order not finished - rest of amount that must be finished
    "makerFee": "0.001",               // maker fee ratio. If the number less than 0.0001 - its rounded to zero    
    "market": "BTC_USDT",              // deal market
    "orderId": 4180284841,             // order id
    "side": "buy",                     // order type
    "takerFee": "0.001",               // maker fee ratio. If the number less than 0.0001 - its rounded to zero
    "timestamp": 1595792396.165973,    // current timestamp
    "type": "stop market"              // order type
}
```
<details>
<summary><b>Errors:</b></summary>

Error codes:
* `1` - market disabled for trading
* `2` - incorrect amount (it is less than or equals zero or its precision is too big)
* `3` - incorrect price (it is less than or equals zero or its precision is too big)
* `4` - incorrect taker fee (it is less than zero or its precision is too big)
* `5` - incorrect maker fee (it is less than zero or its precision is too big)

```json5
{
    "code": 0,
    "errors": {
        "activation_price": [
            "The activation price field is required."
        ],
        "amount": [
            "The amount field is required."
        ],
        "market": [
            "The market field is required."
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
[POST] /api/v4/order/cancel
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

Available statuses:
* `Status 200`
* `Status 422 if inner validation failed`
* `Status 503 if service temporary unavailable`


```json5
{
    "activation_price": "12000",       // activation price if activation price isset
    "amount": "0.001",                 // amount
    "dealFee": "0",                    // fee in money that you pay if order is finished
    "dealMoney": "0",                  // if order finished - amount in money currency that finished
    "dealStock": "0",                  // if order finished - amount in stock currency that finished
    "left": "0.001",                   // if order not finished - rest of amount that must be finished
    "makerFee": "0.001",               // maker fee ratio. If the number less than 0.0001 - its rounded to zero    
    "market": "BTC_USDT",              // deal market
    "orderId": 4180284841,             // order id
    "price": "9800",                   // price if price isset
    "side": "buy",                     // order type
    "takerFee": "0.001",               // maker fee ratio. If the number less than 0.0001 - its rounded to zero
    "timestamp": 1595792396.165973,    // current timestamp
    "type": "stop market"              // order type
}
```
<details>
<summary><b>Errors:</b></summary>

Error codes:
* `1` - market disabled for trading
* `2` - incorrect amount (it is less than or equals zero or its precision is too big)
* `3` - incorrect price (it is less than or equals zero or its precision is too big)
* `4` - incorrect taker fee (it is less than zero or its precision is too big)
* `5` - incorrect maker fee (it is less than zero or its precision is too big)

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
[POST] /api/v4/orders
```
Returns unexecuted(active) orders

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT
limit | Int | **No** | LIMIT is a special clause used to limit records a particular query can return. Default: 50, Min: 1, Max: 100
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

### Query executed order history

```
[POST] /api/v4/trade-account/executed-history
```
Returns orders history sorted by single market if it needed

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **No** | Requested available market. Example: BTC_USDT
limit | Int | **No** | LIMIT is a special clause used to limit records a particular query can return. Default: 50, Min: 1, Max: 100
offset | Int | **No** | If you want the query to return entries starting from a particular line, you can use OFFSET clause to tell it where it should start. Default: 0, Min: 0, Max: 10000

**Request BODY raw:**
```json5
{
    "offset": 0,
    "limit": 100,
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**
```json5
{
    "BTC_USDT": [
        {
            "amount": "0.000076",         // amount in stock
            "deal": "0.70407996",         // amount in money
            "fee": "0.00070407996",       // fee that you pay 
            "id": 160305483,              // orderID
            "price": "9264.21",           // price
            "role": 2,                    // Role - 1 - maker, 2 - taker
            "side": "sell",               // Order side "sell" / "buy"
            "time": 1594667731.724403     // Timestamp of executed order
        },
        {...}
    ],
    "DTBC_DUSDT": [...]
}


```
<details>
<summary><b>Errors:</b></summary>

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

### Query executed order deals

```
[POST] /api/v4/trade-account/order
```
Returns more detail order deals history 

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
orderId | Int | **Yes** | Order ID. Example: 1234
limit | Int | **No** | LIMIT is a special clause used to limit records a particular query can return. Default: 50, Min: 1, Max: 100
offset | Int | **No** | If you want the query to return entries starting from a particular line, you can use OFFSET clause to tell it where it should start. Default: 0, Min: 0, Max: 10000

**Request BODY raw:**
```json5
{
    "orderId": 3135554375,
    "offset": 0,
    "limit": 100,
    "request": "{{request}}",
    "nonce": "{{nonce}}"
}
```

**Response:**

Empty response if order not yours
```json5
{
    "result": {
        "limit": 50,
        "offset": 0,
        "records": [
            {
                "amount": "598",                // amount in stock
                "deal": "0.00419198",           // amount in money
                "dealOrderId": 3134995325,      // completed order ID
                "fee": "0.00000419198",         // fee that you pay 
                "id": 149156519,                // id of trade
                "price": "0.00000701",          // price
                "role": 2,                      // Role - 1 - maker, 2 - taker
                "time": 1593342324.613711       // Timestamp of executed order
            }
        ]
    },
}


```
<details>
<summary><b>Errors:</b></summary>

```json5
{
    "code": 0,
    "errors": {
        "orderId": [
            "The order id field is required."
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
        "limit": [
            "The limit may not be greater than 100."
        ],
        "offset": [
            "The offset may not be greater than 100000."
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

### Query executed orders by market

```
[POST] /api/v4/trade-account/order/history
```
Returns executed order history by market

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **No** | Requested available market. Example: BTC_USDT
limit | Int | **No** | LIMIT is a special clause used to limit records a particular query can return. Default: 50, Min: 1, Max: 100
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

Empty response if order not yours
```json5
{
    "BTC_USDT": [
        {
            "amount": "0.020159",             // amount of trade
            "ctime": 1597486960.311311,       // timestamp of order creation
            "dealFee": "0",                   // fee in money that you pay if order is finished
            "dealMoney": "239.83908183",      // amount in money currency that finished
            "dealStock": "0.020159",          // amount in stock currency that finished
            "ftime": 1597486960.311332,       // executed order timestamp
            "id": 4986126152,                 // order id
            "makerFee": "0",                  // maker fee ratio. If the number less than 0.0001 - its rounded to zero  
            "price": "0",                     // price
            "side": "sell",                   // order side
            "takerFee": "0",                  // maker fee ratio. If the number less than 0.0001 - its rounded to zero
            "type": "margin market"           // order type
        },
        {...}
    ]
}



```
<details>
<summary><b>Errors:</b></summary>

```json5
{
    "code": 0,
    "errors": {
        "limit": [
            "The limit may not be greater than 100."
        ],
        "offset": [
            "The offset may not be greater than 100000."
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