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
___
```json
{
    "success": false,
    "message": "ERROR MESSAGE",
    "params": []
}
```
___
###Terminology

####Pair:

`Stock` - currency that you want to buy or sell

`Money` - currency that you are using to buy or sell something

`Maker` - person who puts an order and waiting till this order will be finished

`Taker` - person who finishes existing order

`Precision` - is the number of digits to the right of the decimal point

`Bid` - buy order

`Ask` - sell order

___
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
___

###Market Activity

```
GET /api/v1/public/tickers
```
This endpoint retrieves information about recent trading activity for the market.

**Response is cached for:**
_1 second_

**Parameters:**
NONE

**Response:**
```json
{
  "success": true,
  "message": "",
  "result": [
    "BTC_USDT": {                         // Name of market pair
      "at": 1594232194,                   // Timestamp in seconds
      "ticker": {
        "bid": "9412.1",                  // Highest bid
        "ask": "9416.33",                 // Lowest ask
        "low": "9203.13",                 // Lowest price for 24h
        "high": "9469.99",                // Highest price for 24h
        "last": "9414.4",                 // Last deal price
        "vol": "27324.819448",            // Volume in stock currency
        "deal": "254587570.43407191",     // Volume in money currency
        "change": "1.53"                  // Change in percent between open and last prices
      }
    },
    {
      ...
    }
  ]
}
```
___

###Single market activity

```
GET /api/v1/public/ticker?market=ETH_BTC
```
This endpoint retrieves information about recent trading activity for the market.

**Response is cached for:**
_1 second_

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | String | **Yes** | Available market. Example: BTC_USDT

**Response:**
```json
{
  "success": true,
  "message": "",
  "result": {
    "bid": "9417.4",                 // The highest bid currently available
    "ask": "9421.64",                // The lowest ask currently available
    "open": "9267.98",               // Open price for day
    "high": "9469.99",               // Highest price for day
    "low": "9203.13",                // Lowest price for day
    "last": "9419.55",               // Latest deal price
    "volume": "27303.824558",        // Volume in stock
    "deal": "254399191.68843464",    // Volume in money
    "change": "1.63"                 // Change between open and close price
  }
}
```
___

