# Public HTTP API V1

## Public endpoints V1

* [Market info](#market-info)
* [Market activity](#market-activity)
* [Single market activity](#single-market-activity)
* [Kline](#kline)
* [Symbols](#symbols)
* [Order depth](#order-depth)
* [Trade history](#trade-history)
    
Base URL is https://whitebit.com

Endpoint example: https://whitebit.com/api/v1/{endpoint}

All endpoints return time in Unix-time format.

All endpoints return either a __JSON__ object or array.

For receiving responses from API calls please use http method __GET__

If an endpoint requires parameters you should send them as `query string`

#### Error messages V1 format:
___
```json5
{
    "success": false,
    "message": "ERROR MESSAGE",
    "params": []
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

___

### Ticker (need to resole)

```
[GET] /api/v4/public/ticker
```
The ticker endpoint is to provide a 24-hour pricing and volume summary for each market pair available on the exchange.

**Response is cached for:**
_1 second_

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
type | String | **No** | Query buy side or sell side only. Can be sell or buy only

**Response:**
```json5
[
  {
    "tradeID": 158092490,                   // Deal id
    "price": "9235.71",                     // Deal price
    "base_volume": "9235.71",               // Deal volume in stock
    "quote_volume": "0.047233",             // Deal volume in money
    "trade_timestamp": 1594395768,          // Time in seconds
    "type": "buy"                           // Deal side
  },
  {
    "tradeID": 158092486,
    "price": "9235.71",
    "base_volume": "9235.71",
    "quote_volume": "0.0472",
    "trade_timestamp": 1594395767,
    "type": "sell"
  },
  {...}
]
```
___

### Assets

```
[GET] /api/v4/public/assets
```
This will return the trades that have executed recently for requested market.

**Response is cached for:**
_1 second_

**Parameters:**
NONE

**Response:**
```json5
{
  "BTC": {
    "name": "Bitcoin",                        // Full name of cryptocurrency.
    "unified_cryptoasset_id": null,           // Unique ID of cryptocurrency assigned by Unified Cryptoasset ID.
    "can_withdraw": true,                     // Identifies whether withdrawals are enabled or disabled.
    "can_deposit": true,                      // Identifies whether deposits are enabled or disabled.
    "min_withdraw": "0.001000000000000000",   // Identifies the single minimum withdrawal amount of a cryptocurrency.
    "max_withdraw": "0.000000000000000000",   // Identifies the single maximum withdrawal amount of a cryptocurrency.
    "maker_fee": "0.1",                       // Fees applied when liquidity is added to the order book.
    "taker_fee": "0.1"                        // Fees applied when liquidity is removed from the order book.
  },
  "ETH": {
    "name": "Ethereum",
    "unified_cryptoasset_id": null,
    "can_withdraw": true,
    "can_deposit": true,
    "min_withdraw": "0.020000000000000000",
    "max_withdraw": "0.000000000000000000",
    "maker_fee": "0.1",
    "taker_fee": "0.1"
  },
  "USDT": {
    "name": "Tether US",
    "unified_cryptoasset_id": null,
    "can_withdraw": true,
    "can_deposit": true,
    "min_withdraw": "10.000000000000000000",
    "max_withdraw": "0.000000000000000000",
    "maker_fee": "0.1",
    "taker_fee": "0.1",
    "networks": {                             // This object will exist if a currency is available on several networks
      "deposits": [                           // Networks available for depositing
        "ERC20",
        "TRC20",
        "OMNI"
      ],
      "withdraws": [                          // Networks available for withdrawing
        "ERC20",
        "TRC20",
        "OMNI"
      ],
      "default": "ERC20"                      // Default network for depositing / withdrawing
    }
  },
  {...}
}
```
___

### Orderbook

```
[GET] /api/v4/public/orderbook/{market}?depth=100&level=2
```
This will return the current order book as two arrays (bids / asks).

**Response is cached for:**
_1 second_

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
depth | int | **No** | Orders depth quantity:[0,5,10,20,50,100,500] Not defined or 0 = full order book
limit | int | **No** | Level 1 – Only the best bid and ask. Level 2 – Arranged by best bids and asks. Level 3 – Complete order book, no aggregation.


**Response:**
```json5
{
  "timestamp": 1594391413,        // Current timestamp
  "asks": [                       // Array of ask orders
    [
      "9184.41",                  // Price of lowest ask
      "0.773162"                  // Amount of lowest ask
    ],
    [ ... ]
  ],
  "bids": [                       // Array of bid orders
    [
      "9181.19",                  // Price of highest bid
      "0.010873"                  // Amount of highest bid
    ],
    [ ... ]
  ]
}
```
___

### Recent Trades

```
[GET] /api/v4/public/trades/{market}?type=sell
```
This will return the trades that have executed recently for requested market.

**Response is cached for:**
_1 second_

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
type | String | **No** | Query buy side or sell side only. Can be sell or buy only


**Response:**
```json5
[
  {
    "tradeID": 158056419,             // A unique ID associated with the trade for the currency pair transaction Note: Unix timestamp does not qualify as trade_id.
    "price": "9186.13",               // Transaction price in base pair volume.
    "base_volume": "9186.13",         // Transaction amount in base pair volume.
    "quote_volume": "0.0021",         // Transaction amount in quote pair volume.
    "trade_timestamp": 1594391747,    // Unix timestamp in milliseconds for when the transaction occurred.
    "type": "sell"                    //  Used to determine whether or not the transaction originated as a buy or sell. Buy – Identifies an ask was removed from the order book. Sell – Identifies a bid was removed from the order book.
  },
  {
    "tradeID": 158056416,
    "price": "9186.13",
    "base_volume": "9186.13",
    "quote_volume": "0.002751",
    "trade_timestamp": 1594391746,
    "type": "sell"
  },
  {...}
}
```
___
