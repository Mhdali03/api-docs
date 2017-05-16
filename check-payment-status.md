
# Check Payment Status
If you want you can manually check payment status for each transaction.

API endpoint:

```
/api/v1/payments/{id}
```
Request type: `GET`


The `{id}` parameter should be replaced by `transactionId` from the new payment response

Our response for new transaction:

```json
{
  "transactionId" : "95bf1d853cf2e040f0ce219221f9b17206525941",
  "amount" : "10.00",
  "currency" : "USD",
  "status" : "NEW",
  "expirationTime" : "1411421014977",
  "currentTime" : "1411403014977",
  "merchantTransactionId" : "2015-03-10/123/1",
  "transactionSpeed" : "LOW",
  "notificationUrl" : "https://example.com/notify",
  "message" : "payment for cookies",
  "merchantTransactionDetails" : "{products: [1,2,3]}"
}
```

Our response for an underpaid transaction:

```json
{
  "transactionId" : "95bf1d853cf2e040f0ce219221f9b17206525941",
  "amount" : "10.00",
  "currency" : "USD",
  "status" : "UNDERPAID",
  "missingAmount" : 0.01,
  "paymentTime" : "1411421013977",
  "expirationTime" : "1411421014977",
  "currentTime" : "1411403014977",
  "merchantTransactionId" : "2015-03-10/123/1",
  "transactionSpeed" : "LOW",
  "notificationUrl" : "https://example.com/notify",
  "message" : "payment for cookies",
  "merchantTransactionDetails" : "{products: [1,2,3]}"
}

Our response for fully and correctly paid transaction:

```json
{
  "transactionId" : "95bf1d853cf2e040f0ce219221f9b17206525941",
  "amount" : "10.00",
  "currency" : "USD",
  "status" : "CONFIRMED",
  "paymentTime" : "1411421013977",
  "expirationTime" : "1411421014977",
  "currentTime" : "1411403014977",
  "merchantTransactionId" : "2015-03-10/123/1",
  "transactionSpeed" : "LOW",
  "notificationUrl" : "https://example.com/notify",
  "message" : "payment for cookies",
  "merchantTransactionDetails" : "{products: [1,2,3]}"
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
| merchantTransactionDetails | A string field that can contain any additional information related to the payment, for example a JSON representation of the order |
