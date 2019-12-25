---
description: Migration guide from MacDue's v2 to PizzaFox's v3.
---

# Migration Guide

## Getting transactions

{% api-method method="get" host="https://discoin.zws.im" path="/transactions" %}
{% api-method-summary %}
Get Transactions
{% endapi-method-summary %}

{% api-method-description %}
Retrieves unprocessed transactions. All retrieved transactions will **NOT** be automatically marked as "Processed". A PATCH request (described later) will do so.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}
"Bearer " + Your token.
{% endapi-method-parameter %}
{% endapi-method-headers %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
An array of transactions.
{% endapi-method-response-example-description %}

```javascript
[{
    "user": "155784937511976960",
    "timestamp": 1502829034,
    "source": "DTS",
    "amount": 1.2,
    "receipt": "e7eed14463d00e05eca7075bbc89aa7be640e494"
},
{
    "user": "155784937511976960",
    "timestamp": 1502829034,
    "source": "DUT",
    "amount": 1,
    "receipt": "f951dae48f7ef5670270717fe57af9eb41b889f0",
    "type": "refund"
}]
```
{% endapi-method-response-example %}

{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="http://discoin.sidetrip.xyz" path="/transaction/:receipt" %}
{% api-method-summary %}
Get Transaction
{% endapi-method-summary %}

{% api-method-description %}
Retrieves transaction info based off receipt.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="receipt" type="string" required=true %}
The receipt you want to look up.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}
Your token.
{% endapi-method-parameter %}
{% endapi-method-headers %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Transaction information.
{% endapi-method-response-example-description %}

```javascript
{
    "amountDiscoin": 120,
    "amountSource": 10,
    "amountTarget": 10,
    "processTime": 1503429255,
    "processed": true,
    "receipt": "3013edf87bf5a369621fd6065b1454ea7ba71464",
    "reversed": true,
    "source": "DUT",
    "target": "DTS",
    "timestamp": 1503428678,
    "type": "normal",
    "user": "132315148487622656"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=404 %}
{% api-method-response-example-description %}
No transaction was found.
{% endapi-method-response-example-description %}

```javascript
{
    "reason": "transaction not found",
    "status": "error"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="http://discoin.sidetrip.xyz" path="/transaction" %}
{% api-method-summary %}
Create New Transaction
{% endapi-method-summary %}

{% api-method-description %}
Request a transaction.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}
Your token.
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="amount" type="integer" required=true %}
Transaction amount in original currency.
{% endapi-method-parameter %}

{% api-method-parameter name="exchangeTo" type="integer" required=true %}
String, 3-letter currency code representing the destination currency. Case insensitive.
{% endapi-method-parameter %}

{% api-method-parameter name="user" type="string" required=true %}
ID of user who requested the transaction
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    "limitNow": 4999,
    "receipt": "dca311df716ad4a6b734e3b92f0b58d797abd98c",
    "resultAmount": 1,
    "status": "approved"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Invalid data \(amount or destination currency\)
{% endapi-method-response-example-description %}

```javascript
{
    "reason": "invalid amount",
    "status": "error"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=403 %}
{% api-method-response-example-description %}
User rejections.
{% endapi-method-response-example-description %}

{% code-tabs %}
{% code-tabs-item title="not verified" %}
```javascript
{
    "reason": "verify required",
    "status": "declined"
}
```
{% endcode-tabs-item %}

{% code-tabs-item title="exceeded daily per user limit" %}
```javascript
{
    "currency": "DUT",
    "limit": 2500,
    "reason": "per-user limit exceeded",
    "status": "declined"
}
```
{% endcode-tabs-item %}

{% code-tabs-item title="exceeded daily total limit" %}
```javascript
{
    "currency": "DUT",
    "limit": 100000,
    "reason": "total limit exceeded",
    "status": "declined"
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="http://discoin.sidetrip.xyz" path="/rates.json" %}
{% api-method-summary %}
Get Exchange Rates
{% endapi-method-summary %}

{% api-method-description %}
Get current exchange rates between currencies.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
A list of exchange rates.
{% endapi-method-response-example-description %}

```javascript
[{
	"EliteLooter": {
		"currencyCode": "ELT",
		"toDiscoin": 0.9,
		"fromDiscoin": 1
	}
}, {
	"KekBot": {
		"currencyCode": "KEK",
		"toDiscoin": 3,
		"fromDiscoin": 1
	}
}, {
	"Pollux": {
		"currencyCode": "RBN",
		"toDiscoin": 1,
		"fromDiscoin": 0.9
	}
}, {
	"Dice": {
		"currencyCode": "OAT",
		"toDiscoin": 0.1,
		"fromDiscoin": 1
	}
}, {
	"DiscordTel": {
		"currencyCode": "DTS",
		"toDiscoin": 1,
		"fromDiscoin": 0.9
	}
}]
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="http://discoin.sidetrip.xyz" path="/transaction/reverse" %}
{% api-method-summary %}
Reverse a transaction
{% endapi-method-summary %}

{% api-method-description %}
Reject a transaction sent to your bot and allow the the source bot to refund the user.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="Authorization" type="string" required=true %}
Your token.
{% endapi-method-parameter %}
{% endapi-method-headers %}

{% api-method-body-parameters %}
{% api-method-parameter name="receipt" type="string" required=true %}
The receipt of the transaction you want to reverse.
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Creates a transaction to the source bot marked as a "refund".
{% endapi-method-response-example-description %}

```javascript
{
    "refundAmount": 100,
    "status": "ok"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
You attempted to reverse a transaction that is a refund, already is reversed, or doesn't exist.
{% endapi-method-response-example-description %}

```javascript
{
    "reason": "invalid receipt",
    "status": "failed"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=403 %}
{% api-method-response-example-description %}
You attempted to reverse a transaction from another bot.
{% endapi-method-response-example-description %}

```javascript
{
    "reason": "transaction must be to your bot",
    "status": "failed"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=404 %}
{% api-method-response-example-description %}
You attempted to reverse a transaction that couldn't be found.
{% endapi-method-response-example-description %}

```javascript
{
    "reason": "transaction not found",
    "status": "failed"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}
