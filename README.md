# Data Contract Specification

The Data Contract Specification is an open initiative to define a common data contract format.
It can be used on its own, or in combination with the [Data Product Specification](https://dataproduct-specification.com).

A [data contract](https://www.datamesh-manager.com/learn/what-is-a-data-contract) is a formal agreement between two parties (the data product provider and the data product consumer) to use a data product. It specifies the guarantees about a provided data set, the purpose of data usage, and costs. Data contracts have a lifecycle to allow the evolution of data products, and they can be used to automate setting of permissions or monitoring data quality via tests.

The specification is based on [PayPal's Data Contract Template](https://github.com/paypal/data-contract-template/blob/main/docs/README.md) and Data Mesh Manager's [Data Contract API](https://app.datamesh-manager.com/swagger/index.html).




Version
---

0.0.1

Example
---

You can use this example as a data contract template for your own data contracts.

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
  teamId: checkout
  teamName: Checkout
  dataProductId: orders
  dataProductName: Orders
  outputPortId: bigquery_orders_latest_pii_v1
  outputPortName: bigquery_orders_latest_pii_v1
consumer:
  teamId: marketing
  teamName: Marketing
  dataProductId: funnel_analytics
  dataProductName: Funnel Analytics
terms:
  purpose: "Funnel analysis to understand user behaviors throughout the customer journey and identify conversion problems."
  usage: "Max queries per minute: 10, Max data processing per day: 1 TiB"
  limitations:
  billing: "$500 per month"
schema:
  type: dbt  # the specification format: dbt, bigquery, jsonschema, openapi, protobuf, paypal, custom
  specification:
    version: 2          # the version of the specification
    description: The subset of the output port's data model that we agree to use
    models:
      - name: orders
        description: >
          One record per order. Includes cancelled and deleted orders.
        columns:
          - name: order_id
            type: string
            description: Primary key of the orders table
            tests:
              - unique
              - not_null
          - name: order_timestamp
            type: timestamptz
            description: The business timestamp in UTC when the order was successfully registered in the source system and the payment was successful.
            tests:
              - not_null
      - name: line_items
        description: >
          The items that are part of an order
        columns:
          - name: lines_item_id
            type: string
            description: Primary key of the lines_item_id table
          - name: order_id`
            type: string
            description: Foreign key to the orders table
tags:
  - business-critical
links:
  schema: https://catalog.example.com/search/search-queries
  catalog: https://catalog.example.com/search/search-queries
custom:
  iamRole: serviceAccount:marketing-data-consumer@example-prod-data.iam.gserviceaccount.com
```

Schema
---

### Data Contract Object

This is the root document.

| Field | Type                                                                | Description                                                                     |
| ----- |---------------------------------------------------------------------|---------------------------------------------------------------------------------|
| dataContractSpecification | string                                                              | REQUIRED. Specifies the Data Contract Specification being used.                 |
| info | [Info Object](#info-object)                                         | REQUIRED. Specifies the metadata of the data contract.                          |
| provider | [Provider Object](#provider-object)                                 | REQUIRED. Specifies the data product provider                                   |
| consumer | [Consumer Object](#consumer-object)                                 | REQUIRED. Specifies the data product consumer                                   |
| terms | [Terms Object](#terms-object)                                       | REQUIRED. Specifies the terms and conditions of the data contract.              |
| schema | [Schema Object](#schema-object)                                     | Specifies the data contract schema. The spcification supports different schemas. |
| tags | [string]                                                            | Specifies the tags of the data contract.                                        |
| links | [Links Object](#links-object)                                       | Specifies further links of the data contract.                                   |
| custom | [Custom Object](#custom-object)                                     | Specifies custom fields of the data contract.                                   |

### Info Object

Metadata and life cycle information about the data contract.

| Field | Type                                                                | Description                                                                                                                                     |
| ----- |---------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| id | string                                                              | REQUIRED. The unique identifier of the data contract.                                                                                           |
| status | string                                                           | The status of the data contract. Typical values are:                   `draft`, `requested`, `approved`, `rejected`, `canceled`                 |
| startDate | string                                                       | The start date of the data contract. May be in the future.                                                                                      |
| endDate | string                                                         | The end date of the data contract. Will be set, when a data contract is canceled.                                                               |
| noticePeriod | string                                                  | The period of time that must be given by either party to terminate or modify the contract.                                                      |
| nextReassessmentDate | string                                         | The date when the data contract will be reassessed by both parties to evaluate the frequency, value, feedback, and further ideas on data usage. |

### Provider Object

Information about the data product provider.

| Field | Type                                                                | Description                                                                                                                                    |
| ----- |---------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| teamId | string                                                              | REQUIRED. The unique identifier of the team that provides the data product.                                                                    |
| teamName | string                                                            | The name of the team that provides the data product.                                                                                |
| dataProductId | string                                                   | REQUIRED. The unique identifier of the data product.                                                                                            |
| dataProductName | string                                                 | The name of the data product.                                                                                                                   |
| outputPortId | string                                                     | REQUIRED. The unique identifier of the output port.                                                                          |
| outputPortName | string                                                   | The name of the output port.                                                                                                                     |

### Consumer Object

Information about the data product consumer.

| Field | Type                                                                | Description                                                                 |
| ----- |---------------------------------------------------------------------|-----------------------------------------------------------------------------|
| teamId | string                                                              | REQUIRED. The unique identifier of the team that consumes the data product. |
| teamName | string                                                            | The name of the team that consumes the data product.                        |
| dataProductId | string                                                   | The unique identifier of the data product where the data is processed.      |
| dataProductName | string                                                 | The name of the data product where the data is processed.                   |


### Terms Object

The terms and conditions of the data contract.

| Field | Type                                                                                                                                          | Description                                                                                                                                  |
| ----- |-----------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| purpose | string                                                                                                                                        | REQUIRED. The purpose describes the reason and the context on why the consumer wants to consume the data.                                    |
| usage | string                                                                                                                                        | The usage describes the way the data is used, such as how often it is queried.                                                               |
| limitations | string                                                                                                                                        | The limitations describe the restrictions on how the data can be used, can be technical or restrictions on what the data may be used for.    |
| billing | string                                                                                                                                        | The billing describes the pricing model for using the data product, such as whether it's free, having a monthly fee, or metered pay-per-use. |



### Schema Object

The schema of the data contract that the provider and consumer agree to use. 
Can be a subset of the schema of the output port.
As the type of the output port depends on the data platform, multiple schema specifications are supported.

A schema may define a single table, a collection of tables as a dataset, or any arbitrary structure.

Some schema types, such as `dbt`, also support defining tests and additional metadata.

It is best practice to use a schema specification that can be directly imported to and exported from the data platform for automated data contract testing.


| Field | Type                                                                                                   | Description                                                                                                                          |
| ----- |--------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| type | string                                                                                                 | REQUIRED. The type of the schema.<br> Typical values are: `dbt`, `bigquery`, `jsonschema`, `openapi`, `protobuf`, `paypal`, `custom` |
| specification | [dbt Schema Object](#dbt-schema-object) \| [BigQuery Schema Object](#bigquery-schema-object) \| string | REQUIRED. The specification of the schema. The schema specification can be encoded as a string or as native yaml.                    |


#### dbt Schema Object

Example:

```yaml
version: 2
models:
  - name: "My Table"
    description: "My description"
    config:
      materialized: table
    columns:
      - name: "My column"
        data_type: text
        description: "My description"
        tests:
          - dbt_expectations.expect_column_to_exist
          - not_null
```



### Links Object

Links can be added as key-value pairs.

Example:

```yaml
links:
  schema: https://schema.example.com/search/search-queries
  catalog: https://catalog.example.com/search/search-queries
```

### Custom Object

Custom information can be added as kay-value pairs.

Example:

```yaml
custom:
  iamRole: serviceAccount:marketing-data-consumer@example-prod-data.iam.gserviceaccount.com
```





Tooling
---
- [Data Mesh Manager](https://www.datamesh-manager.com/) supports the data contract specification and allows the user to import or export data contracts using this specification. The lifecycle, including an approval flow, of those data contracts can be managed within the tool as well.

License
---
[MIT License](LICENSE)
