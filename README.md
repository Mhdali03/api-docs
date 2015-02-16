This repository contains API documentation for [BitcoinPaygate](https://bitcoinpaygate.com) payment processor.

# Index
- [Introduction](#introduction)
- [API Access](#api-access)
	- [API Keys](#api-keys)
	- [API Authentication](#api-authentication)
	- [Transaction Speed](#transaction-speed)
- [Payment States](#payment-states)
- [Request New Payment](#request-new-payment)
- [Check Payment Status](#check-payment-status)
- [Receive Payment Notification](#receive-payment-notification)
- [Request Payment Notification Resend](#request-payment-notification-resend)
- [Payment Workflows/Scenarios](#payment-workflowsscenarios)
- [Staying updated](#staying-updated)
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
| HIGH              | Payment is considered to be `CONFIRMED` immediately upon receipt of payment, that is 0 confirmations on the Bitcoin network, these payments can skip the `PAID` state |
| MEDIUM            | Payment is considered to be `CONFIRMED` after 1 block confirmation on the Bitcoin network approximately 10 minutes         |
| LOW               | Payment is considered to be `CONFIRMED` after 6 block confirmations on the Bitcoin network approximately 1 hour              |


# Payment States

Each payment transaction can exist in one of the following states:

| Payment Status | Description                                                                                             |
|----------------|---------------------------------------------------------------------------------------------------------|
| NEW            | payment requests starts in this state.                                                                  |
| PAID           | When payment is received by Bitcoinpaygate server, payment goes into this state. Payment might stay in this state for up to 60 minutes in case when transaction seed is set to `LOW` or this state might be omitted when transaction speed is `HIGH`                      |
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
	"message" : "Order of flowers and chocolates",
	"paymentAckMessage" : "Thank you for shopping at example.com"
	"merchantTransactionId" : "2015-03-10/123/1"
}

```
All parameters are required.

Explanation of the fields:

| Field Name            | Format And Meaning                                                                                                                                         |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| amount                | String that contains a formatted amount of `currency` you are requesting to receive valid examples: "1.00", "1", "99" invalid examples: "abc", 1, 1.00, 99 |
| currency              | A ISO 4217 currency code of the currency you are requesting to receive examples: "USD", "GBP", "EUR"                                                       |
| notificationUrl       | A HTTPS URL where the payment notification will be send. Notifications are sent when payment reaches `CONFIRMED` status.                                   |
| transactionSpeed      | This can be "HIGH", "MEDIUM", or "LOW". Meaning of each field is described in separate section.                                                            |
| message                  | This will be displayed in the customers' Bitcoin wallet application when requesting the payment.                                                           |
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
| message        | Says what the customer is paying for, this is coming from the `message` field in the payment request |
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
	"transactionSpeed" : "LOW"
	"notificationUrl" : "https://example.com/notify"
	"message" : "payment for cookies"
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
	"transactionSpeed" : "LOW"
	"notificationUrl" : "https://example.com/notify"
	"message" : "payment for cookies"
}
```

Explanation of the fields

| Field Name            | Format And Meaning                                                                                                                                            |
|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| transactionId         | A transaction identifier on our side, for which the payment was made                                                                                          |
| amount                | A amount of currency that was paid for this payment.                                                                                                          |
| currency              | A currency code from the payment request.                                                                                                                     |
| status                | A String with the payment status. Payment statuses are explained in the separate section.                                                                     |
| paymentTime           | A String containing a UNIX timestamp in milliseconds format when the payment was confirmed on our side. This field is optional - it can either be omitted or set to null.                               |
| expirationTime        | A String containing a UNIX timestamp in milliseconds format saying when the payment will expire. This is only valid for `NEW` payments but is always present. |
| currentTime           | A String containing a UNIX timestamp in milliseconds format with the current server time for your reference.                                                  |
| merchantTransactionId | A transaction identifier on your side that was passed when requesting a payment                                                                               |
| transactionSpeed      | Transaction speed |
| notificationUrl 		  | This URL will be called later with POST request when the payment is `CONFIRMED` |
| message               | This message should be saved by the client's wallet as a reminder for what the payment was made |

# Receive Payment Notification

The `notificationUrl` parameter will determine the URL that will receive a `POST` request when given transaction is fully confirmed, the state is `CONFIRMED`. At this time it's not possible to be notified at each state change.

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
	"transactionSpeed" : "LOW"
	"notificationUrl" : "https://example.com/notify"
	"message" : "payment for cookies"
}
```

Please note that this is exactly the same format that is used in "Check Payment Status"

For each `CONFIRMED` transaction we will attempt to call `notificationUrl` at least once. This means that you might receive duplicated notifications. In case you have missed payment notification (for example your server wasn't available) you can login to the dashboard and request a resend or use our API to request additional notification.

# Request Payment Notification Resend
If you want to request a payment notification to be resend to you, please use following endpoint:

API endpoint:

```
/api/v1/payments/notify/{id}
```
Request type: `GET`


The `{id}` parameter should be replaced by `transactionId` from the new payment response

Response after calling this endpoint can be either:

```
{
	"value": 402
}
```

This means that we are still waiting for the payment to change state into `CONFIRMED`

or

```
{
	"value": 200
}
```

This means that the payment notification has been scheduled for resending and should call your service (this usually takes few seconds)


# Payment Workflows/Scenarios

This section is TODO

# Staying updated

This documentation is deployed to Github so you can use Github's "Watch" feature to keep receiving updates on the changes.

# Testing

We provide a testing server which is a physically separate environment and it's running against Bitcoin testnet.  
For testing we recommend using either `bitcoin-qt` wallet in the testnet mode or [Bitcoin Wallet for Testnet](https://play.google.com/store/apps/details?id=de.schildbach.wallet_test&hl=en), there is only version for Android.  
You can request the testnet Bitcoins at one of many faucets:  
* http://tpfaucet.appspot.com/
* http://faucet.xeno-genesis.com/
* http://kuttler.eu/bitcoin/btc/faucet/

To see payment notifications you can use [Request Bin](http://requestb.in/) - just pass a generated URL as notification URL


# Problems

The API server will throw a `Internal Server Error` in majority of cases which doesn't indicate that the problem is on the server side.