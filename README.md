# Data Contract Specification

The Data Contract Specification is an open initiative to define a common data contract format.


Version
---

0.0.1

Example
---

Option Flat:

```yaml
dataContractSpecification: 0.0.1
id: 640864de-83d4-4619-afba-ccea8037ed3a
providerTeamId: 6409a881-90c9-4fbb-8c89-d629e7c45e90
providerDataProductId: snowflake_orders_v2
providerOutputPortId: snowflake_search_queries_all_npii_v1
consumerDataProductId: user_profiles
consumerTeamId: 9c721368-a61f-4a0d-b729-d00e4629a425
status: active
purpose: Use orders to calculate payment amount
terms: The consumer calls the data product max 10 times per day.
startDate: 2023-01-01
endDate: 2023-01-01
noticePeriod: P3M
nextReassessmentDate: 2023-01-01
tags:
- business-critical
links:
  schema: https://catalog.example.com/search/search-queries
  catalog: https://catalog.example.com/search/search-queries
  name: link
custom:
  field: value
```

Option Structured OpenAPI like:
```yaml
dataContractSpecification: 0.0.1
info:
  id: 640864de-83d4-4619-afba-ccea8037ed3a
  status: active
provider:
  ownerId: 6409a881-90c9-4fbb-8c89-d629e7c45e90
  ownerName: Checkout
  dataProductId: snowflake_orders_v2
  dataProductName: snowflake_orders_v2
  outputPortId: snowflake_search_queries_all_npii_v1
  outputPortName: snowflake_search_queries_all_npii_v1
consumer:
  dataProductId: user_profiles
  teamId: 9c721368-a61f-4a0d-b729-d00e4629a425
terms:
  purpose: Use orders to calculate payment amount
  usage: The consumer calls the data product max 10 times per day.
  limitations:
  costs: free
  startDate: 2023-01-01
  endDate: 2023-01-01
  noticePeriod: P3M
  nextReassessmentDate: 2023-01-01
schema:
  Order:
    type: table
    properties:
      name:
        type: string
        dataQuality: 
      address:
        $ref: '#/components/schemas/Address'
      age:
        type: integer
        format: int32
        minimum: 0  
serviceLevelAgreements:
  availability: 
  latency: 
tags:
- business-critical
links:
  schema: https://catalog.example.com/search/search-queries
  catalog: https://catalog.example.com/search/search-queries
  name: link
custom:
  field: value
```

