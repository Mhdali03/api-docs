# Naming Conventions used in API

* `NewPaymentRequest`  
  * Object that can be send via POST request to the API - requesting new payment  
* `NewPaymentResponse`  
  * Our response after receiving correct `NewPaymentRequest`  
* `PaymentReceipt`  
  * Object containing transaction status information, used both when sending payment notification and when the API is queried using GET request  
* `TransactionSpeed`
  * One of 3 values: HIGH, LOW, MEDIUM
* `PaymentStatus`
  * One of values: NEW, UNDERPAID, PAID, CONFIRMED, COMPLETED, EXPIRED, INVALID
* `RequestNotificationResponse`
  * Response after requesting a `PaymentReceipt` to be send again to you.
