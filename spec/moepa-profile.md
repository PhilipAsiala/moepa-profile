# MOEPA Profile – The MOEPA Record for JSON-LD 1.1 and RDF 1.2 (Draft)

| Field | Value |
| --- | --- |
| **Title** | MOEPA Profile – The MOEPA Record for JSON-LD 1.1 and RDF 1.2 |
| **Status** | **Draft** |
| **Version** | 0.2.0-draft |
| **Date** | 2026-10-19 |
| **Supersedes** | SPOP – A Provenance Profile for RDF 1.2, 0.1.0-draft (2026-07-28) |
| **License** | Apache License 2.0 |
| **Preferred serialization** | JSON-LD 1.1 |
| **Acceptable serializations** | Turtle 1.2 annotation syntax; named graphs (TriG) |
| **Namespace** | `https://schema.asiala.net/moepa/v1#` (prefix `moepa:`) |
| **Context document** | [`context/moepa-v1.jsonld`](../context/moepa-v1.jsonld), to be published at `https://schema.asiala.net/moepa/v1` |
| **Normative base** | [JSON-LD 1.1](https://www.w3.org/TR/json-ld11/), [RDF 1.2 Concepts](https://www.w3.org/TR/rdf12-concepts/), [RDF 1.2 Turtle](https://www.w3.org/TR/rdf12-turtle/) |
| **Specified by** | *The MOEPA Cognitive Architecture — Technical Specification* v3.1, [§5.0 The MOEPA Record](https://github.com/PhilipAsiala/moepa-cognitive-architecture/blob/main/catalog/ARCHITECTURE.md) |

> **DRAFT NOTICE**
> This document is a **Draft** technical specification. It is incomplete, subject to change without notice, and is **not** a W3C Recommendation, ISO standard, or other formal standard. Implementers SHOULD treat all normative language in this document as provisional until a later, non-draft publication explicitly supersedes this status.

---

## 1. Abstract

The **MOEPA profile** defines the **MOEPA record**: one asserted RDF triple together with four envelopes, one for each MOEPA layer other than Ontology — Epistemology (why believe it), Metrology (how good it is now), Axiology (what it may mean for people), and Praxeology (what may be done with it). The triple answers the Ontology question (what is this). The five parts answer five separate questions and are never collapsed into one another.

The profile is a **constrained profile** of [JSON-LD 1.1](https://www.w3.org/TR/json-ld11/) and [RDF 1.2](https://www.w3.org/TR/rdf12-concepts/). It defines a context document, a closed set of envelope properties, the fields that are required on every write, and the serializations in which a record may travel. It is intended for systems that store governed knowledge as an append-only ledger and admit a model's output only after deterministic checks against that ledger: enterprise and public-sector retrieval, knowledge graphs, and the cognitive architecture it is named for.

**The MOEPA profile is a profile, not a competing data model.** It does not redefine RDF's abstract data model or semantics, and it does not redefine JSON-LD processing. Every MOEPA record is one RDF triple plus annotations on that triple. The triple stays a triple.

This profile supersedes the SPOP draft (*Subject–Predicate–Object–Provenance*, 0.1.0-draft). SPOP attached a single provenance record to a triple. The MOEPA record attaches four envelopes, keeps SPOP's source-provenance set inside the Epistemology envelope, and splits SPOP's single confidence into the two numbers the architecture distinguishes. Appendix A maps every SPOP term.

---

## 2. Status of This Document

This document is a **Draft**.

- Sections marked **normative** use the keywords defined in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) and [RFC 8174](https://www.rfc-editor.org/rfc/rfc8174): MUST, MUST NOT, SHOULD, SHOULD NOT, MAY.
- All keywords in this Draft are provisional.
- Where this profile and the architecture specification §5.0 disagree, the architecture specification governs and this profile is in error.
- Feedback is welcome via the project repository issue tracker.

---

## 3. Terminology

| Term | Meaning in this profile |
| --- | --- |
| **MOEPA record** | One asserted triple plus its four envelopes, identified by a stable IRI. Implementations may call it a *quintuple*. |
| **Envelope** | A node attached to the record carrying the properties of one layer: Epistemology, Metrology, Axiology, or Praxeology. |
| **Ontology part** | In JSON-LD, the triple term carried by the record as an `rdf:Statement` node. In RDF 1.2 serializations the triple is stated directly and there is no separate Ontology part. |
| **Record IRI** | The `@id` of the record. In RDF 1.2 it is the reifier of the triple; in the named-graph form it names the graph that holds the triple. |
| **Fingerprint** | The SHA-256 hash of one version of the record, `moepa:cryptographicHash`. |
| **Supersession** | A new record with the same record IRI and a new fingerprint. The newest is the current assertion; older versions stay readable. |
| **Measurement confidence** | `moepa:confidenceScore` (Metrology): how well the assertion survived the quality rules at ingest, and how fresh it is. |
| **Producer trust** | `moepa:sensorTrustLevel` (Epistemology): how much the producing agent or source is believed before any quality rule runs. |
| **Authoritative owner** | The agent entitled to publish the highest-trust version of a logical source document. |
| **Ingesting system** | Any system that derives records from documents it does not authoritatively own. |
| **Source provenance set** | The four Epistemology properties that identify a governing document: `moepa:source`, `moepa:documentVersion`, `moepa:documentType`, `moepa:owner`. |

### 3.1 Layers and document types

The envelopes are named for the layers. Source documents are classified by the layer they speak for:

| Layer | Envelope | Document type (`moepa:documentType`) | Working code | Intended content of the document |
| --- | --- | --- | --- | --- |
| Axiology | `moepa:Axiology` | `moepa:OrgValue` | `org_value` | Mission, principles, values, policy intent, ethical constraints |
| Praxeology | `moepa:Praxeology` | `moepa:Process` | `process` | Procedures, workflows, operating instructions |
| Epistemology | `moepa:Epistemology` | `moepa:Informational` | `informational` | Facts, observations, reports, reference knowledge |
| Metrology | `moepa:Metrology` | — | — | Measurement is about the assertion, not a kind of document |
| Ontology | the triple | — | — | — |

A document's type says what kind of authority it carries. It does not change which envelopes a record derived from it must have: every record has all four.

---

## 4. Relationship to JSON-LD 1.1 and RDF 1.2

### 4.1 Profile, not replacement

A conforming implementation:

1. **MUST** interpret the MOEPA record as a profile of JSON-LD 1.1 and RDF 1.2;
2. **MUST NOT** describe the record as an alternative abstract model to RDF;
3. **MUST** produce, for any record, the same RDF dataset from any of the three serializations in §7, up to blank-node naming;
4. **SHOULD** prefer JSON-LD 1.1 with the published context when producer and consumer both process JSON-LD;
5. **MAY** use Turtle 1.2 annotation syntax or named graphs when the consumer is an RDF store.

### 4.2 Core structure

A MOEPA record consists of:

1. a **record IRI** (`@id`), stable across versions;
2. the **type** `moepa:CognitiveQuintuple`;
3. one **asserted triple** — subject, predicate, object;
4. exactly one **envelope** for each of Epistemology, Metrology, Axiology, and Praxeology.

In RDF 1.2 terms the record IRI is a reifier: `<record> rdf:reifies <<( s p o )>>`, and the envelopes are properties of the reifier. In the named-graph form the record IRI names a graph containing the single triple, and the envelopes are properties of the graph IRI in the default graph. In JSON-LD the record is a node object whose `moepa:Ontology` value is an `rdf:Statement` node carrying the triple term, and whose four envelope properties carry the envelopes. The consumer that materializes the asserted triple into a graph (a projection) does so from the Ontology part; the record itself does not assert the triple in its own graph.

---

## 5. The Record (Normative)

### 5.1 Required on every write

A write that is missing any of the following **MUST** be rejected before it reaches storage. The rejection is itself a Metrology quality-gate event.

| Part | Property | Cardinality | Value | Meaning |
| --- | --- | --- | --- | --- |
| Record | `@id` | 1 | IRI (URN recommended) | Stable identity of the assertion across versions |
| Record | `@type` | 1 | `moepa:CognitiveQuintuple` | |
| Ontology | `moepa:Subject` | 1 | IRI | Subject of the asserted triple |
| Ontology | `moepa:Predicate` | 1 | IRI | Predicate of the asserted triple; a compact IRI in the `moepa:` prefix expands against the namespace |
| Ontology | `moepa:Object` | 1 | IRI or literal | Object of the asserted triple |
| Epistemology | `prov:wasGeneratedBy` | 1 | IRI | Agent or activity that produced this version |
| Epistemology | `prov:generatedAtTime` | 1 | `xsd:dateTime` | When this version was produced |
| Epistemology | `moepa:cryptographicHash` | 1 | string `sha256-<hex>` | Fingerprint of this version |
| Metrology | `moepa:confidenceScore` | 1 | decimal in [0.0, 1.0] | Measurement confidence after quality rules |
| Metrology | `moepa:validUntil` | 1 | `xsd:dateTime` | End of validity window |
| Axiology | `moepa:classificationLevel` | 1 | non-empty string | Classification marking from the organization's ladder |
| Praxeology | `moepa:allowedActions` | 1 (set, may be empty) | strings | Action classes a caller may take |
| Praxeology | `moepa:deniedActions` | 1 (set, may be empty) | strings | Action classes a caller may not take |

In RDF 1.2 serializations the three Ontology properties are not used; the triple is stated directly, and the record IRI reifies it or names its graph.

### 5.2 Optional properties

| Part | Property | Value | Meaning |
| --- | --- | --- | --- |
| Record | `moepa:tenantId` | string | Tenant this record belongs to; stamped by the ingesting system, never set by the author (§5.5) |
| Epistemology | `moepa:sensorTrustLevel` | decimal in [0.0, 1.0] | Producer trust (§6) |
| Epistemology | `moepa:source` | IRI | Governing source document (source provenance set, §6.2) |
| Epistemology | `moepa:documentVersion` | literal | Version of that document |
| Epistemology | `moepa:documentType` | `moepa:OrgValue` \| `moepa:Process` \| `moepa:Informational` | Which layer the document speaks for |
| Epistemology | `moepa:owner` | IRI | Authoritative owner of that document |
| Metrology | `moepa:decayRate` | string | How quickly the assertion loses currency |
| Axiology | `moepa:containsPII` | boolean | |
| Axiology | `moepa:missionAlignment` | string | |
| Axiology | `moepa:valueWeight` | integer | A tradeoff input, not a gate |
| Praxeology | `moepa:executionTrigger` | IRI | Procedure or hook invoked when an allowed action is taken |

A consumer **MUST** ignore properties it does not recognize rather than reject the record, except that any value of `moepa:documentType` outside the closed set in §3.1 **MUST** be flagged as non-conformant.

### 5.3 Supersession

A record **MUST NOT** be updated in place. A change is a new record with the same `@id`, a new `moepa:cryptographicHash`, and a new `prov:generatedAtTime`. A write whose `@id` and fingerprint both match the newest stored version **MUST NOT** be appended. Consumers that key derived artifacts (projections, embeddings) to a record **SHOULD** key them to the fingerprint, so a superseding version is re-derived and an unchanged one is reused.

### 5.4 Keep the parts separate

A probability about the assertion **MUST NOT** be written into the Ontology part. A permission **MUST NOT** be derived from a confidence. The classification marking is a fact on the record; the rule that compares a caller's clearance to it is policy and is not part of the record. The envelopes are the data those rules read; they are not the rules.

### 5.5 Tenancy

A record **MAY** carry `moepa:tenantId`, a string naming the tenant the record belongs to. The tenant is the logical partition of the substrate that holds the record: two tenants' records are separate holdings, even when they assert the same triple under the same `@id`.

The tenant is a fact the system establishes, not a claim the author makes. The ingesting system **MUST** set the value from the authenticated identity of the submitter, resolved against the organization's identity, tenant, and clearance registry (DS8 in the capability catalog), and **MUST** overwrite any `moepa:tenantId` present in a submitted record before the record reaches storage. This is the ingest-side form of a rule the architecture states for reads: identity is a stored fact that is looked up, not a value the caller asserts.

Supersession (§5.3) applies within a tenant. A write matches a stored version only when `@id`, fingerprint, and tenant all match; a write whose `@id` and fingerprint match a record of another tenant **MUST** be appended as the submitting tenant's own record. Consumers **MUST** scope deduplication, projection, and retrieval by tenant, so one tenant's records are never returned as another tenant's results.

A record without `moepa:tenantId` is unscoped. Which tenant, if any, an unscoped record belongs to is a deployment decision; a consumer **MUST NOT** infer a tenant from a record's content.

### 5.6 Curation stage

The architecture specification (§5.2) stages definitions by curation: bronze (raw ingest, kept for lineage), silver (human-enriched, provisional), and gold (reviewed, versioned, authoritative). The profile defines no property for that stage. The stage is a fact about where a record is held and how it got there, not a claim the record makes about itself, so it lives in the holding system (a staging table, a promotion ledger) and never in the record.

A producer **MUST NOT** be able to assert a stage by writing it into a record. A consumer **MUST NOT** read a stage from a record's content, and **MUST** ignore any stage-like property in a submitted record (§5.2), the same way a submitted `moepa:tenantId` is overwritten (§5.5). The ingesting system sets the stage from its own configuration: records submitted for ingest are candidates at bronze, and only a recorded human promotion makes a record gold. A consumer that evaluates records for a decision **MUST** read gold only.

The record has this shape at every stage, and one version carries one fingerprint through all of them: the same version staged at bronze, reviewed to silver, and promoted to gold is the same `moepa:cryptographicHash`. A human edit made during review is a new version and needs a new fingerprint (§5.3). A write whose `@id` and fingerprint match a known version, in a stage or in gold, but whose content differs **SHOULD** be rejected rather than skipped, so the edit is not lost without trace.

---

## 6. Confidence Policy

### 6.1 Two numbers, two envelopes

SPOP carried one `confidence`. The MOEPA record carries two, and a consumer **MUST NOT** treat them as interchangeable:

- **`moepa:confidenceScore`** (Metrology, required): how well this assertion survived the quality rules at ingest and how fresh it still is. It is a property of the measurement. It is **not** reduced because of who published it.
- **`moepa:sensorTrustLevel`** (Epistemology, optional): how much the producing agent or source is believed before any quality rule runs. It is a property of the producer. The non-authoritative ingest reduction in §6.3 applies to this number.

A policy or routing rule that reads a confidence **MUST** name which one it reads.

### 6.2 Source provenance set

When a record is derived from a governing document, the Epistemology envelope **MUST** carry all four of `moepa:source`, `moepa:documentVersion`, `moepa:documentType`, and `moepa:owner`. The four are given together or not at all; a partial set is non-conformant. Records produced by a service or agent rather than extracted from a document (for example an access request) carry none of them, and `prov:wasGeneratedBy` names the producer.

### 6.3 Non-authoritative ingest reduction (normative guidance)

When the **ingesting system is not the authoritative owner** of the source document, the published producer trust **SHOULD** be reduced by 10% relative to the system's internal pre-publication estimate:

```text
published sensorTrustLevel = internal trust × 0.9
```

**Rationale.** The reduction leaves headroom so the true owner can later publish a higher-trust version of the same logical document without being capped by a non-owner's prior assertion.

1. If the product would exceed 1.0 the result **MUST** be clamped to 1.0.
2. Authoritative owners **MAY** publish producer trust without the factor.
3. Enterprises **MAY** document a stricter local policy but **SHOULD NOT** omit the reduction when the publisher is non-authoritative.
4. The reduction **MUST NOT** be applied to `moepa:confidenceScore`.

---

## 7. Serialization

### 7.1 Preferred: JSON-LD 1.1

The record is a JSON-LD 1.1 node object using the published context. Keys are compact IRIs in the `moepa:` and `prov:` prefixes exactly as shown; the context supplies type coercion so that IRIs, dates, decimals, and sets expand correctly.

```json
{
  "@context": "https://schema.asiala.net/moepa/v1",
  "@type": "CognitiveQuintuple",
  "@id": "urn:uuid:84a5b2c9-1738-4e1a-9f5b-6d7e8a9b0c1d",
  "moepa:Ontology": {
    "@type": "rdf:Statement",
    "moepa:Subject": "urn:user:phil_a",
    "moepa:Predicate": "moepa:requestedInferenceOn",
    "moepa:Object": "urn:dataset:financial_q3_report"
  },
  "moepa:Epistemology": {
    "@type": "prov:Entity",
    "prov:wasGeneratedBy": "urn:service:assembly",
    "prov:generatedAtTime": "2026-10-08T12:57:00Z",
    "moepa:cryptographicHash": "sha256-a7c9f8e2",
    "moepa:sensorTrustLevel": 0.99
  },
  "moepa:Metrology": {
    "moepa:confidenceScore": 0.98,
    "moepa:decayRate": "High",
    "moepa:validUntil": "2026-10-09T12:57:00Z"
  },
  "moepa:Axiology": {
    "moepa:classificationLevel": "CUI",
    "moepa:containsPII": false,
    "moepa:missionAlignment": "Data Auditing",
    "moepa:valueWeight": 85
  },
  "moepa:Praxeology": {
    "moepa:allowedActions": ["Read", "Encrypt", "RouteToModel"],
    "moepa:deniedActions": ["ExportToPublicStorage", "UnencryptedTransmission"],
    "moepa:executionTrigger": "urn:webhook:log_access_event"
  }
}
```

**Context.** Until `https://schema.asiala.net/moepa/v1` is published, a processor **MUST** resolve that IRI to [`context/moepa-v1.jsonld`](../context/moepa-v1.jsonld) in this repository. A producer **MAY** inline a context, but an inlined context that supplies only prefixes (no type coercion) yields string-valued subjects and untyped dates on expansion; such a document is conformant as JSON but **SHOULD** be expanded against the published context before it is validated or projected.

**Why the Ontology part is an `rdf:Statement`.** JSON-LD 1.1 has no syntax for an RDF 1.2 triple term. The record carries the triple as a statement node so that the record IRI remains the one node the envelopes hang from, and a consumer materializes the asserted triple into its graph projection. This is the JSON-LD counterpart of the reifier in §7.2 and the graph name in §7.3.

### 7.2 Acceptable: Turtle 1.2 annotation syntax

The asserted triple is stated once; the record IRI is the reifier (`~`) and carries the envelopes as blank nodes.

```turtle
VERSION "1.2"
PREFIX moepa: <https://schema.asiala.net/moepa/v1#>
PREFIX prov:  <http://www.w3.org/ns/prov#>
PREFIX xsd:   <http://www.w3.org/2001/XMLSchema#>

<urn:user:phil_a> moepa:requestedInferenceOn <urn:dataset:financial_q3_report>
  ~ <urn:uuid:84a5b2c9-1738-4e1a-9f5b-6d7e8a9b0c1d>
  {|
    a moepa:CognitiveQuintuple ;
    moepa:Epistemology [ a prov:Entity ;
        prov:wasGeneratedBy <urn:service:assembly> ;
        prov:generatedAtTime "2026-10-08T12:57:00Z"^^xsd:dateTime ;
        moepa:cryptographicHash "sha256-a7c9f8e2" ;
        moepa:sensorTrustLevel 0.99 ] ;
    moepa:Metrology  [ moepa:confidenceScore 0.98 ; moepa:decayRate "High" ;
        moepa:validUntil "2026-10-09T12:57:00Z"^^xsd:dateTime ] ;
    moepa:Axiology   [ moepa:classificationLevel "CUI" ; moepa:containsPII false ;
        moepa:missionAlignment "Data Auditing" ; moepa:valueWeight 85 ] ;
    moepa:Praxeology [ moepa:allowedActions "Read", "Encrypt", "RouteToModel" ;
        moepa:deniedActions "ExportToPublicStorage", "UnencryptedTransmission" ;
        moepa:executionTrigger <urn:webhook:log_access_event> ]
  |} .
```

Full example: [`examples/basic.ttl`](../examples/basic.ttl).

### 7.3 Acceptable: named graphs

The record IRI names a graph holding the single asserted triple; the envelopes are stated about the graph IRI in the default graph. This is the form a graph projection naturally holds, and the form the earlier SPOP draft called "TriG / N-Quads style".

```turtle
<urn:uuid:84a5b2c9-1738-4e1a-9f5b-6d7e8a9b0c1d> {
  <urn:user:phil_a> moepa:requestedInferenceOn <urn:dataset:financial_q3_report> .
}
<urn:uuid:84a5b2c9-1738-4e1a-9f5b-6d7e8a9b0c1d>
    a moepa:CognitiveQuintuple ;
    moepa:Epistemology [ ... ] ; moepa:Metrology [ ... ] ;
    moepa:Axiology [ ... ] ; moepa:Praxeology [ ... ] .
```

Full example: [`examples/basic.trig`](../examples/basic.trig). A projection **MAY** instead place all current asserted triples in one named graph (for example `urn:moepa:assertions`) and keep the envelopes in the record store; that is a projection layout, not a record serialization, and it **MUST** be rebuildable from the records.

---

## 8. Examples

| File | Shows |
| --- | --- |
| [`examples/basic.jsonld`](../examples/basic.jsonld) | A service-generated record (an access request); no source provenance set |
| [`examples/org-value.jsonld`](../examples/org-value.jsonld) | A record derived from an `OrgValue` document, owner is authoritative |
| [`examples/process.jsonld`](../examples/process.jsonld) | A record derived from a `Process` document with an execution trigger |
| [`examples/informational-non-authoritative.jsonld`](../examples/informational-non-authoritative.jsonld) | Non-authoritative ingest: internal trust 0.90 published as `sensorTrustLevel` 0.81 while `confidenceScore` is unreduced |
| [`examples/basic.ttl`](../examples/basic.ttl) | The basic record in Turtle 1.2 annotation syntax |
| [`examples/basic.trig`](../examples/basic.trig) | The basic record as a named graph |

---

## 9. Conformance

A document is **MOEPA-profile-conformant (Draft)** if all of the following hold:

1. It expands (JSON-LD) or parses (RDF 1.2, TriG) to a dataset in which every `moepa:CognitiveQuintuple` node has a stable IRI and exactly one of each of the four envelopes.
2. Every property in §5.1 is present with the stated cardinality and value type; in JSON-LD the Ontology part is present and complete.
3. `moepa:confidenceScore` and, if present, `moepa:sensorTrustLevel` are in [0.0, 1.0].
4. `moepa:documentType`, if present, is one of the three closed values, and the source provenance set is complete or absent (§6.2).
5. `moepa:cryptographicHash` has the form `sha256-<hex>`.
6. The same record produced in two of the serializations in §7 yields the same RDF dataset up to blank-node naming.

**Draft SHACL shapes** for automated checking of items 1–5 are in [`moepa-shape.ttl`](moepa-shape.ttl). They are informative until this profile exits Draft status. Because an empty set serializes as no triples, SHACL cannot check the *presence* of `moepa:allowedActions` and `moepa:deniedActions`; a JSON-LD validator checks for the keys.

---

## 10. Out of Scope (Draft)

This Draft does not specify:

- the clearance ladder or the clearance-versus-marking rule (policy, outside the record);
- conflict resolution among competing versions beyond fingerprint supersession;
- trust frameworks or PKI for owners and producers; signing of records;
- the decision record written when a record is read or acted on;
- query language extensions.

---

## 11. Security and Governance Considerations (Informative)

- Envelope properties can themselves be sensitive (owner, source paths, classification). Apply access control appropriate to the context.
- Neither confidence is cryptographic proof of correctness. The fingerprint proves which version was read; it does not prove the version is true.
- The Praxeology envelope supplies action classes to a policy engine. The engine, not the envelope, decides; a denied class is never overridden by either confidence.
- The non-authoritative reduction reduces, but does not eliminate, the risk of overstated third-party trust.

---

## 12. References

### Normative (for the profile base)

- JSON-LD 1.1, W3C Recommendation. https://www.w3.org/TR/json-ld11/
- RDF 1.2 Concepts and Abstract Data Model, W3C. https://www.w3.org/TR/rdf12-concepts/
- RDF 1.2 Turtle, W3C. https://www.w3.org/TR/rdf12-turtle/
- Key words for use in RFCs to Indicate Requirement Levels, RFC 2119 / RFC 8174

### Informative

- *The MOEPA Cognitive Architecture — Technical Specification* v3.1, §5.0 The MOEPA Record, §5.0.1 System of Record and Projections. https://github.com/PhilipAsiala/moepa-cognitive-architecture
- PROV-O: The PROV Ontology, W3C Recommendation. https://www.w3.org/TR/prov-o/
- DCMI Metadata Terms. https://www.dublincore.org/specifications/dublin-core/dcmi-terms/
- Shapes Constraint Language (SHACL), W3C Recommendation. https://www.w3.org/TR/shacl/

---

## Appendix A. Migration from SPOP 0.1.0-draft (Informative)

| SPOP term (`https://w3id.org/spop/ns#`) | MOEPA term (`https://schema.asiala.net/moepa/v1#`) | Envelope | Note |
| --- | --- | --- | --- |
| `spop:source` | `moepa:source` | Epistemology | Now part of the source provenance set, required only for document-derived records |
| `spop:documentVersion` | `moepa:documentVersion` | Epistemology | Same |
| `spop:documentType` | `moepa:documentType` | Epistemology | Same closed set; values `moepa:OrgValue`, `moepa:Process`, `moepa:Informational` |
| `spop:owner` | `moepa:owner` | Epistemology | Same |
| `spop:confidence` | `moepa:sensorTrustLevel` | Epistemology | The × 0.9 ownership rule follows this term. Measurement confidence is the new, required `moepa:confidenceScore` |
| `spop:extractedAt` | `prov:generatedAtTime` | Epistemology | Required; the PROV term is used directly |
| — | `prov:wasGeneratedBy`, `moepa:cryptographicHash` | Epistemology | New, required |
| — | `moepa:confidenceScore`, `moepa:validUntil` | Metrology | New, required |
| — | `moepa:classificationLevel` | Axiology | New, required |
| — | `moepa:allowedActions`, `moepa:deniedActions` | Praxeology | New, required (may be empty) |
| reifier or graph with one flat provenance record | record IRI with four envelopes | — | Envelopes are blank nodes (RDF) or nested objects (JSON-LD) |
| Turtle 1.2 preferred | JSON-LD 1.1 preferred | — | Turtle 1.2 and TriG remain acceptable |

A SPOP document converts to a MOEPA record by moving its six properties into the Epistemology envelope under the names above, and adding the required Metrology, Axiology, and Praxeology fields from the ingest pipeline that produced it. The conversion is not information-preserving in reverse: the new required fields have no SPOP home.

---

## 13. Document History

| Version | Date | Notes |
| --- | --- | --- |
| 0.2.0-draft | 2026-10-19 | Renamed from SPOP to the MOEPA profile. JSON-LD 1.1 preferred; published context; the record is one triple plus four layer envelopes per architecture §5.0; two confidences; source provenance set optional unless document-derived; supersession by fingerprint; SHACL rewritten; SPOP migration appendix |
| 0.1.0-draft | 2026-07-28 | SPOP initial public Draft: core definition, minimum provenance set, confidence policy, examples |

---

*End of Draft specification.*
