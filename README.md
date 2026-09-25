# Fire-Ops Ontology Design Principles

## 1. Purpose

The Fire-Ops ontology is intended to provide a stable semantic foundation for building fire-safety operation, assessment, and decision support.

Its role is not to replace BIM, IFC, sensing systems, simulation tools, databases, or raw files. Instead, it acts as a **semantic coordination layer** that connects:

- building context;
- operational evidence and observations;
- time-varying building states;
- performance-specific functional models;
- linked source artifacts;
- future assessment, risk, and decision outputs.

The ontology should enable the system to reconstruct and explain, as far as practical:

> **what the building is, what state it was in, what evidence supported that state, how the state was interpreted for a specific fire-safety function, and how subsequent assessment and decisions were produced.**

The primary service targets are therefore **machine reasoning, performance assessment, decision support, provenance/auditability, and state-aware visualisation**, rather than general-purpose facility management.

---

## 2. Core Architectural Principle

The ontology shall maintain clear separation between different kinds of knowledge:

**Physical Context ≠ Observation/Evidence ≠ Operational State ≠ Functional Interpretation ≠ Assessment/Decision**

These layers may be connected, but they should not be collapsed into one another.

A typical information chain is:

```text
Source / Evidence
      ↓
Observation
      ↓
Operational State
      ↓
State Snapshot
      ↓
Functional Interpretation
      ↓
Performance / Risk Assessment
      ↓
Decision / Action
```

The same physical building entity may participate in multiple functional interpretations without changing its physical identity.

---

## 3. Design Principles

### 3.1 Preserve physical identity

A physical entity such as a room, corridor, door, chair, or detector should retain one stable semantic identity.

Its identity must not be redefined merely because it temporarily plays another role.

For example:

- a corridor is not inherently an `EgressSegment`;
- a chair is not inherently an `ObstructionObject`;
- an object becomes relevant to obstruction through an `ObstructionState`;
- a physical object may participate in several functional models simultaneously.

**Identity, role, and state must remain conceptually distinct.**

### 3.2 Represent operational states as contextual conditions

An `OperationalState` represents a meaningful time-dependent condition of a physical entity, rather than merely a single property-value record.

A state may therefore contain several related pieces of information, such as:

- affected entity;
- involved physical objects;
- condition-specific values;
- validity period;
- supporting observations.

This allows a condition such as an obstruction, occupancy condition, or door condition to remain a coherent unit for downstream assessment.

Simple scalar values may be represented directly as literals. Physical quantities requiring units should use an explicit quantity/unit representation.

### 3.3 Do not equate absence of information with a negative state

The ontology follows an open-world assumption.

Therefore:

> **not recorded ≠ false**

Where operationally important, negative conditions should be represented explicitly.

For example, “confirmed clear” and “no current information” are different states and must remain distinguishable.

### 3.4 Keep observations separate from interpreted states

Observations describe what was observed, by whom or by what process, about which physical entity, and when.

Operational states describe what the system currently considers to be true about the building.

Multiple observations may support one state, and one observation may contribute to several interpreted states.

Evidence provenance should remain traceable rather than being silently absorbed into state values.

### 3.5 Treat functional models as derived, analysis-specific representations

Functional entities such as `EgressNetwork`, `EgressNode`, and `EgressSegment` are not substitutes for BIM objects.

They represent how physical building entities participate in a particular performance analysis.

Functional models should therefore be considered **derived analytical representations** built from physical context and relevant domain knowledge.

The same physical object may support different functional interpretations for evacuation, compartmentation, detection, suppression, smoke control, or other analyses.

The operational state layer should remain independent of any single functional model.

### 3.6 Do not duplicate authoritative source data unnecessarily

Heavy or authoritative source information should remain in its native artifact whenever practical.

Examples include:

- IFC geometry;
- images and video;
- point clouds;
- meshes;
- simulation files;
- detailed documents.

The KG should primarily store semantic identity, relationships, state, provenance, version information, and references required to retrieve or reconstruct these artifacts.

Derived or lightweight information may be stored in the KG when it is required for reasoning, querying, reproducibility, or decision support.

### 3.7 Make source references reproducible

A semantic entity should be able to refer back to the exact source artifact and source object from which it was obtained.

The design should therefore distinguish:

```text
semantic entity
    ↓
source object reference
    ↓
specific source artifact version
```

Source references should support versioning and stable object identifiers where available, such as IFC `GlobalId`.

A file path alone is not sufficient provenance. Artifact version and integrity information should be retained when reproducibility matters.

### 3.8 Separate observed facts from derived information

The ontology must distinguish between:

- directly observed or imported information;
- inferred operational state;
- function-specific derived parameters;
- assessment outputs;
- risk interpretation;
- recommended actions.

For example, an object's geometry or current location may be observed, while `effectiveWidth`, route dependency, or risk significance may be derived.

Derived values should not be represented as raw physical facts without provenance describing how they were produced.

### 3.9 Reuse established ontologies before creating new vocabulary

New Fire-Ops concepts should only be introduced where existing standards do not adequately express the intended semantics.

Current preferred external vocabularies include:

- **BOT** for building topology and spatial context;
- **SOSA/SSN** for observations and sensing;
- **PROV-O** for provenance and derivation;
- **QUDT** for physical quantities and units;
- **GeoSPARQL** where explicit spatial geometry or spatial relations are genuinely required.

Reuse should remain selective. Importing a large ontology does not by itself improve the model.

### 3.10 Keep ontology semantics separate from validation rules

OWL/RDFS statements such as `rdfs:domain`, `rdfs:range`, and `rdfs:subClassOf` define semantic meaning and support inference.

They should not be treated as data-entry constraints.

Requirements such as:

- exactly one affected entity;
- mandatory evidence;
- valid numeric ranges;
- required units;

should be expressed separately using **SHACL** when validation is introduced.

### 3.11 Prefer derived knowledge over duplicated knowledge

Information that can be reliably derived from existing graph relationships should not automatically be stored again.

For example, if an `ObstructionState` refers to a corridor and an `EgressSegment` is realised by that corridor, the affected segment may be obtained through query or interpretation rather than stored as a permanent direct link.

Materialisation should only be introduced when it provides a clear benefit for performance, auditability, or persistence of a specific historical interpretation.

### 3.12 Model only what serves a competency need

The ontology should not expand simply to appear comprehensive.

A new class, property, or module should be introduced only when it is required to:

- represent real data;
- answer a defined competency question;
- support a calculation or reasoning step;
- preserve provenance;
- enable reconstruction or visualisation;
- support a required decision.

Prototype simplicity is preferred over speculative completeness.

---

## 4. Module Responsibilities

The ontology is modular by concern.

```text
fire_ops_observation.ttl
    Observation vocabulary and evidence semantics

fire_ops_state.ttl
    Time-varying operational conditions and state snapshots

fire_ops_function.ttl
    Performance-specific functional representations

fire_ops_artifact.ttl
    References to source files, model versions, and source objects
```

Future modules such as assessment, risk, and action should only be added when their semantics and required competency questions are sufficiently clear.

Modules may share the Fire-Ops namespace while remaining logically separated.

---

## 5. Extension Rule

Before extending the ontology, the following questions should be answered in order:

1. **What real-world or analytical concept is missing?**
2. **Is it a physical entity, observation, state, functional role, derived result, or artifact reference?**
3. **Does an established ontology already represent it adequately?**
4. **Does the new concept have a clear competency question or system use?**
5. **Can the information already be derived from existing knowledge?**
6. **Will the new concept preserve the separation between identity, state, function, evidence, and derived knowledge?**
7. **Can historical states and previous decisions still be reconstructed after this change?**

If these questions cannot be answered clearly, the ontology should not yet be extended.

---

## 6. Long-Term Design Goal

The ontology should remain sufficiently lightweight to evolve, but sufficiently explicit to support the following principle:

> **A Fire-Ops KG together with its linked and versioned source artifacts should contain enough semantic and provenance information to reconstruct the relevant building context, operational state, analytical interpretation, visualisation, and decision pathway for a given point in time.**

The ontology is therefore not intended to contain all data.

It is intended to make the distributed data, models, states, and reasoning processes **connected, interpretable, reproducible, and usable for fire-safety assessment and decision support**.
