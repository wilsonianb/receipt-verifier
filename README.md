# Receipt Verifier
> Manages [Interledger STREAM](https://interledger.org/rfcs/0029-stream/) receipts

[![npm version](https://badge.fury.io/js/%40coil%2Freceipt-verifier.svg)](https://badge.fury.io/js/%40coil%2Freceipt-verifier)
![](https://github.com/wilsonianb/receipt-verifier/workflows/Node.js%20CI/badge.svg)

STREAM receipts allow recipients or third parties to verify received payments at the recipient's Interledger wallet.

The **Receipt Verifier**:

1. pre-shares a secret key with the receiving wallet for generating receipts, by acting as a proxy for SPSP queries to the recipient's payment pointer
2. verifies receipts

For [Web Monetization](https://github.com/interledger/rfcs/blob/master/0028-web-monetization/0028-web-monetization.md), website visitors submit receipts to the website in `monetizationprogress` events. The website backend can send receipts to the **Receipt Verifier** to confirm the payment.

### Run

```
npm install
sudo docker run -p 6379:6379 -d redis
SPSP_ENDPOINT=https://receiver-endpoint.com npm start
```

### Environment Variables

#### RECEIPT_SEED
* Type: String
* Description: Base64-encoded secret value used to generate receipt secret keys.
* Default: random seed

#### RECEIPT_TTL
* Type: [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)
* Description: The number of seconds since a stream's start time to consider a receipt valid.
* Default: 300

#### REDIS_URI
* Type: String
* Description: The URI at which to connect to Redis. Use `mock` for [in-memory Redis](https://www.npmjs.com/package/ioredis-mock) (NOT RECOMMENDED for production)
* Default: redis://127.0.0.1:6379/

#### REVSHARE_URI
* Type: String
* Description: Revshare service which is queried for each received [SPSP query](https://interledger.org/rfcs/0009-simple-payment-setup-protocol/). The response should be object with the following fields:

| Field Name     | Type   | Description              |
|----------------|--------|--------------------------|
| paymentPointer | string | [Payment pointer](https://paymentpointers.org/) to proxy the SPSP query to |
| webhookUri     | string | URI to POST new receipt amounts to for this STREAM connection |

#### SPSP_PROXY_PORT
* Type: [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)
* Description: The port that SPSP proxy will listen on.
* Default: 3001

#### VERIFIER_PORT
* Type: [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)
* Description: The port that Receipt Verifier API will listen on.
* Default: 3000

### API Documentation

#### `POST /receipts`
Verifies receipt and and returns decoded values

##### Request Body:
* Type: String
* Description: base64-encoded STREAM receipt

##### Return Value:
* Type: Object

| Field Name    | Type   | Description              |
|---------------|--------|--------------------------|
| nonce         | string | Base64-encoded receipt nonce |
| streamId      | string | Stream id |
| totalReceived | string | Total amount received in stream |
