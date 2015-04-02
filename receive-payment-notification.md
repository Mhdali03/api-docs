# Receive Payment Notification

The `notificationUrl` parameter will determine the URL that will receive a `POST` request when given transaction is fully confirmed, the state is `CONFIRMED`. At this time it's not possible to be notified at each state change.

We will call this URL using `POST` request with following content:
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

Please note that this is exactly the same format that is used in "Check Payment Status"

For each `CONFIRMED` transaction we will attempt to call `notificationUrl` at least once. This means that you might receive duplicated notifications. In case you have missed payment notification (for example your server wasn't available) you can login to the dashboard and request a resend or use our API to request additional notification.

## Payment Notification Security

It is possible that malicious user will try to attempt to send `POST` request to your notification endpoint saying that his payment was confirmed when in fact it wasn't paid.

To mitigate that risk it is recommended that after receiving payment notification you will query our API to confirm payment status.

## Underpaid Payments

In the rare case when the client has sent to little Bitcoins we will send you a appropriate notification so you can prompt the client to send the remaining amount.  
This situation shouldn't happen under normal circumstances when the client is either scanning a QR code or clicking a application link to pay, but rather when they make a mistake when typing an amount manually.

## Revoking Payments

This section applies only to `HIGH` speed transactions.  

The `HIGH` speed transaction will go directly from `NEW` to `CONFIRMED` state when we receive a Bitcoin payment, it is possible that this payment ended up as incorrect - for example it wasn't confirmed by the Bitcoin network, it might be a user error or a attempt to perform a double spend attack.  

If we don't receive a Bitcoin payment we will change status from `CONFIRMED` to `INVALID`, we will call your `notificationUrl` endpoint and you should stop processing this transaction on your side.  

We wait for up to 1 hour for a payment to go through.  

It's worth to note that this situation is very rare and it shouldn't prevent you from accepting payments at `HIGH` speed.

Please also note that if the same situation happened for a `LOW` or `MEDIUM` speed transaction, they just wouldn't enter `CONFIRMED` state and will also be marked as `INVALID` 1 hour later.
