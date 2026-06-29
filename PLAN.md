# Drug-Target-Evidence-Cards — PLAN.md

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

An open, source-cited library of **drug-target evidence cards** for cancer: per (molecular target ×
cancer indication), a structured, fully-provenanced summary of the open-data evidence linking that
target to the disease and to candidate therapeutics — built **for researchers, curators, and patient
advocates**, explicitly **not** treatment advice. Each assertion on a card carries a citation to an
**open-access, aggregate, or de-identified** source whose licence has been verified. The project's
hard, first-built control is a **data-licensing & provenance compliance gate**: no source enters a
card unless it is on the open-data allowlist with verified reuse terms, and no assertion ships
without a citation.

---

## Executive summary

Cancer-target research is bottlenecked not by a shortage of data but by its fragmentation. The
evidence that a given gene/protein is a credible therapeutic target in a given cancer — genetic
association, somatic alteration frequency, differential expression, genetic dependency, pathway
context, tractability/druggability, existing drugs and modalities, and the clinical research
landscape — is scattered across Open Targets, ChEMBL, UniProt, Ensembl, Reactome, the Human Protein
Atlas, DepMap, ClinicalTrials.gov, and the open-access literature, each with its own identifiers,
versions, and licences. A researcher or a patient advocate trying to understand "what is the open
evidence for target X in cancer Y?" must stitch these together by hand, usually without recording
provenance, and the result is neither reproducible nor reusable.

Drug-Target-Evidence-Cards produces a **curated, versioned, openly-licensed dataset of evidence
cards** — one per target×indication — where every assertion is graded for evidence strength and
linked to a specific, licence-verified open source (with accession, release version, and retrieval
date). The deliverable is the dataset itself (machine-readable), a human-readable rendering, and the
reproducible, agent-neutral pipeline that builds and validates it.

The single most important design fact is the **compliance posture**, which is binding and non-
negotiable: the project uses **only open-access, aggregate, or de-identified data**. Controlled-
access resources (dbGaP, EGA, individual-level biobanks) and **any** identifiable patient data are
**out of scope** — they require authorized access and IRB oversight this project does not have and
will not seek. Per-source licences are verified before use: open resources (e.g. TCGA open tier,
GEO, PRIDE, Open Targets, UniProt) are usable with attribution; **non-commercial** resources
(COSMIC, OncoKB) are flagged and, by default, **excluded** from the openly-licensed dataset because
a CC-BY output cannot carry a non-commercial dependency. This is enforced by a **licence/provenance
compliance gate built first** (M0), run in CI, that fails the build on any disallowed or uncited
source — the analogue of a safety-critical subsystem, not a documentation footer.

The project is **medium risk-tier** for its core, research-facing cards (it requires domain accuracy
and provenance, reviewed by a qualified scientific curator). A later, **optional patient-facing
plain-language layer is HIGH risk-tier**: any content a patient or family member might read for
decision-making is **education only**, carries a persistent **"research summary — not medical
advice"** notice, and **cannot ship without blocking sign-off from a credentialed oncologist and a
patient advocate**. That review is a hard gate, not advisory: no oncologist + advocate sign-off, no
patient-facing release.

Honesty note: **no partner organisation, requesting lab, or expert reviewer is yet secured.** Every
delivery-dependent task is marked `TO BE SECURED` with `verifiedNeed: false` until a real
beneficiary (a translational-oncology lab, a rare-cancer research foundation, or a patient-advocacy
organisation) adopts or requests the work. The project is **not "shipped" on merge**; it is shipped
only when the cards are in the hands of a real beneficiary who uses them, with provenance and review
gates verified.

**Partner-acquisition plan (dated) and build-vs-mothball decision rule.** Outreach is time-boxed
against the build rather than left open-ended: by **2026-08-31**, the initial target panel and
cancer indication are chosen (criteria in *Open questions*) and ≥ 3 candidate beneficiary/reviewer
partners (a translational-oncology lab, a rare-cancer foundation, an open-data initiative such as
Open Targets, or a patient-advocacy org) are in active conversation; by **2026-10-31**, a qualified
**scientific reviewer** (PhD/MD in cancer biology or a bioinformatics curator) is secured (gates the
M2 research cards); by **2027-01-31**, if the patient-facing layer (M5) is pursued, a **credentialed
oncologist and a patient advocate** are secured (gates M5). **Decision rule:** if no scientific
reviewer is secured by the M2 entry date, M2 card publication does not start and the project holds at
M1 (pipeline + compliance gate + exemplar cards). If no beneficiary is secured by **~2027-03-31**,
the project does **not** ship to no one: it either (a) **pivots** the last-mile beneficiary (e.g.
contribute the cards + pipeline to an existing open initiative such as Open Targets/Pharos as a
curated overlay) or (b) **mothballs** to maintenance-only — recorded in governance. The pipeline and
the exemplar cards remain a reusable public good in either case.

---

## Problem & beneficiaries

**Who is helped (directly):** cancer researchers and bioinformaticians (especially in small,
under-resourced or rare-cancer labs without curation staff); biocurators building target
dossiers; and **patient advocates and advocacy organisations** who must read primary research to
represent patients but lack a provenance-grade, plain-language synthesis.

**Who is helped (ultimately):** cancer patients and families — indirectly — through faster, more
reproducible, better-sourced target research, and (only if the gated patient-facing layer ships)
through trustworthy, expert-reviewed educational summaries that help them understand the science
discussed in their care, **never** to self-direct treatment.

**The need.** Deciding whether a molecular target is worth pursuing in a cancer requires triangulating
a dozen open resources, each versioned and licensed differently. In practice this is done ad hoc, the
provenance is rarely recorded, the licence terms are rarely verified, and the synthesis is not
reusable by the next researcher. For **rare cancers** in particular (where commercial target
intelligence is thin and academic effort is small), an open, cited, reproducible evidence card per
target is disproportionately valuable. Patient advocates face the same fragmentation with the added
burden that almost no synthesis is written for a non-specialist while remaining faithful and sourced.

**Verified need / partner:** **TO BE SECURED.** No specific lab, foundation, advocacy organisation,
or curator has yet agreed to request, adopt, or co-develop the cards. Target partner profiles:
a **translational-oncology or rare-cancer research lab** (primary beneficiary + scientific review);
a **rare-cancer research foundation** (need definition + last-mile distribution); an **open-data
initiative** (Open Targets, Pharos/IDG, DGIdb — contribution/overlay path); a **patient-advocacy
organisation** (need + co-review for the patient-facing layer). Until one is secured, the project
builds the agent-neutral pipeline, the compliance gate, the card schema, and a small set of
**exemplar cards for review**, and marks all delivery/adoption work `TO BE SECURED`. A dated
partner-acquisition plan and a build-vs-mothball/pivot decision rule (see executive summary) govern
what happens if the dates slip — rather than shipping cards to no real beneficiary.

---

## Goals and non-goals

**Goals**

- Produce an open, machine-readable, **versioned dataset of drug-target evidence cards** for an
  initial cancer indication and target panel, every assertion graded and **provenance-linked** to a
  licence-verified open source.
- Build and enforce, **first**, a **data-licensing & provenance compliance gate** that admits only
  allowlisted open/aggregate/de-identified sources, flags non-commercial sources (COSMIC/OncoKB),
  excludes controlled-access data, and fails the build on any uncited assertion.
- Build a **reproducible, agent-neutral pipeline** (ingest → normalise → assemble → grade → validate
  → render) that anyone can re-run to regenerate the cards from pinned source versions.
- Apply an explicit, documented **evidence-grading rubric** so card readers can tell strong evidence
  from weak.
- Have research cards **reviewed and signed off by a qualified scientific curator** (medium tier)
  before release; carry a persistent **"research use — not medical advice"** notice on every card.
- (Optional, gated) Produce a **patient-facing plain-language layer** that is **education only**,
  **oncologist- and patient-advocate-reviewed (blocking, HIGH tier)**, and faithful to the cited
  research.
- Track **real beneficiary outcomes** (cards used in a real research/advocacy workflow), not download
  vanity metrics, and prove the cards are reusable and reproducible.

**Non-goals**

- **Not medical, clinical, or treatment advice**; not a tool for choosing a therapy, dose, or trial
  for any individual; not a clinical decision-support system.
- **Not** a user of controlled-access or identifiable patient data — no dbGaP, EGA, individual-level
  biobank, or any data that could re-identify a person; no IRB-gated work.
- Not a re-distribution of **non-commercial** datasets (COSMIC, OncoKB) inside a CC-BY output;
  such sources may at most be *referenced by pointer* under their own terms, never copied in.
- Not original wet-lab or computational *discovery*; the cards **synthesise existing open evidence**,
  they do not generate new biological claims.
- Not a real-time/live service or API at launch — a versioned, reproducible dataset + rendering
  first; serving infrastructure is a later, optional concern.
- Not exhaustive across all targets/cancers at launch — **depth and verified accuracy** for one
  indication and a small target panel first; breadth later, never breadth without review.

---

## Success metrics (outcomes)

Outcome-centric and beneficiary-first. Downloads, stars, and card counts are **not** success.

| Metric | Baseline | Target | How measured |
|---|---|---|---|
| Assertions carrying a verified open-source citation | n/a | **100%** of shipped assertions (zero uncited) | Provenance-coverage validator in CI |
| Disallowed-source / controlled-access leakage into a card | n/a | **0** (build fails on any) | Compliance gate (CI) + reviewer audit |
| Non-commercial (COSMIC/OncoKB) content copied into a CC-BY card | n/a | **0** (flagged + excluded by default; pointer-only) | Licence-class check in compliance gate |
| Research cards with recorded scientific-reviewer sign-off | n/a | **100%** before release | Reviewers ledger + release checklist |
| Reproducibility: cards regenerated from pinned source versions match the released set | n/a | **byte/■field-stable** re-run on the same source releases (diffs only where a source version changed) | CI re-run + manifest diff |
| Evidence-grade present on every assertion | n/a | **100%** graded against the published rubric | Schema validation |
| Source-version staleness contained | n/a | **0** cards served as "current" past a source's `validUntil` without an auto-flag + re-review | Staleness test against `lastVerified`/`validUntil` |
| Independent reproduction by a third party | 0 | **≥ 1** external party re-runs the pipeline and confirms the cards | Reproduction report (issue/PR) |
| Cards used in a real research/advocacy workflow | 0 | **≥ 1** documented use by a secured beneficiary (e.g. seeded a target dossier, informed an advocacy brief) | Beneficiary-attested log |
| Patient-facing layer (if pursued): oncologist + advocate sign-off before release | n/a | **100%** (blocking; no sign-off, no release) | Reviewers ledger; release gate |
| Patient-facing readability + "not medical advice" notice present | n/a | every page reviewed for plain-language + carries the notice | Advocate review + automated notice check |

The **defining success outcome** (Definition of Shipped): a real beneficiary (researcher, curator,
or advocacy org) uses the evidence cards in their work, with provenance verified, the compliance gate
green, and scientific (and, for any patient-facing content, oncologist + advocate) review recorded.

---

## Scope

**In scope**

- A versioned **Evidence Card schema** (JSON Schema) and an **evidence-grading rubric**.
- A **data-licensing & provenance compliance gate**: source allowlist + licence registry +
  licence-class classifier (open / non-commercial / controlled-access-excluded) + per-assertion
  citation enforcement, run in CI.
- A **reproducible ingestion + assembly pipeline** (TypeScript/ESM) over open APIs/bulk files with
  **pinned source versions** and a recorded **build manifest**.
- **Research-tier evidence cards** for one chosen cancer indication + an initial target panel,
  scientific-reviewer signed off, each card carrying the "research use — not medical advice" notice.
- LLM-assisted **literature extraction from the PMC Open Access subset** (per-article licence
  checked) with **mandatory human verification** of every extracted assertion.
- A human-readable **rendering** of the cards (static site + downloadable dataset), accessible
  (WCAG 2.2 AA) and openly licensed.
- (Optional, gated) a **patient-facing plain-language layer** behind blocking oncologist + advocate
  review.
- **Provenance, source-version pinning, and a staleness fail-safe** (`lastVerified`/`validUntil`).

**Out of scope (explicitly will NOT do)**

- **Any** controlled-access data (dbGaP, EGA, individual-level biobanks) or **any** identifiable /
  re-identifiable patient data — not ingested, not derived from, not referenced at row level.
- **Treatment, dosing, trial-selection, or clinical recommendations** for any individual; ranking
  therapies "best for patient X"; any clinical decision support.
- Copying **non-commercial** datasets (COSMIC, OncoKB) into the openly-licensed output; they may be
  *pointed to* under their own terms only, never redistributed.
- Original biological discovery, novel analyses presented as findings, or unsourced inference; the
  cards report and grade **existing open evidence** only.
- Marketing or promotion of any drug, company, or therapy; any for-profit-primary benefit.
- A clinically-validated or regulated medical device/software; nothing here is for clinical use.

When a request, a source, or an extracted statement falls in the out-of-scope set, the pipeline
**refuses to include it**, records why, and (for sources) routes it to the licence-review queue. A
non-commercial or controlled-access source surfacing in a card is a **build failure**, treated the
same as an uncited assertion.

---

## Solution approach & architecture

This is primarily a **data + content** project with a thin, reproducible software pipeline; it is not
a multi-tenant app. The architecture mirrors Elyos's core/adapter discipline: agent-neutral pipeline
and schema in the core, any LLM/vendor specifics behind a thin adapter.

**Stack.** TypeScript, ESM, pnpm workspace. Node-based pipeline (CLI scripts). Data persisted as
**versioned files** (card JSON + a build manifest) under version control / object storage — no
patient database, by design. JSON Schema (Ajv) for validation. Anthropic Claude for literature
extraction **assistance only**, behind a thin provider-neutral LLM client (model selection/pricing
per the Claude API skill); the LLM never finalises a card — a human verifies every extracted
assertion. Rendering: a static-site generator (e.g. Astro/Next static export). Code licence **MIT**;
cards/content/dataset **CC-BY-4.0** (compatible only with open sources — see *Data, licensing &
compliance*).

**Pipeline (the spine):** `ingest → normalise/ID-map → assemble → grade → validate (compliance +
provenance) → render`.

**Components**

1. **Compliance gate (`packages/compliance`) — built first.** The safety-critical subsystem.
   - *Source allowlist + licence registry*: a checked-in registry mapping each permitted source to
     its licence, **licence class** (`open` | `non-commercial` | `controlled-access-excluded`),
     attribution string, version/release, and reuse notes. A source **not** in the registry cannot be
     ingested.
   - *Licence-class enforcement*: the openly-licensed (CC-BY) output may contain data only from
     `open`-class sources. `non-commercial` sources (COSMIC, OncoKB) are **excluded** from card
     content by default and may appear only as an external **pointer/reference** under their own
     terms. `controlled-access-excluded` sources are hard-blocked. Violations **fail CI**.
   - *Provenance/citation enforcement*: every assertion field on a card must carry ≥ 1 `Source`
     reference (accession/id + version + retrievalDate + url + licence); a card with an uncited
     assertion **fails validation**.
   - *PII / identifiability guard*: a check that ingested data is aggregate/summary level (no
     individual-level genotype/clinical records); row-level or controlled-access shapes are rejected.

2. **Evidence Card schema + grading rubric (`packages/schema-cards`).** A JSON Schema for the card
   (see *data model* below) and a documented, versioned **evidence-grading rubric** (adapted from
   Open Targets-style association scoring / GRADE-like tiers: e.g. *Strong / Moderate / Limited /
   Preclinical-only*, keyed to evidence type and source strength). Every assertion carries a grade.

3. **Source connectors (`adapters/sources/*`).** One adapter per open source (Open Targets GraphQL,
   ChEMBL, UniProt, Ensembl, Reactome, Human Protein Atlas, DepMap, ClinicalTrials.gov, PMC-OA,
   DGIdb/Pharos). Each adapter declares the source's registry entry, pins the **release/version**,
   records `retrievalDate`, and emits normalised, ID-mapped facts with attached provenance. Adapters
   are the only Elyos-style vendor-specific code; the pipeline core stays neutral.

4. **Assembly + ID mapping (`packages/assemble`).** Resolves a target to canonical identifiers
   (HGNC/Ensembl/UniProt) and an indication to an ontology id (EFO/MONDO/OncoTree), then assembles
   per-source facts into a single card, deduplicating and attaching grades + provenance. Surfaces
   **contradictions** (sources that disagree) rather than silently picking one.

5. **Literature extraction (`packages/litextract`, LLM-assisted, human-verified).** Pulls
   open-access full text from the **PMC-OA subset** (checking the **per-article licence** — the OA
   subset mixes CC-BY, CC-BY-NC, and CC0; only commercially-reusable licences feed a CC-BY card),
   uses Claude to draft candidate evidence statements with span-level citations, then routes **every**
   statement to a human curator for verification before it can enter a card. The LLM never writes a
   final, shippable assertion unreviewed.

6. **Validation + manifest (`packages/validate`).** Runs the compliance gate, schema validation,
   grade-coverage, and the **staleness check** (`lastVerified`/`validUntil` per source release);
   emits a reproducible **build manifest** (every source + version + retrieval date + hash) so a
   third party can regenerate and diff the cards.

7. **Rendering (`apps/site`).** A static, accessible (WCAG 2.2 AA) rendering of the cards + a
   downloadable dataset, every card showing its sources, grades, version, and the persistent
   "research use — not medical advice" notice. The (gated) patient-facing layer, if built, is a
   separate, clearly-labelled view behind oncologist + advocate sign-off.

**Data model (Evidence Card, abridged)**

- `cardId`, `schemaVersion`, `cardVersion`, `buildManifestRef`
- `target`: `{ geneSymbol(HGNC), ensemblId, uniprotId, proteinName }`
- `indication`: `{ cancerType, ontologyId(EFO/MONDO/OncoTree), aggregateOnly: true }`
- `associationEvidence[]`: `{ evidenceType(genetic|somatic|copy-number|expression|dependency|
  pathway|tractability|literature|clinical-context), statement, direction, grade, sources[] }`
- `tractability`: `{ smallMolecule, antibody, otherModality, structuresAvailable, sources[] }`
- `knownDrugs[]`: `{ name, chemblId, mechanism, modality, researchStageNote, sources[] }`
  *(research context only — never framed as a recommendation)*
- `clinicalLandscape`: `{ aggregateTrialCounts, phases, sourcedFrom: ClinicalTrials.gov, sources[] }`
  *(aggregate counts only; no patient-level data; not a trial-matching tool)*
- `limitations`, `contradictions[]`, `openQuestions[]`
- `provenance.sources[]`: `{ sourceName, accession, releaseVersion, retrievalDate, url, license,
  licenseClass, lastVerified, validUntil }`
- `review`: `{ scientificReviewer, signedOffCardVersion, date }`,
  `patientFacing?`: `{ oncologistReviewer, advocateReviewer, signedOffVersion, date }`
- `disclaimer`: constant — *"Research summary compiled from open sources. Not medical advice. Not for
  clinical or treatment decisions. Consult a qualified oncologist."*

**Key decisions**

- The **compliance gate is first-class, tested, and a release gate** — not documentation.
- **No database of people** — versioned files only; identifiability risk minimised by construction.
- **Depth-first** on one indication + small target panel with scientific sign-off before any breadth.
- **Human-verified extraction** — the LLM assists, a human is accountable for every shipped assertion.
- **Reproducibility is a feature**: pinned source versions + a build manifest so anyone can re-derive.

---

## Data, licensing & compliance

**THIS SECTION IS BINDING AND LEADS THE PLAN. The cancer-domain guardrails below override any other
consideration; when they conflict with scope, breadth, or convenience, the guardrails win.**

**Cancer-domain guardrails (binding).**
- **Open-access, aggregate, or de-identified data ONLY.** Controlled-access resources — **dbGaP,
  EGA, individual-level biobanks** — and **any identifiable or re-identifiable patient data** are
  **OUT OF SCOPE**. They require authorized access + IRB oversight this project neither has nor
  pursues. The pipeline rejects individual-level / row-level genomic or clinical shapes.
- **Per-source licence verification before any use.** A source is usable only after its licence and
  reuse terms are verified and recorded in the licence registry, with a `licenseClass`.
- **Non-commercial sources are flagged and excluded from the CC-BY output.** COSMIC and OncoKB are
  **non-commercial** — they **cannot** be redistributed inside a CC-BY dataset. By default they are
  **excluded** from card content; at most a card may carry an external **pointer** ("see COSMIC for
  X") under COSMIC/OncoKB's own terms, never their copied data.
- **No medical advice.** Cards are research artifacts. Any patient-facing content is **education
  only**, carries the **"not medical advice"** notice, and is **oncologist- and advocate-reviewed
  (HIGH tier, blocking)**.
- **Provenance on every assertion.** No source, no claim — enforced in CI.

**Source allowlist (initial, each verified before use).** *Classes:* `open` = reusable in CC-BY with
attribution; `NC` = non-commercial, **excluded** from output (pointer-only); `excluded` = controlled
access, hard-blocked.

| Source | Content | Licence (to verify per release) | Class |
|---|---|---|---|
| Open Targets Platform | target–disease association evidence | CC0 1.0 | open |
| ChEMBL | bioactivity, drugs, mechanisms | CC BY-SA 3.0 | open (share-alike noted) |
| UniProt | protein function/annotation | CC BY 4.0 | open |
| Ensembl / Ensembl genes | gene models, IDs | open (EMBL-EBI terms; no restriction) | open |
| Reactome | pathways | CC BY 4.0 | open |
| Human Protein Atlas | expression/localisation | CC BY-SA 3.0 | open (share-alike noted) |
| DepMap (Broad) | genetic dependency (aggregate) | CC BY 4.0 | open |
| IUPHAR/BPS Guide to Pharmacology | targets/ligands | CC BY-SA 4.0 | open (share-alike noted) |
| DGIdb | drug–gene interactions (aggregated) | open (MIT/CC0; verify per upstream) | open (verify) |
| Pharos / IDG (TCRD) | target development level | open (NIH) | open (verify) |
| ClinicalTrials.gov | **aggregate** trial counts/phases | U.S. gov public domain (attribution/linkback per ToU) | open |
| PMC Open Access subset | open-access full text | **per-article**: CC-BY / CC-BY-NC / CC0 | mixed — per-article |
| TCGA (open tier) | aggregate/summary only | NIH open-access tier | open (open tier only) |
| GEO | expression series (aggregate) | per-submission (verify) | open (verify per series) |
| PRIDE / ProteomeXchange | proteomics (aggregate) | CC-BY / CC0 (verify) | open (verify) |
| **COSMIC** | somatic mutations | **non-commercial** (registration) | **NC — excluded** |
| **OncoKB** | variant/therapeutic annotation | **non-commercial / academic** | **NC — excluded** |
| dbGaP / EGA / individual biobanks | individual-level | controlled access | **excluded** |

**Share-alike note.** ChEMBL, HPA, IUPHAR, and any CC-BY-SA source impose **share-alike** on
derivatives *of their data*. Mixing CC-BY-SA data into a single combined record can force the
combined record under a compatible share-alike licence. To keep the dataset cleanly **CC-BY-4.0**,
SA-licensed facts are (a) attributed and licence-tagged per assertion and (b) **kept separable** (the
card records *which assertions came from SA sources*), and the project takes a **governance
decision** on whether to (i) dual-license affected card fields, (ii) restrict SA sources to
pointer/attribution rather than copied values, or (iii) release the dataset CC-BY-SA. **This is an
open question requiring a decision before SA-source data is copied into output.**

**Per-article licence handling for PMC-OA.** The OA subset is **not uniformly reusable**: it mixes
CC-BY, CC-BY-NC, CC-BY-NC-ND, and CC0. The litextract adapter reads each article's machine-readable
licence and **only** lets commercially-reusable licences (CC-BY, CC0) contribute copied text/close
paraphrase to a CC-BY card; NC/ND articles may be **cited and linked** but not quoted/redistributed.
This is enforced in the compliance gate per article, not assumed.

**Provenance model.** Every assertion → ≥ 1 `Source` record `{sourceName, accession, releaseVersion,
retrievalDate, url, license, licenseClass, lastVerified, validUntil}`. The assembler may not emit an
assertion without it; a citation-coverage validator enforces it in CI. The **build manifest** pins
every source release + hash so the cards are reproducible and auditable.

**Staleness is fail-safe.** Sources release new versions (Open Targets, ChEMBL, DepMap on regular
cadences); a card pinned to an old release can silently go stale. Each `Source` carries `lastVerified`
+ `validUntil` (per-source review interval). At validation/render time, an assertion whose source is
**past `validUntil`** is **auto-flagged** ("source release out of date — re-verify") and, for the
patient-facing layer, **withheld** until re-verified and re-signed-off. A staleness test enforces
this.

**Output licensing.** Code: **MIT**. Cards/dataset/content/docs: **CC-BY-4.0** (attribution to every
upstream source preserved per the registry), **subject to the share-alike governance decision above**.
The dataset ships a `LICENSES`/`ATTRIBUTION` manifest listing every source, its licence, and its
required attribution string.

**Privacy / PII stance (conservative).** By construction the project stores **no individual-level
data** — only aggregate/summary facts and citations to public sources. No patient database exists.
The identifiability guard rejects any ingest that looks individual-level. No secrets, tokens, or PII
are written to logs, manifests, or committed files (Elyos rule).

---

## Quality, review & risk gates

**Risk tier.** **MEDIUM** for the core research cards (domain accuracy + provenance; reviewed by a
qualified scientific curator). **HIGH** for any **patient-facing** content (per
`docs/good-deed-definition.md`: health domains require credentialed expert sign-off before merge).
Pure pipeline/schema/infra tasks are low–medium; anything asserting target–disease biology or drug
relevance is medium; anything a patient might read for decision-making is HIGH.

**Required reviews before a deed is "done":**

- **Maintainer** review on all PRs (engineering quality, agent-neutral core/adapters split, no
  secrets/PII, compliance gate + CI green, reproducible manifest).
- **Compliance-gate pass (CI)** — allowlist + licence-class + per-assertion citation + identifiability
  guard all green; a violation **fails the build**.
- **Scientific reviewer sign-off (MEDIUM)** — a qualified cancer-biology/bioinformatics curator
  verifies the accuracy, grading, and faithful sourcing of each research card before release. No
  scientific sign-off, no release. Recorded in the reviewers ledger.
- **Oncologist + patient-advocate sign-off (HIGH, BLOCKING)** — required before **any** patient-facing
  content ships. **This is a hard gate: no oncologist + advocate sign-off, no patient-facing
  release.** The oncologist verifies clinical safety/accuracy and the "not medical advice" framing;
  the advocate verifies plain-language faithfulness and that nothing reads as a treatment
  recommendation.

**Every card carries the persistent "research summary — not medical advice"** notice and routes
clinical questions to a qualified oncologist.

**Definition of Shipped (project):** a real beneficiary uses the cards in a research/advocacy
workflow, with the compliance gate green, every assertion cited and graded, scientific sign-off
recorded, the cards reproducible from the manifest — and, for any patient-facing content, oncologist +
advocate sign-off recorded and the "not medical advice" framing verified.

---

## Roadmap & milestones

Phased: compliance gate + schema first; pipeline next; research cards behind scientific review;
publishing; the patient-facing layer last and gated on a HIGH-tier reviewer pair; sustain.

- **M0 — Compliance gate, schema & exemplar (cold-start).**
  *Goal:* the licensing/provenance safety subsystem, the card schema + grading rubric, and one
  hand-curated exemplar card exist before bulk work.
  *Exit:* compliance gate (allowlist + licence registry + licence-class + per-assertion citation +
  identifiability guard) merged and **passing in CI**; Evidence Card JSON Schema + grading rubric
  merged; **one exemplar card** built by hand for a chosen target, fully cited, scientific-reviewer
  draft-reviewed; monorepo + CI green; "not medical advice" framing wired in. **Initial indication +
  target panel selected** (criteria in *Open questions*) so M2 builds against a real corpus.

- **M1 — Ingestion & provenance pipeline.**
  *Goal:* reproducible ingest → assemble → validate from pinned open sources.
  *Exit:* source adapters for the core open sources (Open Targets, ChEMBL, UniProt, Ensembl,
  Reactome, HPA, DepMap, ClinicalTrials.gov aggregate); ID mapping; build manifest with pinned
  versions/hashes; compliance gate + provenance validator + staleness check enforced in CI; the
  exemplar card regenerated **reproducibly** by the pipeline.

- **M2 — Research evidence cards (scientific-reviewer gated).**
  *Goal:* the initial target panel × indication as cited, graded cards.
  *Exit:* cards for the chosen panel assembled, every assertion cited + graded; **scientific reviewer
  sign-off recorded** for each released card; contradictions surfaced; "research use — not medical
  advice" on every card; compliance gate green. **Kill-gate:** if the scientific reviewer finds the
  synthesis no more reliable/useful than reading the raw sources, pause and reassess before scaling.

- **M3 — Literature extraction & enrichment (human-verified).**
  *Goal:* enrich cards with PMC-OA evidence under per-article licence checks + human verification.
  *Exit:* litextract pipeline pulls OA full text, checks **per-article** licence, drafts candidate
  statements with span citations; **every** statement human-verified before inclusion; NC/ND articles
  cited-not-quoted; enriched cards re-reviewed; compliance gate green.

- **M4 — Publishing & open dataset release.**
  *Goal:* the cards usable by real people, openly.
  *Exit:* accessible (WCAG 2.2 AA) static rendering + downloadable CC-BY dataset + attribution
  manifest; versioned release; reproduction instructions; **≥ 1 third-party reproduction** attempted.

- **M5 — Patient-facing plain-language layer (HIGH-tier, OPTIONAL, blocking-gated).**
  *Goal:* faithful, education-only summaries for patients/advocates.
  *Goal-gate:* **builds only if** a credentialed **oncologist** and a **patient advocate** are
  secured. *Exit (Definition of Shipped for this layer):* plain-language layer drafted from the cited
  cards; **oncologist + advocate sign-off recorded (blocking)**; "not medical advice" on every page;
  readability verified; nothing reads as a treatment recommendation. *(Gated — TO BE SECURED.)*

- **M6 — Sustain & refresh (post-delivery).**
  *Goal:* keep cards current as sources release new versions.
  *Exit:* refresh cadence tied to source releases (re-pin, re-validate, re-review on material change);
  maintenance rotation + ops runbook + an outcomes log (beneficiary uses, reproductions) — not
  download counts.

Dependencies flow M0 → M1 → M2 → M3 → M4 → (M5) → M6. The **indication + target-panel decision is
made in M0 and gates M2–M6** (it fixes the source corpus, the licence surface, and the reviewer
profile). M2 blocks on M0 (gate + schema) and a secured scientific reviewer; **M5 blocks on a secured
oncologist + advocate pair** and never ships without their sign-off.

---

## Work breakdown

The itemized, schema-mapped backlog lives in **`TASKS.md`**: ~17 tasks across milestones M0–M6 plus a
future backlog, each mapped to the Elyos Task JSON schema, with per-task acceptance criteria for the
most important items, milestone Definitions of Done, and a complete example Task JSON for the first M0
task (the compliance gate). The first build item is the **data-licensing & provenance compliance
gate**, reflecting its status as a hard product requirement; the **indication + target-panel
selection** is sequenced in M0 so all card work builds against the right corpus, licence surface, and
reviewer profile.

---

## Governance, roles & stakeholders

- **Maintainer (Owner): TBD.** Owns the agent-neutral pipeline/schema, the core/adapter split, CI,
  the compliance gate, and merge quality.
- **Reviewers / rotation:** at least one engineering reviewer plus a designated **compliance/licensing
  reviewer** who audits the source registry and licence-class decisions independently of adapter
  authors.
- **Scientific reviewer (MEDIUM tier): TO BE SECURED** — a qualified cancer-biology / bioinformatics
  curator (PhD/MD or experienced biocurator) who verifies card accuracy, grading, and sourcing before
  release. Tracked in a reviewers ledger with credentials and consent. **Sign-off is version-scoped**
  (attaches to a specific card version + source manifest; edits require re-sign-off, dovetailing with
  the `validUntil` staleness model).
- **Credentialed expert reviewers (HIGH tier — patient-facing): TO BE SECURED** — a **credentialed
  oncologist** and a **patient advocate**; both must sign off before any patient-facing content ships
  (**blocking**). **COI/independence:** a reviewer recuses from any target/drug in which they hold a
  financial interest; sign-off is version-scoped; name-use is limited to the versions approved, with
  consent, and may not imply endorsement of the whole project. **Disagreement fallback:** the
  oncologist holds a **veto** on whether patient-facing content is clinically safe to ship — a
  maintainer cannot override a "do not ship" on substance; the matter escalates to Elyos governance /
  a second credentialed reviewer.
- **Steward (last-mile owner): TO BE SECURED** — owns the beneficiary relationship and the
  hand-off/adoption that constitutes shipping.
- **Partner / requestor: TO BE SECURED** — the lab, foundation, advocacy org, or open-data initiative
  that requests/adopts the cards.
- **Community / board:** the **CC-BY vs CC-BY-SA (share-alike) licence decision** and edge cases go
  through Elyos governance.

---

## Dependencies & integrations

- **External data sources:** Open Targets, ChEMBL, UniProt, Ensembl, Reactome, Human Protein Atlas,
  DepMap, IUPHAR/BPS, DGIdb, Pharos/IDG, ClinicalTrials.gov (aggregate), PMC-OA, and (open-tier only)
  TCGA/GEO/PRIDE — each with **verified reuse terms** and recorded provenance. COSMIC/OncoKB are
  **excluded** (pointer-only). dbGaP/EGA/biobanks are **hard-blocked**.
- **External services:** Anthropic Claude API (literature-extraction assistance only, behind the
  neutral LLM client; the human verifies every assertion); static-site hosting; object storage for
  the dataset + manifest.
- **Elyos pieces:** `packages/schema` (Task JSON), `CLAUDE.md` work rules + refusal guardrails,
  `docs/good-deed-definition.md` (risk tiers), Elyos governance for the licence/edge-case decisions.
- **Human/decision dependencies (critical path):** the **indication + target-panel decision (M0,
  gates M2–M6)**; a secured **scientific reviewer** (blocks M2 release); a secured **oncologist +
  advocate pair** (blocks M5); the **share-alike licence governance decision** (blocks copying
  SA-source data into output).

---

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
|---|---|---|---|---|
| Controlled-access / identifiable patient data enters a card | Low | Critical | Hard-block in compliance gate; identifiability guard rejects individual-level shapes; out-of-scope by design (no patient DB); reviewer audit | Compliance reviewer / Maintainer |
| Non-commercial data (COSMIC/OncoKB) copied into a CC-BY output | Medium | High | Licence-class registry marks them NC; gate excludes NC from output (pointer-only); CI fails on NC content in a card | Compliance reviewer |
| Share-alike (CC-BY-SA) data forces the dataset's licence | Medium | High | Per-assertion licence tagging + separability; **governance decision** on dual-license / pointer-only / CC-BY-SA before any SA data is copied | Maintainer / Governance |
| Card read as medical/treatment advice | Medium | High | Persistent "research use — not medical advice" notice; patient-facing layer **blocked** behind oncologist + advocate sign-off; no per-patient ranking; routes to oncologist | Maintainer / Oncologist reviewer |
| LLM hallucinates or misattributes an extracted claim | Medium | High | LLM assists only; **every** extracted assertion human-verified with span citation; no-source-no-claim rule; uncited assertion fails CI | Scientific reviewer / Maintainer |
| Source version drift silently makes cards stale | High | Medium | Pinned source versions + manifest; `lastVerified`/`validUntil` **staleness fail-safe** auto-flags/withholds; refresh cadence (M6) | Maintainer |
| Per-article PMC licence mishandled (NC/ND quoted) | Medium | High | Per-article licence read + enforced in gate; NC/ND cited-not-quoted; only CC-BY/CC0 contribute copied text | Compliance reviewer |
| No scientific reviewer secured → cards can't release | Medium | High | Honest `TO BE SECURED`/`verifiedNeed:false`; dated outreach (reviewer by 2026-10-31); hold at M1 (pipeline + exemplar) until secured | Maintainer |
| No beneficiary secured → can't reach Definition of Shipped | High | High | Dated partner plan + **build-vs-mothball/pivot rule** (~2027-03-31): pivot to contribute to an existing open initiative (Open Targets/Pharos) or mothball — not ship to no one | Steward / Maintainer |
| ID-mapping errors conflate genes/targets | Medium | High | Canonical HGNC/Ensembl/UniProt mapping; cross-check across sources; reviewer spot-check; contradictions surfaced not hidden | Maintainer / Scientific reviewer |
| Project drifts toward for-profit benefit (e.g. a company's pipeline) | Low | High | Targets chosen by public-research value (rare cancers prioritised); no drug/company promotion; non-goal enforced; governance review | Maintainer / Governance |

---

## Security & privacy

**Threat surface.** Inclusion of controlled-access/identifiable data (compliance failure, the top
harm); licence violation (NC copied / SA contamination / NC-ND quoted); misuse of a research card as
clinical advice; LLM-introduced fabricated citations; supply-chain/source-integrity (a tampered
source feed); secrets leakage.

**Identifiability & data-minimisation (primary control).** The project stores **no individual-level
data** and operates only on aggregate/summary facts + citations, so the identifiability surface is
minimised **by construction**. The ingest path rejects individual-level/row-level shapes and any
controlled-access source. There is **no patient database** to breach.

**Provenance & integrity.** Pinned source versions + hashed build manifest detect source tampering
and make every card auditable and reproducible. No-source-no-claim and per-article licence checks run
in CI.

**LLM-specific controls.** The LLM is an **assistant for extraction only**; it never finalises a
shippable assertion. Every extracted statement carries a span-level citation and is **human-verified**
before inclusion; fabricated/uncitable statements are rejected. The LLM client is provider-neutral
(Elyos core/adapter rule); prompts/data sent to it contain only open-access content.

**Secrets / abuse.** **No secrets, tokens, or PII in logs, manifests, receipts, or committed files**
(Elyos rule). API keys for source services (where needed) are environment-injected, never committed.
Dependency + secret scanning in CI. The cards never promote a drug/company and never produce a
per-patient recommendation — enforced by scope + review.

---

## Sustainability & maintenance

After delivery, a named **maintenance rotation** owns the pipeline and the compliance gate; the
**steward** owns the beneficiary relationship and the outcomes log. Cards carry a **refresh cadence**
tied to source releases (Open Targets/ChEMBL/DepMap update on known schedules): a material source
update re-pins the manifest, re-runs validation, and triggers **re-review and re-sign-off** for
affected cards (version-scoped). The `lastVerified`/`validUntil` staleness fail-safe makes drift a
visible, gated event, not a silent error. Outcomes tracked: beneficiary uses, third-party
reproductions, and review/compliance status — **not** downloads or stars. Breadth (more targets /
indications) follows a documented, scientifically-reviewed process and only after the first set is
stable; the patient-facing layer is expanded only with continued oncologist + advocate review.

---

## Open questions

- **Dataset licence: CC-BY-4.0 vs CC-BY-SA-4.0?** Share-alike sources (ChEMBL, HPA, IUPHAR) may
  force SA on combined records. Options: (i) dual-license affected fields, (ii) pointer-only for SA
  data (no copied values), (iii) release the whole dataset CC-BY-SA. **Needs an Elyos governance
  decision before SA-source data is copied into output.**
- **Which cancer indication + target panel first? — decided in M0, not deferred,** because it gates
  M2–M6 (corpus, licence surface, reviewer profile). Selection criteria, scored before M2: (1) strong
  **open-data** coverage (good Open Targets/DepMap/ChEMBL signal); (2) **public-research value**, with
  **rare cancers prioritised** (thin commercial intelligence, high marginal benefit); (3) an
  **available scientific reviewer** for that biology; (4) **tractable scope** (a panel of ~10–25
  targets, not the genome). The specific choice is `TO BE SECURED`, but the **rule + deadline
  (2026-08-31) are fixed**.
- **Is the patient-facing layer (M5) pursued at all, or research-only?** Default: research-only ships
  first; M5 is pursued **only** if an oncologist + advocate pair is secured. Governance/steward call.
- **Reviewer compensation/credit model** — volunteer vs. a future funded lane for review hours (hard
  budget cap) without compromising independence (mirrors the COI/independence rules in *Governance*).
- **Grading rubric** — adopt/adapt Open Targets association scoring, a GRADE-like tier, or a hybrid?
  Needs scientific-reviewer input and must be published with the dataset.
- **Contribution path** — if pivoting (no standalone beneficiary), which open initiative (Open
  Targets, Pharos/IDG, DGIdb) best absorbs the cards as a curated overlay, and under what terms?

---

## References

- Proposal: `governance/proposals/drug-target-evidence-cards.md` *(TO BE WRITTEN — none yet on disk)*
- Portfolio entry: `planning/ROADMAP.md` → Track 8b (`drug-target-evidence-cards`, ⚪ med) and the
  binding Track 8 cancer-domain guardrails
- Elyos work rules & refusal guardrails: `CLAUDE.md`
- Good-deed definition & risk tiers: `docs/good-deed-definition.md`
- Task JSON schema: `packages/schema/src/schemas.ts`
- Sibling Elyos plans for house style: `planning/projects/public-official-guide/{PLAN,TASKS}.md`
- Open data sources & licences (verify per release): Open Targets (CC0), ChEMBL (CC BY-SA),
  UniProt (CC BY), Ensembl, Reactome (CC BY), Human Protein Atlas (CC BY-SA), DepMap (CC BY),
  IUPHAR/BPS Guide to Pharmacology (CC BY-SA), DGIdb, Pharos/IDG, ClinicalTrials.gov (US gov PD),
  PMC Open Access subset (per-article licence), TCGA open tier, GEO, PRIDE/ProteomeXchange
- Non-commercial (excluded, pointer-only): COSMIC, OncoKB
- Controlled-access (hard-blocked, out of scope): dbGaP, EGA, individual-level biobanks

---

## Appendix A — Improvements applied

The following 25 specific improvements were identified during drafting and have been **applied** to
this PLAN (and reflected in `TASKS.md`). Each notes where it landed.

1. **Compliance gate built first** — the licence/provenance gate is M0's first task and a CI release
   gate, not a doc (Solution §1; M0; TASKS M0).
2. **Explicit licence-class taxonomy** (`open` / `non-commercial` / `controlled-access-excluded`)
   added so COSMIC/OncoKB and dbGaP/EGA are handled by rule, not memory (Data §allowlist).
3. **COSMIC/OncoKB excluded-by-default with pointer-only allowance** spelled out, instead of a vague
   "non-commercial caution" (Data; Scope out-of-scope; Risks).
4. **Share-alike (CC-BY-SA) contamination risk surfaced** with three concrete resolution options and
   a required governance decision before copying SA data (Data §share-alike; Open questions; Risks).
5. **Per-article PMC-OA licence handling** — recognised that the OA subset mixes CC-BY/NC/ND/CC0 and
   added per-article enforcement (Data; Solution §5; Risks).
6. **Identifiability guard** added to reject individual-level/row-level shapes at ingest, beyond just
   blocking named controlled-access sources (Solution §1; Security).
7. **No-patient-database-by-construction** stated as a security control (versioned files only),
   minimising breach surface (Solution key decisions; Security).
8. **Reproducibility made a first-class success metric** via pinned source versions + a hashed build
   manifest + a third-party reproduction target (Metrics; Solution §6; M4).
9. **Staleness fail-safe** (`lastVerified`/`validUntil`, auto-flag/withhold) added for source-version
   drift, with a CI staleness test (Data; Metrics; Sustainability).
10. **Patient-facing review made a blocking gate** (oncologist **and** advocate), with an explicit
    "no sign-off, no release" rule and an oncologist veto (Exec summary; Quality gates; Governance).
11. **Two-tier risk model** (research cards MEDIUM, patient-facing HIGH) made explicit so reviewers
    and effort scale correctly (Quality gates; TASKS risk column).
12. **Evidence-grading rubric** introduced so readers can distinguish strong from weak evidence,
    with a metric requiring a grade on every assertion (Solution §2; Metrics; Open questions).
13. **Contradiction surfacing** — the assembler exposes disagreeing sources rather than silently
    picking one, improving scientific honesty (Solution §4; Data model; M2).
14. **Human-verified extraction** — the LLM drafts, a human verifies every assertion; the LLM never
    finalises a shippable claim (Solution §5; Security; Risks).
15. **Dated partner-acquisition plan** with concrete dates for panel selection, scientific reviewer,
    and the oncologist+advocate pair (Exec summary; Problem; Risks).
16. **Build-vs-mothball/pivot decision rule** (~2027-03-31) with a concrete pivot (contribute to Open
    Targets/Pharos) rather than shipping to no beneficiary (Exec summary; Risks; Open questions).
17. **Outcome-based metrics** (beneficiary use, reproductions, sign-off coverage) chosen over
    downloads/stars, with baselines/targets (Metrics; Sustainability).
18. **Indication + target-panel decision moved into M0** with scored selection criteria (rare cancers
    prioritised) because it gates M2–M6 (Roadmap; Open questions; TASKS M0).
19. **Core/adapter split honoured** — neutral pipeline core, vendor/source specifics in `adapters/`,
    LLM behind a provider-neutral client (Solution; Security; per Elyos rule).
20. **Attribution manifest** required in the release (every source + licence + attribution string),
    operationalising CC-BY/CC-BY-SA attribution duties (Data; M4).
21. **Version-scoped sign-off + COI/recusal + name-use limits** specified for reviewers, so approval
    doesn't silently carry across edits and credentials aren't misused (Governance).
22. **Aggregate-only ClinicalTrials.gov use** clarified (counts/phases as research context, never a
    patient trial-matching tool) to avoid drifting into clinical guidance (Data model; Scope).
23. **For-profit-drift guardrail** — targets chosen by public-research value, no drug/company
    promotion, enforced as a non-goal + risk (Non-goals; Risks; per Elyos guardrail).
24. **Kill-gate at M2** — if the scientific reviewer finds the synthesis no better than raw sources,
    pause before scaling (Roadmap M2; mirrors a thesis check).
25. **Schema-valid example Task JSON** for the M0 compliance-gate task, with honest
    `verifiedNeed:false`, real `outputLicense`, and `additionalProperties:false`-safe fields only
    (TASKS §Example task JSON).

---

## Review sign-off

A completeness + correctness review was performed against the spec, the schema, and the cancer
guardrails. Findings and fixes:

- **All 17 required H2 sections present and in order** (Executive summary → References). Confirmed.
- **Data/licensing leads the substance and is binding** — the cancer guardrails are stated as
  overriding, with an explicit allowlist, licence classes, and exclusions. Confirmed.
- **HIGH-risk patient-facing review is a blocking gate** (oncologist **and** advocate, with veto and
  "no sign-off, no release") — present in Exec summary, Quality gates, Governance, Roadmap M5, and
  the Risks table. Confirmed.
- **Schema alignment checked** — every field used in `TASKS.md` exists in
  `packages/schema/src/schemas.ts`; the example Task JSON uses only allowed fields
  (`additionalProperties:false`), required fields all present, enums valid, `verifiedNeed:false`.
  Fixed during review: ensured no `funded` task lacks `fundedBudgetUsd` (none are funded), and that
  `deliverable` values use only `pr|dataset|document|translation` (cards → `dataset`, write-ups →
  `document`, pipeline → `pr`).
- **Correctness fix (licensing):** initial draft treated PMC-OA as uniformly reusable; corrected to
  **per-article** licence handling (improvement #5). Initial draft did not flag CC-BY-SA share-alike
  contamination; **added** the governance decision (improvement #4).
- **Correctness fix (medical safety):** ensured no card field can express a per-patient recommendation;
  `clinicalLandscape` constrained to **aggregate** counts; `knownDrugs.researchStageNote` is research
  context, never a recommendation.
- **Honesty:** no partner/reviewer invented; all delivery-dependent items `TO BE SECURED` /
  `verifiedNeed:false`; a dated plan + mothball/pivot rule replace an open-ended TBD.
- **Residual items needing a human decision:** (1) CC-BY vs CC-BY-SA dataset licence; (2) the specific
  indication + target panel; (3) whether to pursue the patient-facing layer; (4) reviewer
  compensation model. All recorded in *Open questions*.

Sign-off: **Draft complete and internally consistent; ready for maintainer + compliance-reviewer
review.** Not yet ready to *ship* — gated on a secured scientific reviewer (M2), a secured beneficiary
(Definition of Shipped), and the share-alike licence decision.
