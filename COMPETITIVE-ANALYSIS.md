# Competitive + Improvement Analysis — `drug-target-evidence-cards`

> Scope: Elyos cancer-wide good-deed project producing source-cited, provenance-first **drug-target
> evidence cards** (target × cancer indication) from OPEN data (Open Targets, ChEMBL, DepMap, Pharos,
> UniProt, Reactome, HPA, ClinicalTrials.gov aggregate, PMC-OA). Research evidence, NOT treatment
> advice. Cancer-wide sibling of `ewing-drug-target-evidence`. Medium risk-tier (HIGH for any
> patient-facing layer). Analysis date: 2026-06-29.

---

## 1. Correctness & completeness review of PLAN.md

The PLAN is unusually mature: compliance-gate-first, licence-class taxonomy, per-assertion provenance,
staleness fail-safe, two-tier risk model, dated partner/mothball rule, and an honest `TO BE SECURED`
posture. The following are concrete gaps, errors, and weak spots.

**A. Evidence-grading methodology is under-specified and conflates incommensurable axes.**
The plan defers the rubric to an Open Question and gestures at "Open Targets-style association scoring /
GRADE-like tiers (Strong/Moderate/Limited/Preclinical-only)." Two problems:
- **GRADE is the wrong borrowed framework.** GRADE grades *certainty of a clinical-outcome estimate
  from comparative studies*; it does not fit "is gene X a credible target in cancer Y," which is an
  aggregation of heterogeneous, non-comparative evidence (genetic association, somatic frequency,
  dependency, tractability). Importing GRADE language risks implying clinical certainty the cards
  cannot support. A target-evidence rubric is closer to **Open Targets' harmonic-sum datatype
  aggregation** ([Open Targets scoring](https://platform-docs.opentargets.org/getting-started)) or a
  **levels-of-evidence/STAR-style** scheme — but those must be *cited and adapted*, not invented, or
  the kill-gate ("no better than reading raw sources") will fire.
- **A single per-assertion grade collapses two different things**: *strength of the underlying study*
  (e.g. a powered GWAS vs. one cell line) and *relevance/direction toward target credibility*. The
  schema's `grade` field should be split (evidence-quality vs. association-direction/effect), or the
  rubric will produce misleading "Strong" labels on directionally weak evidence.

**B. Preclinical-vs-clinical conflation risk is named but not structurally enforced.**
The plan correctly bans per-patient recommendations and constrains `clinicalLandscape` to aggregate
counts, but the data model lets `associationEvidence[].evidenceType=clinical-context` and
`knownDrugs[]` sit beside `dependency`/`expression` evidence with the same grade vocabulary. A reader
can easily read "Strong" dependency evidence (a DepMap CRISPR signal in cell lines) as clinical
readiness. **Human genetic support roughly doubles (≈2–2.6×) the odds of clinical approval**
([Nelson et al., *Nat Genet* 2015](https://www.nature.com/articles/ng.3314);
[Minikel et al., *Nature* 2024](https://www.nature.com/articles/s41586-024-07316-0)) — so genetic vs.
preclinical-only evidence is *not* interchangeable and the rubric must weight by evidence type with a
documented, cited basis. Recommend a mandatory `evidenceMaturity` axis (in-silico / in-vitro /
in-vivo / human-genetic / clinical-aggregate) separate from `grade`.

**C. DepMap dependency reproducibility is treated as "just another source" — it is not.**
DepMap dependency scores are **algorithm-version-sensitive**: the pipeline moved from **CERES to
Chronos at 21Q2**, and the two handle copy-number/common-essential correction differently
([Cancer Data Science blog](https://cancerdatascience.org/blog/posts/ceres-chronos/)). Cross-project
(Broad Achilles vs. Sanger Score) screens show **clear batch effects** driven by library and assay
length, needing ComBat correction (Pearson rose 0.658→0.765 after correction)
([Dempster et al., *Genome Biol* 2019](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6925302/)). Implications
the plan misses: (1) a dependency assertion must pin **not just the DepMap release but the scoring
algorithm and screen project**; (2) a "dependency" grade should encode whether the signal replicates
across Broad *and* Sanger, lineage-specificity, and the standard **−0.5 Chronos / 90th-percentile
common-essential** caveats; (3) "byte/field-stable reproducibility" (a success metric) is unattainable
across DepMap releases because upstream rescoring changes values — the metric should be *manifest-diff
explainable*, not byte-stable.

**D. Conflicting-evidence handling is asserted but the resolution policy is missing.**
The plan says the assembler "surfaces contradictions rather than silently picking one" — good — but
there is no policy for *how a graded card net-summarizes* when sources disagree (e.g. Open Targets
genetic signal positive, DepMap dependency null, expression down). Without a documented
conflict-aggregation rule, two curators will grade the same card differently, undermining
reproducibility and the reviewer kill-gate. Need an explicit, cited contradiction taxonomy
(measurement-difference vs. true biological context-dependence vs. version drift).

**E. License compatibility — the ChEMBL CC-BY-SA share-alike issue is the single biggest legal risk and
the plan slightly understates it.** ChEMBL is **CC-BY-SA 3.0 Unported**: adaptations must be
redistributed under the same licence ([ChEMBL licensing](http://chembl.github.io/chembl-licensing/)).
The plan flags this and proposes per-assertion separability + a governance decision — correct in
spirit — but:
- **CC0 + CC-BY-SA mixing is the real trap.** Open Targets is **CC0**, but Open Targets *ingests
  ChEMBL* for its drug/known-drug datatype. If the card copies Open Targets' drug evidence, it may be
  copying ChEMBL-derived content that carries SA obligations *transitively*. The licence registry must
  track **upstream-of-upstream** provenance, not just the immediate source's licence.
- "Keep SA facts separable per assertion" is sound, but **a combined card is plausibly a single
  adaptation**; per-field tagging may not cleanly sever SA reach under SA 3.0. The safest default is
  **SA-source data as pointer/derived-fact-with-attribution only, or release the dataset CC-BY-SA**.
  The plan lists this as option (iii) but defaults to CC-BY — that default is the riskier one and
  should be revisited. HPA and IUPHAR/GtoPdb are also **CC-BY-SA**, compounding the surface.
- **Note an inconsistency**: the allowlist tags ChEMBL/HPA `licenseClass = open` ("share-alike noted").
  SA is materially different from permissive `open` and should be its own class (`open-sa`) so the gate
  can enforce share-alike propagation, not just "is it open."

**F. ClinicalTrials.gov "aggregate counts" still risks an advice-adjacent surface.**
Even aggregate phase/trial counts per target imply "this target is clinically advanced," which a patient
advocate may read as endorsement. The aggregate-only constraint is good; add an explicit prohibition on
*ranking* targets by trial activity and on linking to recruiting trials (which edges toward
trial-matching).

**G. Scope / overlap with the Ewing siblings is asserted but not operationalized.** The PLAN never
concretely differentiates from `ewing-drug-target-evidence` and `ewing-biomarker-evidence-cards`.
Risks: (1) duplicated schema/rubric/compliance-gate work across three repos; (2) a cancer-wide panel
that *includes Ewing* double-covers the sibling; (3) **target-evidence vs. biomarker-evidence** scope
bleed (a biomarker card and a target card for the same gene/cancer will overlap on genetic/somatic
evidence). The plan should declare the **shared template/engine** as a dependency (one schema, one
gate, one rubric, consumed by all three) and a non-overlap rule (cancer-wide explicitly *excludes*
Ewing/sarcoma indications already owned by the sibling, or treats them as the sibling's depth pilot).
This is both a correctness gap and the clearest efficiency win (see §3–§4).

**H. Weak / untestable metrics.**
- "**byte/field-stable** re-run" — unattainable given upstream rescoring (see C). Reframe as
  "manifest-diff-explainable."
- "**≥1 third-party reproduction**" and "**≥1 documented beneficiary use**" are binary and easily gamed
  (one friendly lab). Fine as a floor, but add a qualitative bar (independent of the maintainers; an
  attested concrete decision the card informed).
- No metric for **grading inter-rater reliability** — for a project whose value *is* trustworthy
  grading, a second-curator agreement metric (e.g. Cohen's κ on a sample) is essential and absent.
- No metric for **contradiction-detection recall** (did the pipeline catch known disagreements?).

**I. Other gaps.**
- **Ensembl licence** is listed as "open (EMBL-EBI terms; no restriction)" — Ensembl data is largely
  no-restriction but *some* incorporated datasets carry third-party terms; verify per data type, don't
  blanket-class.
- **Negative/absence-of-evidence** is unhandled: "no dependency signal" is itself informative for a
  target, but the schema is association-centric and may bias cards toward positive evidence only.
- **Target-safety / on-target liability** is absent from the data model. A credible "is this a good
  target" card needs genetic-constraint/essentiality and tissue-expression-breadth (safety) signals,
  not just association strength; otherwise it over-states tractable targets that are also toxic.
- **No versioned change-log per card** beyond `cardVersion` — reviewers need a diff of *what assertion
  changed and why* across source releases for re-sign-off efficiency.
- **Patient-facing layer ROI**: it is HIGH-risk, blocking-gated, and arguably outside the core value
  prop; the plan already makes it optional — consider deferring it out of v1 scope entirely to reduce
  the reviewer-acquisition critical path.

---

## 2. Competitive landscape

The space is crowded with mature, well-funded resources. None combines *cancer-wide* + *open-only* +
*per-assertion provenance with verified licence class* + *explicit evidence grading* + *reproducible
build manifest* — but each overlaps substantially.

**Open Targets Platform** — the closest competitor. Integrates target–disease associations across
genetic, somatic, expression, literature, animal-model and pathway evidence; **harmonic-sum** scoring
into an overall association score over ~7.8M associations; quarterly releases; **CC0** licence; open
code. ([platform](https://platform.opentargets.org/),
[scoring/docs](https://platform-docs.opentargets.org/getting-started),
[*NAR* 2021](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7779013/)).
*Strengths:* authoritative, CC0 (maximally reusable), comprehensive, maintained, already does
tractability buckets and links Probe Miner. *Weaknesses/gaps:* a scored *platform/database*, not a
**curated, human-reviewed, narrative evidence card** per target×cancer; the harmonic sum is opaque to
non-experts; **no per-assertion verified-licence provenance for downstream reuse** (it relativizes
licences internally but a reuser must re-derive them); not cancer-specialized; not written for patient
advocates; no plain-language layer; contradictions are scored-away, not surfaced.

**Pharos / IDG / TCRD** — target-centric; collates 70+ resources; signature **Target Development Level**
(Tclin/Tchem/Tbio/Tdark) classifying how druggable/studied a target is
([Pharos](https://pharos.nih.gov/about),
[*NAR* 2021 TCRD](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7778974/)).
*Strengths:* best-in-class target *tractability/illumination* framing; NIH-backed; good for "is this
target studied/druggable." *Weaknesses:* **target-centric, weakly disease/cancer-specific**; not a
per-indication evidence synthesis; little narrative provenance grading; not advocate-facing.

**canSAR (ICR)** — largest public *oncology* translational/drug-discovery knowledgebase; integrates
genomic/protein/pharmacology/structural data; **AI-based druggability and "drug-target-likeness"**
assessment ([ICR](https://www.icr.ac.uk/about-us/icr-news/detail/cansar-10-year-anniversary),
[*NAR* 2019](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6323893/)).
*Strengths:* cancer-focused, deep druggability/structural analysis, respected. *Weaknesses:* a
*query knowledgebase*, not licence-verified reusable cards; provenance/licence terms for downstream
redistribution are not the product; not designed for advocate readability; mixed reuse terms.

**DGIdb** — aggregates drug–gene interactions from ~30+ sources incl. CIViC, OncoKB; open downloads
([DGIdb 5.0 *NAR* 2024](https://academic.oup.com/nar/article/52/D1/D1227/7416371)).
*Strengths:* broad drug–gene coverage, aggregator, open. *Weaknesses:* **interaction lists, not graded
disease-contextual evidence**; *inherits the licence heterogeneity of its sources* (incl. OncoKB
non-commercial) — a cautionary tale the Elyos project explicitly avoids by classing licences; no
narrative/provenance card.

**ChEMBL** — bioactivity/drugs/mechanisms; **CC-BY-SA 3.0** (share-alike)
([ChEMBL licensing](http://chembl.github.io/chembl-licensing/)).
*Strengths:* canonical bioactivity/known-drug source. *Weaknesses:* not target-evidence synthesis;
**SA licence imposes propagation obligations** that this project must manage (see §1E).

**OncoKB (MSK)** — precision-oncology variant/therapeutic annotation; **first FDA-partially-recognized**
somatic-variant DB; Levels 1–4 of therapeutic evidence; **non-commercial / registration-gated API**
([MSK](https://www.mskcc.org/news-releases/fda-grants-partial-recognition-status-mskcc-precision-oncology-knowledge-database)).
*Strengths:* gold-standard clinical actionability levels, FDA-recognized. *Weaknesses for us:*
**non-commercial — explicitly excluded** from a CC-BY output (pointer-only); clinically-oriented
(variant→therapy), which is the *advice-adjacent* zone the Elyos project deliberately avoids.

**CIViC** — community-curated clinical interpretation of variants in cancer; **open (CC0), public API,
open code** ([DGIdb 4.0 context](https://academic.oup.com/nar/article/49/D1/D1144/6006193)).
*Strengths:* the open analogue to OncoKB; crowdsourced, transparent, CC0 — a possible *model* for the
Elyos contribution/overlay pivot. *Weaknesses:* variant→clinical-significance focused, not
target-credibility; coverage uneven for rare cancers.

**My Cancer Genome (VICC)** — curated gene/variant/therapy/trial knowledge for clinicians.
*Strengths:* readable clinical context. *Weaknesses:* clinician-facing decision support (advice-zone);
not open-data-reuse-first; less actively distinctive recently.

**Probe Miner** — quantitative, objective chemical-probe assessment over ~1.8M compounds / 2,220
targets (potency <100 nM, >10× selectivity, cell activity <10 µM); linked from Open Targets
([Cell Chem Biol 2017](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5814752/)).
*Strengths:* rigorous tractability/tool-compound signal. *Weaknesses:* narrow (chemical probes only),
not disease-contextual evidence.

**DepMap (Broad)** — genetic-dependency (CRISPR/RNAi) across ~1000+ cell lines; **CC-BY 4.0**, aggregate.
*Strengths:* the dependency evidence backbone. *Weaknesses for us:* a *dataset*, not a card; version/
algorithm-sensitive (CERES→Chronos, batch effects — §1C).

**Adjacent / academic "cancer dependency analyses"** (e.g. integrated DepMap prioritization papers) —
one-off analyses, not maintained, reproducible, or licence-verified products.

---

## 3. Gaps we can fill (the open, provenance-first, validity-tiered, cancer-wide angle)

None of the incumbents delivers **all** of: (1) **cancer-wide, per-indication** target-evidence cards;
(2) **open-data-only with per-assertion verified licence class** so the *output is itself cleanly and
provably reusable/redistributable* (Open Targets is CC0 but doesn't hand you a per-fact licence/
attribution ledger; DGIdb inherits NC obligations silently); (3) an **explicit, published evidence-
grading + maturity rubric** that a non-expert can read; (4) **surfaced contradictions** rather than a
single opaque score; (5) a **reproducible build manifest** (pinned source release + algorithm + hash)
so a third party can re-derive and diff; (6) **human scientific sign-off** recorded version-scoped; and
(7) a faithful, **advocate-readable** rendering that never crosses into advice. Specific fillable gaps:

- **A licence-class-aware reuse ledger** as a first-class output (the `LICENSES`/`ATTRIBUTION` manifest
  + per-assertion `licenseClass`, incl. transitive SA tracking) — nobody publishes this.
- **Reproducibility-as-product**: pinned-release + algorithm-version + hash manifests for cancer target
  evidence — incumbents are living databases, not re-derivable snapshots.
- **Contradiction surfacing** for non-experts (Open Targets scores it away; canSAR/Pharos don't narrate
  disagreement).
- **Rare-cancer depth** where commercial/clinical knowledge is thin (canSAR/OncoKB skew to common,
  actionable cancers).
- **Provenance-grade plain-language synthesis for patient advocates** — a genuinely unserved audience,
  behind the HIGH-tier gate.
- **A safety/tractability dimension fused with association evidence** (most tools silo druggability
  [Pharos/canSAR] from disease association [Open Targets]).

---

## 4. Differentiators to win

1. **Provenance + verified-licence-class on every assertion** — the output is *provably* CC-BY-clean
   (NC excluded, SA tracked), so other open projects can redistribute it without re-auditing. This is
   the one thing Open Targets (CC0 but no per-fact licence ledger), canSAR, and DGIdb (silently
   NC-contaminated) do **not** offer.
2. **Reproducible, version-pinned build manifest** (source release + DepMap algorithm + hash) → cards
   are re-derivable and diffable, not a moving target. A research artifact, not a live black box.
3. **Published, cited grading + evidence-maturity rubric with surfaced contradictions** → readers
   distinguish a powered GWAS from one cell line, and see disagreement rather than an opaque harmonic
   sum.
4. **Human scientific sign-off, version-scoped** → curated trust Open Targets/Pharos (automated) don't
   claim; the kill-gate keeps it honest ("must beat reading raw sources").
5. **Advocate-readable, gated plain-language layer** → an audience the incumbents ignore, delivered
   under blocking oncologist + advocate review.
6. **Open-only by construction** → cleaner reuse story than OncoKB (NC) or canSAR (mixed terms).
7. **Vs. the Ewing sibling — a shared template/engine, depth-vs-breadth split.** The winning structural
   move: **one schema + one compliance gate + one rubric + one assembler engine**, with
   `ewing-drug-target-evidence` as the **deep, single-disease pilot** (rare-cancer depth, dedicated
   sarcoma reviewer) and `drug-target-evidence-cards` as the **cancer-wide breadth instance** that
   *explicitly excludes the Ewing/sarcoma indications the sibling owns* (no double coverage), and
   `ewing-biomarker-evidence-cards` reusing the same engine for the **biomarker** card type. The
   differentiator is the *generalized engine + non-overlapping panels*, not three hand-built silos.

---

## 5. Claude API leverage

**Where Claude clearly helps (assistive, human-verified):**
1. **Literature evidence extraction with span-level provenance** from the PMC-OA subset — Claude drafts
   candidate evidence statements *each tied to a quoted source span*, respecting per-article licence
   (only CC-BY/CC0 contribute copied text). This is the plan's M3 and the strongest fit: long-context
   reading + structured citation output, with a human verifying every assertion. Use prompt-cached
   source text and a strict "every claim must cite a span or be dropped" output contract.
2. **Structuring/normalizing heterogeneous source facts into the card schema** — mapping Open Targets /
   ChEMBL / DepMap / UniProt records into `associationEvidence[]` with `evidenceType`, `direction`,
   candidate `grade`, and `sources[]`; tool-use/JSON-schema-constrained output (Ajv-validated) keeps it
   well-formed.
3. **Contradiction detection across sources** — Claude is good at flagging "source A says up-regulated,
   source B null/down" and drafting the contradiction note + a candidate taxonomy label
   (measurement-difference / context-dependence / version-drift) for the curator to confirm.
4. **Drafting tractability/safety and REMARK-style appraisal text** — summarizing Pharos TDL, Probe
   Miner thresholds, structural availability, genetic-constraint/expression-breadth into a readable
   `tractability`/`limitations` narrative the curator edits.
5. **Plain-language rewriting** (HIGH-tier layer) — faithful, reading-level-controlled rewrites of
   *already-signed-off* cards, then routed to oncologist + advocate (never auto-published).
6. **Licence-class triage assistant** — pre-classifying a new source's licence text into
   open/open-sa/NC/excluded as a *draft* for the compliance reviewer.

**Where Claude must NOT decide (hard human gates):**
- **No clinical / treatment / per-patient recommendations** — ever; structurally impossible in the
  schema and reinforced in prompts.
- **No final evidence grade or final card** — grades need the defined rubric + scientific-reviewer
  sign-off; the LLM proposes, the human disposes.
- **No fabricated or uncited claims** — any statement without a verifiable span is dropped; "no source,
  no claim" is CI-enforced, not model-trusted.
- **No licence or medical/legal determination** — Claude may *draft* a licence-class triage, but the
  compliance reviewer makes the binding call; medical-safety framing is the oncologist's.
- **No contradiction *resolution*** — Claude surfaces disagreement; it must not silently pick a winner.
- **Send only open-access content to the API**, never controlled-access/identifiable data (none exists
  by construction), and never secrets.

---

## 6. Ten concrete optimizations

1. **Split the grade field** into `evidenceQuality` (study strength) × `evidenceMaturity`
   (in-silico/in-vitro/in-vivo/human-genetic/clinical-aggregate), and weight maturity per the cited
   genetic-support literature (§1B). Drop GRADE framing; adopt a cited, target-evidence-appropriate
   rubric.
2. **Pin DepMap by release + scoring algorithm + screen project (Broad/Sanger)** and add a
   `replicatesAcrossProjects` flag and lineage-specificity note to every dependency assertion (§1C).
3. **Add an `open-sa` licence class and transitive-provenance tracking** (upstream-of-upstream), so
   ChEMBL-derived facts arriving *via* CC0 Open Targets still carry SA obligations (§1E). Reconsider
   defaulting the dataset to **CC-BY-SA** to sidestep the whole problem.
4. **Add a contradiction-aggregation policy + taxonomy** (measurement vs. biological vs. version) and a
   contradiction-detection recall metric.
5. **Add target-safety/on-target-liability fields** (genetic constraint, essentiality, expression
   breadth) so cards don't over-promote toxic-but-tractable targets.
6. **Add a grading inter-rater-reliability metric** (second-curator κ on a sample) — directly measures
   the project's core value.
7. **Replace "byte/field-stable reproducibility" with "manifest-diff-explainable"** and ship a per-card
   change-log keyed to source-release diffs to streamline re-sign-off.
8. **Build the schema + compliance gate + rubric as a shared package consumed by all three sibling
   repos** (one engine), with a declared non-overlap rule excluding Ewing/sarcoma indications from the
   cancer-wide panel (§4.7).
9. **Constrain `clinicalLandscape` further**: forbid ranking targets by trial activity and linking to
   recruiting trials; counts only, with a "not trial-matching" notice.
10. **Defer the HIGH-tier patient-facing layer out of v1** to shorten the reviewer-acquisition critical
    path; ship research cards + reuse ledger first, add the advocate layer only once an oncologist +
    advocate pair is secured.

---

## 7. Parallel & perpendicular spin-offs

- **Generalized target-evidence engine** (`target-evidence-core`): the schema + gate + rubric +
  assembler as a reusable library that all card projects instantiate (cancer-wide, Ewing-specific,
  future non-cancer rare-disease). This is the highest-leverage spin-off and resolves the sibling
  overlap.
- **`ewing-drug-target-evidence` / `ewing-biomarker-evidence-cards` integration**: deep single-disease
  pilots feeding the shared engine; biomarker cards reuse the same provenance/grading machinery on a
  biomarker schema.
- **`oncogene-knowledge-graph`**: emit the cards as RDF/graph triples (target–evidence–source) with
  licence-class edges — a provenance-first open knowledge graph other tools can query.
- **`biomarker-extraction`**: the PMC-OA span-cited extraction pipeline generalized as a standalone
  open biomarker/assertion-extraction service.
- **`ewing-immunotherapy-target-catalog`**: a modality-specific instance (surface antigens / IO targets)
  on the same engine.
- **MCP server** (`target-evidence-mcp`): expose the cards + provenance/licence ledger as an MCP tool so
  any agent can query "open, cited evidence for target X in cancer Y" with attribution and grades — a
  natural agent-ecosystem distribution channel and a candidate *contribution/overlay* deliverable if the
  project pivots into Open Targets/Pharos.
- **Reuse-compliance toolkit**: the licence-class registry + transitive-SA tracker spun out as a generic
  open-data licence-compliance linter (useful well beyond cancer).

---

## 8. Open questions for the maintainer

1. **Dataset licence**: given transitive ChEMBL/HPA/IUPHAR share-alike via even CC0 Open Targets, is
   defaulting to CC-BY-4.0 actually defensible, or should v1 ship **CC-BY-SA-4.0** (or SA-data-as-
   pointer-only)? This blocks copying any SA data into output.
2. **Sibling boundary**: will the three card repos share one engine, and will the cancer-wide panel
   *exclude* Ewing/sarcoma indications the sibling owns? If not, how is double coverage justified?
3. **Grading rubric**: adopt/adapt which published scheme (Open Targets harmonic-sum components? a
   levels-of-evidence variant?) — and will it carry separate quality vs. maturity axes?
4. **DepMap handling**: pin to which algorithm baseline (Chronos), and is cross-project (Broad+Sanger)
   replication required before a dependency assertion grades above "Limited"?
5. **Which indication + panel first** (the M0 decision) — and does the rare-cancer priority risk
   colliding with the Ewing sibling's territory?
6. **Patient-facing layer**: pursue at all in v1, or defer until an oncologist + advocate pair is
   secured (recommended)?
7. **Safety dimension**: will cards include on-target-safety/genetic-constraint signals, or is the card
   association-only (and thus prone to over-promoting toxic targets)?
8. **Pivot target**: if no standalone beneficiary by ~2027-03-31, is the contribution path Open Targets,
   Pharos/IDG, **CIViC** (the closest open, CC0, community-curated analogue), or an MCP overlay?
