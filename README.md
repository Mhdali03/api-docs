This repository contains API documentation for bitcoinpaygate.com payment processor.

- [Introduction](#introduction)
- [API Access](#api-access)
	- [API Keys](#api-keys)
	- [API Authentication](#api-authentication)
	- [Transaction Speed](#transaction-speed)
- [Payment States](#payment-states)
- [Request New Payment](#request-new-payment)
- [Check Payment Status](#check-payment-status)
- [Receive Payment Notification](#receive-payment-notification)
- [Payment Workflows/Scenarios](#payment-workflowsscenarios)
- [Testing](#testing)
- [Problems](#problems)

# Introduction
The BitcoinPaygate Payment Gateway API is a tool for merchants to allow clients to pay in Bitcoins while receiving fiat currencies to your bank account.

Our API uses REST and JSON as a primary way of communication.

The following actions are available through our API:

* Create new payment request
* Check payment request status
* Register callback to receive notification when the payment is completed

# API Access
API server location is given only to our clients.
The API is responding only to HTTPS requests.

## API Keys
Your keys can be generated in the merchant dashboard and **should be kept private**

## API Authentication
The API relies on HTTP Basic Authentication mechanism.

You should set the username as your API key and leave the password field blank.


## Transaction Speed

In the dashboard you are able to specify how fast you want the transactions to be processed:

| Transaction Speed | Description                                                                                                                   |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------|
| HIGH              | Payment is considered to be confirmed immediately upon receipt of payment, that is 0 confirmations on the Bitcoin p2p network |
| MEDIUM            | Payment is considered to be confirmed after 1 block confirmation on the Bitcoin p2p network approximately 10 minutes          |
| LOW               | Payment is considered to be confirmed after 1 block confirmation on the Bitcoin p2p network approximately 1 hour              |


# Payment States

Each payment transaction can exist in one of the following states:

| Payment Status | Description                                                                                             |
|----------------|---------------------------------------------------------------------------------------------------------|
| NEW            | payment requests starts in this state.                                                                  |
| PAID           | When payment is received by Bitcoinpaygate server, payment goes into this state                         |
| CONFIRMED      | The transaction speed preferences of a payment determines when payments are confirmed.                  |
| COMPLETE       | Payment has been credited into merchant's account                                                       |
| EXPIRED        | This happens when payment has not been received before its expiration time                              |
| INVALID        | Payment is considered invalid when it was paid but payment was not confirmed within an hour of receipt or client has paid too low amount of Bitcoins |

# Request New Payment

API endpoint:

```
/api/v1/payments/new
```

Request type: `POST`

Content of the request:

```
{
  "amount" : "10.00",
  "currency" : "USD",
  "notificationUrl" : "https://example.com/pay",
  "transactionSpeed" : "HIGH",
  "memo" : "Order of flowers and chocolates",
  "paymentAckMessage" : "Thank you for shopping at example.com"
  "merchantTransactionId" : "2015-03-10/123/1"
}

```

Explanation of the fields:

| Field Name            | Format And Meaning                                                                                                                                         |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| amount                | String that contains a formatted amount of `currency` you are requesting to receive valid examples: "1.00", "1", "99" invalid examples: "abc", 1, 1.00, 99 |
| currency              | A ISO 4217 currency code of the currency you are requesting to receive examples: "USD", "GBP", "EUR"                                                       |
| notificationUrl       | A HTTPS URL where the payment notification will be send. Notifications are sent when payment reaches `CONFIRMED` status.                                   |
| transactionSpeed      | This can be "HIGH", "MEDIUM", or "LOW". Meaning of each field is described in separate section.                                                            |
| memo                  | This will be displayed in the customers' Bitcoin wallet application when requesting the payment.                                                           |
| paymentAckMessage     | This will be displayed to the client in the Bitcoin wallet application when we confirm the payment - this is currently not used.                           |
| merchantTransactionId | A identifier for this payment on the merchant side. We will pass this field to you in the `POST` message when the payment is confirmed.                    |


Our response:

```
{
  "transactionId" : "95bf1d853cf2e040f0ce219221f9b17206525941",
  "amount" : "0.03444190",
  "address" : "mjSk1Ny9spzU2fouzYgLqGUD8U41iR35QN"
  "label"  : "Your store name"
  "message"  : "Order of flowers and chocolates"
  "paymentAddress" : "bitcoin:mjSk1Ny9spzU2fouzYgLqGUD8U41iR35QN?amount=0.03444190&label=Your+store+name&message=Order+of+flowers+%26+chocolates"
}
```

Explanation of the fields:

| Field Name     | Format And Meaning                                                                                |
|----------------|---------------------------------------------------------------------------------------------------|
| transactionId  | A transaction identifier on our side.                                                             |
| amount         | A amount of Bitcoin we are expecting to receive, this is a String-formatted number                |
| address        | A Bitcoin address where the payment should be sent                                                |
| label          | Identifier of the merchant (this is configurable in the dashboard)                                |
| message        | Says what the customer is paying for, this is coming from the `memo` field in the payment request |
| paymentAddress | A BIP21 formatted URL that is recognized by the customer's Bitcoin wallet                         |                                                                                     |

This means that we are expecting the `0.03444190` of BTC to be paid to this address `mjSk1Ny9spzU2fouzYgLqGUD8U41iR35QN`

You can also use the `paymentAddress` field to make a clickable link (this should open a default Bitcoin client installed on the users's computer) or make a QR code that can be scanned using the smartphone with the Bitcoin wallet app installed.


# Check Payment Status
If you want you can manually check payment status for each transaction.

API endpoint:

```
/api/v1/payments/{id}
```
Request type: `GET`


The `{id}` parameter should be replaced by `transactionId` from the new payment response

Our response for new transaction:

```
{
  "transactionId" : "95bf1d853cf2e040f0ce219221f9b17206525941",
  "amount" : "10.00",
  "currency" : "USD",
  "status" : "NEW",
  "expirationTime" : "1411421014977",
  "currentTime" : "1411403014977"
  "merchantTransactionId" : "2015-03-10/123/1"
}
```

Our response for fully and correctly paid transaction:

```
{
  "transactionId" : "95bf1d853cf2e040f0ce219221f9b17206525941",
  "amount" : "10.00",
  "currency" : "USD",
  "status" : "CONFIRMED",
  "paymentTime" : "1411421013977",
  "expirationTime" : "1411421014977",
  "currentTime" : "1411403014977"
  "merchantTransactionId" : "2015-03-10/123/1"
}
```

Explanation of the fields

| Field Name            | Format And Meaning                                                                                                                                            |
|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| transactionId         | A transaction identifier on our side, for which the payment was made                                                                                          |
| amount                | A amount of currency that was paid for this payment.                                                                                                          |
| currency              | A currency code from the payment request.                                                                                                                     |
| status                | A String with the payment status. Payment statuses are explained in the separate section.                                                                     |
| paymentTime           | A String containing a UNIX timestamp in milliseconds format when the payment was confirmed on our side. This field is optional.                               |
| expirationTime        | A String containing a UNIX timestamp in milliseconds format saying when the payment will expire. This is only valid for `NEW` payments but is always present. |
| currentTime           | A String containing a UNIX timestamp in milliseconds format with the current server time for your reference.                                                  |
| merchantTransactionId | A transaction identifier on your side that was passed when requesting a payment                                                                               |

# Receive Payment Notification

The `notificationUrl` parameter will determine the URL that will receive a `POST` request when given transaction is fully confirmed

We will call this URL using `POST` request with following content:
```
{
  "transactionId" : "95bf1d853cf2e040f0ce219221f9b17206525941",
  "amount" : "10.00",
  "currency" : "USD",
  "status" : "CONFIRMED",
  "paymentTime" : "1411421013977",
  "expirationTime" : "1411421014977",
  "currentTime" : "1411403014977"
  "merchantTransactionId" : "2015-03-10/123/1"
}
```

Please note that this is exactly the same format that is used in "Check Payment Status"

We will attempt to call this URL only once, so in case you have missed payment notification you can login to the dashboard and request a resend.

# Payment Workflows/Scenarios
This section is TODO

# Testing

We provide a testing server which is a physically separate environment and it's running against Bitcoin testnet.

# Problems

The API server will throw a `Internal Server Error` in majority of cases which doesn't indicate that the problem is on the server side.
