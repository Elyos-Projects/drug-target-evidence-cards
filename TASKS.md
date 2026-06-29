# Drug-Target-Evidence-Cards — TASKS.md

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

Backlog for **Drug-Target-Evidence-Cards** (slug: `drug-target-evidence-cards`): an open, source-cited
library of drug-target evidence cards for cancer, built **for researchers/curators/advocates** and
explicitly **not** treatment advice. See `PLAN.md` for full context.

The **data-licensing & provenance compliance gate is the first build item** and a hard product
requirement. Core research cards are **MEDIUM risk-tier** (qualified scientific-reviewer sign-off
before release); any **patient-facing** content is **HIGH risk-tier** and **cannot ship without
blocking sign-off from a credentialed oncologist AND a patient advocate**. No partner, beneficiary, or
reviewer is yet secured, so delivery-dependent tasks carry `requestor: TO BE SECURED` and
`verifiedNeed: false`.

## How these tasks map to Elyos

Each task below becomes an Elyos **Task JSON** validated against `packages/schema/src/schemas.ts`.
Field mapping:

- **id** — stable slug `drug-target-evidence-cards-<area>-NNN`.
- **title** — the task title in the milestone table.
- **project** — `drug-target-evidence-cards`.
- **type** — one of `code | research | writing | data | design-spec | maintenance`.
- **lane** — `donated` (default; no funded tasks planned. Any `funded` task must add
  `fundedBudgetUsd` with a hard cap).
- **priority** — `high | medium | low`.
- **domain** — array, e.g. `["cancer","bioinformatics","open-data","research"]`.
- **riskTier** — `low | medium | high`. Pipeline/schema/infra = low–medium; cards asserting
  target–disease biology = medium; **patient-facing content = high**.
- **urgent** — boolean (no urgent tasks at cold-start).
- **deliverable** — `pr | dataset | document | translation`. Cards → `dataset`; specs/write-ups →
  `document`; code/pipeline → `pr`.
- **tokenEstimate** — `small | medium | large` (maps to the Size column).
- **status** — `open | in-progress | review | delivered | done` (all start `open`).
- **context / objective / acceptanceCriteria[] / resources[] / output** — per task.
- **requestor** — partner/steward/reviewer; `TO BE SECURED` where unknown.
- **verifiedNeed** — `true` only once a specific beneficiary/need is confirmed; otherwise `false`
  (currently `false` everywhere — none secured).
- **outputLicense** — code: `MIT`; cards/dataset/content/docs: `CC-BY-4.0` (subject to the CC-BY vs
  CC-BY-SA governance decision in PLAN *Open questions*).

Size legend: small ≈ `small`, med ≈ `medium`, large ≈ `large`.
Reviewer "Scientific reviewer" = qualified cancer-biology/bioinformatics curator (MEDIUM sign-off,
**TO BE SECURED**). "Oncologist + Advocate" = credentialed oncologist **and** patient advocate (HIGH,
**blocking**, **TO BE SECURED**). "Compliance reviewer" = licensing/provenance auditor.

---

## Milestone M0 — Compliance gate, schema & exemplar (cold-start)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| drug-target-evidence-cards-compliance-001 | Data-licensing & provenance compliance gate: source allowlist + licence registry + licence-class + per-assertion citation + identifiability guard (CI) | code | large | high | pr | — | Compliance reviewer + Maintainer |
| drug-target-evidence-cards-schema-002 | Evidence Card JSON Schema + evidence-grading rubric | design-spec | medium | medium | document | — | Scientific reviewer + Maintainer |
| drug-target-evidence-cards-panel-003 | Indication + target-panel selection (scored criteria; rare cancers prioritised) — gates M2–M6 | research | small | medium | document | — | Scientific reviewer + Maintainer |
| drug-target-evidence-cards-repo-004 | Monorepo + pnpm + TS/ESM + CI (build/test/lint) skeleton with core/adapter split | code | small | low | pr | — | Maintainer |
| drug-target-evidence-cards-exemplar-005 | One hand-curated exemplar card (fully cited, graded, "not medical advice") for review | data | medium | medium | dataset | 001, 002, 003 | Scientific reviewer + Compliance reviewer |

**Acceptance criteria — key tasks**

- **drug-target-evidence-cards-compliance-001** (compliance gate)
  - Ships a checked-in **source allowlist + licence registry** mapping each permitted source to its
    licence, `licenseClass` (`open` | `non-commercial` | `controlled-access-excluded`), attribution
    string, and reuse notes; a source not in the registry **cannot** be ingested.
  - Enforces **licence-class rules**: only `open`-class data may enter the CC-BY output;
    `non-commercial` (COSMIC/OncoKB) is excluded from card content (pointer-only); controlled-access
    (dbGaP/EGA/biobank) is hard-blocked. A violation **fails CI**.
  - Enforces **per-assertion citation** ("no source, no claim") and an **identifiability guard** that
    rejects individual-level/row-level data shapes.
  - For PMC-OA, enforces **per-article** licence (only CC-BY/CC0 may contribute copied text; NC/ND
    cited-not-quoted).
  - Reviewed by the compliance reviewer; decisions recorded.

- **drug-target-evidence-cards-schema-002** (card schema + rubric)
  - JSON Schema for the Evidence Card per the PLAN data model (target IDs, indication ontology id,
    `associationEvidence[]` with `evidenceType`/`grade`/`sources[]`, tractability, knownDrugs as
    research context, aggregate clinicalLandscape, contradictions, provenance, review, disclaimer).
  - A published, versioned **evidence-grading rubric** (tiers keyed to evidence type + source
    strength); every assertion must carry a grade.
  - `clinicalLandscape` constrained to **aggregate** counts; no field can express a per-patient
    recommendation. Constant "research use — not medical advice" disclaimer required.

- **drug-target-evidence-cards-panel-003** (indication + target-panel selection)
  - Scores candidates against explicit criteria: (1) strong open-data coverage; (2) public-research
    value with **rare cancers prioritised**; (3) an available scientific reviewer for that biology;
    (4) tractable scope (~10–25 targets).
  - Records the decision (or shortlist + the 2026-08-31 deadline) with rationale; the chosen
    indication/panel becomes a dependency for M2 card tasks.

- **drug-target-evidence-cards-exemplar-005** (exemplar card)
  - One target×indication card assembled **by hand**, every assertion cited to an `open`-class source
    with accession + release version + retrieval date + licence, and graded per the rubric.
  - Passes the compliance gate and schema validation; carries the "not medical advice" disclaimer;
    contradictions (if any) surfaced; scientific-reviewer draft review recorded.

**M0 Definition of Done:** compliance gate merged and **passing in CI** (allowlist + licence-class +
per-assertion citation + identifiability guard); Evidence Card schema + grading rubric merged;
TS/ESM + green CI with core/adapter split; one exemplar card built, cited, graded, and draft-reviewed;
"not medical advice" framing wired in; **indication + target panel selected (or shortlisted with a
fixed decision)**.

---

## Milestone M1 — Ingestion & provenance pipeline

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| drug-target-evidence-cards-adapters-006 | Source adapters for core open sources (Open Targets, ChEMBL, UniProt, Ensembl, Reactome, HPA, DepMap, ClinicalTrials.gov aggregate) with pinned versions | code | large | medium | pr | 001, 004 | Maintainer + Compliance reviewer |
| drug-target-evidence-cards-assemble-007 | ID mapping (HGNC/Ensembl/UniProt; EFO/MONDO/OncoTree) + assembler with contradiction surfacing | code | large | medium | pr | 002, 006 | Maintainer + Scientific reviewer |
| drug-target-evidence-cards-validate-008 | Validation + build manifest (pinned versions/hashes) + staleness fail-safe (`lastVerified`/`validUntil`) | code | medium | medium | pr | 006, 007 | Maintainer + Compliance reviewer |

**Acceptance criteria — key tasks**

- **drug-target-evidence-cards-adapters-006** (source adapters)
  - One adapter per open source; each declares its registry entry, **pins the release/version**,
    records `retrievalDate`, and emits normalised facts with attached provenance.
  - Adapters live in `adapters/` (vendor/source-specific); the pipeline core stays neutral.
  - ClinicalTrials.gov adapter emits **aggregate counts/phases only** (no patient-level data; not a
    trial-matching tool). Any source not in the registry is rejected by the gate.

- **drug-target-evidence-cards-assemble-007** (ID mapping + assembler)
  - Resolves targets to canonical HGNC/Ensembl/UniProt IDs and indications to an ontology id;
    cross-checks IDs across sources to avoid gene/target conflation.
  - **Surfaces contradictions** (disagreeing sources) on the card rather than silently choosing one.
  - Emits cards conforming to the schema with grades + provenance on every assertion.

- **drug-target-evidence-cards-validate-008** (validation + manifest + staleness)
  - Runs the compliance gate, schema validation, and grade-coverage in CI; emits a reproducible
    **build manifest** (every source + version + retrieval date + hash).
  - Staleness fail-safe: an assertion whose source is past `validUntil` is **auto-flagged**; a
    staleness test asserts no card serves as "current" past its window.
  - The M0 exemplar card is regenerated **reproducibly** by the pipeline (matches the hand-curated
    version modulo recorded source-version diffs).

**M1 Definition of Done:** reproducible ingest → assemble → validate pipeline over pinned open
sources; ID mapping + contradiction surfacing; build manifest; compliance gate + provenance validator
+ staleness check enforced in CI; exemplar card regenerated reproducibly.

---

## Milestone M2 — Research evidence cards (scientific-reviewer gated)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| drug-target-evidence-cards-sources-009 | Per-source licence verification + provenance record for the chosen indication's sources | data | medium | high | dataset | 003, 006 | Compliance reviewer + Maintainer |
| drug-target-evidence-cards-cards-010 | Assemble research cards for the chosen target panel (cited, graded, contradictions surfaced) | data | large | medium | dataset | 007, 008, 009 | Scientific reviewer |
| drug-target-evidence-cards-review-011 | Scientific-reviewer sign-off pass for each released card (version-scoped) | research | medium | medium | document | 010 | Scientific reviewer |

**Acceptance criteria — key tasks**

- **drug-target-evidence-cards-sources-009** (per-source licence verification)
  - Reuse terms verified and recorded **per source release** for every source feeding the chosen
    indication (licence, `licenseClass`, attribution string, version, URL, retrieval date).
  - COSMIC/OncoKB confirmed **excluded** from output (pointer-only); any CC-BY-SA source flagged for
    the share-alike governance decision before its data is copied.

- **drug-target-evidence-cards-cards-010** (research cards)
  - Cards for the chosen panel × indication assembled; **every assertion cited + graded**;
    contradictions surfaced; aggregate-only clinical landscape; "research use — not medical advice"
    on every card; compliance gate green.

- **drug-target-evidence-cards-review-011** (scientific sign-off)
  - A qualified scientific reviewer verifies accuracy, grading, and faithful sourcing of each card;
    **sign-off recorded (version-scoped)** in the reviewers ledger before release.
  - **Kill-gate:** if the reviewer judges the synthesis no more reliable/useful than reading raw
    sources, the project **pauses and reassesses** before scaling card production.

**M2 Definition of Done:** research cards for the chosen panel released, every assertion cited +
graded, contradictions surfaced, compliance gate green, **scientific-reviewer sign-off recorded** per
card, "not medical advice" on every card; the M2 kill-gate passed.

---

## Milestone M3 — Literature extraction & enrichment (human-verified)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| drug-target-evidence-cards-litextract-012 | PMC-OA extraction pipeline (per-article licence check; LLM drafts span-cited statements) | code | large | medium | pr | 001, 007 | Maintainer + Compliance reviewer |
| drug-target-evidence-cards-verify-013 | Human verification workflow: every extracted assertion verified before card inclusion | research | medium | medium | document | 010, 012 | Scientific reviewer |

**Acceptance criteria — key tasks**

- **drug-target-evidence-cards-litextract-012** (literature extraction)
  - Pulls open-access full text from the **PMC-OA subset**, reads each article's machine-readable
    licence, and only lets **CC-BY/CC0** articles contribute copied text/close paraphrase to a CC-BY
    card; **NC/ND** articles are cited-and-linked, **not** quoted/redistributed.
  - LLM (behind the provider-neutral client) drafts candidate evidence statements with **span-level
    citations**; it never finalises a shippable assertion.

- **drug-target-evidence-cards-verify-013** (human verification)
  - **Every** extracted statement is human-verified against its cited span before it can enter a
    card; unverifiable/fabricated statements are rejected.
  - Enriched cards re-pass the compliance gate and re-receive scientific sign-off.

**M3 Definition of Done:** PMC-OA literature evidence enriches the cards under per-article licence
enforcement, with every extracted assertion human-verified, NC/ND cited-not-quoted, and enriched
cards re-reviewed and compliance-green.

---

## Milestone M4 — Publishing & open dataset release

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| drug-target-evidence-cards-publish-014 | Accessible static rendering + downloadable CC-BY dataset + attribution manifest + reproduction docs | code | medium | medium | pr | 010, 011 | Maintainer + Compliance reviewer |

**Acceptance criteria — drug-target-evidence-cards-publish-014**
- Static rendering meets **WCAG 2.2 AA**; every card shows its sources, grades, version, and the
  persistent "research use — not medical advice" notice.
- Downloadable dataset ships an **attribution/`LICENSES` manifest** listing every source, its licence,
  and required attribution; dataset licence per the governance decision (CC-BY-4.0 default).
- Reproduction instructions published; **≥ 1 third-party reproduction** of the pipeline attempted and
  its result recorded.

**M4 Definition of Done:** cards published as an accessible rendering + openly-licensed dataset with a
complete attribution manifest and reproduction instructions; a third-party reproduction attempted.

---

## Milestone M5 — Patient-facing plain-language layer (HIGH-tier, OPTIONAL, blocking-gated)

> **Builds only if** a credentialed **oncologist** AND a **patient advocate** are secured. **No
> oncologist + advocate sign-off, no patient-facing release.**

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| drug-target-evidence-cards-plainlang-015 | Patient-facing plain-language layer drafted from cited cards (education only) | writing | large | high | document | 011, 014 | Oncologist + Advocate (blocking) |
| drug-target-evidence-cards-signoff-016 | Oncologist + advocate blocking sign-off + "not medical advice" / readability verification | research | medium | high | document | 015 | Oncologist + Advocate (blocking) |

**Acceptance criteria — key tasks**

- **drug-target-evidence-cards-plainlang-015** (plain-language layer)
  - Plain-language summaries derived **only** from the cited research cards; **education only**;
    nothing reads as a treatment/trial recommendation; "not medical advice" notice on every page;
    routes clinical questions to a qualified oncologist.

- **drug-target-evidence-cards-signoff-016** (blocking HIGH-tier sign-off)
  - A **credentialed oncologist** verifies clinical safety/accuracy and the "not medical advice"
    framing; a **patient advocate** verifies plain-language faithfulness and that nothing reads as a
    recommendation. **Both sign-offs are recorded (version-scoped) and are blocking** — content does
    not ship without them. Oncologist holds a veto on clinical safety.

**M5 Definition of Done:** patient-facing layer ships **only** with recorded oncologist **and**
advocate sign-off, "not medical advice" on every page, readability verified, and no content reading as
a recommendation. *(Gated — TO BE SECURED; layer is skipped entirely if the reviewer pair is not
secured.)*

---

## Milestone M6 — Sustain & refresh (post-delivery)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| drug-target-evidence-cards-ops-017 | Refresh cadence (source-release re-pin/re-validate/re-review) + maintenance rotation + outcomes log + ops runbook | maintenance | medium | medium | document | 014 | Maintainer + Steward |

**Acceptance criteria — drug-target-evidence-cards-ops-017**
- Refresh cadence tied to source releases: a material source update re-pins the manifest, re-runs
  validation, and triggers **re-review + re-sign-off** for affected cards (version-scoped).
- Named maintenance rotation; ops runbook (regenerate, validate, publish, incident response).
- Outcomes log tracks **beneficiary uses + third-party reproductions + review/compliance status** —
  not downloads/stars.

**M6 Definition of Done:** project sustainably maintained with a source-release refresh cadence,
a rotation owning it, an ops runbook, and outcomes tracked.

---

## Backlog / future

| ID | Title | Type | Size | Risk | Deliverable | Notes |
|---|---|---|---|---|---|---|
| drug-target-evidence-cards-breadth-018 | Expand to a second cancer indication / target panel | data | large | medium | dataset | Only after M2 stable; full source-vetting + scientific sign-off |
| drug-target-evidence-cards-api-019 | Read-only query API / serving layer over the dataset | code | medium | low | pr | Optional; dataset-first remains source of truth |
| drug-target-evidence-cards-i18n-020 | Translations of patient-facing layer (per-language review) | writing | medium | high | translation | Only after M5; oncologist/advocate + native review per language |
| drug-target-evidence-cards-contribute-021 | Contribute cards as a curated overlay to an open initiative (Open Targets/Pharos/DGIdb) | data | medium | medium | dataset | Pivot path from the build-vs-mothball rule |
| drug-target-evidence-cards-cosmic-022 | COSMIC/OncoKB pointer-reference integration (NC-safe, no copied data) | code | small | medium | pr | Pointer-only under their terms; never redistributed |

---

## Example task JSON

Complete, schema-valid Task JSON for the first M0 task (`drug-target-evidence-cards-compliance-001`):

```json
{
  "id": "drug-target-evidence-cards-compliance-001",
  "title": "Data-licensing & provenance compliance gate (source allowlist + licence registry + licence-class + per-assertion citation + identifiability guard)",
  "project": "drug-target-evidence-cards",
  "type": "code",
  "lane": "donated",
  "priority": "high",
  "domain": ["cancer", "bioinformatics", "open-data", "licensing", "compliance", "software"],
  "riskTier": "high",
  "urgent": false,
  "deliverable": "pr",
  "tokenEstimate": "large",
  "status": "open",
  "context": "Drug-Target-Evidence-Cards builds an open, source-cited library of drug-target evidence cards for cancer, for researchers/curators/advocates and explicitly NOT treatment advice. The binding cancer-domain guardrails require open-access/aggregate/de-identified data ONLY: controlled-access resources (dbGaP, EGA, individual-level biobanks) and any identifiable patient data are out of scope, non-commercial sources (COSMIC, OncoKB) must be excluded from the CC-BY output (pointer-only), and every assertion must be provenance-linked. This is the cold-start task that builds that compliance gate FIRST, as a CI release gate (a safety-critical subsystem, not a disclaimer), before any card or pipeline work. No partner, beneficiary, or reviewer is yet secured.",
  "objective": "Build the data-licensing & provenance compliance gate that all later ingestion and card assembly must pass: a checked-in source allowlist + licence registry with a licence-class taxonomy (open | non-commercial | controlled-access-excluded), enforcement that only open-class data enters the CC-BY output (NC excluded/pointer-only, controlled-access hard-blocked), per-article PMC-OA licence handling, per-assertion citation enforcement (no source, no claim), and an identifiability guard that rejects individual-level/row-level data shapes - all wired into CI so a violation fails the build.",
  "acceptanceCriteria": [
    "Ships a checked-in source allowlist + licence registry mapping each permitted source to its licence, licenseClass (open | non-commercial | controlled-access-excluded), attribution string, version, and reuse notes; a source not in the registry cannot be ingested",
    "Enforces licence-class rules: only open-class data may enter the CC-BY output; non-commercial sources (COSMIC, OncoKB) are excluded from card content (pointer-only); controlled-access sources (dbGaP, EGA, individual-level biobanks) are hard-blocked - a violation fails CI",
    "Enforces per-assertion citation ('no source, no claim'): a card with any uncited assertion fails validation",
    "Includes an identifiability guard that rejects individual-level / row-level genomic or clinical data shapes at ingest",
    "Handles PMC Open Access per-article licence: only CC-BY/CC0 articles may contribute copied text/close paraphrase to a CC-BY card; CC-BY-NC / ND articles may be cited and linked but not quoted/redistributed",
    "Flags CC-BY-SA (share-alike) sources for the governance licence decision before their data is copied into output",
    "Runs in CI (build fails on any disallowed/uncited/NC-in-output/controlled-access violation); decisions reviewed and recorded by the compliance reviewer",
    "No secrets, tokens, or PII written to logs, manifests, or committed files"
  ],
  "resources": [
    "planning/projects/drug-target-evidence-cards/PLAN.md",
    "CLAUDE.md",
    "docs/good-deed-definition.md",
    "packages/schema/src/schemas.ts",
    "planning/ROADMAP.md"
  ],
  "output": "A merged compliance package (source allowlist + licence registry + licence-class enforcement + per-assertion citation enforcement + identifiability guard + per-article PMC-OA licence handling) wired into CI as a release gate - the contract that the ingestion adapters (drug-target-evidence-cards-adapters-006) and the validator/manifest (drug-target-evidence-cards-validate-008) implement and are tested against.",
  "requestor": "TO BE SECURED",
  "verifiedNeed": false,
  "outputLicense": "MIT"
}
```
