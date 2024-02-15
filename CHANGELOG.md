# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

Please note,  while the major version is zero (0.y.z), Anything MAY change at any time. 
The public API SHOULD NOT be considered stable.

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
