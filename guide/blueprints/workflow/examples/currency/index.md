---
title: Simple Currency Rates as Sensors
title_in_menu: Currency (HTTP)
layout: website-normal
---

The following code defines a workflow that makes a REST API call to retrieve selected currency exchange rates
and then publish them as sensors:

```
name: Get exchange rates
steps:
  - type: http
    endpoint: https://api.apilayer.com/fixer/latest?base=USD&symbols=EUR,GBP
    headers:
      apikey: ${entity.config.apilayer_private_key}
  - let map exchange_data = ${content}                  
  - set-sensor rate_USD_EUR = ${exchange_data.rates.EUR}
  - set-sensor rate_USD_GBP = ${exchange_data.rates.GBP}
```

(The above assumes that you have generated a (free) `apikey` and subscribed it to the "fixer" service,
and that the key is set in the local entity's config `apilayer_private_key`.)

You can also use workflow to add this as a sensor which runs periodically in order to refresh.
Don't set this too frequent though if you're using the free tier as you don't get a lot of requests!

```
name: Get exchange rates
steps:
  - type: apply-initializer
    blueprint:
      type: workflow-sensor
      brooklyn.config:
        sensor: rate_GBP_USD
        period: 24 hours
        steps:
          - type: http
            endpoint: https://api.apilayer.com/fixer/latest?base=GBP&symbols=USD
            headers:
              apikey: ${entity.config.apilayer_private_key}
          - let map exchange_data = ${content}                  
          - return ${exchange_data.rates.USD}
```

And you can bake it in to a blueprint:

```
services:
- type: workflow-entity
  brooklyn.config:
    start:
      steps:
      - no-op
    stop:
      steps:
      - no-op
  brooklyn.initializers:
  - type: workflow-sensor
    brooklyn.config:
      sensor: rate_GBP_USD
      period: 24 hours
      steps:
      - type: http
        endpoint: https://api.apilayer.com/fixer/latest?base=GBP&symbols=USD
        headers:
          apikey: ${entity.config.apilayer_private_key}
      - let map exchange_data = ${content}
      - return ${exchange_data.rates.USD}
```

