# System Design Challenge: A reporting system

## Goal
The goal of the challenge is to design a system that allows the trading team to visualise reports and metrics about their 
trading activities on the power exchanges, so that they can visualise their performance and adjust their strategies.

## Deals
Deals are pushed to us from the exchange through a websocket connection. 

As soon as one of our traders, for example trader_1, performs a deal, we get a confirmation from the exchange formatted in json.
Assume she buys 12.3 megawatt within the hour 8 to 9 for 20.5 euros/megawatt-hour, then we get the following message:
```json
{
  "deal_confirmation": 
    {
      "id": "123",
      "price": 20.5,
      "quantity": 12.3,
      "direction": "buy",
      "delivery_day": "2023-02-06",
      "delivery_hour": "08-09",
      "trader_id": "trader_1",
      "execution_time": "2023-02-06T06:13:45Z"
    }
}
```

It is essential that we maintain an open connection to the exchange at all times so that we don't miss any deals).


## Reference Price

We have a third party data provider for all types of market data. 

For the purposes of this task, we are interested only in the so called **reference price**, which is the average price for a megawatthour sold on the exchange for a given hour.

For example, if for the hour 12-13 on the 2023-02-06 at 2023-02-06T11:00:00Z, 354 deals took place on the exchange, the reference price would be
```python
sum(quantity * price for quantity, price in deals) / sum(quantity for quantity in deals)
```
If no deals happened yet for a given hour, the value is undefined.

To access the data, the provider offers an API, with the following entrypoint:

```yaml
swagger: "2.0"
info:
  title: Market Data API
  version: 1.0.0
host: market-data-provider.energy
basePath: /v1
schemes:
  - https
paths:
  /reference-price:
    get:
      summary: Reference price.
      parameters:
        - in: query
          name: delivery_day
          required: true
          type: string
          example: "2023-02-06"
        - in: query
          name: delivery_hour
          required: true
          type: string
          example: "12-13"
      produces:
        - application/json
      responses:
        200:
          description: A data object.
          schema:
            type: object
            properties:
              delivery_day:
                type: string
                example: "2023-02-06"
              delivery_hour:
                type: string
                example: "12-13"
              value:
                type: float
                example: 100.0
              unit:
                type: string
                example: euro
              last_update:
                type: string
                example: "2023-02-06T09:15:45Z"
```

## Reporting:

The reporting frontend contains dashboards that display the following dashboards:
- total bought quantity
- total sold quantity
- PnL
- average realised price for a megawatt hour
- green or red light icon, green if the trader's average realised price is higher than the reference price, red otherwise.

It can be accessed by the traders from their browsers.
