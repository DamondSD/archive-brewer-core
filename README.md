# Archive Brewer Core

Archive Brewer Core is a shared support module for the Archive brewer ecosystem.

It is **not** a user-facing builder app.

Its purpose is to provide shared infrastructure for brewer modules such as:

- Archive Creature Brewer
- Archive Item Brewer
- Archive Spell Brewer
- Archive Feat Brewer
- Archive Species Brewer
- Archive Subclass Brewer

---

## What Core Does

Archive Brewer Core provides shared systems for brewer modules, including:

- shared ApplicationV2 base app patterns
- neutral source document helpers
- document normalization and versioning helpers
- validation runner support
- form binding helpers
- template and partial registration
- brewer definition registry
- system adapter registry
- exporter registry
- shared compendium and save helpers

---

## What Core Does Not Do

Archive Brewer Core does **not** contain:

- creature-specific builder logic
- item-specific builder logic
- spell-specific builder logic
- subclass-specific builder logic
- species-specific builder logic
- system-export logic for a specific brewer
- user-facing unified launcher logic

Each brewer remains its own user-facing module and application.

Archive Tab may act as the ecosystem launcher, but Archive Brewer Core is infrastructure only.

---

## Architecture Summary

```text
Archive Tab
     │
     │ launches
     ▼
Archive Creature Brewer
Archive Item Brewer
Archive Spell Brewer
Archive Feat Brewer
Archive Species Brewer
Archive Subclass Brewer
     │
     ▼
Archive Brewer Core
```

Core sits beneath the brewer modules and provides shared contracts, registries, helpers, and infrastructure.

---

## Core Design Rules

### Brewers Import Core, Never the Reverse

Core must never import brewer modules.

Allowed direction:

```text
Brewer Module -> Archive Brewer Core
```

Forbidden direction:

```text
Archive Brewer Core -> Brewer Module
```

This keeps the system modular and prevents architectural drift.

---

### Core Owns Contracts, Not Brewer Content

Core defines the shared contracts that the ecosystem relies on, such as:

- blueprint shape
- brewer registration shape
- exporter result shape
- validation result shape

Brewers define their own internal blueprint content and business logic.

---

### Neutral First

Core must remain:

- brewer-neutral
- system-neutral
- export-neutral where possible

If something is system-specific, it belongs in an adapter or exporter.  
If something is brewer-specific, it belongs in that brewer.

---

## Shared Blueprint Contract

All brewer blueprints must follow the same top-level structure:

```js
{
  meta: {},
  identity: {},
  classification: {},
  data: {},
  relations: {},
  presentation: {},
  flags: {},
  notes: {}
}
```

This gives every brewer a shared structural contract while still allowing each brewer to define its own internal content.

---

## Brewer Registration Contract

Each brewer must register itself with Core using a stable definition object.

Example:

```js
{
  id: "creature",
  label: "Archive Creature Brewer",
  moduleId: "archive-creature-brewer",
  blueprint: {
    version: 1,
    createEmpty: () => ({})
  },
  document: {
    type: "Actor",
    subtypes: ["npc"]
  },
  capabilities: {
    export: true,
    validate: true,
    saveBlueprint: true,
    preview: true
  }
}
```

This gives Core enough information to support the brewer without Core needing to know the brewer’s internal logic.

---

## Exporter Result Contract

Exporters must return a predictable result envelope.

Recommended shape:

```js
{
  ok: true,
  primaryDocument: {
    type: "Actor",
    data: {}
  },
  relatedDocuments: [],
  warnings: [],
  errors: [],
  meta: {
    brewerId: "creature",
    systemId: "dnd5e",
    outputType: "Actor"
  }
}
```

This allows clean handling of:

- success
- warnings
- partial export
- future multi-document export

---


## Documentation Standard

This repo follows a strict documentation rule:

- every exported function must have JSDoc
- every class must have a top-level JSDoc
- every non-obvious code block must have comments
- comments should explain **why**, not just **what**
- larger files should use section dividers

This is required to keep the core understandable and maintainable as the ecosystem grows.

---

## Definition of Success

Archive Brewer Core succeeds if it does all of the following:

- reduces duplicate code across brewers
- standardizes shared contracts
- isolates system-specific logic cleanly
- supports multiple brewer modules without coupling
- stays stable as new brewers are added
- remains infrastructure only

---
