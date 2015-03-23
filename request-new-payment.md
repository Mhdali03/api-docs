
# Request New Payment

API endpoint:

```
/api/v1/payments/new
```

Request type: `POST`

Content of the request:

```json
{
  "amount" : "10.00",
  "currency" : "USD",
  "notificationUrl" : "https://example.com/pay",
  "transactionSpeed" : "HIGH",
  "message" : "Order of flowers and chocolates",
  "paymentAckMessage" : "Thank you for shopping at example.com",
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

```json
{
  "transactionId" : "95bf1d853cf2e040f0ce219221f9b17206525941",
  "amount" : "0.03444190",
  "address" : "mjSk1Ny9spzU2fouzYgLqGUD8U41iR35QN",
  "label" : "Your store name",
  "message" : "Order of flowers and chocolates",
  "paymentAddress" : "bitcoin:mjSk1Ny9spzU2fouzYgLqGUD8U41iR35QN?amount=0.03444190&label=Your+store+name&message=Order+of+flowers+%26+chocolates",
  "expirationTime" : "1424273503000",
  "merchantTransactionId" : "2015-03-10/123/1"
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
| paymentAddress | A BIP21 formatted URL that is recognized by the customer's Bitcoin wallet                         |
| expirationTime | A String containing a UNIX timestamp in milliseconds format saying when the payment will expire. It can be displayed to the customer |
| merchantTransactionId | A transaction identifier on your side that was passed when requesting a payment |

This means that we are expecting the `0.03444190` of BTC to be paid to this address `mjSk1Ny9spzU2fouzYgLqGUD8U41iR35QN`

You can also use the `paymentAddress` field to make a clickable link (this should open a default Bitcoin client installed on the users's computer) or make a QR code that can be scanned using the smartphone with the Bitcoin wallet app installed.
