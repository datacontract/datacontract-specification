# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.1.0] - 2024-09-09

### Added
- Data quality on model and field level
- Support for server-specific data types as config map ([#63](https://github.com/datacontract/datacontract-specification/issues/63))
- AWS Glue Catalog server support
- sftp server support
- info.status field
- oracle server support
- field.title attribute
- model.title attribute
- AWS Kinesis Data Streams server support
- field.links attribute
- Trino support
- Field `type: map` support with properties `keys` and `values`
- Definitions: `fields`, for type `object`, `record`, and `struct`

### Removed

- `quality` on top-level removed
- `schema` removed


## [0.9.3] - 2024-03-06

### Added

- Service levels as a top level `servicelevels` element
- pubsub server support
- primary key and relationship support via `field.primary` and `field.references` attributes
- databricks server support improved

## [0.9.2] - 2024-01-04

### Added

- Format and validation attributes to fields in models and definitions
- Postgres support
- Databricks support

## [0.9.1] - 2023-11-19

### Added

- A logical data model (#13), mainly to simplify editor support with a defined schema, easier to detect breaking changes, and better Databricks support.
- Definitions (#14) for reusable semantic definitions within one data contract or across data contracts. 

### Removed

- Property `info.dataProduct` as data products should define which data contracts they implement.
- Property `info.outputPort` as data products should define which data contracts they implement.

Those removals are not considered as breaking changes, as these attributes are now treated as specification extensions.

## [0.9.0] - 2023-09-12

First public release.
