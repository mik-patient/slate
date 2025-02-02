---
title: Bitso API Reference

language_tabs:
  - shell: Shell
  - javascript: NodeJS
  - python: Python
  - ruby: Ruby
  - java: Java
  - php: PHP

toc_footers:
  - <a href='https://bitso.com/api_setup'>Sign Up for a Bitso Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

The Bitso API allows you to integrate the Bitso trading platform
with third party applications, such as trading applications, charting programs,
point of sale systems, and much more. Below you will find details on how the
system functions, along with examples in common programming languages.

# Changelog

### 2022-08-18
Add new error code '0506'
Added withdrawal new endpoints documentation.
Added withdrawal methodds new endpoints documentation.
Deprecated Crypto withdrawal endpoints.
Deprecated Spei withdrawal endpoints.

### 2022-03-10
Add new error codes '0414' - '0417'

### 2022-01-18
Add new error codes when trading or conversions are disabled on your account

### 2021-11-24
Documentation inconsistency between REST Api service and implementation was fixed:

* in open_orders request parameters are wrong, now request parameters are 'book' and 'currency' as defined in the service implementation


### 2021-09-27
Updated `General\Developer Testing Server` to point to the newly
configured `sandbox` environment.

### 2021-07-27
Documentation inconsistency between REST Api service and implementation was fixed:

* in open_orders and lookup_orders services documentation, now 'status' uses 'partially filled' instead of 'partial-fill' as an option.

### 2021-07-19
Documentation inconsistency between REST Api service and implementation was fixed:

* in user_trades service documentation, now 'book' is not required.

### 2021-06-25

There was an inconsistency for Diff-Orders and for Orders API. The "t" field was being described as number 0 for selling
and 1 for buying, but the correct implementation is the opposite. To make the documentation consistent with its
implementation, the field was updated to number 0 for buying and number 1 for selling.
This update was for documentation only, nothing changes in the consumer side.

### 2021-06-24

Fixed documentation inconsistency between REST Api service and the implementation:

* in order_trades service documentation the field 'make_side' have changed to 'maker_side'
* also in the same service the 'created_at' field format have been upgraded from 2021-06-11T09:25:05+0000 to 2021-06-11T09:25:05.000+00:00

### 2020-08-21

Deleted some old API errors from v2 and migrating to v3 errors for some cases:

* 20 to 0301: Unknown Order book, if book is not valid
* 22 to 0408: Incorrect amount value, it must be a non-zero positive value.
* 0410 is documented now: Trading not enabled
* 0411 is documented now: Trading not enabled for market orders
* 22 to 0302: Incorrect time frame (not ‘hour’ or 'minute’), when the time_in_force parameter is not valid
* 22 to 0407: Invalid precision, when price parameter is not present
* 22 to 0407: Invalid precision, when tick_size is not valid
* 20 to 0201: Invalid Nonce or Invalid Credentials when used_id isn't present

### 2020-06-10

Add the ability to query a withdrawal by its origin id

* `GET /v3/withdrawals?origin_ids=<origin_id>,<origin_id>,<origin_id>`

### 2020-06-09

Deprecate `/v3/<api_method>/client_id/<client_id>`.

* `GET /v3/order_trades/client_id/<client_id>`
* `GET /v3/orders/client_id/<client_id>-<client_id>-<client_id>/`
* `DELETE /v3/orders/client_id/<client_id>-<client_id>-<client_id>/`

# General

## HTTP API Responses

Our REST-like APIs will always return a JSON Object.

For successful API calls, our JSON response objects looks like:

`
{   "success": true,
    "payload": {RELEVANT_DATA_HERE}
}
`

For unsuccessful API calls, our JSON response objects look like:

`
{
    "success": false,
    "error": {"message": ERROR_MESSAGE, "code": ERROR_CODE}
}
`

## Developer Testing Server

When working on integrations, we recommend that you use our sandbox server
before running your code against production. The URL for this server
is `https://api-sandbox.bitso.com/v3/`

You can fund accounts on the dev server with Testnet Bitcoin and
"Ropsten" test network Eth.




## Notations

**Major** denotes the cryptocurrency, in our case Bitcoin (BTC) or Ether (ETH).

**Minor** denotes fiat currencies, in our case Mexican Peso (MXN)

An order book is always referred to in the API as "Major_Minor". For example: "**btc_mxn**"

## Precision

We return decimal numbers as strings to preserve full precision across platforms. We recommend you also convert your numbers to string in order to avoid undesired consequences from precision and truncation errors.


## Rate Limits

Rate limits are based on one minute windows. For public API requests, the limit is by IP address and allows 60 requests per minute. For private API requests, the limit is by user and allows 300
requests per minute. If you exceed these limits, you will get locked out for one minute. Continuous one minute lockouts may result in a 24-hour block. Order cancellations aren’t subject to API rate limiting.

## Error Codes

Error codes consist of four digits, first two digits correspond to
error categories, the last two digits define specific errors.

### Unknown Errors: 01 (HTTP 500)
* 0101: "Unknown Error"
* 0102: "Invalid Ripple Withdrawal"

### Authentication Errors: 02 (HTTP 401)
* 0201: Check your credentials
* 0202: API key is not authorized to execute the requested method
* 0203: Login token is invalid or expired
* 0204: Incorrect PIN
* 0205: Too many login attempts
* 0206: Invalid nonce type
* 0207: Invalid nonce value
* 0208: Authentication is required to execute requested operation
* 0209: Two Factor authentication required
* 0210: Device authentication required
* 0211: Device authentication failed (Invalid IP or expired Token)
* 0212: Access Denied - `This code is reserved for internal use`
* 0213: IP address is not allowed to access this resource

### Validation Errors: 03 (HTTP 400)
* 0301: Unknown Order book
* 0302: Incorrect time frame (not 'hour' or 'minute')
* 0303: Required field missing
* 0304: Required field not valid (email, phone_number)
* 0305: Invalid SMS code (would also apply to correct code but not-correct client id)
* 0306: Order side not in (buy, sell)
* 0307: Order type not in (limit, market)
* 0308: Order request cannot include both minor and major
* 0309: Order request must include either minor or major
* 0310: Incorrect WID (non-existent or does not belong to user)
* 0311: Incorrect FID (non-existent or does not belong to user)
* 0312: Incorrect OID (non-existent or does not belong to user)
* 0313: Selected currency not in (mxn, btc, eth)
* 0314: Auto-trade not available for selected currencies
* 0315: Invalid address
* 0316: Invalid Ripple currency
* 0317: Invalid SPEI number
* 0318: Invalid SPEI numeric_ref
* 0319: Invalid SPEI notes_ref
* 0320: Invalid pagination parameters
* 0321: Incorrect TID (non-existent)
* 0322: Not a Valid URL
* 0323: No associated country code
* 0324: Number already in use
* 0325: Phone already verified
* 0326: API key is not active
* 0327: Service unavailable for requesting location
* 0328: Service unavailable for requesting country
* 0329: Market order type must be in (maj, min)
* 0330: Withdrawals locked for this account
* 0331: Invalid referral code for Bitso Transfer
* 0332: Empty PIN
* 0333: PIN locked. Too many attempts
* 0334: Bitso Transfers need either an email, phone_number, or refcode specified
* 0335: Invalid SPEI recipient name
* 0336: No data found for KYC (CURP-Mexico, CUIL-Argentina, CPF-Brazil)
* 0337: No KYC found for data (CURP-Mexico, CUIL-Argentina, CPF-Brazil)
* 0338: Multiple KYCs found for data (CURP-Mexico, CUIL-Argentina, CPF-Brazil)
* 0339: Email is already in use
* 0340: Not supported country
* 0341: Invalid postal code
* 0342: Invalid CVU
* 0343: Incorrect amount
* 0344: Invalid BIND recipient name
* 0345: Quote is expired or invalid
* 0346: You need to agree to the new terms and condition
* 0347: Too many attempts to validate KYC (CURP-Mexico, CUIL-Argentina, CPF-Brazil)
* 0348: KYC data did not match with user data. (CURP-Mexico, CUIL-Argentina, CPF-Brazil)
* 0349: Already in use
* 0350: Cannot disable a currency with positive balance
* 0351: Unable to generate CVU for user
* 0352: Disposable email not allowed
* 0353: Current withdrawal fee is higher than specified maximum
* 0354: No defined legal operation entity
* 0355: Incorrect hash (non-existent or does not belong to user)
* 0356: Duplicate origin id provided
* 0357: Incorrect origin id (non-existent or does not belong to user)
* 0358: The password must have at least 8 characters
* 0359: The password is too long
* 0360: At least one field is required but none was submitted
* 0361: Two or more fields were submitted when only one is required
* 0362: Invalid callback URL
* 0363: Invalid Transaction ID (Either non-existent or does not belong to the user)
* 0364: Password doesn't meet security requirements
* 0365: Two factor authentication method is already enabled
* 0366: Two factor authentication method is not enabled
* 0367: Unable to generate quote at the present time
* 0368: Rejected withdrawal to restricted address
* 0369: Incorrect device id
* 0370: File type not allowed
* 0371: File size limit exceeded
* 0372: Incorrect value type for field
* 0373: Request has expired
* 0374: Invalid 2FA recovery code
* 0375: Reserved for internal use
* 0376: Limit of Bitso Transfer reached


### System Limit Errors: 04 (HTTP 400)
* 0401: Incorrect price, below the minimum
* 0402: Incorrect price, above than maximum
* 0403: Incorrect major, below the minimum
* 0404: Incorrect major, above the maximum
* 0405: Incorrect minor, below the minimum
* 0406: Incorrect minor, above the maximum
* 0407: Invalid precision
* 0408: Incorrect amount value, it must be a non-zero positive value.
* 0410: Trading not enabled
* 0411: Trading not enabled for market orders
* 0412: Incorrect stop, below the minimum
* 0413: Incorrect stop, above the maximum
* 0414: Internal use
* 0415: Internal use
* 0416: Internal use
* 0417: Internal use


### User Limit Error: 05 (HTTP 400)
* 0501: Exceeds user limit for withdrawals
* 0502: Exceeds user limit for deposits
* 0503: Exceeds destination user limit for deposits
* 0504: Exceeds cashout withdrawal limit for phone number
* 0506: Withdrawal limit due to funds being on hold

### Funds Error: 06 (HTTP 400)
* 0601: Not enough btc funds
* 0602: Not enough mxn funds

### Errors related to an account: 07 (HTTP 400)
* 0701: Action not permitted for unverified account
* 0702: Action not permitted for unverified business account
* 0703: For your protection, your withdrawal ability has been suspended
* 0704: A specific feature is disabled as the user has decided to opt-out
* 0705: Your account is currently suspended
* 0706: You must accept the Terms of service
* 0707: 2FA Locked
* 0708: Max attempts reached to perform operation
* 0709: Action not permitted for missing KYC data
* 0710: Password must be updated
* 0711: Verification completed. No missing requirements to fill
* 0712: Error saving user beneficiary
* 0713: Error fetching user beneficiaries
* 0714: Invalid data for user beneficiary
* 0715: Error saving users business person(s)
* 0716: Error fetching users business person(s)
* 0717: Incorrect value for users business persons(s) types
* 0718: User business person doesn't belongs to the user
* 0719: User's PGP key not found
* 0720: The maximum number of paired devices has been reached
* 0721: Error when performing a security action (i.e. block withdrawals)
* 0722: Error deleting user beneficiary
* 0723: Action restricted by country regulation
* 0724: No funding methods available
* 0725: No withdrawal methods available
* 0726: Action restricted for business accounts
* 0727: Minimum number of ubos and directors required

### Throttling Errors: 08 (HTTP 420)
* 0801: You have hit the request rate-limit
* 0802: Too many attempts to perform an operation

### Unsupported HTTP method: (400 error)
* 0901: Unsupported HTTP method

### Miscellaneous Errors: 10 (400 error)
* 1000: API temporarily disabled (More info in error message)
* 1001: Too many open orders
* 1002: Unable to process order
* 1003: Operation timeout
* 1004: Deprecated functionality
* 1005: Invalid operation
* 1006: No content for given mobile client name (204 code)
* 1007: Internal use
* 1008: Trading disabled
* 1009: Conversions disabled

### Operation Errors: 11 (500 error)
* 1101: Error when processing the withdrawal
* 1102: Error registering callback URL
* 1103: System-wide method disabled
* 1104: Multiple accounts are associated with a device
* 1105: Account creation limit has been reached on a device.
* 1106: There are no currencies available.

### Phone related errors: 12 (400 error)
* 1201: The number provided is not a mobile number
* 1202: We cannot send an SMS to the number provided (out of jurisdiction)
* 1299: Unexpected error when trying to send SMS

### OTP errors: 13 (400 error)
* 1301: OTP hash value is not valid
* 1302: Duplicated token save attempt, there is another valid OTP
* 1303: Invalid otp token

### Not found related errors: 14 (404 error)
* 1401: Requested resource with this id was not found
* 1402: Not found otp token

## Client Libraries

The following client libraries will allow you to integrate quickly with our APIs

* [Java](https://github.com/bitsoex/bitso-java)
* [Python](https://github.com/bitsoex/bitso-py)

## Language detection

The initial phase of the detection if you are using API keys is the language you have on your settings. (This defaults depending on your country of residence the moment you sign up)
To force this to a specific language we then use `Accept-Language` header on the request to override this setting.
If none of these settings are set the default is `en`.


# Public REST API


## Available Books

```shell
curl "https://api.bitso.com/v3/available_books/"
```

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
        "book": "btc_mxn",
        "minimum_amount": ".003",
        "maximum_amount": "1000.00",
        "minimum_price": "100.00",
        "maximum_price": "1000000.00",
        "minimum_value": "25.00",
        "maximum_value": "1000000.00"
    }, {
        "book": "eth_mxn",
        "minimum_amount": ".003",
        "maximum_amount": "1000.00",
        "minimum_price": "100.0",
        "maximum_price": "1000000.0",
        "minimum_value": "25.0",
        "maximum_value": "1000000.0"
    }]
}
```

This endpoint returns a list of existing exchange order books and
their respective order placement limits.

### HTTP Request

`GET https://api.bitso.com/v3/available_books/`


### JSON Response Payload

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**book** | String | Order book symbol | Major_Minor
**minimum_amount** | String | Minimum amount of major when placing orders | Major
**maximum_amount** | String | Maximum amount of major when placing orders | Major
**minimum_price** | String | Minimum price when placing orders | Minor
**maximum_price** | String | Maximum price when placing orders | Minor
**minimum_value** | String | Minimum value amount (amount*price) when placing orders | Minor
**maximum_value** | String | Maximum value amount (amount*price) when placing orders | Minor



## Ticker

```shell
curl "https://api.bitso.com/v3/ticker/?book=btc_mxn"
```

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "book": "btc_mxn",
        "volume": "22.31349615",
        "high": "5750.00",
        "last": "5633.98",
        "low": "5450.00",
        "vwap": "5393.45",
        "ask": "5632.24",
        "bid": "5520.01",
        "created_at": "2016-04-08T17:52:31.000+00:00"
    }
}
```

This endpoint returns trading information from the specified book.

### HTTP Request

`GET https://api.bitso.com/v3/ticker/`

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**book** |  | YES | Specifies which book to use

### JSON Response Payload

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**book** | String | Order book symbol | Major_Minor
**volume** | String | Last 24 hours volume | Major
**high** | String | Last 24 hours price high | Minor/Major
**last** | String | Last traded price | Minor/Major
**low** | String | Last 24 hours price low | Minor/Major
**vwap** | String | Last 24 hours volume weighted average price: [vwap](http://en.wikipedia.org/wiki/Volume-weighted_average_price) | Minor/Major
**ask** | String | Lowest sell order | Minor/Major
**bid** | String | Highest buy order | Minor/Major
**created_at** | String | Timestamp at which the ticker was generated | ISO 8601 timestamp


## Order Book

```shell
curl "https://api.bitso.com/v3/order_book/?book=btc_mxn"
```

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "asks": [{
            "book": "btc_mxn",
            "price": "5632.24",
            "amount": "1.34491802"
        },{
            "book": "btc_mxn",
            "price": "5633.44",
            "amount": "0.4259"
        },{
            "book": "btc_mxn",
            "price": "5642.14",
            "amount": "1.21642"
        }],
        "bids": [{
            "book": "btc_mxn",
            "price": "6123.55",
            "amount": "1.12560000"
        },{
            "book": "btc_mxn",
            "price": "6121.55",
            "amount": "2.23976"
        }],
        "updated_at": "2016-04-08T17:52:31.000+00:00",
        "sequence": "27214"
    }
}
```

```shell
curl "https://api.bitso.com/v3/order_book/?book=btc_mxn&aggregate=false"
```

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "asks": [{
            "book": "btc_mxn",
            "price": "5632.24",
            "amount": "1.34491802",
            "oid": "VN5lVpgXf02o6vJ6"
        },{
            "book": "btc_mxn",
            "price": "5633.44",
            "amount": "0.4259",
            "oid": "RP8lVpgXf04o6vJ6"
        },{
            "book": "btc_mxn",
            "price": "5642.14",
            "amount": "1.21642",
            "oid": "46efbiv72drbphig"
        }],
        "bids": [{
            "book": "btc_mxn",
            "price": "6123.55",
            "amount": "1.12560000",
            "oid": "11brtiv72drbphig"
        },{
            "book": "btc_mxn",
            "price": "6121.55",
            "amount": "2.23976",
            "oid": "1ywri0yg8miihs80"
        }],
        "updated_at": "2016-04-08T17:52:31.000+00:00",
        "sequence": "27214"
    }
}
```

This endpoint returns a list of all open orders in the specified
book. If the *aggregate* parameter is set to true, orders will be
aggregated by price, and the response will only include the top 50
orders for each side of the book. If the *aggregate* parameter is set
to false, the response will include the full order book.

### HTTP Request

`GET https://api.bitso.com/v3/order_book/`

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**book** |  | YES | Specifies which book to use
**aggregate** | true  | NO | Specifies if orders should be aggregated by price.


### JSON Response Payload

Returns JSON object with "bids" and "asks". Each is a JSON Array
of open orders and each open order is represented as a JSON object

Field Name | Type | Description
---------- | ---- | -----------
**asks** | JSON Array | List of open asks
**bids** | JSON Array | List of open bids
**updated_at** |  String | Timestamp at which the order was last updated | ISO 8601 timestamp
**sequence** | Long | Increasing integer value for each order book update.

**Asks** and **Bids** in the aggregated order books are JSON Dictionaries with the following fields:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**book** | String | Order book symbol | Major_Minor
**price** | String | Price per unit of major | Minor
**amount** | String | Major amount in order | Major

**Asks** and **Bids** in the unaggregated (full) order books are JSON Dictionaries with the following fields:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**book** | String | Order book symbol | Major_Minor
**price** | String | Price per unit of major | Minor
**amount** | String | Major amount in order | Major
**oid** | String | Order ID |


## Trades

```shell
curl "https://api.bitso.com/v3/trades/?book=btc_mxn"
```

> The JSON Array returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
        "book": "btc_mxn",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "amount": "0.02000000",
        "maker_side": "buy",
        "price": "5545.01",
        "tid": 55845
    }, {
        "book": "btc_mxn",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "amount": "0.33723939",
        "maker_side": "sell",
        "price": "5633.98",
        "tid": 55844
    }]
}
```

This endpoint returns a list of recent trades from the specified book.

### HTTP Request

`GET https://api.bitso.com/v3/trades/`

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**book** |   | Yes | Specifies which book to use
**marker** |  | No | Returns objects that are older or newer (depending on 'sort') than the object with this ID
**sort** | desc | No | Specifies ordering direction of returned objects ('asc', 'desc')
**limit** | 25 | No | Specifies number of objects to return. (Max is 100)


### JSON Response Payload

Returns descending JSON Array of transactions. Every element in the array is a JSON object:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**book** | String | Order book symbol | Major_Minor
**created_at** | String | Timestamp at which the trade was executed | ISO 8601 timestamp
**amount** | String | Major amount transacted | Major
**maker_side** | String | Indicates the maker order side (maker order is the order that was open on the order book) |
**price** | String | Price per unit of major | Minor
**tid** | Long | Trade ID |




# Private REST API

The Private REST API is used to manage your account and your orders. These requests must be signed (more on this below).

<aside class="notice">
Private endpoints require API Keys. Make sure you read more about obtaining your private keys <a href="#generating-api-keys">here</a>
</aside>

## Generating API Keys

Bitso uses **API Keys** to allow access to the API.
You can register a new Bitso API key at our [developer portal](https://bitso.com/api_setup).

When setting up a new API, you will need to choose an **API Name** to identify your API.
This name will never be shown anywhere apart from on your API Index page within your account.
You have the option of adding a **Withdrawal Bitcoin Address**, which can be used to lock the API Withdrawal function to a specific Bitcoin address of your choosing. This field is optional.

The two elements you will need to sign requests are:

* Bitso API Key
* Bitso API Secret

## Creating and Signing Requests
```shell
#!/bin/bash
# requires:
# -httpie: https://github.com/jkbrzt/httpie

URL=https://api.bitso.com/v3/balance/
API_KEY="BITSO_KEY"
API_SECRET="BITSO_SECRET"
DNONCE=$(date +%s)
HTTPmethod=GET
JSONPayload=""
RequestPath="/v3/balance/"
SIGNATURE=$(echo -n $DNONCE$HTTPmethod$RequestPath$JSONPayload | openssl dgst -binary -sha256 -hmac $API_SECRET | xxd -p -c 256 )
AUTH_HEADER="Bitso $API_KEY:$DNONCE:$SIGNATURE"
http GET $URL Authorization:"$AUTH_HEADER"
```


```javascript
// Define your request
var key = "BITSO API KEY";
var secret = "BITSO API SECRET";
var http_method="GET";  // Change to POST if endpoint requires data
var request_path="/v3/balance/"
var json_payload={};    // Needed for POST endpoints requiring data

// Create the signature
var nonce = new Date().getTime();
var message = nonce+http_method+request_path;
var payload = JSON.stringify(json_payload)
if (http_method == "POST")
  message += payload;
var crypto = require('crypto');
var signature = crypto.createHmac('sha256', secret).update(message).digest('hex');

// Build the auth header
var auth_header = "Bitso "+key+":" +nonce+":"+signature;

// Send request
var options = {
  host: 'api.bitso.com',
  path: request_path,
  method: http_method,
  headers: {
    'Authorization': auth_header,
    'Content-Type': 'application/json'
  }
};

// Send request
var http = require('https');
var req = http.request(options, function(res) {
  console.log(`statusCode: ${res.statusCode}`);
  console.log(`statusMessage: ${res.statusMessage}`);
  res.on('data', function (chunk) {
    console.log(`body: ${chunk}`);
  });
});
req.on('error', (error) => {
  console.error(error)
})
if (http_method == "POST") {
  req.write(payload);
}
req.end();

```

```python
#!/usr/bin/python

import time
import hmac
import hashlib
import requests
import json


bitso_key = "BITSO_KEY"
bitso_secret = "BITSO_SECRET"
http_method = "GET" # Change to POST if endpoint requires data
request_path = "/v3/balance/"
parameters = {}     # Needed for POST endpoints requiring data

# Create signature
nonce =  str(int(round(time.time() * 1000)))
message = nonce+http_method+request_path
if (http_method == "POST"):
  message += json.dumps(parameters)
signature = hmac.new(bitso_secret.encode('utf-8'),
                                            message.encode('utf-8'),
                                            hashlib.sha256).hexdigest()

# Build the auth header
auth_header = 'Bitso %s:%s:%s' % (bitso_key, nonce, signature)

# Send request
if (http_method == "GET"):
  response = requests.get("https://api.bitso.com" + request_path, headers={"Authorization": auth_header})
elif (http_method == "POST"):
  response = requests.post("https://api.bitso.com" + request_path, json = parameters, headers={"Authorization": auth_header})

print response.content

```


```ruby
#!/usr/bin/ruby
require 'date'
require 'json'
require 'openssl'
require 'typhoeus'

bitso_key = "API_KEY"
bitso_secret = "API_SECRET"
nonce = DateTime.now.strftime('%Q')
http_method = "POST"
request_path = "/v3/orders/"


payload_data = {"book"  => "btc_mxn",
                "side"  => "buy",
                "major" => ".01",
                "price" => "1000",
                "type"  => "limit"}



json_payload = payload_data.to_json

# Create signature
message = nonce+http_method+request_path+json_payload
signature = OpenSSL::HMAC.hexdigest(OpenSSL::Digest.new('sha256'), bitso_secret, message)

# Build the auth header
auth_header = "Bitso #{bitso_key}:#{nonce}:#{signature}"

# Send request
response = Typhoeus::Request.new(
  "https://api.bitso.com/v3/orders/",
   method: "POST",
   body: json_payload,
   headers: {"Authorization" => auth_header,
             "Content-Type"  => "application/json"}
).run

puts response.body
```

```java

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.math.BigInteger;
import java.net.URL;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.HttpURLConnection;

public class BitsoJavaExample {
    public static void main(String[] args) throws Exception {
    String bitsoKey = "BITSO API KEY";
    String bitsoSecret = "BITSO API SECRET";
    long nonce = System.currentTimeMillis();
    String HTTPMethod = "GET";
    String RequestPath = "/v3/balance/";
    String JSONPayload = "";

    // Create the signature
    String message = nonce + HTTPMethod + RequestPath + JSONPayload;
    String signature = "";
    byte[] secretBytes = bitsoSecret.getBytes();
    SecretKeySpec localMac = new SecretKeySpec(secretBytes, "HmacSHA256");
    Mac mac = Mac.getInstance("HmacSHA256");
    mac.init(localMac);
    byte[] arrayOfByte = mac.doFinal(message.getBytes());
    BigInteger localBigInteger = new BigInteger(1, arrayOfByte);
    signature = String.format("%0" + (arrayOfByte.length << 1) + "x", new Object[] { localBigInteger });

    String authHeader = String.format("Bitso %s:%s:%s", bitsoKey, nonce, signature);
    String url = "https://api.bitso.com/v3/balance/";
    URL obj = new URL(url);
    HttpURLConnection con = (HttpURLConnection) obj.openConnection();
    con.setRequestProperty("User-Agent", "Bitso Java Example");
    con.setRequestMethod("GET");
    con.setRequestProperty("Authorization", authHeader);

    // Send request
    int responseCode = con.getResponseCode();
    System.out.println("\nSending 'GET' request to URL : " + url);
    System.out.println("Response Code : " + responseCode);

    BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));
    String inputLine;
    StringBuffer response = new StringBuffer();

    while ((inputLine = in.readLine()) != null) {
        response.append(inputLine);
    }
    in.close();
    System.out.println(response.toString());
    }
}


```

```php
<?php
  $bitsoKey = "API_KEY";
  $bitsoSecret = "API_SECRET"
  $nonce = round(microtime(true) * 1000);
  $HTTPMethod = "POST";
  $RequestPath = "/v3/orders/";
  $JSONPayload = json_encode(['book'  => 'btc_mxn',
                              'side'  => 'buy',
                              'major' => '.01',
                              'price' => '1000',
                              'type'  => 'limit']);

  // Create signature
  $message = $nonce . $HTTPMethod . $RequestPath . $JSONPayload;
  $signature = hash_hmac('sha256', $message, $bitsoSecret);

  // Build the auth header
  $format = 'Bitso %s:%s:%s';
  $authHeader =  sprintf($format, $bitsoKey, $nonce, $signature);


  // Send request
  $ch = curl_init();
  curl_setopt($ch, CURLOPT_URL, 'https://api.bitso.com/v3/orders/');
  curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
  curl_setopt($ch, CURLOPT_POSTFIELDS, $JSONPayload);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array(
      'Authorization: ' .  $authHeader,
      'Content-Type: application/json'));
  $result = curl_exec($ch);

  echo $result;
?>
```

All REST requests should be valid JSON. You must include 3 fields as a
payload in the "Authorization" header for all Private API Endpoints in order to perform authentication:

* **key** – The API Key you generated
* **nonce** – An integer that must be unique and increasing for each API call (we recommend using a UNIX timestamp)
* **signature** – See below

### Signature

The signature is generated by creating a SHA256 HMAC using the **Bitso API Secret** on the concatenation of **nonce** + **HTTP method** + **requestPath** + **JSON payload** (no ’+’ signs in the concatenated string) and hex encode the output. The **nonce** value should be the same as the **nonce** field in the Authorization header. The **requestPath** and **JSON payload** must, of course, be exactly as the ones used in the request.

### Authorization Header

The header should be constructed, using the fields described above, in
the following form:

**Authorization: Bitso \<key>:\<nonce>:\<signature>**

## Account Status

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "client_id": "1234",
        "first_name": "Claude",
        "last_name":  "Shannon",
        "status": "active",
        "daily_limit": "5300.00",
        "monthly_limit": "32000.00",
        "daily_remaining": "3300.00",
        "monthly_remaining": "31000.00",
        "cash_deposit_allowance": "5300.00",
        "cellphone_number": "verified",
        "cellphone_number_stored":"+525555555555",
        "email_stored":"shannon@maxentro.py",
        "official_id": "submitted",
        "proof_of_residency": "submitted",
        "signed_contract": "unsubmitted",
        "origin_of_funds": "unsubmitted"
    }
}

```
This endpoint returns information concerning the user's account
status, documents uploaded, and transaction limits.

### HTTP Request

`GET https://api.bitso.com/v3/account_status/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### JSON Response Payload


Returns a JSON Array. Every element in the array is a JSON object with the following fields.


Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**client_id** | String | The user's Client ID |
**first_name** | String | The user's first name |
**last_name** | String | The user's last name |
**status** | String | Total balance | Enum of (active, inactive)
**daily_limit** | String | The user's total daily limit  | MXN
**daily_remaining** | String | Remaining amount of user's total daily limit  | MXN
**monthly_limit** | String | The user's total monthly limit  | MXN
**monthly_remaining** | String | Remaining amount of user's total monthly limit  | MXN
**cash_deposit_allowance** | String | Remaining cash allowance today | MXN
**cellphone_number** | String | Status of user's registered cellphone number | Enum of (unsubmitted, submitted, verified)
**cellphone_number_stored** | String | user's registered cellphone number |
**email_stored** | String | user's registered email |
**official_id** | String | Status of user's official ID document | Enum of (unsubmitted, submitted, verified, rejected)
**proof_of_residency** | String | Status of user's 'proof of residency' document | Enum of (unsubmitted, submitted, verified, rejected)
**signed_contract** | String | Status of user's 'signed contract' document  | Enum of (unsubmitted, submitted, verified, rejected)
**origin_of_funds** | String | Status of user's 'origin of funds' document | Enum of (unsubmitted, submitted, verified, rejected)


## Document Upload


> The JSON object returned by the API looks like this:

```json
{
    "success": true
}

```
This endpoint is used to upload KYC documents for verification. [**Coming Soon**]

### HTTP Request

`GET https://api.bitso.com/v3/kyc_documents/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**document_type** | - | Yes | Specifies what document you are uploading. Enum of (official_id, proof_of_residency, signed_contract, origin_of_fund)
**filetype** | - | Yes | Specifies filetype for this upload. Enum of (jpg, png)
**file** | - | Yes | Base64 encoded binary file. Max size is 7.5MB


## Mobile Phone Number Registration

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "phone": "5552525252"
    }
}

```
This endpoint is used to register Mobile phone number for
verification.

### HTTP Request

`POST https://api.bitso.com/v3/phone_number/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)


### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**phone_number** | - | Yes | Mobile phone number to register (10 digits)


### JSON Response Payload

Returns a JSON Object with the following fields.


Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**client_id** | String | User's client ID |
**phone** | String | Registered phone number |

## Mobile Phone Number Verification

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "phone": "5554181042"
    }
}

```
This endpoint is used to verify a registered mobile phone number

### HTTP Request

`POST https://api.bitso.com/v3/phone_verification/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)


### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**verification_code** | - | Yes | Verification code sent to registered mobile number


### JSON Response Payload

Returns a JSON Object with the following fields.


Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**client_id** | String | User's client ID |
**phone** | String | Registered phone number |



## Account Balance

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "balances": [{
            "currency": "mxn",
            "total": "100.1234",
            "locked": "25.1234",
            "available": "75.0000"
        }, {
            "currency": "btc",
            "total": "4.12345678",
            "locked": "25.00000000",
            "available": "75.12345678"
        }, {
            "currency": "eth",
            "total": "50.1234",
            "locked": "40.1234",
            "available": "10.0000"
        }]
    }
}
```

This endpoint returns information concerning the user's balances for all supported currencies.

### HTTP Request

`GET https://api.bitso.com/v3/balance/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### JSON Response Payload


Returns a JSON Array. Every element in the array is a JSON object with the following fields.


Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**currency** | String | Currency symbol |
**total** | String | Total balance | Currency
**locked** | String | Currency balance locked in open orders | Currency
**available** | String | Currency balance available for use | Currency

## Fees

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "fees": [{
            "book": "btc_mxn",
            "taker_fee_decimal": "0.0001",
            "taker_fee_percent": "0.01",
            "maker_fee_decimal": "0.0001",
            "maker_fee_percent": "0.01"
        }, {
            "book": "eth_mxn",
            "taker_fee_decimal": "0.0001",
            "taker_fee_percent": "0.01",
            "maker_fee_decimal": "0.0001",
            "maker_fee_percent": "0.01"
        }],
        "withdrawal_fees": {
            "btc": "0.001",
            "eth": "0.0025"
        }
    }
}
```

This endpoint returns information on customer fees for all available
order books, and withdrawal fees for applicable currencies.

### HTTP Request

`GET https://api.bitso.com/v3/fees/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### JSON Response Payload

Returns a JSON object with keys, **"fees"** and **"withdrawal_fees"**.

**"fees"** contains a JSON Array. Every element in the array is a JSON object with the following fields.


Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**book** | String | Order book symbol | Major_Minor
**taker_fee_decimal** | String | Customer taker trading fee as a decimal |
**taker_fee_percent** | String | Customer taker trading fee as a percentage |
**maker_fee_decimal** | String | Customer maker trading fee as a decimal |
**maker_fee_percent** | String | Customer maker trading fee as a percentage |
**fee_decimal** | String | **DEPRECATED** Customer trading fee as a decimal (same as **maker_fee_decimal**). This was the field used before we had a Maker/Taker fee schedule. |
**fee_percent** | String | **DEPRECATED** Customer trading fee as a percentage (same as **maker_fee_percent**). This was the field used before we had a Maker/Taker fee schedule. |


**"withdrawal_fees"** is an object keyed by each curency and value of
net amount withdrawal fees denominated in the corresponding currency.


## Ledger

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
        "eid": "c4ca4238a0b923820dcc509a6f75849b",
        "operation": "trade",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "balance_updates": [{
            "currency": "btc",
            "amount": "-0.25232073"
        }, {
            "currency": "mxn",
            "amount": "1013.540958479115"
        }],
        "details": {
            "tid": 51756,
            "oid": "wri0yg8miihs80ngk"
        }
    }, {
        "eid": "6512bd43d9caa6e02c990b0a82652dca",
        "operation": "fee",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "balance_updates": [{
            "currency": "mxn",
            "amount": "-10.237787459385"
        }],
        "details": {
            "tid": 51756,
            "oid": "19vaqiv72drbphig"
        }
    }, {
        "operation": "trade",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "balance_updates": [{
            "currency": "eth",
            "amount": "4.86859395"
        }, {
            "currency": "mxn",
            "amount": "-626.77"
        }],
        "details": {
            "tid": 51757,
            "oid": "19vaqiv72drbphig"
        }
    }, {
        "eid": "698d51a19d8a121ce581499d7b701668",
        "operation": "fee",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "balance_updates": [{
            "currency": "eth",
            "amount": "0.04917771"
        }],
        "details": {
            "tid": 51757,
            "oid": "19vaqiv72drbphig"
        }
    }, {
        "eid": "b59c67bf196a4758191e42f76670ceba",
        "operation": "funding",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "balance_updates": [{
            "currency": "btc",
            "amount": "0.48650929"
        }],
        "details": {
            "fid": "fc23c28a23905d8614499816c3ade455",
            "method": "btc",
            "funding_address": "18MsnATiNiKLqUHDTRKjurwMg7inCrdNEp"
        }
    }, {
        "eid": "b0baee9d279d34fa1dfd71aadb908c3f",
        "operation": "funding",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "balance_updates": [{
            "currency": "mxn",
            "amount": "300.15"
        }],
        "details": {
            "fid": "3ef729ccf0cc56079ca546d58083dc12",
            "method": "sp"
        }

    }, {
        "eid": "96e79218965eb72c92a549dd5a330112",
        "operation": "withdrawal",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "balance_updates": [{
            "currency": "mxn",
            "amount": "-200.15"
        }],
        "details": {
            "wid": "c5b8d7f0768ee91d3b33bee648318688",
            "method": "sp"
        }
    }]
}
```

Returns a list of all the user's registered operations.

### HTTP Request

`GET https://api.bitso.com/v3/ledger/`

`GET https://api.bitso.com/v3/ledger/trades/`

`GET https://api.bitso.com/v3/ledger/fees/`

`GET https://api.bitso.com/v3/ledger/fundings/`

`GET https://api.bitso.com/v3/ledger/withdrawals/`


### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)


### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**marker** |  | No | Returns objects that are older or newer (depending on 'sort') than the object with this ID
**sort** | desc | No | Specifies ordering direction of returned objects
**limit** | 25 | No | Specifies number of objects to return. (Max is 100)


### JSON Response Payload

Returns a descending JSON Array of transactions. Every element in the array is a JSON object:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**eid** | String | Entry ID | -
**operation** | String | Indicates type of operation (funding, withdrawal, trade, fee) | -
**created_at** | String | Timestamp at which the operation was recorded | ISO 8601 timestamp
**balance_updates** | JSON object | Updates to user balances for this operation (See dictionary specification below) | -
**details** | JSON object | Specific operation  details | -

### JSON Object for balance_updates

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**currency** | String | Currency for this balance update | -
**amount** | String | Amount added or subtracted to user balance | Currency


### Filter Ledger by operation type

You can specify that the ledger endpoint return only objects that are
a specific operation type.

`GET https://api.bitso.com/v3/ledger/trades/`

`GET https://api.bitso.com/v3/ledger/fundings/`

`GET https://api.bitso.com/v3/ledger/withdrawals/`

`GET https://api.bitso.com/v3/ledger/fees/`

## Withdrawals

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
        "wid": "c5b8d7f0768ee91d3b33bee648318688",
        "status": "pending",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "btc",
        "method": "Bitcoin",
        "amount": "0.48650929",
        "details": {
            "withdrawal_address": "18MsnATiNiKLqUHDTRKjurwMg7inCrdNEp",
            "tx_hash": "d4f28394693e9fb5fffcaf730c11f32d1922e5837f76ca82189d3bfe30ded433"
        }
    }, {
        "wid": "p4u8d7f0768ee91d3b33bee6483132i8",
        "status": "complete",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "mxn",
        "method": "sp",
        "amount": "2612.70",
        "details": {
            "beneficiary_name": "BERTRAND RUSSELL",
            "beneficiary_bank": "BANAMEX",
            "beneficiary_clabe": "002320700708015728",
            "numeric_reference": "99548",
            "concepto": "Por los 🌮 del viernes",
            "clave_rastreo": "BNET01001604080002076841",
            "cep": {
                "return": {
                    "cda": {
                        "cadenaOriginal": "||1|13062016|13062016|172053|40002|STP|Bitso - BERTRAND RUSSELL|40|646180115400000002|BIT140123U70|BANAMEX|BERTRAND RUSSELL|40|002320700708015728|ND|-|0.00|2612.70|00001000000401205824||",
                        "conceptoPago": "-",
                        "cuentaBeneficiario": "002320700708015728",
                        "cuentaOrdenante": "646180115400000002",
                        "fechaCaptura": "20160613",
                        "fechaOperacion": "20160613",
                        "hora": "17:08:42",
                        "iva": "0.00",
                        "monto": "2612.70",
                        "nombreBeneficiario": "BERTRAND RUSSELL",
                        "nombreInstBeneficiaria": "BANAMEX",
                        "nombreInstOrdenante": "STP",
                        "nombreOrdenante": "Bitso - Russell",
                        "referenciaNumerica": "99548",
                        "rfcCurpBeneficiario": "ND",
                        "rfcCurpOrdenante": "BIT140123U70",
                        "selloDigital": "cd7yUrnmUQ7CG6M+LX7WOZeizOpkTyMlEAunJaP2j5MAaNPZxy+vAJtgiVL73i1LNSrwK10eBb66Rh4\/RxU6AT2S03chQ\/BS1beknH5xPpGQg+wEXeANtnF2lp71lAD6QZ2O0NE4MIDvLhGGjTGklSP+2fS6joTAaV+tLbtrIp8JiR0MOX1rGPC5h+0ZHNvXQkcHJz3s68+iUAvDnQBiSu768b2C4zpHzteGEnJhU8sAdk83spiWogKALAVAuN4xfSXni7GTk9HObTTRdY+zehfWVPdE\/7uQSmMTzOKfPbQU02Jn\/5DdE3gYk6JZ5m70JsUSFBTF\/EVX8hhg0pu2iA==",
                        "serieCertificado": "",
                        "tipoOperacion": "C",
                        "tipoPago": "1"
                    },
                    "estadoConsulta": "1",
                    "url": "http:\/\/www.banxico.org.mx\/cep?i=90646&s=20150825&d=viAKjS0GVYB8qihmG9I%2B9O1VUvrR2td%2Fuo3GyVDn8vBp371tVx5ltRnk4QsWP6KP%2BQvlWjT%2BzfwWWTA3TMk4tg%3D%3D"
                }
            }
        }
    }, {
        "wid": "of40d7f0768ee91d3b33bee64831jg73",
        "status": "complete",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "mxn",
        "method": "sp",
        "amount": "500.00",
        "details": {
            "beneficiary_name": "ALFRED NORTH WHITEHEAD",
            "beneficiary_bank": "BANAMEX",
            "beneficiary_clabe": "5204165009315197",
            "numeric_reference": "30535",
            "concepto": "-",
            "clave_rastreo": "BNET01001604080002076841",
            "cep": {
                "return": {
                    "cda": {
                        "cadenaOriginal": "||1|07042016|07042016|095656|40002|STP|Bitso - Al|40|646180115400000002|BIT140123U70|BANAMEX|ALFRED NORTH WHITEHEAD|3|5204165009315197|ND|-|0.00|500.00|00001000000401205824||",
                        "conceptoPago": "-",
                        "cuentaBeneficiario": "5204165009315197",
                        "cuentaOrdenante": "646180115400000002",
                        "fechaCaptura": "20160407",
                        "fechaOperacion": "20160407",
                        "hora": "09:56:51",
                        "iva": "0.00",
                        "monto": "500.00",
                        "nombreBeneficiario": "ALFRED NORTH WHITEHEAD",
                        "nombreInstBeneficiaria": "BANAMEX",
                        "nombreInstOrdenante": "STP",
                        "nombreOrdenante": "Bitso - RUSSELL",
                        "referenciaNumerica": "30535",
                        "rfcCurpBeneficiario": "ND",
                        "rfcCurpOrdenante": "BIT140123U70",
                        "selloDigital": "GaXpeaKgkc+gc0w9XgBbRCMmKWLNdSTV5C4CNQ4DL4ZVT+1OBSqNtX\/pv2IGjI7bKjCkaNrKUdaCdFwG6SdZ0nS9KtYSx1Ewg2Irg6x4kSzeHdlzBDr6ygT+bb+weizxcXMARKkciPuSQlyltCrEwSi07yVzachKfcEN8amj2fsEzim7gSyUc3ecKA1n8DX89158fwukKTIg4ECfOLsgueKF8unwbICWHXwRaaxIAA6PVw7O6WwGXxMtMBTCdiT202c8I2SnULFqK9QVJlQ\/YDRXFI4IMMAwGQZWbbmk8gf\/J3Fixy+0lcQV35TBBrbHyFPiaHaRN95yK\/BUxPOhag==",
                        "serieCertificado": "",
                        "tipoOperacion": "C",
                        "tipoPago": "1"
                    },
                    "estadoConsulta": "1",
                    "url": "http:\/\/www.banxico.org.mx\/cep?i=90646&s=20150825&d=3AeATtn9mM9yySMqwClgSTnKIddFN7JVwo38kDBVjOBRtcYVENx1LblV%2BXOHnKEGTfp0g%2BVLM76C3ewQ0c9vpA%3D%3D"
                }
            },
            "folio_origen": "BITSO4405016499736144"
        }
    }]
}
```

Returns detailed info on a user's fund withdrawals.

### HTTP Request


`GET https://api.bitso.com/v3/withdrawals/`

`GET https://api.bitso.com/v3/withdrawals/wid/`

`GET https://api.bitso.com/v3/withdrawals?wids=<wid>,<wid>,<wid>`

`GET https://api.bitso.com/v3/withdrawals?origin_ids=<origin_id>,<origin_id>,<origin_id>`

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**wid** |   | No | Specifies which withdrawal to obtain
**wids** |   | No | Specifies which withdrawals to obtain (by wid)
**origin_ids** |   | No | Specifies which withdrawals to obtain (by origin_id)

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**limit** | 25 | No | Specifies number of objects to return. (Max is 100)
**marker** | - | No | Returns objects that are older than the object with this WID (for pagination)
**status** | - | No | Restricts the withdrawals to those of the specified status (pending, processing, complete, failed)
**method** | - | No | Restricts the withdrawals to those of the specified method (SP, BTC, ETH, ...)

### JSON Response Payload

Returns a JSON Array of open orders. Every element in the array is a JSON object:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**wid** | String | The unique withdrawal ID | -
**currency** | String | Currency of the withdrawal | -
**method** | String | Method for this withdrawal (SP, BTC, ETH, ...). | -
**amount** | String | The withdrawn amount | currency
**status** | String | The status for this withdrawal (pending, processing, complete, failed) | -
**created_at** | String | Timestamp at which the withdrawal as created |ISO 8601 timestamp
**details** | JSON object | Specific withdrawal details | -


## Fundings

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
        "fid": "c5b8d7f0768ee91d3b33bee648318688",
        "status": "pending",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "btc",
        "method": "btc",
        "amount": "0.48650929",
        "details": {
            "tx_hash": "d4f28394693e9fb5fffcaf730c11f32d1922e5837f76ca82189d3bfe30ded433"
        }
    }, {
        "fid": "p4u8d7f0768ee91d3b33bee6483132i8",
        "status": "complete",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "mxn",
        "method": "sp",
        "amount": "300.15",
        "details": {
            "sender_name": "BERTRAND RUSSELL",
            "sender_bank": "BBVA Bancomer",
            "sender_clabe": "012610001967722183",
            "receive_clabe": "646180115400467548",
            "numeric_reference": "80416",
            "concepto": "Para el 🐖",
            "clave_rastreo": "BNET01001604080002076841",
            "beneficiary_name": "ALFRED NORTH WHITEHEAD"
        }
    }]
}
```

Returns detailed info on a user's fundings.

### HTTP Request


`GET https://api.bitso.com/v3/fundings/`

`GET https://api.bitso.com/v3/fundings/fid/`

`GET https://api.bitso.com/v3/fundings/fid-fid-fid/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)


### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**limit** | 25 | No | Specifies number of objects to return. (Max is 100)
**marker** | - | No | Returns objects that are older than the object with this FID (for pagination)
**status** | - | No | Restricts the fundings to those of the specified status (pending, in_progress, complete, failed)
**method** | - | No | Restricts the fundings to those of the specified method (SP, BTC, ETH, ...)
**txids** | - | No | Allows lookups of specific Transaction IDs - comma delimited list possible. **Please note** that all other parameters will be ignored

### JSON Response Payload

Returns a JSON Array of fundings. Every element in the array is a JSON object:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**fid** | String | The unique funding ID | -
**currency** | String | Currency funded | -
**method** | String | Method for this funding (MXN, BTC, ETH). | -
**amount** | String | The funding amount | currency
**status** | String | The status for this funding (pending, complete, failed) | -
**created_at** | String | Timestamp at which the funding was received |ISO 8601 timestamp
**details** | JSON object | Specific funding details | -

## User Trades

```shell
curl "https://api.bitso.com/v3/user_trades/?book=btc_mxn"
```

> The JSON Array returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
        "book": "btc_mxn",
        "major": "-0.25232073",
        "created_at": "2021-06-11T09:25:05.000+00:00",
        "minor": "1013.540958479115",
        "fees_amount": "-10.237787459385",
        "fees_currency": "mxn",
        "price": "4057.45",
        "tid": 51756,
        "oid": "g81d3y1ywri0yg8m",
        "side": "sell",
        "maker_side": "sell"
    }, {
        "book": "eth_mxn",
        "major": "4.86859395",
        "created_at": "2021-06-11T09:25:05.000+00:00",
        "minor": "-626.77",
        "fees_amount": "-0.04917771",
        "fees_currency": "btc",
        "price": "127.45",
        "tid": 51757,
        "oid": "19vaqiv72drbphig",
        "side": "buy",
        "maker_side": "sell"
    }]
}
```

This endpoint returns a list of the user's trades.

### HTTP Request

`GET https://api.bitso.com/v3/user_trades/`

`GET https://api.bitso.com/v3/user_trades/tid/`

`GET https://api.bitso.com/v3/user_trades/tid-tid-tid/`

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**book** |   | No | Specifies which book to use
**marker** |  | No | Returns objects that are older or newer (depending on 'sort') than the object with this ID
**sort** | desc | No | Specifies ordering direction of returned objects
**limit** | 25 | No | Specifies number of objects to return. (Max is 100)


### JSON Response Payload

Returns descending JSON Array of transactions. Every element in the array is a JSON object:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**book** | String | Order book symbol | Major_Minor
**major** | String | Major amount traded | Major
**minor** | String | Minor amount traded | Minor
**price** | String | Price per unit of major | Minor
**side** | String | Indicates the user's side for this trade (buy, sell) |
**maker_side** | String | Indicates the maker's side for this trade (buy, sell), if it matches with `side` you're maker |
**fees_currency** | String | Indicates the currency in which the trade fee was charged | -
**fees_amount** | String | Indicates the amount charged as trade fee |
**tid** | Long | Trade ID |
**oid** | String | Users' Order ID |
**origin_id** | String | Users' Order 'origin_id' (if any) |
**created_at** | String | Timestamp at which the trade was executed | ISO 8601 timestamp

## Order Trades

```shell
curl "https://api.bitso.com/v3/order_trades/Jvqrschkgdkc1go3"
```

> The JSON Array returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
            "book": "btc_mxn",
            "major": "-0.25232073",
            "created_at": "2021-06-11T09:25:05.000+00:00",
            "minor": "1013.540958479115",
            "fees_amount": "-10.237787459385",
            "fees_currency": "mxn",
            "price": "4057.45",
            "tid": 51756,
            "oid": "Jvqrschkgdkc1go3",
            "origin_id": "origin_id1",
            "side": "sell",
            "maker_side": "sell"
        },
        {
            "book": "btc_mxn",
            "major": "-0.25",
            "created_at": "2021-06-11T09:25:05.000+00:00",
            "minor": "513.540958479115",
            "fees_amount": "-10.237787459385",
            "fees_currency": "mxn",
            "price": "4057.45",
            "tid": 51755,
            "oid": "Jvqrschkgdkc1go3",
            "origin_id": "origin_id1",
            "side": "sell",
            "maker_side": "sell"
        }
    ]
}
```

This endpoint returns a list of trades that were created from one of your orders.

### HTTP Request


`GET https://api.bitso.com/v3/order_trades/<oid>/`

`GET https://api.bitso.com/v3/order_trades?origin_id=<origin_id>`

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**oid** |   | No | Specifies which order to get corresponding trades for
**origin_id** |   | No | Specifies which order to get corresponding trades for (by origin_id)

### JSON Response Payload

Returns descending JSON Array of transactions. Every element in the array is a JSON object:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**book** | String | Order book symbol | Major_Minor
**major** | String | Major amount traded | Major
**minor** | String | Minr amount traded | Minor
**price** | String | Price per unit of major | Minor
**side** | String | Indicates the user's side for this trade (buy, sell) |
**maker_side** | String | Indicates the maker's side for this trade (buy, sell), if it matches with `side` you're maker | |
**fees_currency** | String | Indicates the currency in which the trade fee was charged | -
**fees_amount** | String | Indicates the amount charged as trade fee |
**tid** | Long | Trade ID |
**oid** | String | Users' Order ID |
**origin_id** | String | Users' Order 'origin_id' (if any) |
**created_at** | String | Timestamp at which the trade was executed | ISO 8601 timestamp

## Open Orders

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
        "book": "btc_mxn",
        "original_amount": "0.01000000",
        "unfilled_amount": "0.00500000",
        "original_value": "56.0",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "updated_at": "2016-04-08T17:52:51.000+00:00",
        "price": "5600.00",
        "oid": "543cr2v32a1h68443",
        "origin_id": "origin_id1",
        "side": "buy",
        "status": "partially filled",
        "type": "limit"
    }, {
        "book": "btc_mxn",
        "original_amount": "0.12680000",
        "unfilled_amount": "0.12680000",
        "original_value": "507.2",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "updated_at": "2016-04-08T17:52:41.000+00:00",
        "price": "4000.00",
        "oid": "qlbga6b600n3xta7",
        "side": "sell",
        "status": "open",
        "type": "limit"
    }, {
        "book": "btc_mxn",
        "original_amount": "1.12560000",
        "unfilled_amount": "1.12560000",
        "original_value": "6892.66788",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "updated_at": "2016-04-08T17:52:41.000+00:00",
        "price": "6123.55",
        "oid": "d71e3xy2lowndkfm",
        "side": "sell",
        "status": "open",
        "type": "limit"
    }]
}
```

Returns a list of the user's open orders.

### HTTP Request

`GET https://api.bitso.com/v3/open_orders?book=btc_mxn`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)


### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**book** | all  | no | Specifies which book to use
**currency** |  | No | Specifies which orders are going to be filtered by currency (minor or major)
**limit** | 500 | No | Limits the amount of results from the result set


### JSON Response Payload

Returns a JSON Array of open orders. Every element in the array is a JSON object:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**oid** | String | The Order ID | -
**book** | String | Order book symbol | Major_Minor
**original_amount** | String | The order's initial major currency amount | Major
**unfilled_amount** | String | The order's unfilled major currency amount | Major
**original_value** | String | The order's initial minor currency amount | Minor
**created_at** | String | Timestamp at which the trade was executed |ISO 8601 timestamp
**updated_at** | String | Timestamp at which the trade was updated (can be null) | ISO 8601 timestamp
**price** | String | The order's price | Minor
**side** | String | The order side (buy, sell) | -
**status** | String | The order's status (queued, open, partially filled) |
**type** | String | The order type (will always be 'limit' for open orders) | -


## Lookup Orders

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
        "book": "btc_mxn",
        "original_amount": "0.01000000",
        "unfilled_amount": "0.00500000",
        "original_value": "56.0",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "updated_at": "2016-04-08T17:52:51.000+00:00",
        "price": "5600.00",
        "oid": "543cr2v32a1h6844",
        "side": "buy",
        "status": "partially filled",
        "type": "limit"
    }, {
        "book": "btc_mxn",
        "original_amount": "0.12680000",
        "unfilled_amount": "0.12680000",
        "original_value": "507.2",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "updated_at": "2016-04-08T17:52:41.000+00:00",
        "price": "4000.00",
        "oid": "qlbga6b600n3xta7a",
        "side": "sell",
        "status": "open",
        "type": "limit"
    }]
}
```

Returns details for one or more of your orders. Only opened or partially filled orders will be returned by this endpoint. 
As soon as an order has been fully matched or canceled, it will not be included in the response. 
Please refer to the <a href='#order-trades'>Order Trades</a> endpoint to pull the list of trades for orders.

### HTTP Request

`GET https://api.bitso.com/v3/orders/<oid>/`

`GET https://api.bitso.com/v3/orders?oids=<oid>,<oid>,<oid>`

`GET https://api.bitso.com/v3/orders?origin_ids=<origin_id>,<origin_id>,<origin_id>`

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**oid** |   | No | Specifies which order to obtain
**oids** |   | No | Specifies which order to obtain (by order_id)
**origin_ids** |   | No | Specifies which order to obtain (by origin_id)

### JSON Response Payload

Returns a JSON Array of open orders. Every element in the array is a JSON object:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**oid** | String | The Order ID | -
**book** | String | Order book symbol | Major_Minor
**original_amount** | String | The order's initial major currency amount | Major
**unfilled_amount** | String | The order's unfilled major currency amount | Major
**original_value** | String | The order's initial minor currency amount | Minor
**created_at** | String | Timestamp at which the order was created |ISO 8601 timestamp
**updated_at** | String | Timestamp at which the order was updated (can be null) | ISO 8601 timestamp
**price** | String | The order's price | Minor
**side** | String | The order side (buy, sell) | -
**status** | String | The order's status (queued, open, partially filled, closed) | -
**type** | String | The order type (market, limit) | -
**origin_id** | String | The origin_id if any | -
**time_in_force** | String | The time in force paramater for limit orders | -
**stop** | String | The stop price for Stop orders | - | Major
**triggered_at** | String | Timestamp at which a stop order was triggered | ISO 8601 timestamp

## Cancel Order

> The string returned by the API looks like this:

```json
{
    "success": true,
    "payload":[
        "cME2F7uZKJcMKXqU",
        "FwllxXRKvcgJmyFy",
        "zhDI9iBRglW9s9Vu"
    ]
}
```

Cancels open order(s)

### HTTP Request

`DELETE https://api.bitso.com/v3/orders/<oid>/`

`DELETE https://api.bitso.com/v3/orders?oids=<oid>,<oid>,<oid>`

`DELETE https://api.bitso.com/v3/orders?origin_ids=<origin_id>,<origin_id>,<orgin_id>`

`DELETE https://api.bitso.com/v3/orders/all`

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**oid** |   | No | Specifies the order that should be cancelled
**oids** |   | No | Specifies which orders should be cancelled (by order_id)
**origin_ids** |   | No | Specifies which orders should be cancelled (by origin_id)
**all** |   | No | Cancells all the open orders of the user

### JSON Response Payload

The response is a list of Order IDs (OIDs) for the canceled orders. Orders may not be successfully cancelled if they have been filled, have been already cancelled, or the OIDs are incorrect.

## Place an Order

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "oid": "qlbga6b600n3xta7"
    }
}
```

Places a buy or sell order (both limit and market orders are available)

### HTTP Request

`POST https://api.bitso.com/v3/orders/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**book** | - | Yes | Specifies which book to use
**side** | - | Yes | The order side (buy, sell)
**type** | - | Yes | The order type (market, limit) |
**major** | - | No | The amount of major currency for this order. An order must be specified in terms of major or minor, never both.
**minor** | - | No | The amount of minor currency for this order. An order must be specified in terms of major or minor, never both.
**price** | - | No | Price per unit of major. For use only with limit orders | Minor (MXN)
**stop**  | - | No | Price per unit of major at which to stop and place order. For use only with stop orders.
**time_in_force**  | - | No | Indicates how long a limit order will remain active before it is executed or expires (goodtillcancelled, fillorkill, immediateorcancel, postonly)
**origin_id** | - | No | Client supplied unique ID. Can be used to lookup and cancel orders


### JSON Response Payload

Returns a JSON object representing the order:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**oid** | String | The Order ID | -


## Funding Destination

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "account_identifier_name": "SPEI CLABE",
        "account_identifier": "646180115400346012"             
    }
}
```

Returns account funding information for specified currencies.

### HTTP Request

`GET https://api.bitso.com/v3/funding_destination/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)


### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**fund_currency** | - | Yes | Specifies which currency to fund with.



### JSON Response Payload

Returns a JSON object representing the order:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**account_identifier_name** | String | Account identifier name to fund with the specified currency. | -
**account_identifier** | String | Identifier to where the funds can be sent to. | -

## Withdrawal taxonomy

In order to execute withdrawals is it mandatory to know under which available method, network, asset and protocol you want to operate.
Withdrawal methods endpoint returns all required metadata (taxonomy) in order to execute a successful withdrawal.

> The string returned by the API looks like this (example for BTC):

```json
{
    "success": true,
    "payload": [
        {
            "method": "btc",
            "name": "Bitcoin",
            "network_name": "Bitcoin Network",
            "network_description": "Send to an address of the Bitcoin network with a fee.",
            "required_fields": [
                "address"
            ],
            "optional_fields": [
                "origin_id",
                "client_withdrawal_id",
                "max_fee"
            ],
            "currency_configurations": [
                {
                    "currency": "btc",
                    "legal_operating_entity": {
                        "legal_operation_entity": "Bitso International",
                        "country_code": "GI"
                    },
                    "fee": {
                        "amount": "0.00002999",
                        "type": "fixed"
                    },
                    "limits": {
                        "system_min": "0.00002730",
                        "system_max": "0.01002858",
                        "tx_limit": "0.01002858"
                    },
                    "status": {
                        "type": "active",
                        "description": "Ok"
                    },
                    "asset": "btc"
                }
            ],
            "network": "btc",
            "protocol": "btc",
        }
    ]
}
```

### HTTP Request

`GET https://api.bitso.com/v3/withdrawal_methods/<currency>/`

### JSON Response Payload

Returns a JSON object representing the order:

Field Name | Type | Description
---------- | ---- | -----------
**method** | String | Main method identifier.
**network** | String | Network identifier like bitcoin network, ethereum network.
**protocol** | String | Protocol identifier allowed under network.
**currency** | String | Currency identifier.
**asset** | String | Asset identifier on which you can withdraw the selected currency.
**fee** | String | Required fee suggested to pay on a given time to perform a successful transaction.

## Withdrawals

> The string returned by the API looks like this (example for BTC):

```json
{
    "success": true,
    "payload": {
        "wid": "c5b8d7f0768ee91d3b33bee648318688",
        "status": "pending",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "btc",
        "method": "btc",
        "amount": "0.48650929",
        "details": {
            "withdrawal_address": "3EW92Ajg6sMT4hxK8ngEc7Ehrqkr9RoDt7",
            "tx_hash": null
        }
    }
}
```

The following endpoints are available for making a cryptocurrency withdrawal from the user's account:

### HTTP Request

`POST https://api.bitso.com/v3/withdrawals/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**amount** | - | Yes | The amount of the asset to withdraw from your account
**address** | - | Yes | The address to send that amount to
**max_fee** | - | No | The maximum withdrawal you are willing to pay
**destination_tag** | - | No | Destination Tag (Ripple XRP only, optional)
**origin_id** | - | No | Unique identifier to avoid double spend due race conditions
**currency** | - | Yes | The currency to withdraw using available balance. See [Withdrawal Taxonomy](#withdrawal-taxonomy)
**asset** | - | Yes | Most cases equal to withdrawal currency. On some cases like USD you can use a 1:1 paired asset like usdp or usdc. See [Withdrawal Taxonomy](#withdrawal-taxonomy)
**method** | - | Yes | See [Withdrawal Taxonomy](#withdrawal-taxonomy)
**protocol** | - | Yes | See [Withdrawal Taxonomy](#withdrawal-taxonomy) 
**network** | - | Yes | See [Withdrawal Taxonomy](#withdrawal-taxonomy)

### JSON Response Payload

Returns a JSON object representing the order:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**wid** | String | Unique Withdrawal ID | -
**status** | String | Status of the withdrawal request (pending, complete) | -
**created_at** | String | Timestamp at which the withdrawal request was created | ISO 8601 timestamp
**currency** | String | Currency specified for this withdrawal (e.g. BTC) | -
**method** | String | Method for this withdrawal (e.g. BTC). | -
**amount** | String | Amount to withdraw. | units of e.g. BTC
**details** | String | Method specific details for this withdrawal | -

## Crypto Withdrawals (Deprecated)

See [Withdrawal taxonomy](#withdrawal-taxonomy) and [Withdrawals](#withdrawals) instead.

> The string returned by the API looks like this (example for BTC):

```json
{
    "success": true,
    "payload": {
        "wid": "c5b8d7f0768ee91d3b33bee648318688",
        "status": "pending",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "btc",
        "method": "btc",
        "amount": "0.48650929",
        "details": {
            "withdrawal_address": "3EW92Ajg6sMT4hxK8ngEc7Ehrqkr9RoDt7",
            "tx_hash": null
        }
    }
}
```

The following endpoints are available for making a cryptocurrency withdrawal from the user's account:

### endpoints

Asset | Endpoint
----- | --------
**all** | `POST https://api.bitso.com/v3/crypto_withdrawal/`
**btc** | `POST https://api.bitso.com/v3/bitcoin_withdrawal/`
**eth** | `POST https://api.bitso.com/v3/ether_withdrawal/`
**xrp** | `POST https://api.bitso.com/v3/ripple_withdrawal/`
**bch** | `POST https://api.bitso.com/v3/bcash_withdrawal/`
**ltc** | `POST https://api.bitso.com/v3/litecoin_withdrawal/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**currency** | - | Yes | The currency to withdraw (only required for `crypto_withdrawal` endpoint)
**amount** | - | Yes | The amount of the asset to withdraw from your account
**address** | - | Yes | The address to send that amount to
**max_fee** | - | No | The maximum withdrawal you are willing to pay
**destination_tag** | - | No | Destination Tag (Ripple XRP only, optional)

### JSON Response Payload

Returns a JSON object representing the order:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**wid** | String | Unique Withdrawal ID | -
**status** | String | Status of the withdrawal request (pending, complete) | -
**created_at** | String | Timestamp at which the withdrawal request was created | ISO 8601 timestamp
**currency** | String | Currency specified for this withdrawal (e.g. BTC) | -
**method** | String | Method for this withdrawal (e.g. BTC). | -
**amount** | String | Amount to withdraw. | units of e.g. BTC
**details** | String | Method specific details for this withdrawal | -



## SPEI Withdrawal (Deprecated)

See [Withdrawal taxonomy](#withdrawal-taxonomy) and [Withdrawals](#withdrawals) instead.

> The string returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "wid": "p4u8d7f0768ee91d3b33bee6483132i8",
        "status": "pending",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "mxn",
        "method": "sp",
        "amount": "300.15",
        "details": {
            "sender_name": "JUAN ESCUTIA",
            "receive_clabe": "012610001967722183",
            "sender_clabe": "646180115400467548",
            "numeric_reference": "80416",
            "concepto": "Tacos del viernes",
            "clave_rastreo": null,
            "beneficiary_name": "FRANCISCO MARQUEZ"
        }
    }
}
```

Triggers a SPEI withdrawal from your account.
These withdrawals are immediate during banking hours for some banks (M-F 9:00AM - 5:00PM Mexico City Time), 24 hours for others.


### HTTP Request

`POST https://api.bitso.com/v3/spei_withdrawal/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**amount** | - | Yes | The amount of MXN to withdraw from your account
**recipient_given_names** | - | Yes | The recipient's first and middle name(s)
**recipient_family_names** | - | Yes | The recipient's last name
**clabe** | - | Yes | The [CLABE](https://en.wikipedia.org/wiki/CLABE) number where the funds will be sent to
**notes_ref** | - | No | The alpha-numeric reference number for this SPEI (max. 40 characters)
**numeric_ref** | - | No | The numeric reference for this SPEI (max. 7 digits)


### JSON Response Payload

Returns a JSON object representing the order:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**wid** | String | Unique Withdrawal ID | -
**status** | String | Status of the withdrawal request (pending, complete) | -
**created_at** | String | Timestamp at which the withdrawal request was created | ISO 8601 timestamp
**currency** | String | Currency specified for this withdrawal (MXN) | -
**method** | String | Method for this withdrawal (SPEI Transfer) | -
**amount** | String | Amount to withdraw | -
**details** | String | Method specific details for this withdrawal | -

## Bank Codes

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": [{
        "code": "01",
        "name": "Banregio"
    }, {
        "code": "02",
        "name": "BBVA"
    }]
}
```

This endpoint returns codes and bank names to be used in the Debit Card WIthdrawal and Phone Number Withdrawal endpoints

### HTTP Request

`GET https://api.bitso.com/v3/mx_bank_codes/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### JSON Response Payload


Returns a JSON Array. Every element in the array is a JSON object with the following fields.


Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**code** | String | Corresponding bank's code |
**name** | String | Corresponding bank's name |


## Debit Card Withdrawal

> The string returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "wid": "p4u8d7f0768ee91d3b33bee6483132i8",
        "status": "pending",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "mxn",
        "method": "sp",
        "amount": "300.15",
        "details": {
            "sender_name": "JUAN ESCUTIA",
            "receive_clabe": "012610001967722183",
            "sender_clabe": "646180115400467548",
            "numeric_reference": "80416",
            "concepto": "Tacos del viernes",
            "clave_rastreo": null,
            "beneficiary_name": "FRANCISCO MARQUEZ"
        }
    }
}
```

Triggers a Debit Cards withdrawal from your account.
These **withdrawals are immediate** during banking hours for some
banks (M-F 9:00AM - 5:00PM Mexico City Time), 24 hours for others.



### HTTP Request

`POST https://api.bitso.com/v3/debit_card_withdrawal/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)


### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.


Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**amount** | - | Yes | The amount of MXN to withdraw from your account
**recipient_given_names** | - | Yes | The recipient's first and middle name(s)
**recipient_family_names** | - | Yes | The recipient's last name
**card_number** | - | Yes | The debit card number where the funds will be sent to
**bank_code** | - | Yes | The bank code for this card's issuer as returned by the "Bank Codes" endpoint



### JSON Response Payload

Returns a JSON object representing the order:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**wid** | String | Unique Withdrawal ID | -
**status** | String | Status of the withdrawal request (pending, complete) | -
**created_at** | String | Timestamp at which the withdrawal request was created | ISO 8601 timestamp
**currency** | String | Currency specified for this withdrawal (MXN) | -
**method** | String | Method for this withdrawal (Debit Card Withdrawal) | -
**amount** | String | Amount to withdraw | -
**details** | String | Method specific details for this withdrawal | -


## Phone Number Withdrawal

> The string returned by the API looks like this:

```json
{
    "success": true,
    "payload": {
        "wid": "p4u8d7f0768ee91d3b33bee6483132i8",
        "status": "pending",
        "created_at": "2016-04-08T17:52:31.000+00:00",
        "currency": "mxn",
        "method": "sp",
        "amount": "300.15",
        "details": {
            "sender_name": "JUAN ESCUTIA",
            "receive_clabe": "012610001967722183",
            "sender_clabe": "646180115400467548",
            "numeric_reference": "80416",
            "concepto": "Tacos del viernes",
            "clave_rastreo": null,
            "beneficiary_name": "FRANCISCO MARQUEZ"
        }
    }
}
```

Triggers a withdrawal from your account to a phone number. (Phone
number must be registered for SPEI Transfers with their corresponding bank)
These **withdrawals are immediate** during banking hours for some
banks (M-F 9:00AM - 5:00PM Mexico City Time), 24 hours for
others.



### HTTP Request

`POST https://api.bitso.com/v3/phone_withdrawal/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)


### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**amount** | - | Yes | The amount of MXN to withdraw from your account
**recipient_given_names** | - | Yes | The recipient's first and middle name(s)
**recipient_family_names** | - | Yes | The recipient's last name
**phone_number** | - | Yes | The phone number associated with the account where the funds will be sent to
**bank_code** | - | Yes | The bank code for this card's issuer as returned by the "Bank Codes" endpoint



### JSON Response Payload

Returns a JSON object representing the order:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**wid** | String | Unique Withdrawal ID | -
**status** | String | Status of the withdrawal request (pending, complete) | -
**created_at** | String | Timestamp at which the withdrawal request was created | ISO 8601 timestamp
**currency** | String | Currency specified for this withdrawal (MXN) | -
**method** | String | Method for this withdrawal (Debit Card Withdrawal) | -
**amount** | String | Amount to withdraw | -
**details** | String | Method specific details for this withdrawal | -

# WebSocket API

## General

The **Trades channel** send a message whenever a new trade is executed in the corresponding order book.

The **Orders channel** maintains an up-to-date list of the top 20 asks and the top 20 bids, new messages are sent across the channel whenever there is a change in either top 20.

The **Diff-Orders** channel will send across any modifications to the
order book. Specifically, any state changes in existing orders
(including orders not in the top 20), and any new orders. An order
could be removed, in which case it won't have an 'a' field (amount),
or an order could have been partially filled (you can look up an
order's state via the lookup_order endpoint) which will be reflected
in the amount field. Each message contains a sequence number, which
are increasing integer values, each new message incrementing the
sequence number by one. If you see a sequence number that is more than
one value that the previous, this means a message has been dropped and
you need to update the order book to get to correct state. In theory,
you can get a copy of the full order book via REST once, and keep it
up to date by using the diff-orders channel with the following
algorithm:

### Algorithm

1. Subscribe to the diff-orders channel.
2. Queue any message that come in to this channel.
3. Get the full orderbook from the REST orderbook endpoint.
4. Playback the queued message, discarding the ones with sequence
number below or equal to the one from the REST orderbook.
5. Apply the next queued messages to your local order book data
structure.
6. Apply real-time messages to your local orderbook as they come in
   trough the stream.

An order's timestamp field is immutable. Even if the amount field is mutated, or the order removed, the timestamp field remains as it was when the order was created. Note that a timestamp is not unique. Different orders can have the same timestamp.

### How to connect

> Create a WebSocket instance:

```blab
websocket = new WebSocket('wss://ws.bitso.com');
```

> Subscribe to each channel you wish to connect to:

```blab
websocket.onopen = function() {
    websocket.send(JSON.stringify({ action: 'subscribe', book: 'btc_mxn', type: 'trades' }));
    websocket.send(JSON.stringify({ action: 'subscribe', book: 'btc_mxn', type: 'diff-orders' }));
    websocket.send(JSON.stringify({ action: 'subscribe', book: 'btc_mxn', type: 'orders' }));
};
```

> The server will acknowledge each subscription to a channel with a message. For example, a successful subscription to the 'trades' channel
will be acknowledged in the following manner:

```blab
{action: "subscribe", response: "ok", time: 1455831538045, type: "trades"}
```

> Once you've succesfully subscribed to a channel, listen for messages and handle them appropriately:

```blab
websocket.onmessage = function(message){
                var data = JSON.parse(message.data);

                if (data.type == 'trades' && data.payload) {

                }
                else if (data.type == 'diff-orders' && data.payload) {

                }
                else if (data.type == 'orders' && data.payload) {

                }
            };
```

> Keep alive messages look like this:

```blab
{"type":"ka"}
```

### Example Implementation

Use this example native javascript implementation for your reference:
[https://bitso.com/demo_ws.html](https://bitso.com/demo_ws.html)

## Trades Channel

> Messages on this channel look like this:

```json
{
  "type": "trades",
  "book": "btc_mxn",
  "payload": [
    {
      "i": 72022,
      "a": "0.0035",
      "r": "7190",
      "v": "25.16"
    }
  ]
}
```

### Client subscription message

`{ action: 'subscribe', book: 'btc_mxn', type: 'trades' }`

### Server subscription response message

`{action: "subscribe", response: "ok", time: 1455831538045, type: "trades"}`

### Server JSON message

The payload contains an array with one or more trades of the following form:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**i** | Number | A unique number identifying the transaction | -
**a** | String | Amount | Major
**r** | String | Rate | Minor
**v** | String | Value | Minor
**t** | Number | Maker side, 0 indicates buy 1, indicates sell | -
**mo** | String | Maker Order ID | -
**to** | String | Taker Order ID | -

## Diff-Orders

> Messages on this channel look like this:

```json
{
  "type": "diff-orders",
  "book": "btc_mxn",
  "sequence": 2734,
  "payload": [
    {
      "d": 1455315979682,
      "r": "7251.1",
      "t": 1,
      "a": "0.29437179",
      "v": "2134.51",
      "o": 'VM7lVpgXf04o6vJ6'
    }
  ]
}
```

### Client subscription message

`websocket.send(JSON.stringify({ action: 'subscribe', book: 'btc_mxn', type: 'diff-orders' }));`

### Server subscription response message

`{action: "subscribe", response: "ok", time: 1455831538047, type: "diff-orders"}`

### Server JSON Message

The payload contains an array with one or more orders of the following form:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**d** | Number | Unix timestamp | Milliseconds
**r** | String | Rate | Minor
**t** | Number | 0 indicates sell 1 indicates buy | -
**a** | String | Amount | Major
**v** | String | Value | Minor
**o** | String | Order ID | -

## Orders

> Messages on this channel look like this:

```json
{
  "type": "orders",
  "book": "btc_mxn",
  "payload": {
    "bids": [
      {
        "r": "7185",
        "a": "0.001343",
        "v": "9.64",
        "t": 1,
        "d": 1455315394039
      },
      {
        "r": "7183.01",
        "a": "0.007715",
        "v": "55.41",
        "t": 1,
        "d": 1455314938419
      },
      {
        "r": "7183",
        "a": "1.59667303",
        "v": "11468.9",
        "t": 1,
        "d": 1455314894615
      }
    ],
    "asks": [
      {
        "r": "7251.1",
        "a": "0.29437179",
        "v": "2134.51",
        "t": 0,
        "d": 1455315979682
      },
      {
        "r": "7251.72",
        "a": "1.32057812",
        "v": "9576.46",
        "t": 0,
        "d": 1455303931277
      }
    ]
  }
}
```

### Client Subscription message

`{ websocket.send(JSON.stringify({ action: 'subscribe', book: 'btc_mxn', type: 'orders' }));`

### Server subscription response message

`{action: "subscribe", response: "ok", time: 1455831538048, type: "orders"}`

### Server JSON message

The payload contains a JSON with two keys, one for the bids and the other for asks on the order book. Both bids and asks contain orders of the following form:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**r** | String | Rate | Minor
**a** | String | Amount | Major
**v** | String | Value | Minor
**t** | Number | 0 indicates sell 1 indicates buy | -
**d** | Number | Unix timestamp | Milliseconds

# Webhooks

## Registering URLs

Users can register a callback url that will get hit with payloads
corresponding to certain events described below. (Callback urls that take more than 5 seconds to respond will timeout)

> The JSON object returned by the API looks like this:

```json
{
    "success": true,
    "payload": "Succesfully registered URL: <callback_url>"
}
```

### HTTP Request

`POST https://api.bitso.com/v3/webhooks/`

### Authorization Header Parameters

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**key** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**signature** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)
**nonce** | - | Yes | See [Creating and Signing Requests](#creating-and-signing-requests)

### Body Parameters

Body parameters should be JSON encoded and should be exactly the same
as the JSON payload used to construct the signature.

Parameter | Default | Required | Description
--------- | ------- | -------- | -----------
**callback_url** |  | Yes | Specifies a url that will be hit on events

## Fundings

Users that register a webhook will get a POST payload to that URL
with the following fields on deposits.

> The JSON Object posted to the webhook URL looks like this

```json
{
    "event": "funding",
    "payload":

    {
        "fid": "p4u8d7f0768ee91d3b33bee6483132i8",
        "status": "complete",
        "created_at": "2016-04-08T17:52:31+00:00",
        "currency": "mxn",
        "method": "sp",
        "method_name": "Transferencia SPEI",
        "amount": "300.15",
        "details": {
            "sender_name": "HUGO HERNANDEZ MANZANO",
            "sender_bank": "BBVA Bancomer",
            "sender_clabe": "012610001967722183",
            "receive_clabe": "646180115400467548",
            "numeric_reference": "80416",
            "concepto": "Para el 🐖",
            "clave_rastreo": "BNET01001604080002076841",
            "beneficiary_name": "HUGO HERNANDEZ MANZANO"
        }
    }
}
```

### JSON  Payload

Returns a JSON object with the following fields:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**fid** | String | The unique funding ID | -
**currency** | String | Currency funded | -
**method** | String | Method for this funding (mxn, btc, eth). | -
**method_name** | String | Long name for the method | -
**amount** | String | The funding amount | currency
**status** | String | The status for this funding (pending, complete, cancelled) | -
**created_at** | String | Timestamp at which the funding was received |ISO 8601 timestamp
**details** | JSON object | Specific funding details, may vary depending on funding method | -

## Withdrawals

Users that register a webhook will get a POST payload to that URL
with the following fields on withdrawals.

> The JSON Object posted to the webhook URL looks like this

```json
{
    "event": "withdrawal",
    "payload":

    {
        "wid": "p4u8d7f0768ee91d3b33bee6483132i8",
        "status": "complete",
        "created_at": "2017-07-09T19:22:38+00:00",
        "currency": "xrp",
        "method": "rp",
        "method_name": "Ripple",
        "amount": "57",
        "details": {
            "address": "r9cZA1mLK5R5Am25ArfXFmqgNwjZgnfk59",
            "destination_tag": "64136557",
            "ripple_transaction_hash": "33EA42FC7A06F062A7B843AF4DC7C0AB00D6644DFDF4C5D354A87C035813D321"
        }
    }
}
```

### JSON  Payload

Returns a JSON object with the follwing fields:

Field Name | Type | Description | Units
---------- | ---- | ----------- | -----
**wid** | String | The unique withdrawal ID | -
**currency** | String | Currency withdrawn | -
**method** | String | Method for this withdrawal (btc, eth). | -
**method_name** | String | Long name for the method | -
**amount** | String | The withdrawal amount | currency
**status** | String | The status for this withdrawal (pending, complete, cancelled) | -
**created_at** | String | Timestamp at which the withdrawal was received |ISO 8601 timestamp
**details** | JSON object | Specific withdrawal details, may vary depending on withdrawal method | -
