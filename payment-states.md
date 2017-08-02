# Payment States

Each payment transaction can exist in one of the following states:

| Payment Status | Description                                                                                             |
|----------------|---------------------------------------------------------------------------------------------------------|
| NEW            | payment requests starts in this state.                                                                  |
| PAID           | When payment is received by Bitcoinpaygate server, payment goes into this state. Payment might stay in this state for up to 60 minutes in case when transaction seed is set to `LOW` or this state might be omitted when transaction speed is `HIGH`                      |
| UNDERPAID     | We have received a transaction, but it wasn't fully paid |
| CONFIRMED      | The transaction speed preferences of a payment determines when payments are confirmed.                  |
| COMPLETED       | Payment has been credited into merchant's account                                                       |
| EXPIRED        | This happens when payment has not been received before its expiration time                              |
| INVALID        | Payment is considered invalid when it was paid but payment was not confirmed within an hour of receipt or client has paid too low amount of Bitcoins |

![State transitions](https://github.com/bitcoinpaygate/api-docs/blob/master/images/state-transitions.png)
