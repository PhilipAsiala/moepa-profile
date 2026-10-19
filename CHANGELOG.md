# Changelog

All notable changes to the MOEPA profile Draft will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project aims to follow [Semantic Versioning](https://semver.org/) once it leaves Draft status.

## [0.2.0-draft] – 2026-10-19

### Changed

- Renamed from **SPOP** (`spop-profile`) to the **MOEPA profile** (`moepa-profile`). Namespace moves from `https://w3id.org/spop/ns#` to `https://schema.asiala.net/moepa/v1#`.
- The unit is now the **MOEPA record**: one triple plus Epistemology, Metrology, Axiology, and Praxeology envelopes, as specified in *The MOEPA Cognitive Architecture — Technical Specification* v3.1 §5.0. SPOP's flat provenance record becomes the source provenance set inside the Epistemology envelope.
- **JSON-LD 1.1 is the preferred serialization.** Turtle 1.2 annotation syntax and named graphs (TriG) remain acceptable.
- SPOP's single `confidence` is split: `moepa:confidenceScore` (Metrology, required, measurement confidence) and `moepa:sensorTrustLevel` (Epistemology, optional, producer trust). The × 0.9 non-authoritative ingest rule now applies to producer trust only.
- `spop:extractedAt` is replaced by `prov:generatedAtTime` used directly.
- Source provenance (`source`, `documentVersion`, `documentType`, `owner`) is required only for document-derived records, and is all-or-nothing.
- SHACL shapes rewritten to target `moepa:CognitiveQuintuple` with one sub-shape per envelope.

### Added

- `context/moepa-v1.jsonld`: JSON-LD 1.1 context with type coercions for IRIs, dates, decimals, and sets.
- Required fields: `prov:wasGeneratedBy`, `moepa:cryptographicHash`, `moepa:confidenceScore`, `moepa:validUntil`, `moepa:classificationLevel`, `moepa:allowedActions`, `moepa:deniedActions`.
- Append-only supersession by `@id` and fingerprint (§5.3).
- JSON-LD examples for a service-generated record and for each document type; the same record in Turtle 1.2 and TriG.
- Appendix A: term-by-term migration from SPOP.

### Removed

- The `spop:` vocabulary and the three separate Turtle examples by document type.

### Notes

- Source: the record shape and required fields are those written and validated by the running `moepa-lab` stack; its sample records conform to these shapes when expanded against the published context.
- Status remains **Draft**; no stability guarantees.

## [0.1.0-draft] – 2026-07-28

### Added

- Initial SPOP Draft specification, vocabulary, SHACL shapes, Turtle examples, README, CONTRIBUTING, Apache-2.0 LICENSE.
