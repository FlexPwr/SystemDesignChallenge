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

The reporting frontend displays the following dashboard:
![img.png](dashboard.png)
It can be accessed by the traders from their browsers.

## What is expected:
This is an open challenge, it describes the environment in which this system evolves, the different interfaces with the 
rest of the world and the functional outcome that we want to achieve. In architectural terms, this would be a black box view:
![img_1.png](black_box.png)

In other words, we don't have one solution in mind and there is no right or wrong answer, we are looking forward to a 
discussion about a solution you think is appropriate.

We are interested in how you would design such a system:
- which components would you need? How do you decide on the boundaries between these components?
- which databases or any other data management systems?
- how would these components communicate with each other?
- which technologies would you use for each component and why?
- How and where would you run such a system? Which cloud services would you use?
- How would you management updates and deployments in such a system?
- Anything else that you think is relevant

Please take some time to prepare an overview of your solution, we really like to use [miro](https://miro.com) as a digital whiteboard,
but feel free to chose any medium you prefer.


