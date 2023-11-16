# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [0.9.1] - 2023-11-16

### Added

- A generic data model (#13), mainly to simplify editor support with a defined schema, and better Databricks support that has no standard schema model.
- Definitions (#14) for reusable semantic definitions within one data contract or across data contracts. 

### Removed

- Property `info.dataProduct` as data products should define which data contracts they implement.
- Property `info.outputPort` as data products should define which data contracts they implement.

Those removals are not considered as breaking changes, as these attributes are now treated as specification extensions.

## [0.9.0] - 2023-09-12

First public release.