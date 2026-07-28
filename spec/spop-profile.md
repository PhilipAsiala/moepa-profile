# SPOP – A Provenance Profile for RDF 1.2 (Draft)

| Field | Value |
| --- | --- |
| **Title** | SPOP – A Provenance Profile for RDF 1.2 |
| **Status** | **Draft** |
| **Version** | 0.1.0-draft |
| **Date** | 2026-07-28 |
| **License** | Apache License 2.0 |
| **Preferred serialization** | Turtle 1.2 |
| **Normative base** | [RDF 1.2 Concepts](https://www.w3.org/TR/rdf12-concepts/), [RDF 1.2 Turtle](https://www.w3.org/TR/rdf12-turtle/) |

> **DRAFT NOTICE**  
> This document is a **Draft** technical specification. It is incomplete, subject to change without notice, and is **not** a W3C Recommendation, ISO standard, or other formal standard. Implementers SHOULD treat all normative language in this document as provisional until a later, non-draft publication explicitly supersedes this status.

---

## 1. Abstract

**SPOP** (*Subject–Predicate–Object–Provenance*) is a **constrained profile** of [RDF 1.2](https://www.w3.org/TR/rdf12-concepts/) and [Turtle 1.2](https://www.w3.org/TR/rdf12-turtle/). It defines a minimum set of provenance properties that MUST accompany asserted knowledge in systems that require:

- clear document identity and versioning;
- document-type governance;
- ownership attribution; and
- numerical confidence scoring.

SPOP is intended for enterprise retrieval-augmented generation (RAG), knowledge graphs, and cognitive architectures where statements must remain traceable to governing source documents.

**SPOP is a profile of RDF 1.2, not a competing standard.** It does not redefine RDF’s abstract data model, semantics, or core vocabularies. It only constrains how provenance is attached and which provenance properties are required for SPOP-conformant data.

---

## 2. Status of This Document

This document is a **Draft**.

- Sections marked **normative** use the keywords defined in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) and [RFC 8174](https://www.rfc-editor.org/rfc/rfc8174): MUST, MUST NOT, SHOULD, SHOULD NOT, MAY.
- All keywords in this Draft are provisional.
- Feedback is welcome via the project repository issue tracker.

---

## 3. Terminology and Layer Mapping

### 3.1 Working Terms (Normative for This Profile)

SPOP classifies source documents into three **document types**. The following philosophical layer names are acknowledged **once** for orientation; the remainder of this specification uses only the working terms.

| Philosophical layer (acknowledged once) | Working term | Code / value | Intended content |
| --- | --- | --- | --- |
| Axiology | **Organization Value Documents** | `org_value` | Mission, principles, values, policy intent, ethical constraints |
| Praxeology | **Process Documents** | `process` | Procedures, workflows, operating instructions, how-to governance |
| Epistemology | **Informational Documents** | `informational` | Facts, observations, reports, reference knowledge, situational data |

After this section, this document uses **only**:

- `org_value`
- `process`
- `informational`

and the corresponding working names: *Organization Value Documents*, *Process Documents*, and *Informational Documents*.

### 3.2 Other Terms

| Term | Meaning in this profile |
| --- | --- |
| **SPOP statement** | An RDF triple (or RDF 1.2 triple term) together with required provenance |
| **Provenance record** | The set of required provenance properties attached to a statement or graph |
| **Authoritative owner** | The agent entitled to publish the highest-confidence version of a logical document |
| **Ingesting system** | Any system that extracts or asserts SPOP statements from documents it does not authoritatively own |
| **Logical document** | A stable identity for a document across versions (e.g., a document IRI independent of version string) |

---

## 4. Relationship to RDF 1.2

### 4.1 Profile, Not Replacement

SPOP:

1. **MUST** be interpreted as a **profile** of RDF 1.2;
2. **MUST NOT** be described as an alternative abstract model to RDF;
3. **MAY** be implemented with any RDF 1.2–compatible stack that supports either triple-term / annotation syntax or named graphs;
4. **SHOULD** prefer Turtle 1.2 annotation syntax when both producer and consumer support RDF 1.2 Turtle.

### 4.2 Core Structure

A SPOP statement consists of:

1. **Subject – Predicate – Object** — a standard RDF triple, or an RDF 1.2 triple term when reification is required; and  
2. **Provenance** — attached using one of:

   - **Preferred:** RDF 1.2 Turtle **annotation syntax** (asserted triple + reifier + annotation block); or  
   - **Acceptable:** **named graphs** (TriG / N-Quads style), where the named graph carries the provenance record for the triples it contains.

Both attachment styles are SPOP-conformant when the required provenance properties are present and correctly valued.

---

## 5. Required Provenance Properties (Minimum Set)

The following properties constitute the **minimum provenance record** for SPOP-conformant data.

| # | Property (SPOP term) | Cardinality | Value type | Description |
| --- | --- | --- | --- | --- |
| 1 | `spop:source` | exactly 1 | IRI | Document identity (logical document or concrete source resource) |
| 2 | `spop:documentVersion` | exactly 1 | literal (string or typed) | Version of the source document |
| 3 | `spop:documentType` | exactly 1 | one of the three typed values | `spop:OrgValue`, `spop:Process`, or `spop:Informational` (serializations MAY use the short codes `org_value`, `process`, `informational` where a closed enumeration is used) |
| 4 | `spop:owner` | exactly 1 | IRI | Agent or organization that owns the source document |
| 5 | `spop:confidence` | exactly 1 | `xsd:decimal` or `xsd:double` in range **[0.0, 1.0]** | Confidence in the statement as published |
| 6 | `spop:extractedAt` | exactly 1 | `xsd:dateTime` | Timestamp when the statement was extracted or created in the publishing system |

### 5.1 Recommended Alignments (Informative)

Implementers SHOULD align or dual-assert with existing vocabularies where practical:

| SPOP property | Suggested alignment |
| --- | --- |
| `spop:source` | `prov:wasDerivedFrom`, `dcterms:source` |
| `spop:owner` | `dcterms:publisher`, `prov:wasAttributedTo` |
| `spop:extractedAt` | `prov:generatedAtTime`, `dcterms:created` |
| `spop:documentVersion` | `dcterms:hasVersion` (on the source document resource) |

These alignments are **informative**. Conformance is defined by the presence and correct typing of the SPOP minimum set.

### 5.2 Document Type Values

| Working code | Vocabulary IRI (recommended) | Label |
| --- | --- | --- |
| `org_value` | `spop:OrgValue` | Organization Value Document |
| `process` | `spop:Process` | Process Document |
| `informational` | `spop:Informational` | Informational Document |

A SPOP consumer MUST reject or flag as non-conformant any document type value outside this closed set unless an explicit, versioned extension profile is declared.

---

## 6. Confidence Policy

### 6.1 Range

`spop:confidence` MUST be a number *c* such that:

```text
0.0 ≤ c ≤ 1.0
```

### 6.2 Non-Authoritative Ingest Reduction (Normative Guidance)

When the **ingesting system is not the authoritative owner** of the source document, the published confidence **SHOULD** be reduced by **10%** relative to the system’s internal pre-publication score:

```text
published_confidence = internal_confidence × 0.9
```

**Rationale.** The reduction leaves headroom so that the true owner can later publish a higher-confidence version of the same logical document without being capped by a non-owner’s prior assertion.

**Notes:**

1. If `internal_confidence × 0.9` would exceed 1.0, the result MUST be clamped to 1.0 (in practice this only arises if internal scores already violate the range).
2. Authoritative owners MAY publish confidence without applying the 0.9 factor.
3. Systems MAY record both `spop:confidence` (published) and an optional internal score; only `spop:confidence` is required for SPOP conformance.
4. This rule is a **policy SHOULD**: enterprises MAY document a stricter local policy (e.g., larger reduction) but SHOULD NOT omit reduction entirely when the publisher is non-authoritative.

---

## 7. Serialization

### 7.1 Preferred: Turtle 1.2 Annotation Syntax

Preferred form (asserted triple + provenance on the reifier):

```turtle
VERSION "1.2"
PREFIX ex:   <https://example.example/ns#>
PREFIX spop: <https://w3id.org/spop/ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>

ex:PolicyA ex:requiresReview true {|
    spop:source          <https://example.example/docs/code-of-conduct> ;
    spop:documentVersion "2.1" ;
    spop:documentType    spop:OrgValue ;
    spop:owner           <https://example.example/org/ethics-office> ;
    spop:confidence      0.81 ;
    spop:extractedAt     "2026-07-28T14:30:00Z"^^xsd:dateTime
|} .
```

Equivalent expansion (informative):

```turtle
VERSION "1.2"
PREFIX ex:   <https://example.example/ns#>
PREFIX spop: <https://w3id.org/spop/ns#>
PREFIX rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>

ex:PolicyA ex:requiresReview true .
_:p1 rdf:reifies <<( ex:PolicyA ex:requiresReview true )>> .
_:p1 spop:source          <https://example.example/docs/code-of-conduct> ;
     spop:documentVersion "2.1" ;
     spop:documentType    spop:OrgValue ;
     spop:owner           <https://example.example/org/ethics-office> ;
     spop:confidence      0.81 ;
     spop:extractedAt     "2026-07-28T14:30:00Z"^^xsd:dateTime .
```

### 7.2 Acceptable: Named Graphs

```turtle
PREFIX ex:   <https://example.example/ns#>
PREFIX spop: <https://w3id.org/spop/ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>

# TriG-style illustration
ex:g1 {
  ex:PolicyA ex:requiresReview true .
}

ex:g1 spop:source          <https://example.example/docs/code-of-conduct> ;
      spop:documentVersion "2.1" ;
      spop:documentType    spop:OrgValue ;
      spop:owner           <https://example.example/org/ethics-office> ;
      spop:confidence      0.81 ;
      spop:extractedAt     "2026-07-28T14:30:00Z"^^xsd:dateTime .
```

When using named graphs, the provenance record applies to the **graph** (or to each triple by graph membership). Producers SHOULD document whether confidence is statement-level or graph-level.

---

## 8. Concrete Examples

### 8.1 Organization Value Document (`org_value`)

```turtle
VERSION "1.2"
PREFIX ex:   <https://example.example/ns#>
PREFIX spop: <https://w3id.org/spop/ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>

ex:Org ex:coreValue "Safety before schedule" {|
    spop:source          <https://example.example/docs/values-charter> ;
    spop:documentVersion "1.0" ;
    spop:documentType    spop:OrgValue ;
    spop:owner           <https://example.example/org/board> ;
    spop:confidence      0.95 ;
    spop:extractedAt     "2026-07-01T09:00:00Z"^^xsd:dateTime
|} .
```

### 8.2 Process Document (`process`)

```turtle
VERSION "1.2"
PREFIX ex:   <https://example.example/ns#>
PREFIX spop: <https://w3id.org/spop/ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>

ex:IncidentResponse ex:step "Escalate to on-call within 15 minutes" {|
    spop:source          <https://example.example/docs/sop-incident-response> ;
    spop:documentVersion "3.4.1" ;
    spop:documentType    spop:Process ;
    spop:owner           <https://example.example/org/sre> ;
    spop:confidence      0.90 ;
    spop:extractedAt     "2026-07-15T16:45:00Z"^^xsd:dateTime
|} .
```

### 8.3 Informational Document (`informational`)

```turtle
VERSION "1.2"
PREFIX ex:   <https://example.example/ns#>
PREFIX spop: <https://w3id.org/spop/ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>

ex:Facility42 ex:headcount 1280 {|
    spop:source          <https://example.example/docs/hr-snapshot-2026-q2> ;
    spop:documentVersion "2026-Q2" ;
    spop:documentType    spop:Informational ;
    spop:owner           <https://example.example/org/hr-analytics> ;
    spop:confidence      0.72 ;
    spop:extractedAt     "2026-07-20T11:10:00Z"^^xsd:dateTime
|} .
```

### 8.4 Non-Authoritative Ingest (Confidence × 0.9)

An ingesting system estimates internal confidence `0.90` for a fact extracted from a document owned by another organization. Published confidence SHOULD be `0.81`:

```turtle
VERSION "1.2"
PREFIX ex:   <https://example.example/ns#>
PREFIX spop: <https://w3id.org/spop/ns#>
PREFIX xsd:  <http://www.w3.org/2001/XMLSchema#>

ex:SupplierX ex:isoCertified true {|
    spop:source          <https://partner.example/docs/quality-statement> ;
    spop:documentVersion "2025.08" ;
    spop:documentType    spop:Informational ;
    spop:owner           <https://partner.example/org> ;
    # internal 0.90 × 0.9 = 0.81 (ingestor is not the owner)
    spop:confidence      0.81 ;
    spop:extractedAt     "2026-07-28T08:00:00Z"^^xsd:dateTime
|} .
```

Additional machine-readable examples are provided under [`/examples`](../examples/).

---

## 9. Conformance

A dataset or document is **SPOP-conformant (Draft)** if all of the following hold:

1. Every SPOP-managed statement is a valid RDF 1.2 triple (or is carried as an RDF 1.2 triple term with reification as defined by RDF 1.2).
2. Provenance is attached via annotation syntax / reifiers **or** named graphs.
3. Each SPOP-managed statement (or its governing named graph, under a documented graph-level policy) has all six minimum provenance properties.
4. `spop:documentType` is one of the three allowed values.
5. `spop:confidence` is in `[0.0, 1.0]`.
6. `spop:extractedAt` is a valid `xsd:dateTime`.

**Draft SHACL shapes** for partial automated checking are provided in [`spop-shape.ttl`](spop-shape.ttl). Those shapes are informative until this profile exits Draft status.

---

## 10. Out of Scope (Draft)

This Draft does not yet specify:

- conflict resolution among competing confidence values;
- mandatory trust frameworks or PKI for owners;
- full ontology of document lifecycle states;
- query language extensions;
- RDF Dataset profile beyond the named-graph option above.

---

## 11. Security and Governance Considerations (Informative)

- Provenance properties can themselves be sensitive (owner, source paths). Apply access control appropriate to the enterprise or government context.
- Confidence scores MUST NOT be treated as cryptographic proof of correctness.
- Document-type governance (`org_value` vs `process` vs `informational`) is intended to support policy engines (e.g., higher scrutiny for process claims used in automated actions).
- Non-authoritative ingest reduction reduces—but does not eliminate—risk of overstated third-party confidence.

---

## 12. References

### Normative (for the profile base)

- RDF 1.2 Concepts and Abstract Data Model, W3C. https://www.w3.org/TR/rdf12-concepts/
- RDF 1.2 Turtle, W3C. https://www.w3.org/TR/rdf12-turtle/
- Key words for use in RFCs to Indicate Requirement Levels, RFC 2119 / RFC 8174

### Informative

- PROV-O: The PROV Ontology, W3C Recommendation. https://www.w3.org/TR/prov-o/
- DCMI Metadata Terms. https://www.dublincore.org/specifications/dublin-core/dcmi-terms/
- Shapes Constraint Language (SHACL), W3C Recommendation. https://www.w3.org/TR/shacl/

---

## 13. Document History

| Version | Date | Notes |
| --- | --- | --- |
| 0.1.0-draft | 2026-07-28 | Initial public Draft: core definition, minimum provenance set, confidence policy, examples |

---

*End of Draft specification.*
