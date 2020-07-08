# Public HTTP API V1

## Public endpoints V1

* [Market info](#service)
* [Market activity](#kline)
* [Single market activity](#last-price)
* [Kline](#market-statistics)
* [Symbols](#market-statistics-for-current-day-utc)
* [Order depth](#market-trades)
* [Trade history](#market-depth)
    
Base endpoint is https://whitebit.com

Example how to use: https://whitebit.com/api/v1/{endpoint}

All endpoints return time in Unix-time format.

All endpoints return either a __JSON__ object array.

For receiving responses from API calls please use http method __GET__

If endpoint required parameters you will need to send them as `query string`

####Error messages V1 format:
```json
{
    "success": false,
    "message": "ERROR MESSAGE",
    "params": []
}
```

###Terminology

####Pair:

`Stock` - currency that you want to buy or sell

`Money` - currency that you are using to buy or sell something

`Maker` - person who puts an order and waiting till this order will be finished

`Taker` - person who finishes existing order

`Precision` - is the number of digits to the right of the decimal point

`Bid` - buy order

`Ask` - sell order


###Market Info

```
GET /api/v1/public/markets
```
This endpoint retrieves all information about markets for trading.

**Parameters:**
NONE

**Response:**
```json
{
  "success": true,
  "message": "",
  "result": [
    {
      "name": "BTC_USDT",      // Name of market pair
      "moneyPrec": "2",        // Precision of money currency
      "stock": "BTC",          // Ticker of stock currency
      "money": "USDT",         // Ticker of money currency
      "stockPrec": "6",        // Precision of stock currency
      "feePrec": "4",          // Precision of fee
      "minAmount": "0.001",    // Minimal amount of stock to trade
      "makerFee": "0.001",     // Default maker fee
      "takerFee": "0.001"      // Default taker fee
    },
    {
      ...
    }
  ]
}
```




**JSON Structure of response message:**

* `success` - **Bool**. Response status
* `message` - **String**. Error message
* `result` - **Array**. for success, **JSON Object** for failure:
    * `name` - **String**. Name of market pair
    * `moneyPrec` - **Int in string**. Precision of money currency
    
Code | Message
--- | ---
**1** | invalid argument
**2** | internal error
**3** | service unavailable
**4** | method not found
**5** | service timeout

## Types of response messages

* Query result
* Subscription status (success/failed)
* Update events

---

## Examples

**Example** messages for request with response:

#### :arrow_heading_up: Request:

```json
{
    "id": 0,
    "method": "ping",
    "params": []
}
```

#### :arrow_heading_down: Response: 
```json
{
    "id": 0,
    "result": "pong",
    "error": null
}
```

**Example** subscription:

#### :arrow_heading_up: Request:

```json
{
    "id": 0,
    "method": "candles_subscribe",
    "params": []
}
```

#### :arrow_heading_down: Response: 
```json
{
    "id": 0,
    "result": {
        "status": "success"
    },
    "error": null
}
```

#### :arrows_counterclockwise: Update events: 
```json5
{
    "id": null,
    "method": "candles_update",
    "params": [] // look below for params 
}
```

---

## API

### Service

#### Ping

##### :arrow_heading_up: Request:

```json
{
    "id": 0,
    "method": "ping",
    "params": []
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 0,
    "result": "pong",
    "error": null
}
```

#### Time

##### :arrow_heading_up: Request:

```json
{
    "id": 1,
    "method": "time",
    "params": []
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 1,
    "result": 1493285895,
    "error": null
}
```

---

### Kline

#### Query

##### :arrow_heading_up: Request:

```json5
{
    "id": 2,
    "method": "candles_request",
    "params": [
        "ETH_BTC",  // market
        1579569940, // start time 
        1580894800, // end time 
        900         // interval in seconds 
    ]
}
```

##### :arrow_heading_down: Response:

```json5
{
    "id": 2,
    "result": [
        [
            1580860800,       // time
            "0.020543",       // open
            "0.020553",       // close
            "0.020614",       // highest
            "0.02054",        // lowest
            "7342.597",       // volume in stock
            "151.095481849",  // volume in deal
            "ETH_BTC"         // market
        ],
        ...
    ],
    "error": null
}
```

#### Subscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 3,
    "method": "candles_subscribe",
    "params": [
        "BTC_USD", // market
        900        // interval in seconds
    ]
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 3,
    "result": {
        "status": "success"
    },
    "error": null
}
```

##### :arrows_counterclockwise: Update events:

```json5
{
    "id": null,
    "method": "candles_update",
    "result": [
        1580895000,     // time
        "0.020683",     // open
        "0.020683",     // close
        "0.020683",     // high
        "0.020666",     // low
        "504.701",      // volume in stock
        "10.433600491", // volume in money (deal)
        "ETH_BTC"       // market
    ]
}
```

#### Unsubscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 4,
    "method": "candles_unsubscribe",
    "params": []
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 4,
    "result": {
        "status": "success"
    },
    "error": null
}
```

---

### Last price

#### Query

##### :arrow_heading_up: Request:

```json5
{
    "id": 5,
    "method": "lastprice_request",
    "params": [
        "ETH_BTC", // market
    ]
}
```

##### :arrow_heading_down: Response:

```json5
{
    "id": 5,
    "result": "0.020553",
    "error": null
}
```

#### Subscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 6,
    "method": "lastprice_subscribe",
    "params": [
        "ETH_BTC", // markets
        "BTC_USDT",
        ...
    ]
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 6,
    "result": {
        "status": "success"
    },
    "error": null
}
```

##### :arrows_counterclockwise: Update events:

```json5
{
    "id": null,
    "method": "lastprice_update",
    "result": [
        "ETH_BTC",  // market
        "0.020683", // price
    ]
}
```

#### Unsubscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 7,
    "method": "lastprice_unsubscribe",
    "params": []
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 7,
    "result": {
        "status": "success"
    },
    "error": null
}
```

---

### Market statistics

#### Query

##### :arrow_heading_up: Request:

```json5
{
    "id": 5,
    "method": "market_request",
    "params": [
        "ETH_BTC", // market
        86400      // period in seconds
    ]
}
```

##### :arrow_heading_down: Response:

```json5
{
    "id": 5,
    "result": {
        "period": 86400,         // period in seconds 
        "last": "0.020981",      // last price
        "open": "0.02035",       // open price that was at 'now - period' time
        "close": "0.020981",     // price that closes this period
        "high": "0.020988",      // highest price
        "low": "0.020281",       // lowest price
        "volume": "135220.218",  // volume in stock
        "deal": "2776.587022649" // volume in money
    },
    "error": null
}

```

#### Subscribe

You can subscribe only for 86400s (24h from now).

##### :arrow_heading_up: Request:

```json5
{
    "id": 6,
    "method": "market_subscribe",
    "params": [
        "ETH_BTC", // markets
        "BTC_USDT",
        ...
    ]
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 6,
    "result": {
        "status": "success"
    },
    "error": null
}
```

##### :arrows_counterclockwise: Update events:

```json5
{
    "id": null,
    "method": "market_update",
    "result": [
        "ETH_BTC",                   // market
         {                           // response same as 'market_request'
            "period": 86400,         // period in seconds 
            "last": "0.020964",      // last price
            "open": "0.020349",      // open price that was at 'now - period' time
            "close": "0.020964",     // price that closes this period
            "high": "0.020997",      // highest price
            "low": "0.020281",       // lowest price
            "volume": "135574.476",  // volume in stock
            "deal": "2784.413999488" // volume in money
        }
    ]
}
```

#### Unsubscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 7,
    "method": "market_unsubscribe",
    "params": []
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 7,
    "result": {
        "status": "success"
    },
    "error": null
}
```

---

### Market statistics for current day UTC

#### Query

##### :arrow_heading_up: Request:

```json5
{
    "id": 14,
    "method": "marketToday_request",
    "params": [
        "ETH_BTC" // only one market per request
    ]
}
```

##### :arrow_heading_down: Response:

```json5
{
    "id": 14,
    "result": {
        "last": "0.020981",      // last price
        "open": "0.02035",       // open price that was at 'now - period' time
        "high": "0.020988",      // highest price
        "low": "0.020281",       // lowest price
        "volume": "135220.218",  // volume in stock
        "deal": "2776.587022649" // volume in money
    },
    "error": null
}

```

#### Subscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 15,
    "method": "marketToday_subscribe",
    "params": [
        "ETH_BTC", // markets
        "BTC_USDT",
        ...
    ]
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 15,
    "result": {
        "status": "success"
    },
    "error": null
}
```

##### :arrows_counterclockwise: Update events:

```json5
{
    "id": null,
    "method": "marketToday_update",
    "result": [
        "ETH_BTC",                   // market
         {                           // response same as 'market_request'
            "last": "0.020964",      // last price
            "open": "0.020349",      // open price that was at 'now - period' time
            "high": "0.020997",      // highest price
            "low": "0.020281",       // lowest price
            "volume": "135574.476",  // volume in stock
            "deal": "2784.413999488" // volume in money
        }
    ]
}
```

#### Unsubscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 16,
    "method": "marketToday_unsubscribe",
    "params": []
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 16,
    "result": {
        "status": "success"
    },
    "error": null
}
```

---

### Market trades

#### Query

##### :arrow_heading_up: Request:

```json5
{
    "id": 8,
    "method": "trades_request",
    "params": [
        "ETH_BTC", // market
        100,       // limit
        41358445   // largest id from which you want to request trades
    ]
}
```

##### :arrow_heading_down: Response:

```json5
{
    "id": 8,
    "result": [
        {
            "id": 41358530,           // trade id
            "time": 1580905394.70332, // time in milliseconds
            "price": "0.020857",      // trade price
            "amount": "5.511",        // trade amount
            "type": "sell"            // type of trade (buy/sell)
        },
        ...
    ],
    "error": null
}

```

#### Subscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 9,
    "method": "trades_subscribe",
    "params": [
        "ETH_BTC", // markets
        "BTC_USDT",
        ...
    ]
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 9,
    "result": {
        "status": "success"
    },
    "error": null
}
```

##### :arrows_counterclockwise: Update events:

```json5
{
    "id": null,
    "method": "trades_update",
    "result": [
        "ETH_BTC",                         // market
         [                                 // response same as 'market_request'
             {                            
                 "id": 41358530,           // trade id
                 "time": 1580905394.70332, // time in milliseconds
                 "price": "0.020857",      // trade price
                 "amount": "5.511",        // trade amount
                 "type": "sell"            // type of trade (buy/sell)
             },
             ...
         ]
    ]
}
```

#### Unsubscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 10,
    "method": "trades_unsubscribe",
    "params": []
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 10,
    "result": {
        "status": "success"
    },
    "error": null
}
```

---

### Market depth

#### Query

##### :arrow_heading_up: Request:

```json5
{
    "id": 11,
    "method": "depth_request",
    "params": [
        "ETH_BTC", // market
        100,       // limit, max value is 100
        "0"        // price interval units. "0" - no interval, available values - "0.00000001", "0.0000001", "0.000001", "0.00001", "0.0001", "0.001", "0.01", "0.1"
    ]
}
```

##### :arrow_heading_down: Response:

```json5
{
    "id": 11,
    "result": {
        "asks": [                   // sorted ascending         
            ["0.020846", "29.369"], // [price, amount]
            ...
        ],
        "bids": [                   // sorted descending 
            ["0.02083", "9.598"],   // [price, amount]
            ...
        ]
    },
    "error": null
}

```

#### Subscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 12,
    "method": "depth_subscribe",
    "params": [
        "ETH_BTC", // market
        100,       // limit, max value is 100
        "0"        // price interval units. "0" - no interval, available values - "0.00000001", "0.0000001", "0.000001", "0.00001", "0.0001", "0.001", "0.01", "0.1"
    ]
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 12,
    "result": {
        "status": "success"
    },
    "error": null
}
```

##### :arrows_counterclockwise: Update events:

```json5
{
    "id": null,
    "method": "depth_update",
    "result": [
        false,                          // true - full reload, false - partial update
        {
            "asks": [
                ["0.020861", "0"],      // for partial update - finished orders will be [price, "0"]
                ...
            ],
            "bids": [
                ["0.020844", "5.949"],
                ...
            ]
        },
        "ETH_BTC"                       // market
    ]
}
```

#### Unsubscribe

##### :arrow_heading_up: Request:

```json5
{
    "id": 13,
    "method": "depth_unsubscribe",
    "params": []
}
```

##### :arrow_heading_down: Response:

```json
{
    "id": 13,
    "result": {
        "status": "success"
    },
    "error": null
}
```