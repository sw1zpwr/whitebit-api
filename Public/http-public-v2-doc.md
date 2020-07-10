# Public HTTP API V1

## Public endpoints V1

* [Market info](#market-info)
* [Market activity](#market-activity)
* [Recent Trades](#recent-trades)
* [Fee](#fee)
* [Asset Status List](#asset-status-list)
* [Orderbook](#orderbook)
    
Base endpoint is https://whitebit.com

Example how to use: https://whitebit.com/api/v2/{endpoint}

All endpoints return time in Unix-time format.

All endpoints return either a __JSON__ object array.

For receiving responses from API calls please use http method __GET__

If endpoint required parameters you will need to send them as `query string`

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
### Market Info

```
[GET] /api/v2/public/markets
```
This endpoint retrieves all information about markets for trading.

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
    {
      "name": "SON_USD",         // Name of market pair
      "moneyPrec": "2",          // Precision of money currency
      "stock": "SON",            // Ticker of stock currency
      "money": "USD",            // Ticker of money currency
      "stockPrec": "3",          // Precision of stock currency
      "feePrec": "4",            // Precision of fee
      "minAmount": "0.001",      // Minimal amount of stock to trade
      "tradesEnabled": true,     // Is trading enabled
      "minTotal": "0.001",       // Minimal amount of money to trade
      "makerFee": "0.001",       // Default maker fee ratio
      "takerFee": "0.001"        // Default taker fee ratio
    },
    {
      ...
    }
  ]
}
```
___

### Market activity

```
[GET] /api/v2/public/ticker
```
This endpoint retrieves information about recent trading activity for the market.

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
    {
      "lastUpdateTimestamp": "2020-07-10T13:37:27.000Z",     // Timestamp of last update
      "tradingPairs": "ETH_BTC",                             // Name of market pair
      "lastPrice": "0.026014",                               // Last deal price
      "lowestAsk": "0.026027",                               // Lowest ask price
      "highestBid": "0.026001",                              // Highest bid price
      "baseVolume24h": "13445.988",                          // Volume in stock currency
      "quoteVolume24h": "350.113082102",                     // Volume in money currency
      "tradesEnabled": true                                  // Is trading enabled on exchange
    },
    {
      ...
    }
  ]
}
```
___

### Recent Trades

```
[GET] /api/v2/public/trades/{market}
```
This will return the trades that have executed recently for requested market.

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
    {
      "tradeId": 157257950,                 // A unique ID associated with the trade for the currency pair transaction Note: Unix timestamp does not qualify as trade_id.
      "price": "9371.69",                   // Price.
      "volume": "0.145642",                 // Amount.
      "time": "2020-07-09T14:13:01.000Z",   // Time.
      "isBuyerMaker": true                  // Is Maker close this order or taker
    },
    {
      ...
    }
  }
}
```
___

### Fee

```
[GET] /api/v2/public/fee
```
This endpoint retrieves trading fee.

**Response is cached for:**
_1 second_

**Parameters:**
NONE

**Response:**
```json5
{
  "success": true,
  "message": "",
  "result": {
    "makerFee": "0.1",  // Default maker fee percent number
    "takerFee": "0.1"   // Default taker fee percent number
  }
}
```
___

### Asset Status List

```
[GET] /api/v2/public/assets
```
This will return the trades that have executed recently for requested market.

**Response is cached for:**
_1 second_

**Parameters:**
NONE

**Response:**
```json5
{
  "success": true,
  "message": "",
  "result": {
    "BTC": {
      "id": "4f37bc79-f612-4a63-9a81-d37f7f9ff622",         //  Asset id
      "lastUpdateTimestamp": "2020-07-10T13:20:07.000Z",    // Timestamp of last update
      "name": "Bitcoin",                                    // Name of currency
      "canWithdraw": true,                                  // Is currency withdrawable
      "canDeposit": true,                                   // Is currency depositable
      "minWithdrawal": "0.001",                             // Minimal amount to withdraw
      "maxWithdrawal": "0",                                 // Maximum amount to withdraw
      "makerFee": "0.1",                                    // Maker fee for currency
      "takerFee": "0.1"                                     // Taker fee for currency
    },
    {...}
  }
}
```
___

### Orderbook

```
[GET] /v2/public/depth/{market}
```
This will return the current order book as two arrays (bids / asks).

**Response is cached for:**
_1 second_

**Parameters:**
NONE

**Response:**
```json5
{
  "success": true,
  "message": "",
  "result": {
      "lastUpdateTimestamp": "2020-07-09T14:49:12.000Z",     // Timestamp of last update
      "asks": [
        [
          "9431.9",                                          // Price of lowest bid
          "0.705088"                                         // Amount of lowest bid
        ],
        [
          "9433.67",                                         // Price of next bid
          "0.324509"                                         // Amount of next bid
        ],
        [...]
      ],
      "bids": [
        [
          "9427.65",                                         // Price of highest ask
          "0.547909"                                         // Amount of highest ask
        ],
        [
          "9427.3",                                          // Price of next ask
          "0.669249"                                         // Amount of next ask
        ],
      ],
      [...]
  },
}
```
___