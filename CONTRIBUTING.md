# Contributing to SPOP (Draft)

Thank you for helping improve this Draft profile. SPOP is intended to stay a **thin profile of RDF 1.2**, not a large new ontology.

## Principles

1. **Profile, not competitor.** Changes must remain compatible with RDF 1.2 Concepts and Turtle 1.2.
2. **Minimal vocabulary.** Prefer PROV-O, Dublin Core, and existing RDF terms before adding SPOP-specific terms.
3. **Working terms only.** After the single layer-mapping section in the spec, use `org_value`, `process`, and `informational` (and their vocabulary IRIs)—not philosophical layer names.
4. **Enterprise clarity.** Prefer precise, auditable language over informal phrasing.
5. **Mark Draft material.** Do not remove Draft status banners until maintainers agree the document is ready for a non-draft release.

## How to contribute

1. Open an issue describing the problem or proposal.
2. Keep pull requests focused (one concern per PR when practical).
3. Update examples and SHACL if you change required properties or value spaces.
4. Update `CHANGELOG.md` under an `[Unreleased]` or new Draft version section.
5. Do not expand scope into full trust frameworks, PKI, or query languages unless agreed in an issue.

## Spec editing guidance

- Normative keywords: MUST, MUST NOT, SHOULD, SHOULD NOT, MAY (RFC 2119 / 8174).
- Keep the confidence policy (`× 0.9` for non-authoritative ingest) unless a tracked issue revises it with rationale.
- Prefer Turtle 1.2 annotation examples; named-graph examples remain welcome as the acceptable alternative.

## License

By contributing, you agree that your contributions are licensed under the Apache License 2.0 (see [LICENSE](./LICENSE)).
