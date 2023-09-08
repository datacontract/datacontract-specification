# Data Contract Specification

The Data Contract Specification is an open initiative to define a common data contract format. Think of an [OpenAPI specification](https://www.openapis.org/), but for data. 

A data contract defines the terms, schema, semantics, and quality attributes for exchanging data between a data provider and their consumers.

A data consumer concludes a Data Usage Agreement with the data provider based on a specific data contract version.

A data contract comes into play when data is exchanged between different teams or organizational units, such as in a decentralized data mesh architecture. A formal data contract specification can act as the basis for automation, such as testing, monitoring, access control, and computational governance policies. A data contract can also be used as a collaboration tool for data providers and consumers to discuss requirements and make assumptions explicit.



_Note: The term "data contract" thus does not align with a "contract" in a legal sense as a mutual agreement between two parties. The term "contract" may be somewhat misleading, but it is how it is used in practice. The mutual agreement between data provider and data consumer is the "data usage agreement"._

The specification is inspired by [PayPal's Data Contract Template](https://github.com/paypal/data-contract-template/blob/main/docs/README.md) and Data Mesh Manager's [Data Contract API](https://app.datamesh-manager.com/swagger/index.html) and follows [OpenAPI](https://www.openapis.org/) and [AsyncAPI](https://www.asyncapi.com/) conventions.



Version
---

0.0.1

Example
---

You can use this example as a data contract template for your own data contracts.

```yaml
dataContractSpecification: 0.0.1
id: 640864de-83d4-4619-afba-ccea8037ed3a
info:
  title: Orders Latest NPII
  version: 1.0.0
  description: Successful customer orders in the webshop. All orders since 2020-01-01. Orders with their line items are in their current state (no history included). PII data is removed.
  status: proposed / active # TBD
  contact: # TBD
    name: Checkout Team
    url: https://demo.datamesh-manager.com/acme/teams/checkout
    email: checkout@example.com
provider: #TBD
  teamId: checkout
  teamName: Checkout
  dataProductId: orders
  dataProductName: Orders
  outputPortId: bigquery_orders_latest_npii_v1
  outputPortName: bigquery_orders_latest_npii_v1
servers: # TBD
  - url: jdbc:bigquery://https://www.googleapis.com/bigquery/v2:443;ProjectId=MyBigQueryProject;OAuthType=1;
    description: Production
terms:
  usage: >
    Data can be used for reports, analytics and machine learning use cases.
    Order may be linked and joined by other tables
  limitations: >
    Not suitable for real-time use cases.
    Data may not be used to identify individual customers.
  quota: >
    Max data processing per day: 10 TiB
  billing: # string or complex
    amount: 5000
    currency: USD
    unit: month
  noticePeriod: P3M
schema:
  type: dbt  # the specification format: dbt, bigquery, jsonschema, avro, protobuf, sql, custom
  specification:  # expressed as string or inline yaml
    version: 2 
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
          - name: order_id
            type: string
            description: Foreign key to the orders table
            tests:
              - relationships:
                to: ref('orders')
                field: order_id
          - name: sku
            type: string
            description: The purchased article number
quality:
  type: SodaCL   # data quality check format: SodaCL, montecarlo, dbt-tests, custom
  specification: # expressed as string or inline yaml
    checks for orders:
      - row_count > 0
      - duplicate_count(order_id) = 0
    checks for line_items:
      - row_count > 0
serviceLevelObjectives:
  freshness: < 60 seconds
  availability: 99.9%
  completeness: All orders since 2020-01-01T00:00:00Z
  performance: Full table scan < 60 seconds
examples:  # TBD samples, sampleData, examples
  - type: csv
    model: orders
    data: |-
      order_id,customer_id,email,order_timestamp,order_total
      1,101,user1@example.com,2023-07-01,100.50
      2,102,user2@example.com,2023-07-02,75.25
      3,103,user3@example.com,2023-07-03,50.00
      4,104,user4@example.com,2023-07-04,200.20
      5,105,user5@example.com,2023-07-05,300.75
      6,106,user6@example.com,2023-07-06,120.80
      7,107,user7@example.com,2023-07-07,50.50
      8,108,user8@example.com,2023-07-08,90.00
      9,109,user9@example.com,2023-07-09,180.60
      10,110,user10@example.com,2023-07-10,250.40
tags: # TBD move to info (like in AsyncAPI)?
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

| Field | Type                                                                | Description                                                                                                                     |
| ----- |---------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| id | string                                                              | REQUIRED. The unique identifier of the data contract.                                                                           |
| purpose | string                                                                                                                                        | The purpose describes the reason and the context on why the consumer wants to consume the data.                                 |
| status | string                                                           | The status of the data contract. Typical values are:                   `draft`, `requested`, `approved`, `rejected`, `canceled` |
| startDate | string                                                       | The start date of the data contract. May be in the future.                                                                      |
| endDate | string                                                         | The end date of the data contract. Will be set, when a data contract is canceled.                                               |

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

| Field                | Type   | Description                                                                                                                                                       |
|----------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| usage                | string | The usage describes the way the data is used, such as how often it is queried.                                                                                    |
| limitations          | string | The limitations describe the restrictions on how the data can be used, can be technical or restrictions on what the data may be used for.                         |
| billing              | string | The billing describes the pricing model for using the data product, such as whether it's free, having a monthly fee, or metered pay-per-use.                      |
| noticePeriod         | string | The period of time that must be given by either party to terminate or modify the contract. Uses ISO-8601 period format, e.g., `P3M` for a period of three months. |
| individualAgreements | string | Any additional individual agreements between the provider and the consumer.                                                                                       |


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
