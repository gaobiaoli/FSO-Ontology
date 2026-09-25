# FSO Ontology

**FSO (Fire Safety Operations) Ontology** provides a semantic foundation for representing building operational information in support of fire-safety assessment and decision-making.

The ontology is intended to connect heterogeneous building information, observations, operational states, functional interpretations, and subsequent assessment processes within a consistent knowledge representation.

It is not intended to replace BIM/IFC models, simulation tools, sensor systems, databases, or other source data. Instead, the knowledge graph provides a semantic layer through which these resources can be connected, interpreted, queried, and traced.

## Design Scope

The ontology currently distinguishes four main concerns:

- **Observation** — evidence or observations describing what has been detected or recorded;
- **State** — time-dependent operational conditions of physical building entities;
- **Function** — analysis-specific interpretations of how physical entities participate in fire-safety functions;
- **Artifact** — references between semantic entities and external source data such as IFC models.

Future assessment, risk, and decision concepts will be introduced only when their required semantics are sufficiently clear.

## Core Design Principles

### Separate physical identity, state, and function

A physical building entity retains its physical identity independently of its current condition or analytical role.

For example, a corridor may realise an evacuation segment, and a chair may temporarily contribute to an obstruction, without either being redefined as those functional or operational concepts.

### Represent operational conditions explicitly

Dynamic building conditions are represented as states rather than being written directly onto otherwise persistent physical entities.

This supports temporal information, provenance, multiple simultaneous conditions, and historical reconstruction.

### Keep observations and interpreted states distinct

Observations describe available evidence. States represent the operational conditions inferred or established from that evidence.

This distinction allows different observations or data sources to support the same state while preserving traceability.

### Treat functional models as analytical representations

Functional entities such as evacuation nodes and segments describe how physical building entities are interpreted for a particular assessment task.

They should remain distinguishable from the underlying physical building representation.

### Link rather than duplicate source information

Information already maintained in authoritative source artifacts, such as IFC geometry, should normally remain in those artifacts.

The knowledge graph should maintain the semantic identity, relationships, provenance, and references necessary to retrieve and interpret the underlying information.

### Distinguish source information from derived knowledge

Observed or imported information, interpreted states, functional parameters, assessment results, and decisions should remain distinguishable where this distinction is important for explanation or reproducibility.

### Reuse existing vocabularies where appropriate

Established ontologies and standards should be reused when they provide suitable semantics. New FSO concepts should be introduced only where they are necessary for the intended fire-safety operational representation.

### Keep the ontology purpose-driven

The ontology should remain as simple as possible while supporting the required data representation, queries, reasoning, assessment, visualisation, and decision-support tasks.

New concepts should be added in response to concrete competency requirements rather than for completeness alone.

## Current Modules

```text
fire_ops_observation.ttl
    Observation and evidence-related vocabulary

fire_ops_state.ttl
    Operational states and state snapshots

fire_ops_function.ttl
    Fire-safety functional and analytical representations

fire_ops_artifact.ttl
    Links between semantic entities and external source artifacts
```

## Extension Principle

When extending the ontology, first determine whether the new information represents:

```text
physical context
observation
operational state
functional interpretation
source artifact
derived assessment or decision
```

The new concept should be placed in the appropriate layer without unnecessarily duplicating information already represented elsewhere.

The ontology is expected to evolve with the research. Its architecture should therefore remain modular, traceable, and sufficiently lightweight to accommodate new fire-safety scenarios and assessment methods.