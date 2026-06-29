# Drug-Target-Evidence-Cards — PLAN.md

> Status: Draft · Version: 0.2.0 · Last updated: 2026-06-29 · Owner: TBD (maintainer) · Lane: donated

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

The space is crowded with mature, well-funded incumbents (Open Targets, Pharos/IDG, canSAR Black,
DGIdb, OncoKB, CIViC), but **none combines** *cancer-wide* + *open-data-only* + *per-assertion
verified licence-class provenance* + *explicit quality×maturity grading with surfaced contradictions*
+ *a reproducible, version-pinned build manifest*. The **defining differentiator** is that every
assertion carries a **verified licence-class provenance record**, making the output **provably
CC-BY-clean** (non-commercial sources excluded, share-alike tracked transitively) and **re-derivable**
from a pinned build manifest (source release + scoring algorithm + hash) — so other open projects can
redistribute it without re-auditing, which Open Targets (CC0 but no per-fact licence ledger), canSAR
(mixed terms), and DGIdb (silently NC-contaminated via OncoKB) do not offer. See
*Competitive landscape & differentiation*.

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
- Apply an explicit, documented, **cited** evidence-grading rubric — with **two separate axes**,
  `evidenceQuality` (study strength) and `evidenceMaturity` (in-silico / in-vitro / in-vivo /
  human-genetic / clinical-aggregate) — so card readers can tell strong evidence from weak **and**
  preclinical signal from clinical readiness, and so a DepMap cell-line signal is never read as
  clinical certainty. The rubric **adapts a published, cited scheme** (Open Targets harmonic-sum
  datatype aggregation / a levels-of-evidence scheme), **not GRADE** (which grades clinical-outcome
  certainty and does not fit heterogeneous target-credibility evidence).
- Make the dataset **provably reusable**: ship a **per-assertion licence-class ledger** (with
  transitive share-alike tracking) and a **version-pinned, hash-stamped build manifest** (source
  release + scoring algorithm + screen project) so a third party can re-derive, diff, and
  redistribute the cards without re-auditing licences.
- Operationalize the project as **one shared engine** (schema + compliance gate + rubric + assembler)
  consumed by the sibling card repos, with a **non-overlap rule**: this cancer-wide panel
  **explicitly excludes** the Ewing/sarcoma indications owned by `ewing-drug-target-evidence` and
  `ewing-biomarker-evidence-cards` (no double coverage).
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
| Reproducibility: cards regenerated from pinned source versions are **manifest-diff-explainable** | n/a | every value change on a re-run is **explained by a recorded source-release/algorithm diff** (no unexplained drift) — *not* byte-stable, since upstream rescoring (e.g. DepMap CERES→Chronos) legitimately changes values | CI re-run + manifest diff + per-card change-log |
| Evidence-grade present on every assertion | n/a | **100%** carry **both** `evidenceQuality` and `evidenceMaturity` graded against the published rubric | Schema validation |
| Grading inter-rater reliability (the project's core value) | n/a | second-curator agreement (Cohen's κ) on a sampled set **measured and reported per release** (target substantial agreement) | Dual-curator sample + κ computation |
| Contradiction-detection recall (does the pipeline catch known disagreements?) | n/a | **measured against a seeded set of known cross-source disagreements**; misses triaged | Contradiction-recall test vs curated gold set |
| Source-version staleness contained | n/a | **0** cards served as "current" past a source's `validUntil` without an auto-flag + re-review | Staleness test against `lastVerified`/`validUntil` |
| Independent reproduction by a third party | 0 | **≥ 1** party **independent of the maintainers** re-runs the pipeline and confirms the cards (a friendly-lab self-reproduction does not count) | Reproduction report (issue/PR) |
| Cards used in a real research/advocacy workflow | 0 | **≥ 1** documented use by a secured beneficiary that **informed a concrete decision** (seeded a target dossier, informed an advocacy brief) — qualitative, not a mere download | Beneficiary-attested log |
| Patient-facing layer (if pursued): oncologist + advocate sign-off before release | n/a | **100%** (blocking; no sign-off, no release) | Reviewers ledger; release gate |
| Patient-facing readability + "not medical advice" notice present | n/a | every page reviewed for plain-language + carries the notice | Advocate review + automated notice check |

The **defining success outcome** (Definition of Shipped): a real beneficiary (researcher, curator,
or advocacy org) uses the evidence cards in their work, with provenance verified, the compliance gate
green, and scientific (and, for any patient-facing content, oncologist + advocate) review recorded.

---

## Scope

**In scope**

- A versioned **Evidence Card schema** (JSON Schema) and a **cited evidence-grading rubric** with
  separate `evidenceQuality` × `evidenceMaturity` axes (see *Solution approach*).
- A **target-safety / on-target-liability dimension** (genetic constraint/essentiality, tissue
  expression-breadth) recorded alongside association evidence, so a card cannot over-promote a
  tractable-but-toxic target by reporting association strength alone.
- **Negative / absence-of-evidence** as first-class card content (e.g. "no dependency signal in
  lineage X" is informative), so cards are not biased toward positive evidence only.
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
- (**Deferred out of v1**, optional, gated) a **patient-facing plain-language layer** behind blocking
  oncologist + advocate review. It is HIGH-risk, blocking-gated, and outside the core value prop;
  v1 ships research cards + the reuse ledger first, and the advocate layer is added **only once an
  oncologist + advocate pair is secured** — shortening the reviewer-acquisition critical path.
- **Provenance, source-version pinning, and a staleness fail-safe** (`lastVerified`/`validUntil`),
  plus a **per-card change-log** keyed to source-release diffs (what assertion changed and why) to
  streamline reviewer re-sign-off.
- A **shared engine boundary**: this repo consumes (and helps build) the shared
  schema/gate/rubric/assembler engine, and its panel **excludes** the Ewing/sarcoma indications owned
  by the sibling repos (see *Competitive landscape & differentiation*).

**Out of scope (explicitly will NOT do)**

- **Any** controlled-access data (dbGaP, EGA, individual-level biobanks) or **any** identifiable /
  re-identifiable patient data — not ingested, not derived from, not referenced at row level.
- **Treatment, dosing, trial-selection, or clinical recommendations** for any individual; ranking
  therapies "best for patient X"; any clinical decision support.
- **Ranking targets by clinical-trial activity**, or **linking to recruiting trials** — even aggregate
  trial counts must not be turned into an endorsement or edge toward trial-matching. Counts only,
  with a "not trial-matching" notice.
- **Double-covering the Ewing/sarcoma indications** owned by `ewing-drug-target-evidence` /
  `ewing-biomarker-evidence-cards`; this cancer-wide panel excludes them (they are the siblings' depth
  pilots).
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

## Competitive landscape & differentiation

The space is crowded with mature, well-funded resources. **None** combines *cancer-wide* +
*open-data-only* + *per-assertion verified licence class* + *explicit quality×maturity grading* +
*surfaced contradictions* + *a reproducible build manifest*. Each overlaps in part:

- **Open Targets Platform** — the closest competitor. Target–disease associations across genetic,
  somatic, expression, literature, animal-model and pathway evidence; **harmonic-sum** scoring over
  ~7.8M associations; quarterly releases; **CC0**; open code. *Gap we fill:* it is a scored
  *platform/database*, not a curated, human-reviewed **narrative card** per target×cancer; the
  harmonic sum is opaque to non-experts; it offers **no per-assertion verified-licence provenance for
  downstream reuse** (a reuser must re-derive licences); it is not cancer-specialized, not
  advocate-facing, and scores contradictions away rather than surfacing them.
- **Pharos / IDG / TCRD** — target-centric; collates 70+ resources; signature **Target Development
  Level** (Tclin/Tchem/Tbio/Tdark). *Gap:* best-in-class tractability/illumination framing but
  **weakly disease/cancer-specific**; not a per-indication evidence synthesis; little provenance
  grading; not advocate-facing.
- **canSAR Black (ICR)** — largest public *oncology* drug-discovery knowledgebase; AI-based
  druggability / "drug-target-likeness." *Gap:* a *query knowledgebase*, not licence-verified reusable
  cards; downstream-redistribution provenance/licence terms are not the product; mixed reuse terms;
  not advocate-readable.
- **DGIdb** — aggregates drug–gene interactions from ~30+ sources (incl. CIViC, OncoKB); open
  downloads. *Gap:* interaction lists, not graded disease-contextual evidence; **inherits the licence
  heterogeneity of its sources (incl. OncoKB non-commercial) silently** — exactly the trap this
  project avoids by classing licences.
- **OncoKB (MSK)** — precision-oncology variant/therapeutic annotation; first FDA-partially-recognized
  somatic-variant DB; therapeutic Levels 1–4. ***Excluded from our output:*** **non-commercial /
  registration-gated** (pointer-only under its own terms), and clinically-oriented (variant→therapy)
  — the advice-adjacent zone this project deliberately avoids.
- **CIViC** — community-curated clinical interpretation of variants in cancer; **open (CC0), public
  API, open code** — the open analogue to OncoKB and a candidate **model / pivot target** for a
  contribution overlay. *Gap:* variant→clinical-significance focused, not target-credibility; rare-
  cancer coverage uneven.
- **My Cancer Genome (VICC)** — readable clinical context, but clinician-facing decision support
  (advice-zone), not open-data-reuse-first, and **less actively maintained/distinctive recently**.
- **Probe Miner** — rigorous quantitative chemical-probe/tractability signal (linked from Open
  Targets), but narrow (chemical probes only), not disease-contextual evidence.
- **DepMap (Broad)** — the dependency backbone (**CC-BY 4.0**, aggregate), but a *dataset*, not a card,
  and **version/algorithm-sensitive** (CERES→Chronos, batch effects — see *Solution approach*).

**Differentiators to win.**
1. **Per-assertion verified licence-class provenance** — the output is *provably* CC-BY-clean (NC
   excluded, SA tracked transitively), so other open projects can redistribute without re-auditing.
   The one thing Open Targets (CC0 but no per-fact ledger), canSAR (mixed), and DGIdb (silently
   NC-contaminated) do **not** offer.
2. **Reproducible, version-pinned build manifest** (source release + DepMap algorithm + hash) → cards
   are re-derivable and diffable, a research artifact rather than a moving black box.
3. **Published, cited quality×maturity grading with surfaced contradictions** → readers distinguish a
   powered GWAS from one cell line and see disagreement, not an opaque harmonic sum.
4. **Human scientific sign-off, version-scoped**, kept honest by the M2 kill-gate.
5. **Advocate-readable, gated plain-language layer** — an audience the incumbents ignore (deferred
   out of v1).
6. **Open-only by construction** — a cleaner reuse story than OncoKB (NC) or canSAR (mixed).
7. **Shared engine + non-overlapping panels vs the Ewing siblings.** The winning structural move is
   **one schema + one compliance gate + one rubric + one assembler engine** (a `target-evidence-core`
   library), with `ewing-drug-target-evidence` as the **deep single-disease pilot** (rare-cancer
   depth, dedicated sarcoma reviewer), `drug-target-evidence-cards` as the **cancer-wide breadth
   instance that explicitly excludes the Ewing/sarcoma indications the sibling owns** (no double
   coverage), and `ewing-biomarker-evidence-cards` reusing the same engine for the **biomarker** card
   type. The differentiator is the *generalized engine + non-overlapping panels*, not three hand-built
   silos — this is both the clearest correctness fix and the largest efficiency win.

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
   (see *data model* below) and a documented, versioned **evidence-grading rubric** built on **two
   separate, non-collapsible axes** so that incommensurable things are not averaged into one
   misleading label:
   - `evidenceQuality` — strength of the underlying study (e.g. a powered GWAS vs. a single cell
     line), and
   - `evidenceMaturity` — `in-silico` | `in-vitro` | `in-vivo` | `human-genetic` | `clinical-aggregate`,
     a **weighted** axis because evidence types are **not interchangeable**: human genetic support
     roughly doubles (≈2–2.6×) the odds of eventual clinical approval (Nelson et al., *Nat Genet*
     2015; Minikel et al., *Nature* 2024), so genetic vs. preclinical-only evidence must be weighted
     with a documented, cited basis.
   The rubric **adapts a published, cited scheme** — Open Targets' harmonic-sum datatype aggregation
   or a levels-of-evidence/STAR-style scheme — and **explicitly does not use GRADE**, which grades
   *certainty of a clinical-outcome estimate from comparative studies* and would imply clinical
   certainty these cards cannot support. Every assertion carries **both** axes; the rubric is
   published with the dataset (or the M2 kill-gate — "no better than reading raw sources" — fires).

3. **Source connectors (`adapters/sources/*`).** One adapter per open source (Open Targets GraphQL,
   ChEMBL, UniProt, Ensembl, Reactome, Human Protein Atlas, DepMap, ClinicalTrials.gov, PMC-OA,
   DGIdb/Pharos). Each adapter declares the source's registry entry, pins the **release/version**,
   records `retrievalDate`, and emits normalised, ID-mapped facts with attached provenance. Adapters
   are the only Elyos-style vendor-specific code; the pipeline core stays neutral.
   - **DepMap is not "just another source."** Its dependency scores are **algorithm-version-sensitive**:
     the pipeline moved from **CERES to Chronos at 21Q2** (different copy-number / common-essential
     correction), and **cross-project Broad-Achilles vs. Sanger-Score screens carry batch effects**
     driven by library and assay length, needing ComBat-style correction (Dempster et al., *Genome
     Biol* 2019; Cancer Data Science). Therefore a dependency assertion **pins not just the DepMap
     release but the scoring algorithm (e.g. Chronos) and the screen project (Broad/Sanger)**, and
     each dependency assertion carries a `replicatesAcrossProjects` flag, a **lineage-specificity**
     note, and the standard caveats (e.g. **−0.5 Chronos** dependency threshold, **90th-percentile
     common-essential** exclusion). A dependency grade may not rise above "Limited" on a single
     un-replicated project signal (see *Open questions*).
   - **Ensembl licence is verified per data type**, not blanket-classed: Ensembl is largely
     no-restriction, but **some incorporated third-party datasets carry their own terms** — the
     adapter records the licence of the specific data type it pulls.

4. **Assembly + ID mapping (`packages/assemble`).** Resolves a target to canonical identifiers
   (HGNC/Ensembl/UniProt) and an indication to an ontology id (EFO/MONDO/OncoTree), then assembles
   per-source facts into a single card, deduplicating and attaching grades + provenance. Surfaces
   **contradictions** (sources that disagree) rather than silently picking one — and applies a
   **documented contradiction-aggregation policy** so two curators grade the same card the same way.
   Each contradiction is tagged with a **taxonomy label**: `measurement-difference` (assay/method
   artefact), `biological-context-dependence` (true lineage/subtype-specific effect), or
   `version-drift` (an upstream re-release changed a value). The policy states **how a graded card
   net-summarizes** under disagreement (it never silently resolves; it records the disagreement and
   the net read), and a **contradiction-detection recall** test checks the pipeline against a curated
   gold set of known disagreements.

5. **Literature extraction (`packages/litextract`, LLM-assisted, human-verified).** Pulls
   open-access full text from the **PMC-OA subset** (checking the **per-article licence** — the OA
   subset mixes CC-BY, CC-BY-NC, CC-BY-NC-ND, and CC0; only commercially-reusable licences feed a
   CC-BY card), uses Claude to draft candidate evidence statements with **mandatory span-level
   citations** (long-context reading + structured citation output; prompt-cached source text; a strict
   "every claim must cite a quoted span or be dropped" output contract), then routes **every**
   statement to a human curator for verification before it can enter a card. The LLM never writes a
   final, shippable assertion unreviewed.

   **Claude's bounded, assistive roles across the pipeline** (provider-neutral client; model
   selection/pricing per the Claude API skill): (a) span-cited PMC-OA evidence extraction (above);
   (b) **structuring/normalizing** heterogeneous source facts (Open Targets / ChEMBL / DepMap /
   UniProt) into the JSON-schema card via tool-use / Ajv-validated constrained output; (c)
   **cross-source contradiction *detection*** — flagging "source A up-regulated, source B null/down"
   and drafting a candidate taxonomy label for the curator to confirm (**surface, never resolve**);
   (d) drafting tractability/safety and limitations narrative (Pharos TDL, Probe Miner thresholds,
   genetic-constraint/expression-breadth) for the curator to edit; (e) plain-language rewriting of
   *already-signed-off* cards (HIGH-tier layer, never auto-published); (f) **licence-class triage** —
   pre-classifying a new source's licence text into `open`/`open-sa`/`NC`/`excluded` as a *draft*.

   **Hard human gates the LLM must NOT cross:** no clinical / treatment / per-patient recommendation
   (structurally impossible in the schema and reinforced in prompts); **no final evidence grade and
   no final/shippable card** (the LLM proposes, the rubric + scientific reviewer dispose); no
   fabricated or uncited claim (dropped; "no source, no claim" is CI-enforced, not model-trusted);
   **no binding licence or medical/legal determination** (the compliance reviewer makes the licence
   call, the oncologist owns medical-safety framing); no contradiction *resolution*; and **only
   open-access content is ever sent to the API** — never controlled-access/identifiable data (none
   exists by construction) and never secrets.

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
  pathway|tractability|literature|clinical-context|safety), statement, direction,
  evidenceQuality, evidenceMaturity(in-silico|in-vitro|in-vivo|human-genetic|clinical-aggregate),
  isAbsenceOfEvidence(bool), sources[] }`
  *(two grade axes — quality and maturity — never collapsed; `isAbsenceOfEvidence` lets "no
  dependency signal in lineage X" be recorded as informative, not omitted)*
- `dependencyDetail?` (for `dependency` evidence): `{ depmapRelease, scoringAlgorithm(e.g. Chronos),
  screenProject(Broad|Sanger), replicatesAcrossProjects(bool), lineageSpecificity, caveats }`
- `targetSafety`: `{ geneticConstraint, essentiality, expressionBreadth, onTargetLiabilityNote,
  sources[] }`
  *(so a tractable-but-toxic target is not over-promoted by association strength alone)*
- `tractability`: `{ smallMolecule, antibody, otherModality, structuresAvailable, sources[] }`
- `knownDrugs[]`: `{ name, chemblId, mechanism, modality, researchStageNote, sources[] }`
  *(research context only — never framed as a recommendation)*
- `clinicalLandscape`: `{ aggregateTrialCounts, phases, sourcedFrom: ClinicalTrials.gov, sources[] }`
  *(aggregate counts only; no patient-level data; **no ranking by trial activity; no links to
  recruiting trials**; not a trial-matching tool)*
- `limitations`, `contradictions[]`: `{ statement, taxonomy(measurement-difference|
  biological-context-dependence|version-drift), disagreeingSources[], netRead }`, `openQuestions[]`
- `provenance.sources[]`: `{ sourceName, accession, releaseVersion, retrievalDate, url, license,
  licenseClass(open|open-sa|non-commercial|controlled-access-excluded), upstreamProvenance[],
  lastVerified, validUntil }`
  *(`upstreamProvenance[]` records transitive source-of-source — e.g. ChEMBL-derived facts arriving
  via CC0 Open Targets still carry the ChEMBL share-alike licence class)*
- `changeLog[]`: `{ fromCardVersion, toCardVersion, sourceReleaseDiff, assertionsChanged, reason }`
  *(per-card diff keyed to source-release changes, to streamline reviewer re-sign-off)*
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

**Source allowlist (initial, each verified before use).** *Classes:* `open` = permissively reusable in
CC-BY with attribution; `open-sa` = open **but share-alike** (CC-BY-SA — reusable, but obligations
**propagate** to combined records and must be tracked, including **transitively**); `NC` =
non-commercial, **excluded** from output (pointer-only); `excluded` = controlled access, hard-blocked.
**`open-sa` is its own class** (not lumped into `open`) precisely so the gate can enforce share-alike
*propagation*, not merely "is it open."

| Source | Content | Licence (to verify per release) | Class |
|---|---|---|---|
| Open Targets Platform | target–disease association evidence | CC0 1.0 | open *(but see transitive note — ingests ChEMBL)* |
| ChEMBL | bioactivity, drugs, mechanisms | CC BY-SA 3.0 Unported | **open-sa** |
| UniProt | protein function/annotation | CC BY 4.0 | open |
| Ensembl / Ensembl genes | gene models, IDs | per data type (mostly no-restriction; **some incorporated datasets carry third-party terms**) | open *(verify per data type)* |
| Reactome | pathways | CC BY 4.0 | open |
| Human Protein Atlas | expression/localisation | CC BY-SA 3.0 | **open-sa** |
| DepMap (Broad) | genetic dependency (aggregate) | CC BY 4.0 | open *(pin release + algorithm + screen project)* |
| IUPHAR/BPS Guide to Pharmacology | targets/ligands | CC BY-SA 4.0 | **open-sa** |
| DGIdb | drug–gene interactions (aggregated) | open (MIT/CC0; verify per upstream) | open (verify) |
| Pharos / IDG (TCRD) | target development level | open (NIH) | open (verify) |
| ClinicalTrials.gov | **aggregate** trial counts/phases | U.S. gov public domain (attribution/linkback per ToU) | open |
| PMC Open Access subset | open-access full text | **per-article**: CC-BY / CC-BY-NC / CC-BY-NC-ND / CC0 | mixed — per-article |
| TCGA (open tier) | aggregate/summary only | NIH open-access tier | open (open tier only) |
| GEO | expression series (aggregate) | per-submission (verify) | open (verify per series) |
| PRIDE / ProteomeXchange | proteomics (aggregate) | CC-BY / CC0 (verify) | open (verify) |
| **COSMIC** | somatic mutations | **non-commercial** (registration) | **NC — excluded** |
| **OncoKB** | variant/therapeutic annotation | **non-commercial / academic** | **NC — excluded** |
| dbGaP / EGA / individual biobanks | individual-level | controlled access | **excluded** |

**Share-alike note (the single biggest legal risk).** ChEMBL (**CC-BY-SA 3.0 Unported**), HPA, and
IUPHAR/GtoPdb (CC-BY-SA) impose **share-alike** on derivatives *of their data*: adaptations must be
redistributed under the same licence. Two compounding hazards:
- **Transitive SA via CC0 aggregators.** Open Targets is **CC0**, but it *ingests ChEMBL* for its
  drug/known-drug datatype. Copying Open Targets' drug evidence may copy **ChEMBL-derived content that
  still carries SA obligations transitively.** The licence registry therefore tracks
  **upstream-of-upstream provenance** (`upstreamProvenance[]`), not just the immediate source's
  licence, so SA reach is detected even when laundered through a CC0 layer.
- **A combined card is plausibly a single adaptation**, and per-field tagging may not cleanly sever SA
  reach under SA 3.0. "Keep SA facts separable per assertion" is necessary but **may not be
  sufficient.** The **safest defaults** are therefore (i) treat SA-source data as
  **pointer/derived-fact-with-attribution only** (no copied values), or (ii) **release the dataset
  CC-BY-SA-4.0**. The v0.1 default of CC-BY-4.0 is the *riskier* option; **this v0.2 flags that the
  default should be revisited toward CC-BY-SA** and records it as a blocking governance decision
  (see *Open questions*). No SA-source data is copied into output until that decision is made.

**Per-article licence handling for PMC-OA.** The OA subset is **not uniformly reusable**: it mixes
CC-BY, CC-BY-NC, CC-BY-NC-ND, and CC0. The litextract adapter reads each article's machine-readable
licence and **only** lets commercially-reusable licences (CC-BY, CC0) contribute copied text/close
paraphrase to a CC-BY card; NC/ND articles may be **cited and linked** but not quoted/redistributed.
This is enforced in the compliance gate per article, not assumed.

**Provenance model.** Every assertion → ≥ 1 `Source` record `{sourceName, accession, releaseVersion,
retrievalDate, url, license, licenseClass(open|open-sa|NC|excluded), upstreamProvenance[],
lastVerified, validUntil}`. The assembler may not emit an assertion without it; a citation-coverage
validator enforces it in CI. The **build manifest** pins every source release **+ scoring algorithm
(e.g. DepMap Chronos) + screen project + hash**, so the cards are reproducible/auditable and a re-run
is **manifest-diff-explainable** (not byte-stable — upstream rescoring legitimately changes values).

**Staleness is fail-safe.** Sources release new versions (Open Targets, ChEMBL, DepMap on regular
cadences); a card pinned to an old release can silently go stale. Each `Source` carries `lastVerified`
+ `validUntil` (per-source review interval). At validation/render time, an assertion whose source is
**past `validUntil`** is **auto-flagged** ("source release out of date — re-verify") and, for the
patient-facing layer, **withheld** until re-verified and re-signed-off. A staleness test enforces
this.

**Output licensing.** Code: **MIT**. Cards/dataset/content/docs: **CC-BY-4.0 *or* CC-BY-SA-4.0 —
pending the share-alike governance decision above**, with this v0.2 noting that **CC-BY-SA-4.0 (or
SA-data-as-pointer-only) is the safer default** given transitive ChEMBL/HPA/IUPHAR share-alike. The
dataset ships a `LICENSES`/`ATTRIBUTION` manifest listing every source, its licence (and class), and
its required attribution string — itself the **per-assertion licence-class reuse ledger** that is the
project's primary differentiator.

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
  hand-curated exemplar card exist before bulk work — **built as the shared `target-evidence-core`
  engine** the sibling repos consume.
  *Exit:* compliance gate (allowlist + licence registry + **`open`/`open-sa`/`NC`/`excluded` classes
  with transitive SA tracking** + per-assertion citation + identifiability guard) merged and
  **passing in CI**; Evidence Card JSON Schema + **cited grading rubric with separate
  `evidenceQuality` × `evidenceMaturity` axes (no GRADE) + target-safety fields** merged; **one
  exemplar card** built by hand for a chosen target, fully cited, scientific-reviewer draft-reviewed;
  monorepo + CI green; "not medical advice" framing wired in. **Initial indication + target panel
  selected** (criteria in *Open questions*; **excludes Ewing/sarcoma**) so M2 builds against a real
  corpus.

- **M1 — Ingestion & provenance pipeline.**
  *Goal:* reproducible ingest → assemble → validate from pinned open sources.
  *Exit:* source adapters for the core open sources (Open Targets, ChEMBL, UniProt, Ensembl,
  Reactome, HPA, DepMap, ClinicalTrials.gov aggregate); **DepMap adapter pins release + scoring
  algorithm + screen project and emits `replicatesAcrossProjects`/lineage notes**; ID mapping; build
  manifest with pinned versions/algorithms/hashes; compliance gate + provenance validator + staleness
  check enforced in CI; the exemplar card regenerated **manifest-diff-explainably** (not byte-stable)
  by the pipeline.

- **M2 — Research evidence cards (scientific-reviewer gated).**
  *Goal:* the initial target panel × indication as cited, graded cards.
  *Exit:* cards for the chosen panel assembled, every assertion graded on **both** axes
  (`evidenceQuality` × `evidenceMaturity`); **scientific reviewer sign-off recorded** for each
  released card; contradictions surfaced **and tagged with the taxonomy** (measurement /
  biological-context / version-drift) under the documented contradiction-aggregation policy; a
  **second-curator inter-rater κ** measured on a sample; "research use — not medical advice" on every
  card; compliance gate green. **Kill-gate:** if the scientific reviewer finds the synthesis no more
  reliable/useful than reading the raw sources, pause and reassess before scaling.

- **M3 — Literature extraction & enrichment (human-verified).**
  *Goal:* enrich cards with PMC-OA evidence under per-article licence checks + human verification.
  *Exit:* litextract pipeline pulls OA full text, checks **per-article** licence, drafts candidate
  statements with span citations; **every** statement human-verified before inclusion; NC/ND articles
  cited-not-quoted; enriched cards re-reviewed; compliance gate green.

- **M4 — Publishing & open dataset release.**
  *Goal:* the cards usable by real people, openly.
  *Exit:* accessible (WCAG 2.2 AA) static rendering + downloadable CC-BY dataset + attribution
  manifest; versioned release; reproduction instructions; **≥ 1 third-party reproduction** attempted.

- **M5 — Patient-facing plain-language layer (HIGH-tier, OPTIONAL, DEFERRED OUT OF v1, blocking-gated).**
  *Goal:* faithful, education-only summaries for patients/advocates.
  *Goal-gate:* **deferred out of v1 by default** (to shorten the reviewer-acquisition critical path)
  and **builds only if** a credentialed **oncologist** and a **patient advocate** are secured. *Exit (Definition of Shipped for this layer):* plain-language layer drafted from the cited
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
| Share-alike (CC-BY-SA) data forces the dataset's licence | Medium | High | `open-sa` licence class + per-assertion licence tagging + separability; **governance decision** (v0.2 leans CC-BY-SA / pointer-only) before any SA data is copied | Maintainer / Governance |
| **Transitive** SA contamination (ChEMBL-derived facts arriving via CC0 Open Targets) | Medium | High | Registry tracks **upstream-of-upstream provenance** (`upstreamProvenance[]`); gate enforces SA class even when laundered through a CC0 layer | Compliance reviewer |
| DepMap algorithm/screen drift mis-grades dependency (CERES→Chronos, Broad/Sanger batch effects) | High | Medium | Pin release + algorithm + screen project; `replicatesAcrossProjects` flag + lineage note + common-essential caveats; no grade above "Limited" on single un-replicated signal; manifest-diff-explainable (not byte-stable) | Maintainer / Scientific reviewer |
| Curators grade the same card differently (no conflict-aggregation policy) | Medium | Medium | Documented contradiction-aggregation policy + taxonomy; second-curator inter-rater κ metric; contradiction-detection recall test | Scientific reviewer |
| Sibling overlap: duplicated engine work / double-covered Ewing indications | Medium | Medium | Shared `target-evidence-core` engine; declared non-overlap rule (cancer-wide panel excludes Ewing/sarcoma); panel selection checked against sibling territory | Maintainer / Governance |
| Card over-promotes a tractable-but-toxic target (association-only) | Medium | Medium | Mandatory `targetSafety` dimension (genetic constraint, essentiality, expression breadth); absence-of-evidence recorded, not hidden | Scientific reviewer |
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

## Adjacent opportunities

These are **not v1 scope** but are the natural, high-leverage extensions the architecture enables:

- **`target-evidence-core` (the highest-leverage spin-off).** The schema + compliance gate + rubric +
  assembler factored as a **reusable library** that all card projects instantiate (cancer-wide,
  Ewing-specific, and future non-cancer rare-disease). This resolves the sibling overlap and is the
  structural payoff of v0.2.
- **Ewing sibling integration.** `ewing-drug-target-evidence` and `ewing-biomarker-evidence-cards` are
  deep single-disease pilots feeding the shared engine; the biomarker repo reuses the same
  provenance/grading machinery on a biomarker schema.
- **`oncogene-knowledge-graph`.** Emit the cards as RDF/graph triples (target–evidence–source) with
  **licence-class edges** — a provenance-first open knowledge graph other tools can query.
- **`biomarker-extraction`.** Generalize the PMC-OA span-cited extraction pipeline as a standalone
  open biomarker/assertion-extraction service.
- **`ewing-immunotherapy-target-catalog`.** A modality-specific instance (surface antigens / IO
  targets) on the same engine.
- **`target-evidence-mcp` (MCP server).** Expose the cards + provenance/licence ledger as an MCP tool
  so any agent can query "open, cited evidence for target X in cancer Y" with attribution and grades —
  a natural agent-ecosystem distribution channel and a candidate **contribution/overlay deliverable**
  if the project pivots into Open Targets/Pharos/CIViC.
- **Reuse-compliance toolkit.** The licence-class registry + transitive-SA tracker spun out as a
  generic open-data licence-compliance linter, useful well beyond cancer.

---

## Open questions

- **Dataset licence: CC-BY-4.0 vs CC-BY-SA-4.0?** Share-alike sources (ChEMBL, HPA, IUPHAR) may
  force SA on combined records — **including transitively** via even CC0 Open Targets (which ingests
  ChEMBL). Options: (i) dual-license affected fields, (ii) pointer-only for SA data (no copied
  values), (iii) release the whole dataset CC-BY-SA. **v0.2 view:** option (ii)/(iii) is the *safer*
  default and CC-BY-4.0 is the riskier one; **needs an Elyos governance decision before SA-source
  data is copied into output.**
- **Sibling boundary (engine + non-overlap).** Will the three card repos share **one
  `target-evidence-core` engine**, and will the cancer-wide panel **exclude** the Ewing/sarcoma
  indications the sibling owns? (v0.2 assumes **yes** to both.) If not, how is double coverage
  justified?
- **Which cancer indication + target panel first? — decided in M0, not deferred,** because it gates
  M2–M6 (corpus, licence surface, reviewer profile). Selection criteria, scored before M2: (1) strong
  **open-data** coverage (good Open Targets/DepMap/ChEMBL signal); (2) **public-research value**, with
  **rare cancers prioritised** (thin commercial intelligence, high marginal benefit) — **checked
  against the Ewing sibling's territory to avoid collision**; (3) an **available scientific reviewer**
  for that biology; (4) **tractable scope** (a panel of ~10–25 targets, not the genome). The specific
  choice is `TO BE SECURED`, but the **rule + deadline (2026-08-31) are fixed**.
- **Grading rubric** — adopt/adapt which **published, cited** scheme (Open Targets harmonic-sum
  datatype components? a levels-of-evidence variant?), and will it carry the **separate
  `evidenceQuality` vs. `evidenceMaturity` axes** v0.2 specifies (not GRADE)? Needs scientific-reviewer
  input and must be published with the dataset.
- **DepMap handling** — pin to which algorithm baseline (Chronos), and is **cross-project
  (Broad + Sanger) replication required** before a dependency assertion may grade above "Limited"?
- **Safety dimension** — will cards include on-target-safety / genetic-constraint signals (v0.2
  default: **yes**), or stay association-only (and thus risk over-promoting toxic-but-tractable
  targets)?
- **Is the patient-facing layer (M5) pursued at all, or research-only?** Default (v0.2): **deferred
  out of v1**; research-only ships first; M5 is pursued **only** if an oncologist + advocate pair is
  secured. Governance/steward call.
- **Reviewer compensation/credit model** — volunteer vs. a future funded lane for review hours (hard
  budget cap) without compromising independence (mirrors the COI/independence rules in *Governance*).
- **Contribution / pivot path** — if no standalone beneficiary by ~2027-03-31, which open initiative
  best absorbs the cards as a curated overlay, and under what terms: Open Targets, Pharos/IDG, **CIViC**
  (the closest open, CC0, community-curated analogue), or an **MCP overlay**?

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

---

## Changelog — v0.2 (analysis merged)

This version merges `COMPETITIVE-ANALYSIS.md` (2026-06-29) into the plan. No facts were invented;
guardrails were preserved or strengthened, never weakened.

**Correctness / schema / licence fixes applied**

- **Grading rubric corrected.** Dropped GRADE (which grades clinical-outcome certainty); adopted a
  **cited** scheme (Open Targets harmonic-sum / levels-of-evidence). **Split the single grade into two
  axes** — `evidenceQuality` × `evidenceMaturity` (in-silico/in-vitro/in-vivo/human-genetic/
  clinical-aggregate), maturity **weighted** by the genetic-support literature (Nelson 2015, Minikel
  2024). (Goals; Solution §2; Data model; Metrics; M0/M2.)
- **Preclinical-vs-clinical** made a **separate, structurally-enforced weighted axis** (`evidenceMaturity`)
  so a DepMap cell-line signal cannot be read as clinical readiness. (Solution §2; Data model.)
- **DepMap treated as algorithm-version-sensitive** (CERES→Chronos at 21Q2; Broad/Sanger batch
  effects, ComBat). Dependency assertions now **pin release + scoring algorithm + screen project** and
  carry `replicatesAcrossProjects` / lineage / common-essential caveats; **"byte/field-stable"
  reproducibility replaced with "manifest-diff-explainable."** (Solution §3; Data model; Metrics;
  Provenance; M1; Risks.)
- **Contradiction policy operationalized:** documented aggregation policy + **taxonomy**
  (measurement-difference / biological-context-dependence / version-drift), `netRead`, and a
  **contradiction-detection recall** metric. (Solution §4; Data model; Metrics; M2; Risks.)
- **ChEMBL CC-BY-SA share-alike resolved:** added a distinct **`open-sa` licence class**, **transitive
  upstream-of-upstream provenance** tracking (SA via CC0 Open Targets), and a stated lean toward
  **CC-BY-SA / pointer-only** as the safer default. (Data §allowlist/§share-alike; Provenance; Output
  licensing; Risks; Open questions.)
- **Sibling differentiation operationalized:** a shared **`target-evidence-core` engine** (one
  schema/gate/rubric/assembler) consumed by `ewing-drug-target-evidence` and
  `ewing-biomarker-evidence-cards`, with this cancer-wide panel **excluding** Ewing/sarcoma
  indications the sibling owns. (Goals; Scope; Competitive landscape §7; Adjacent opportunities; M0;
  Open questions; Risks.)
- **Other fixes:** Ensembl licence verified **per data type**; **target-safety / on-target-liability**
  fields added; **absence-of-evidence** made first-class; **per-card change-log** added;
  ClinicalTrials.gov constrained further (**no ranking by trial activity, no recruiting-trial links**);
  metrics hardened (**inter-rater κ**; independent + decision-informing qualitative bars on
  reproduction/use).

**Strategy integrated**

- New **"Competitive landscape & differentiation"** section (Open Targets, Pharos/IDG/TCRD, canSAR
  Black, DGIdb, OncoKB [excluded], CIViC, My Cancer Genome [abandoned], Probe Miner, DepMap), with the
  seven differentiators — anchored on **per-assertion verified licence-class provenance (provably
  CC-BY-clean) + a version-pinned build manifest.**
- **Claude API leverage folded into architecture** (Solution §5): PMC-OA span-level-cited extraction
  under per-article licence checks; schema-constrained structuring/normalizing; cross-source
  contradiction **detection (surface, never resolve)** — with explicit **hard human gates** (no
  clinical/per-patient recommendations; no final grade/card; no licence or medical/legal
  determination; open-access content only).
- **Optimizations folded into the Roadmap** (M0/M1/M2/M5) and the patient-facing layer **deferred out
  of v1**.
- New **"Adjacent opportunities"** section (`target-evidence-core` engine; ties to
  `oncogene-knowledge-graph` / `biomarker-extraction` / `ewing-immunotherapy-target-catalog`; an MCP
  server; a reuse-compliance toolkit).
- **Open questions merged** (transitive-SA licence default, sibling boundary, DepMap baseline, safety
  dimension, CIViC/MCP pivot path).

**Preserved unchanged:** all cancer-domain guardrails (open data only; per-source licence verify;
provenance per assertion; research-not-advice), the compliance-gate-first posture, the two-tier risk
model with blocking HIGH-tier patient-facing sign-off, the honest `TO BE SECURED` / `verifiedNeed:
false` stance, and the dated partner/mothball-pivot rule.
