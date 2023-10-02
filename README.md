# Data Contract Specification

![datacontract.png](images/datacontract.png)

Data contracts bring data providers and data consumers together.

A _data contract_ is a document that defines the structure, format, semantics, quality, and terms of use for exchanging data between a data provider and their consumers. A data contract is implemented by a data product's output port or other data technologies. Data contracts can also be used for the input port to specify the expectations of data dependencies and verify given guarantees.

The _data contract specification_ defines a YAML format to describe attributes of provided data sets. It is data platform neutral, yet supports well-known formats to express schemas (e.g., dbt models, JSON Schema, Protobuf, SQL DDL) and quality tests (e.g., SodaCL, SQL queries) to avoid unnecessary abstractions. The data contract specification is an open initiative to define a common data contract format. Think of an [OpenAPI specification](https://www.openapis.org/), but for data sets.

Data contracts come into play when data is exchanged between different teams or organizational units, such as in a [data mesh architecture](https://www.datamesh-architecture.com/). 
First, and foremost, data contracts are a communication tool to express a common understanding of how data should be structured and interpreted. They make semantic and quality expectations explicit. They are often created in [workshops](/workshop). Later in development and production, they also serve as the basis for code generation, testing, schema validations, quality checks, monitoring, access control, and computational governance policies.

_Note: The term "data contract" refers to a specification that is usually owned by the data provider and thus does not align with a "contract" in a legal sense as a mutual agreement between two parties. The term "contract" may be somewhat misleading, but it is how it is used in practice. The mutual agreement between one data provider and one data consumer is the "data usage agreement" that refers to a data contract. Data usage agreements have a defined lifecycle, start/end date, and help the data provider to track who accesses their data and for which purposes._

The specification is inspired by [AIDA User Group's Open Data Contract Standard](https://github.com/AIDAUserGroup/open-data-contract-standard), (formerly [PayPal's Data Contract Template](https://github.com/paypal/data-contract-template/blob/main/docs/README.md)) and Data Mesh Manager's [Data Contract API](https://www.datamesh-manager.com). 
It follows [OpenAPI](https://www.openapis.org/) and [AsyncAPI](https://www.asyncapi.com/) conventions.

Version
---

0.9.0

Example
---

[![Open in Data Contract Studio](https://img.shields.io/badge/open%20in-Data%20Contract%20Studio-blue)](https://studio.datacontract.com/)

```yaml
dataContractSpecification: 0.9.0
id: urn:datacontract:checkout:orders-latest-npii
info:
  title: Orders Latest NPII
  version: 1.0.0
  description: Successful customer orders in the webshop. All orders since 2020-01-01. Orders with their line items are in their current state (no history included). PII data is removed.
  owner: Checkout Team
  dataProduct: urn:dataproduct:checkout:orders
  outputPort: urn:outputport:checkout:orders:bigquery_orders_latest_npii_v1
  contact:
    name: John Doe (Data Product Owner)
    email: john.doe@example.com
servers:
  production:
    type: BigQuery
    project: acme_orders_prod
    dataset: bigquery_orders_latest_npii_v1
terms:
  usage: >
    Data can be used for reports, analytics and machine learning use cases.
    Order may be linked and joined by other tables
  limitations: >
    Not suitable for real-time use cases.
    Data may not be used to identify individual customers.
    Max data processing per day: 10 TiB
  billing: 5000 USD per month
  noticePeriod: P3M
schema:
  type: dbt  # the specification format: dbt, bigquery, avro, protobuf, sql, json-schema, custom
  specification:  # expressed as string or inline yaml or via "$ref: model.yaml"
    version: 2 
    description: The subset of the output port's data model that we agree to use
    models:
      - name: orders
        description: >
          One record per order. Includes cancelled and deleted orders.
        columns:
          - name: order_id
            data_type: string
            description: Primary key of the orders table
          - name: order_timestamp
            data_type: timestamptz
            description: The business timestamp in UTC when the order was successfully registered in the source system and the payment was successful.
          - name: order_total
            data_type: integer
            description: "Total amount of the order in the smallest monetary unit (e.g., cents)."
      - name: line_items
        description: >
          The items that are part of an order
        columns:
          - name: lines_item_id
            data_type: string
            description: Primary key of the lines_item_id table
          - name: order_id
            data_type: string
            description: Foreign key to the orders table
          - name: sku
            data_type: string
            description: The purchased article number
examples:
  - type: csv # csv, json, yaml, custom
    model: orders
    data: |- # expressed as string or inline yaml or via "$ref: data.csv"
      order_id,order_timestamp,order_total
      "1001","2023-09-09T08:30:00Z",2500
      "1002","2023-09-08T15:45:00Z",1800
      "1003","2023-09-07T12:15:00Z",3200
      "1004","2023-09-06T19:20:00Z",1500
      "1005","2023-09-05T10:10:00Z",4200
      "1006","2023-09-04T14:55:00Z",2800
      "1007","2023-09-03T21:05:00Z",1900
      "1008","2023-09-02T17:40:00Z",3600
      "1009","2023-09-01T09:25:00Z",3100
      "1010","2023-08-31T22:50:00Z",2700
  - type: csv
    model: line_items
    data: |-
      lines_item_id,order_id,sku
      "1","1001","5901234123457"
      "2","1001","4001234567890"
      "3","1002","5901234123457"
      "4","1002","2001234567893"
      "5","1003","4001234567890"
      "6","1003","5001234567892"
      "7","1004","5901234123457"
      "8","1005","2001234567893"
      "9","1005","5001234567892"
      "10","1005","6001234567891"
quality:
  type: SodaCL   # data quality check format: SodaCL, montecarlo, custom
  specification: # expressed as string or inline yaml or via "$ref: checks.yaml"
    checks for orders:
      - freshness(order_timestamp) < 24h
      - row_count > 500000
      - duplicate_count(order_id) = 0
    checks for line_items:
      - row_count > 500000
```

Schema
---

[JSON Schema](https://github.com/datacontract/datacontract-specification/blob/main/datacontract.schema.json) of the Data Contract Specification.

### Data Contract Object

This is the root document.

It is _RECOMMENDED_ that the root document be named: `datacontract.yaml`.

| Field                     | Type                               | Description                                                                                           |
|---------------------------|------------------------------------|-------------------------------------------------------------------------------------------------------|
| dataContractSpecification | `string`                           | REQUIRED. Specifies the Data Contract Specification being used.                                       |
| id                        | `string`                           | REQUIRED. An organization-wide unique technical identifier, such as a UUID, URN, slug, string, or number                                             |
| info                      | [Info Object](#info-object)        | REQUIRED. Specifies the metadata of the data contract. May be used by tooling.                        |
| servers                   | [Servers Object](#servers-object)  | Specifies the servers of the data contract.                                                           |
| terms                     | [Terms Object](#terms-object)      | Specifies the terms and conditions of the data contract.                                    |
| schema                    | [Schema Object](#schema-object)    | Specifies the data contract schema. The specification supports different schemas.                     |
| examples                  | [Examples Object](#examples-object) | Specifies example data sets for the schema. The specification supports different example types.       |
| quality                   | [Quality Object](#quality-object)  | Specifies the quality attributes and checks. The specification supports different quality check DSLs. |

This object _MAY_ be extended with [Specification Extensions](#specification-extensions).




### Info Object

Metadata and life cycle information about the data contract.


| Field   | Type   | Description                                                                                                                                                      |
|---------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| title   | `string` | REQUIRED. The title of the data contract.                                                                                                                        |
| version | `string` | REQUIRED. The version of the data contract document (which is distinct from the Data Contract Specification version or the Data Product implementation version). |
| description | `string` | A description of the data contract.                                                                                                                              |
| owner   | `string` | The owner or team responsible for managing the data contract and providing the data.                                                                            |
| dataProduct | `string` | The identifier of the data product that contains the output port providing the data. For contract-first approaches, this field MAY be optional.                                                                                              |
| outputPort | `string` | The identifier of the output port that implements the data contract. For contract-first approaches, this field MAY be optional.                                                                                                              |
| contact | [Contact Object](#contact-object) | Contact information for the data contract.                                                                                                                       |




### Contact Object

Contact information for the data contract.

| Field | Type     | Description                                                                                           |
|-------|----------|-------------------------------------------------------------------------------------------------------|
| name  | `string` | The identifying name of the contact person/organization.                                              |
| url   | `string` | The URL pointing to the contact information. This _MUST_ be in the form of a URL.                     |
| email | `string` | The email address of the contact person/organization. This _MUST_ be in the form of an email address. |

This object _MAY_ be extended with [Specification Extensions](#specification-extensions).

### Servers Object

Information about the servers.

The Servers Object is a map of [Server Objects](#server-object).

### Server Object

The fields are dependent on the defined type.

| Field       | Type     | Description                                                                                                                                                              |
|-------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| type        | `string` | The type of the data product technology that implements the data contract. Well-known server types are: `bigquery`, `s3`, `redshift`, `snowflake`, `databricks`, `kafka` |
| description | `string` | An optional string describing the server.                                                                                                                                |


This object _MAY_ be extended with [Specification Extensions](#specification-extensions).

#### BigQuery Server Object

| Field   | Type     | Description |
|---------|----------|-------------|
| type    | `string` | `bigquery`  |
| project | `string` |             |
| dataset | `string` |             |

#### S3 Server Object

| Field    | Type     | Description                    |
|----------|----------|--------------------------------|
| type     | `string` | `s3`                           |
| location | `string` | S3 URL, starting with `s3://`  |

Example:

```yaml
servers:
  production:
    type: s3
    location: s3://acme-orders-prod/orders/
```


#### Redshift Server Object

| Field    | Type     | Description |
|----------|----------|-------------|
| type     | `string` | `redshift`  |
| account  | `string` |             |
| database | `string` |             |
| schema   | `string` |             |

#### Snowflake Server Object

| Field    | Type     | Description |
|----------|----------|-------------|
| type     | `string` | `snowflake` |
| account  | `string` |             |
| database | `string` |             |
| schema   | `string` |             |

#### Databricks Server Object

| Field    | Type     | Description  |
|----------|----------|--------------|
| type     | `string` | `databricks` |
| share     | `string` |              |

#### Kafka Server Object

| Field | Type     | Description |
|-------|----------|-------------|
| type  | `string` | `kafka`     |
| host  | `string` |             |
| topic | `string` |             |



### Terms Object

The terms and conditions of the data contract.

| Field                | Type   | Description                                                                                                                                                                 |
|----------------------|--------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| usage                | `string` | The usage describes the way the data is expected to be used. Can contain business and technical information.                                                                |
| limitations          | `string` | The limitations describe the restrictions on how the data can be used, can be technical or restrictions on what the data may not be used for.                               |
| billing              | `string` | The billing describes the pricing model for using the data, such as whether it's free, having a monthly fee, or metered pay-per-use.                                        |
| noticePeriod         | `string` | The period of time that must be given by either party to terminate or modify a data usage agreement. Uses ISO-8601 period format, e.g., `P3M` for a period of three months. |


### Schema Object

The schema of the data contract describes the syntax and semantics of provided data sets. 
As the type of the output port depends on the data platform, multiple schema specifications are supported.

A schema may define a single table, a collection of tables as a dataset, a file structure, or any arbitrary structure.

To avoid unnecessary abstractions, the data contract specification supports existing well-known formats. Some schema types, such as `dbt`, also support defining tests and additional metadata.


| Field | Type                                                                                                                                                                                                                 | Description                                                                                                                         |
| ----- |----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| type | `string`                                                                                                                                                                                                               | REQUIRED. The type of the schema.<br> Typical values are: `dbt`, `bigquery`, `json-schema`, `sql-ddl`, `avro`, `protobuf`, `custom` |
| specification | [dbt Schema Object](#dbt-schema-object) \|<br> [BigQuery Schema Object](#bigquery-schema-object) \|<br> [JSON Schema Schema Object](#bigquery-schema-object) \|<br> [SQL DDL Schema Object](#sql-ddl-schema-object) \|<br> `string` | REQUIRED. The specification of the schema. The schema specification can be encoded as a string or as inline YAML.                  |


#### dbt Schema Object

https://docs.getdbt.com/reference/model-properties

Example (inline YAML):

```yaml
schema:
  type: dbt
  specification:
    version: 2
    models:
      - name: "My Table"
        description: "My description"
        columns:
          - name: "My column"
            data_type: text
            description: "My description"
```

Example (string):

```yaml
schema:
  type: dbt
  specification: |-
    version: 2
    models:
      - name: "My Table"
        description: "My description"
        columns:
          - name: "My column"
            data_type: text
            description: "My description"
```

#### BigQuery Schema Object

The schema structure is defined by the [Google BigQuery Table](https://cloud.google.com/bigquery/docs/reference/rest/v2/tables#resource:-table) object. You can extract such a Table object via the [tables.get](https://cloud.google.com/bigquery/docs/reference/rest/v2/tables/get) endpoint.

Instead of providing a single Table object, you can also provide an array of such objects. Be aware that [tables.list](https://cloud.google.com/bigquery/docs/reference/rest/v2/tables/list) only returns a subset of the full Table object. You need to call every Table object via [tables.get](https://cloud.google.com/bigquery/docs/reference/rest/v2/tables/get) to get the full Table object, including the actual schema.

Learn more: [Google BigQuery REST Reference v2](https://cloud.google.com/bigquery/docs/reference/rest)



Example:

```yaml
schema:
  type: bigquery
  specification: |-
    {
      "tableReference": {
        "projectId": "my-project",
        "datasetId": "my_dataset",
        "tableId": "my_table"
      },
      "description": "This is a description",
      "type": "TABLE",
      "schema": {
        "fields": [
          {
            "name": "name",
            "type": "STRING",
            "mode": "NULLABLE",
            "description": "This is a description"
          }
        ]
      }
    }
```

#### JSON Schema Schema Object

JSON Schema can be defined as JSON or rendered as YAML, following the [OpenAPI Schema Object dialect](https://spec.openapis.org/oas/v3.1.0#properties)

Example (inline YAML):

```yaml
schema:
  type: json-schema
  specification:
    orders:
      description: One record per order. Includes cancelled and deleted orders.
      type: object
      properties:
        order_id:
          type: string
          description: Primary key of the orders table
        order_timestamp:
          type: string
          format: date-time
          description: The business timestamp in UTC when the order was successfully registered in the source system and the payment was successful.
        order_total:
          type: integer
          description: Total amount of the order in the smallest monetary unit (e.g., cents).
    line_items:
      type: object
      properties:
        lines_item_id:
          type: string
          description: Primary key of the lines_item_id table
        order_id:
          type: string
          description: Foreign key to the orders table
        sku:
          type: string
          description: The purchased article number
```

Example (string):

```yaml
schema:
  type: json-schema
  specification: |-
    {
      "$schema": "http://json-schema.org/draft-07/schema#",
      "type": "object",
      "properties": {
        "orders": {
          "type": "object",
          "description": "One record per order. Includes cancelled and deleted orders.",
          "properties": {
            "order_id": {
              "type": "string",
              "description": "Primary key of the orders table"
            },
            "order_timestamp": {
              "type": "string",
              "format": "date-time",
              "description": "The business timestamp in UTC when the order was successfully registered in the source system and the payment was successful."
            },
            "order_total": {
              "type": "integer",
              "description": "Total amount of the order in the smallest monetary unit (e.g., cents)."
            }
          },
          "required": ["order_id", "order_timestamp", "order_total"]
        },
        "line_items": {
          "type": "object",
          "properties": {
            "lines_item_id": {
              "type": "string",
              "description": "Primary key of the lines_item_id table"
            },
            "order_id": {
              "type": "string",
              "description": "Foreign key to the orders table"
            },
            "sku": {
              "type": "string",
              "description": "The purchased article number"
            }
          },
          "required": ["lines_item_id", "order_id", "sku"]
        }
      },
      "required": ["orders", "line_items"]
    }
```

#### SQL DDL Schema Object

Classical SQL DDLs can be used to describe the structure.


Example (string):

```yaml
schema:
  type: sql-ddl
  specification: |-
      -- One record per order. Includes cancelled and deleted orders.
      CREATE TABLE orders (
        order_id TEXT PRIMARY KEY, -- Primary key of the orders table
        order_timestamp TIMESTAMPTZ NOT NULL, -- The business timestamp in UTC when the order was successfully registered in the source system and the payment was successful.
        order_total INTEGER NOT NULL -- Total amount of the order in the smallest monetary unit (e.g., cents)
      );
    
      -- The items that are part of an order
      CREATE TABLE line_items (
        lines_item_id TEXT PRIMARY KEY, -- Primary key of the lines_item_id table
        order_id TEXT REFERENCES orders(order_id), -- Foreign key to the orders table
        sku TEXT NOT NULL -- The purchased article number
      );

```

### Examples Object

The Examples Object is an array of [Example Objects](#examples-object).

### Example Object

| Field       | Type     | Description                                                                                                                             |
|-------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------|
| type        | `string` | The type of the data product technology that implements the data contract. Well-known server types are: `csv`, `json`, `yaml`, `custom` |
| description | `string` | An optional string describing the example.                                                                                              |
| model       | `string` | The reference to the model in the schema, e.g. a table name.                                                                            |                                                                                        
| data        | `string` | Example data for this model.                                                                                                            |

Example:

```yaml
examples:
- type: csv
  model: orders
  data: |-
    order_id,order_timestamp,order_total
    "1001","2023-09-09T08:30:00Z",2500
    "1002","2023-09-08T15:45:00Z",1800
    "1003","2023-09-07T12:15:00Z",3200
    "1004","2023-09-06T19:20:00Z",1500
    "1005","2023-09-05T10:10:00Z",4200
    "1006","2023-09-04T14:55:00Z",2800
    "1007","2023-09-03T21:05:00Z",1900
    "1008","2023-09-02T17:40:00Z",3600
    "1009","2023-09-01T09:25:00Z",3100
    "1010","2023-08-31T22:50:00Z",2700
```

### Quality Object

The quality object contains quality attributes and checks.

| Field | Type                                                                                                                          | Description                                                                                                                     |
| ----- |-------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| type | `string`                                                                                                                        | REQUIRED. The type of the schema.<br> Typical values are: `SodaCL`, `montecarlo`, `custom`                                      |
| specification | [SodaCL Quality Object](#sodacl-quality-object) \|<br> [Monte Carlo Schema Object](#monte-carlo-quality-object) \|<br> `string` | REQUIRED. The specification of the quality attributes. The quality specification can be encoded as a string or as inline YAML.  |


#### SodaCL Quality Object

Quality attributes in [Soda Checks Language](https://docs.soda.io/soda-cl/soda-cl-overview.html).

The `specification` represents the content of a `checks.yml` file.

Example (inline):

```yaml
quality:
  type: SodaCL   # data quality check format: SodaCL, montecarlo, dbt-tests, custom
  specification: # expressed as string or inline yaml or via "$ref: checks.yaml"
    checks for orders:
      - row_count > 0
      - duplicate_count(order_id) = 0
    checks for line_items:
      - row_count > 0
```

Example (string):

```yaml
quality:
  type: SodaCL
  specification: |-
    checks for search_queries:
      - freshness(search_timestamp) < 1d
      - row_count > 100000
      - missing_count(search_query) = 0
```

#### Monte Carlo Quality Object

Quality attributes defined as Monte Carlos [Monitors as Code](https://docs.getmontecarlo.com/docs/monitors-as-code).

The `specification` represents the content of a `montecarlo.yml` file.

Example (string):

```yaml
quality:
  type: montecarlo
  specification: |-
    montecarlo:
      field_health:
        - table: project:dataset.table_name
          timestamp_field: created
      dimension_tracking:
        - table: project:dataset.table_name
          timestamp_field: created
          field: order_status
```

### Specification Extensions

While the Data Contract Specification tries to accommodate most use cases, additional data can be added to extend the specification at certain points.

A custom fields can be added with any name. The value can be null, a primitive, an array or an object. 

### Design Principles

The Data Contract Specification follows these design principles:

- Feels familiar to people already used to OpenAPI and AsyncAPI
- Supports existing well-known formats to avoid unnecessary abstractions
- Supports contract-first and code-first approaches

Tooling
---
- [Data Contract Studio](https://studio.datacontract.com/) is a free web tool to develop and share data contracts.
- [Data Contract CLI](https://github.com/datacontract/cli) is a free CLI tool to help you create, develop, and maintain your data contracts.
- [Data Mesh Manager](https://www.datamesh-manager.com/) is a commercial tool to manage data products and data contracts. It supports the data contract specification and allows the user to import or export data contracts using this specification.


Other Data Contract Specifications
---
- [AIDA User Group's Open Data Contract Standard](https://github.com/AIDAUserGroup/open-data-contract-standard)
- [PayPal's Data Contract Template](https://github.com/paypal/data-contract-template/blob/main/docs/README.md)

Literature
---
- [Driving Data Quality with Data Contracts](https://www.amazon.com/dp/B0C37FPH3D) by Andrew Jones

Authors
---
The Data Contract Specification was originally created by [Jochen Christ](https://www.linkedin.com/in/jochenchrist/) and [Dr. Simon Harrer](https://www.linkedin.com/in/simonharrer/), and is currently maintained by them.


Contributing
---
Contributions are welcome! Please open an issue or a pull request.

License
---
[MIT License](LICENSE)
