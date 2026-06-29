# drug-target-evidence-cards

> Cancer-target research is bottlenecked not by a shortage of data but by its fragmentation. The evidence that a given gene/protein is a credible therapeutic target in a given cancer — genetic associati  ·  **Risk tier:** med  ·  **Status:** planning

Cancer-target research is bottlenecked not by a shortage of data but by its fragmentation. The evidence that a given gene/protein is a credible therapeutic target in a given cancer — genetic association, somatic alteration frequency, differential expression, genetic dependency, pathway context, tractability/druggability, existing drugs and modalities, and the clinical research landscape — is scattered across Open Targets, ChEMBL, UniProt, Ensembl, Reactome, the Human Protein Atlas, DepMap, ClinicalTrials.gov, and the open-access literature, each with its own identifiers, versions, and licences. A researcher or a patient advocate trying to understand "what is the open evidence for target X in cancer Y?" must stitch these together by hand, usually without recording provenance, and the result is neither reproducible nor reusable.

**Definition of shipped:** or advocacy org) uses the evidence cards in their work, with provenance verified, the compliance gate green, and scientific (and, for any patient-facing content, oncologist + advocate) review recorded.

This is an **Elyos** good-deed project. Contributors pull a task, do it with their own coding agent, and open a PR. Platform: https://github.com/jdev1977/elyos

## Plan
- [PLAN.md](./PLAN.md) — robust enterprise plan (vision, architecture, roadmap, risks; includes an applied-improvements appendix + review sign-off)
- [TASKS.md](./TASKS.md) — schema-mapped task backlog
- [tasks/](./tasks/) — ready-to-pull task JSON(s)

## Contribute
```bash
elyos browse
elyos next --repo Elyos-Projects/drug-target-evidence-cards --no-fork
```

## Licensing & review
- Open license (see PLAN.md).
- Risk tier **med** — deeds are *delivered, not merged*; a domain reviewer (and expert sign-off for any high-stakes content) must approve before merge.

> Planning stage; no adopting partner secured yet (`verifiedNeed: false` on delivery-dependent tasks).
