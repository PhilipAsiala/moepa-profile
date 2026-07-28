# SPOP – A Provenance Profile for RDF 1.2

[![Status](https://img.shields.io/badge/status-Draft-yellow)](./spec/spop-profile.md)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](./LICENSE)

**SPOP** (*Subject–Predicate–Object–Provenance*) is a **Draft** technical profile of [RDF 1.2](https://www.w3.org/TR/rdf12-concepts/) and [Turtle 1.2](https://www.w3.org/TR/rdf12-turtle/).

It defines a minimal, enterprise-oriented way to attach **provenance**, **document-type governance**, and **confidence scores** to RDF statements—without inventing a competing data model.

> **This repository is a Draft.** Nothing here is a formal standard. Normative language is provisional.

---

## Purpose

SPOP targets systems that must reason over governed knowledge, including:

- enterprise and government **RAG** pipelines;
- **knowledge graphs** with multi-source ingestion;
- **cognitive architectures** that separate values, processes, and facts.

Every SPOP-managed statement remains a normal RDF triple (Subject–Predicate–Object). Provenance is attached using RDF 1.2 **annotation syntax** (preferred) or **named graphs** (acceptable).

### Document types (working terms)

| Code | Working name | Role |
| --- | --- | --- |
| `org_value` | Organization Value Documents | Mission, principles, policy intent |
| `process` | Process Documents | Procedures, workflows, SOPs |
| `informational` | Informational Documents | Facts, reports, reference knowledge |

(Philosophical layer names—axiology, praxeology, epistemology—are mapped once in the specification and then set aside in favor of these working terms.)

### Minimum provenance set

1. Source / document identity  
2. Document version  
3. Document type (`org_value` \| `process` \| `informational`)  
4. Owner  
5. Confidence (0.0–1.0)  
6. Extracted/created timestamp  

**Confidence policy (summary):** when the ingesting system is not the authoritative owner, published confidence SHOULD be `internal_confidence × 0.9` so the true owner can later publish a higher-confidence version of the same logical document.

---

## Repository layout

```text
.
├── README.md                 # This file
├── LICENSE                   # Apache-2.0
├── CHANGELOG.md
├── CONTRIBUTING.md
├── spec/
│   ├── spop-profile.md       # Main Draft specification
│   └── spop-shape.ttl        # Draft SHACL shapes
├── vocabulary/
│   └── spop.ttl              # Lightweight SPOP terms
└── examples/
    ├── basic-spop.ttl
    ├── org-value-example.ttl
    ├── process-example.ttl
    └── informational-example.ttl
```

---

## How to read the specification

1. Start with **[spec/spop-profile.md](./spec/spop-profile.md)** — the normative Draft text.  
2. Skim **[vocabulary/spop.ttl](./vocabulary/spop.ttl)** for term IRIs and the three document-type values.  
3. Open **[examples/](./examples/)** for concrete Turtle 1.2 illustrations.  
4. Optionally use **[spec/spop-shape.ttl](./spec/spop-shape.ttl)** for draft SHACL validation of provenance records.

**Reading order for implementers:** Abstract → Core definition → Required provenance → Confidence policy → Serialization → Examples → Conformance.

---

## Design principles

- **Profile, not competitor.** SPOP constrains RDF 1.2; it does not replace it.  
- **Minimal vocabulary.** Prefer PROV-O, Dublin Core, and RDF 1.2 concepts; define only what the profile needs.  
- **Governance-ready.** Document types support policy and review workflows.  
- **Serialization-practical.** Prefer Turtle 1.2 annotation syntax; named graphs remain acceptable.  
- **Enterprise tone.** Precise language suitable for regulated and government contexts.

---

## Status

| Item | Value |
| --- | --- |
| Spec status | **Draft** |
| Spec version | 0.1.0-draft |
| Preferred serialization | Turtle 1.2 |
| License | Apache License 2.0 |

See [CHANGELOG.md](./CHANGELOG.md) for version history.

---

## Contributing

Contributions that improve clarity, examples, SHACL coverage, or alignment with RDF 1.2 / PROV-O are welcome. Please read [CONTRIBUTING.md](./CONTRIBUTING.md) before opening a pull request.

---

## License

Copyright and license terms are in [LICENSE](./LICENSE) (Apache License 2.0).
