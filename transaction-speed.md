# Transaction Speed

In the dashboard you are able to specify how fast you want the transactions to be processed:

| Transaction Speed | Description                                                                                                                   |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------|
| HIGH              | Payment is considered to be `CONFIRMED` immediately upon receipt of payment, that is 0 confirmations on the Bitcoin network, these payments can skip the `PAID` state |
| MEDIUM            | Payment is considered to be `CONFIRMED` after 1 block confirmation on the Bitcoin network approximately 10 minutes         |
| LOW               | Payment is considered to be `CONFIRMED` after 6 block confirmations on the Bitcoin network approximately 1 hour              |
