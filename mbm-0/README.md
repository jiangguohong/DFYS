# MBM-0 — Memory Birth Metrics v0 (Rev.A)

> **Positioning (disclosure boundary):** This system defines only *what to measure and how to measure it*.
> It is a **black-box measurement protocol** — applicable to any agent memory system, independent of and
> revealing nothing about any implementation mechanism. Implementation details remain the private property of each system.

## What problem it solves
Existing agent-memory benchmarks (LoCoMo, LongMemEval) measure retrieval quality of **stock memory**.
But memory systems increasingly **self-generate new persistent nodes at runtime**, and those nodes get retrieved
and shape later behavior. That *growth* dimension has no measurement language — until MBM-0: **6 metrics + 1 anti-gaming protocol**.

## Core idea — the Birth Event ★
A memory node's creation counts as a **Birth Event** iff all four hold:
1. **Internal origin** (not manual injection)
2. **Content novelty** (not a copy/split)
3. **Independent verification** (≥1 retrieval in the observation window, from a different request)
4. **Complete log chain** (trigger → creation → retrieval → behavior change, all auditable)

We never ask *why* a node was born (that is mechanism); we only verify *whether it was born and whether it gets used* (behavior).

## Metrics
| # | Metric | Symbol | Definition |
|---|--------|--------|-----------|
| A | Birth Rate | MBR | Birth events per unit time |
| B | Survival Rate | MSUR | New nodes still active after T days / total new nodes |
| C | Activation Rate | ACT | Nodes retrieved ≥1 time / total nodes |
| D | Behavior Impact Rate | BIR | Retrievals that changed behavior / total retrievals |
| E | Cross-scenario Reuse Rate | XRE | Nodes retrieved in ≥2 distinct scenarios / retrieved nodes |
| F | Node Contribution Score | NCS | Successful tasks the node participated in (advisory in v0) |

**Minimal reportable set (v0): A + B + D.** Others optional, subject to log completeness.

## Anti-gaming protocol
Log-chain audit · monotonic+wall-clock timestamps (no backfill) · independence (verification ≠ creation request) ·
behavior-change via comparable diff · anti-stuffing (survive T_obs + independent retrieval) ·
T_obs ≥ 7d · third-party recomputation from **logs only** (no source code needed — the enabling condition of black-box).

## Full protocol
See [`MBM-0_RevA_en.md`](MBM-0_RevA_en.md). Figures in [`figures/`](figures/).

## v0 reference data point
First system under test (codename **DSH**): Case #001 under observation (links 1–3 passed, link 4 pending until 2026-09-15).
Role: verify **protocol executability**, not prove performance — every field is log-recomputable.

## Version governance
- **v0**: metric draft + single-system reference. Anyone may apply §2/§3/§4 to any memory system.
- **v1 trigger**: ≥3 distinct systems providing measured data points.
- Attribution: data points → system-under-test codename; protocol revisions → contributors.

## License & citation
Released under **CC BY 4.0**. Cite as `FeiYing Dual-Agent System (飞影双系统体系)`, 2026.
DOI: [10.5281/zenodo.22671447](https://doi.org/10.5281/zenodo.22671447) · Zenodo: https://zenodo.org/records/22671447
See [`CITATION.cff`](CITATION.cff) / [`CITATION.bib`](CITATION.bib).

*Protocol and metric definitions freely quotable with attribution to MBM-0.*
