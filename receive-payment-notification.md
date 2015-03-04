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

### Payment Notification Security

It is possible that malicious user will try to attempt to send `POST` request to your notification endpoint saying that his payment was confirmed when in fact it wasn't paid.

To mitigate that risk it is recommended that after receiving payment notification you will query our API to confirm payment status.
