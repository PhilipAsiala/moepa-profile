# Contributing to the MOEPA Profile (Draft)

Thank you for helping improve this Draft profile. It is intended to stay a **thin profile of JSON-LD 1.1 and RDF 1.2**, not a large new ontology.

## Principles

1. **Profile, not competitor.** Changes must remain compatible with JSON-LD 1.1, RDF 1.2 Concepts, and Turtle 1.2.
2. **The architecture leads.** Required fields and envelope structure follow *The MOEPA Cognitive Architecture — Technical Specification* §5.0. Propose a change to a required field there first; this profile follows.
3. **One triple, four envelopes.** Do not propose a property that writes a confidence into the triple or derives a permission from a confidence.
4. **Minimal vocabulary.** Prefer PROV-O and existing RDF terms before adding `moepa:` terms.
5. **Mark Draft material.** Do not remove Draft status banners until maintainers agree the document is ready for a non-draft release.

## How to contribute

1. Open an issue describing the problem or proposal.
2. Keep pull requests focused (one concern per PR when practical).
3. If you change a term, update the context, the vocabulary, the SHACL shapes, and the examples together, and re-run validation (see README, "Validating").
4. Update `CHANGELOG.md` under an `[Unreleased]` or new Draft version section.
5. Do not expand scope into trust frameworks, PKI, signing, or query languages unless agreed in an issue.

## Spec editing guidance

- Normative keywords: MUST, MUST NOT, SHOULD, SHOULD NOT, MAY (RFC 2119 / 8174).
- Keep the two confidences distinct; the `× 0.9` ownership rule applies to producer trust only, unless a tracked issue revises it with rationale.
- Prefer JSON-LD examples; Turtle 1.2 and TriG examples are welcome as the acceptable alternatives and should carry the same record as a JSON-LD example.

## License

By contributing, you agree that your contributions are licensed under the Apache License 2.0 (see [LICENSE](./LICENSE)).
