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
