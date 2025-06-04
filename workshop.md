# Data Contract Workshop

Bring data producers and consumers together to define data contracts in a facilitated workshop.

## Goal

A defined and agreed upon data contract between data producers and consumers.

## Participants

- Facilitator
  - Neutral moderator and typist
  - Should know the used data contract formal ([Data Contract Specification](https://datacontract.com) or [ODCS](https://bitol-io.github.io/open-data-contract-standard/latest/)) and its tools well
  - Get the [authors of the Data Contract Specification](https://datacontract.com/#authors) as facilitators for your workshop.
- Data producer
  - Product Owner
  - Software Engineers
- Data consumers
  - Product Owner
  - Data Engineers / Scientist / Analyst

Recommendation: keep the group small (not more than 5 people)

## Settings

- Show data contract the whole workshop on the screen (projector, screenshare, ...)
- Facilitator is the typist
- Facilitator is moderator
- Data Producer and Data Consumers discuss and give commands to the facilitator

## Guidelines for the Data Contract Specification

### Recommended Order of Completion (Data Contract Specification)

1. Info (get the context)
2. Examples (example-driven facilitation)
3. Model (you will spend most of your time here)
   - Use the [Data Contract CLI](https://cli.datacontract.com) to test the model against the previously created examples:\\
    `datacontract test --examples datacontract.yaml`
4. Quality
5. Terms
6. Servers (if already applicable)
   - Start with a "local" server with actual, real data you downloaded
   - Use the [Data Contract CLI](https://cli.datacontract.com) to test the model against the actual data on a specific server:\\
    `datacontract test datacontract.yaml`
   - Switch to the actual remote server, if applicable

### Tooling (Data Contract Specification)

- Open the [starter template](https://datacontract.com/datacontract.init.yaml) in the [Data Contract Editor](https://editor.datacontract.com) and get going. If you lack an experienced facilitator, ignore any validation errors and warnings within the editor.
- Use the [Data Contract Editor](https://editor.datacontract.com) to share the results of the workshop afterward with the participants and other stakeholders.
- Use the [Data Contract CLI](https://cli.datacontract.com) to validate the data contract after the workshop.
- Use the [Data Mesh Manager](https://www.datamesh-manager.com) to publish the data contract and have it in a central place

## Guidelines for ODCS

We recommend to use the [Excel template](https://github.com/datacontract/open-data-contract-standard-excel-template) for workshops as it is easier to work with in such a setting as it comes with a nice visualization.

### Recommended Order of Completion (ODCS)

1. Fundamentals (get the context)
   - [Fill in the fundamentals][fundamentals](https://bitol-io.github.io/open-data-contract-standard/latest/#fundamentals) consisting of id, name, version, status, and description.
2. Schema (you will spend most of your time here)
   - [Fill in the schemas](https://bitol-io.github.io/open-data-contract-standard/latest/#schema) (tables) and their properties (columns) along with their name and logicalType as a start in the schema part. 
   - After that, add information like `description`, `classification`, ...
   - Use tags or customProperties add additional metadata where there is no direct support by ODCS
3. Quality
   - [Add quality checks](https://bitol-io.github.io/open-data-contract-standard/latest/#data-quality) at the schema or the property level. Start with quality checks of type text first to capture the requirements.
   - OPTIONAL Conver the text-based requirements into automated sql-based quality checks
4. SLAs
   - [Add SLAs](https://bitol-io.github.io/open-data-contract-standard/latest/#service-level-agreement-sla) that the data provider guarantees towards all data consumers.
5. Team & Support
   - Add the [team members](https://bitol-io.github.io/open-data-contract-standard/latest/#team) so that the data consumer knows who is part of the team that owns the data protected by the data contracts.
   - Add a [support channel](https://bitol-io.github.io/open-data-contract-standard/latest/#support-and-communication-channels) so (potential) data consumers know how to get support and reach the data owners.
6. Servers (if already applicable)
   - [Add the server information](https://bitol-io.github.io/open-data-contract-standard/latest/#infrastructure-and-servers) on where the data is available
   - Use the [Data Contract CLI](https://cli.datacontract.com) to test the schema against the actual data on a specific server:\\
    `datacontract test datacontract.yaml`

### Tooling (ODCS)

- Use the [Excel template](https://github.com/datacontract/open-data-contract-standard-excel-template) for the workshop
- Use the [Data Contract CLI](https://cli.datacontract.com) to validate the data contract after the workshop.
- Use the [Data Mesh Manager](https://www.datamesh-manager.com) to publish the data contract and have it in a central place

## Related

- This data contract workshop could be a followup to a data product design workshop using the [Data Product Canvas](https://www.datamesh-architecture.com/data-product-canvas), making the offered contract at the output port of the designed data product more concrete.
