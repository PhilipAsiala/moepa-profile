# MOEPA Profile – The MOEPA Record for JSON-LD 1.1 and RDF 1.2

![Status](https://img.shields.io/badge/status-draft-orange) ![License](https://img.shields.io/badge/license-Apache--2.0-blue)

The **MOEPA profile** defines the **MOEPA record**: one RDF triple plus four envelopes, one for each layer of the MOEPA cognitive architecture other than Ontology.

| Part | Question | Carries |
| --- | --- | --- |
| Ontology | What is this? | Subject, predicate, object — the triple |
| Epistemology | Why believe it? | Generating agent, time, SHA-256 fingerprint, producer trust, source document |
| Metrology | How good is it now? | Measurement confidence after quality rules, validity window, decay |
| Axiology | What may it mean for people? | Classification marking, PII flag, mission alignment, value weight |
| Praxeology | What may be done with it? | Allowed and denied action classes, execution trigger |

The profile constrains [JSON-LD 1.1](https://www.w3.org/TR/json-ld11/) and [RDF 1.2](https://www.w3.org/TR/rdf12-concepts/); it does not invent a competing data model. **The triple stays a triple.** The envelopes are annotations on it.

> **This repository is a Draft.** Nothing here is a formal standard. Normative language is provisional.

This repository supersedes **SPOP** (*Subject–Predicate–Object–Provenance*, 0.1.0-draft), which attached one flat provenance record to a triple in Turtle 1.2. The MOEPA record keeps SPOP's source-provenance set inside the Epistemology envelope, splits its single confidence into measurement confidence and producer trust, and prefers JSON-LD. The old namespace `https://w3id.org/spop/ns#` is retired; see the migration appendix in the specification.

---

## Where this fits

| Repo | Role |
| --- | --- |
| [moepa-cognitive-architecture](https://github.com/PhilipAsiala/moepa-cognitive-architecture) | Specifies the record (§5.0), the system of record and projections (§5.0.1), and the curation ladder the record moves through (§5.2) |
| **This repo** | Normative shape of the record: context document, vocabulary, SHACL shapes, serializations |
| [moepa-lab](https://github.com/PhilipAsiala/moepa-lab) | Writes, stages, promotes, and reads the record; its `schema/` resolves the context from here until it is published |
| [moepa-thesis](https://github.com/PhilipAsiala/moepa-thesis) | The argument for why the parts are kept separate |

Namespace: `https://schema.asiala.net/moepa/v1#` (prefix `moepa:`). Context document: [`context/moepa-v1.jsonld`](context/moepa-v1.jsonld), to be published at `https://schema.asiala.net/moepa/v1`.

---

## Required on every write

`@id`, `@type`; the triple; `prov:wasGeneratedBy`, `prov:generatedAtTime`, `moepa:cryptographicHash`; `moepa:confidenceScore`, `moepa:validUntil`; `moepa:classificationLevel`; `moepa:allowedActions`, `moepa:deniedActions`. A record is never updated in place: a change is a new row with the same `@id` and a new fingerprint.

**Two confidences.** `moepa:confidenceScore` (Metrology) is how well the assertion survived the quality rules. `moepa:sensorTrustLevel` (Epistemology) is how much the producer is believed. When the ingesting system is not the authoritative owner of the source, published producer trust SHOULD be `internal × 0.9`; measurement confidence is never reduced for ownership.

---

## Repository layout

```
.
├── README.md
├── LICENSE                        Apache-2.0
├── CHANGELOG.md
├── CONTRIBUTING.md
├── context/
│   └── moepa-v1.jsonld            JSON-LD 1.1 context (the published document)
├── spec/
│   ├── moepa-profile.md           Main Draft specification
│   └── moepa-shape.ttl            Draft SHACL shapes
├── vocabulary/
│   └── moepa.ttl                  Vocabulary terms
└── examples/
    ├── basic.jsonld               service-generated record (preferred serialization)
    ├── org-value.jsonld           document-derived, OrgValue
    ├── process.jsonld             document-derived, Process
    ├── informational-non-authoritative.jsonld   producer trust × 0.9
    ├── basic.ttl                  same record, Turtle 1.2 annotation syntax
    └── basic.trig                 same record, named graph
```

## How to read the specification

1. **spec/moepa-profile.md** — the normative Draft text: record structure, required fields, two confidences, serializations, conformance, SPOP migration.
2. **context/moepa-v1.jsonld** — the terms and type coercions a JSON-LD processor needs.
3. **vocabulary/moepa.ttl** — term definitions and alignments to PROV-O.
4. **examples/** — the same record in JSON-LD, Turtle 1.2, and TriG.
5. **spec/moepa-shape.ttl** — draft SHACL for validating expanded records.

## Validating

The JSON-LD examples expand with the context and conform to the shapes; the TriG example parses and conforms. With `pyld`, `rdflib`, and `pyshacl` installed, expand an example to N-Quads and run `pyshacl -s spec/moepa-shape.ttl` over the result. Turtle 1.2 annotation syntax needs an RDF 1.2 parser.

## Design principles

- **Profile, not competitor.** Constrain JSON-LD 1.1 and RDF 1.2; do not replace them.
- **One triple, four envelopes.** Nothing from an envelope is written into the triple; no permission is derived from a confidence.
- **Minimal vocabulary.** Use PROV-O where it fits; define only what the record needs.
- **Append-only.** Supersession by `@id` and fingerprint.
- **Stage is not a claim.** Bronze, silver, and gold are facts about where a record is held, kept by the holding system; the record carries no stage property and one version keeps one fingerprint through every stage (§5.6).
- **Serialization-practical.** JSON-LD for services and the lakehouse; Turtle 1.2 and TriG for graph stores.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Changes to required fields must be made in the architecture specification §5.0 first; this profile follows it.

## License

Apache License 2.0; see [LICENSE](LICENSE).
