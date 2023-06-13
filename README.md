# Data Contract Specification

The Data Contract Specification is an open initiative to define a common data contract format for further automation and quality testing.

The specification is based on [PayPal's Data Contract Template](https://github.com/paypal/data-contract-template/blob/main/docs/README.md) and Data Mesh Manager's [Data Contract API](https://app.datamesh-manager.com/swagger/index.html).


Version
---

0.0.1

Example
---

```yaml
dataContractSpecification: 0.0.1
info:
  id: 640864de-83d4-4619-afba-ccea8037ed3a
  status: approved
  startDate: 2023-04-01
  endDate:
  noticePeriod: P3M
  nextReassessmentDate: 2024-04-01
provider:
  teamId: 6409a881-90c9-4fbb-8c89-d629e7c45e90
  teamName: Checkout
  dataProductId: 9be77c17-cda8-4b80-b6c6-cc00062b5686
  dataProductName: Orders
  outputPortId: a2197ee5-e0e9-45f8-b111-3138b59ad350
  outputPortName: bigquery_orders_latest_pii_v1
consumer:
  teamId: 9c721368-a61f-4a0d-b729-d00e4629a425
  teamName: Marketing
  dataProductId: 20e28cca-28a8-4991-88c6-64d443cbb797
  dataProductName: Funnel Analytics
terms:
  purpose: Funnel analysis to understand user behaviors throughout the customer journey and identify conversion problems.
  usage: Max queries per minute: 10, Max data processing per day: 1 TiB
  limitations:
  costs: $500 per month
schema:
  Order:
    type: table
    properties:
      order_id:
        type: string
      order_timestamp:
        type: string
        format: date-time
      customer:
        $ref: '#/components/schemas/Customer'
      line_items:
        type: array
        items:
          $ref: '#/components/schemas/LineItems'        
serviceLevelAgreements:
  intervalOfChange: Continuous streaming
  latency: < 60 seconds
  completeness: All orders since 2020-01-01T00:00:00Z
  freshness: Near real time, max. 60 seconds delay
  availability: 99.9%
  performance: Query all orders of last 12 months < 30 seconds
  dataVolume: 5,000-10,000 orders per day expected, ~50 KiB / order
tags:
- business-critical
links:
  schema: https://catalog.example.com/search/search-queries
  catalog: https://catalog.example.com/search/search-queries
custom:
  iamRole: serviceAccount:marketing-data-consumer@example-prod-data.iam.gserviceaccount.com
```
