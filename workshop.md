# Data Contract Workshop

Bring data producers and consumers together to define data contracts in a facilitated workshop.

## Goal

A defined and agreed upon data contract between data producers and consumers.

## Participants

- Facilitator
  - Neutral moderator and typist
  - Should know the [Data Contract Specification](https://datacontract.com) and its tools well
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

## Recommended Order of Completion

1. Info (get the context)
2. Examples (example-driven facilitation)
3. Model (you will spend most of your time here)
   - Use the [Data Contract CLI](https://cli.datacontract.com) to test the model against the previously created examples:
    `datacontract test --examples datacontract.yaml`
4. Quality
5. Terms
6. Servers (if already applicable)
   - Start with a "local" server with actual, real data you downloaded
   - Use the [Data Contract CLI](https://cli.datacontract.com) to test the model against the actual data on a specific server:
    `datacontract test datacontract.yaml`
   - Switch to the actual remote server, if applicable

## Tooling

- Open the [starter template](https://datacontract.com/datacontract.init.yaml) in the [Data Contract Studio](https://studio.datacontract.com) and get going. If you lack an experienced facilitator, ignore any validation errors and warnings within the studio.
- Use the [Data Contract Studio](https://studio.datacontract.com) to share the results of the workshop afterward with the participants and other stakeholders.
- Use the [Data Contract CLI](https://cli.datacontract.com) to validate the data contract after the workshop.

## Related

- This data contract workshop could be a followup to a data product design workshop using the [Data Product Canvas](https://www.datamesh-architecture.com/data-product-canvas), making the offered contract at the output port of the designed data product more concrete.
