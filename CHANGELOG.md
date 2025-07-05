# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2024-07-05

### Added
- Support for `models.additionalFields` to define if additional fields (columns) are allowed or not in the physical server ([#99](https://github.com/datacontract/datacontract-specification/pull/99))
- Add `time` data type ([#123](https://github.com/datacontract/datacontract-specification/issues/123))
- Added `variant` data type ([#113](https://github.com/datacontract/datacontract-specification/issues/113))
- Added `json` data types ([#112](https://github.com/datacontract/datacontract-specification/issues/112))

### Changed
- `server.type` changed from enum to simple string to support custom types ([#107](https://github.com/datacontract/datacontract-specification/pull/107))

## [1.1.0] - 2024-10-30

### Added
- Data quality on model and field level ([#55](https://github.com/datacontract/datacontract-specification/issues/55))
- Lineage support ([#90](https://github.com/datacontract/datacontract-specification/issues/90))
- Field and definition `examples` as array of any type, instead of `example` as a single value ([#29](https://github.com/datacontract/datacontract-specification/issues/29)
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
- Field `field.primaryKey` (Replaces `field.primary`)
- Field `model.primaryKey` to describe a composite primary key
- Add Redshift server properties `clusterIdentifier`, `endpoint`, `host` and `port`.

### Removed

- `definitions.domain` removed (use a hierarchical structure instead)
- `definitions.name` removed (use a hierarchical structure instead)
- `quality` on top-level removed
- `examples` on top-level removed
- `schema` removed in favor of encoding any physical schema configuration in the `model` using the `config` map at the field level and supporting import/export ([#21](https://github.com/datacontract/datacontract-specification/issues/21)).

### Deprecated

- `field.primary` (use `field.primaryKey` instead)


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
