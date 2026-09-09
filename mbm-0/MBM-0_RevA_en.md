# Memory Birth Metrics (MBM-0) — Rev.A

> **Positioning statement (disclosure boundary)**: This system defines only *what to measure and how to measure it*. It is a **black-box measurement protocol** — applicable to any agent memory system, independent of and revealing nothing about any implementation mechanism. Implementation details remain the private property of each system. The protocol is fully decoupled from the system under test.

---

## 1. Background: Memory Evaluation Lacks the "Growth" Dimension

Existing agent memory benchmarks (LoCoMo, LongMemEval) measure the retrieval quality of **stock memory** — can the system remember, and does it find things accurately?

Practice has run ahead of evaluation: memory systems increasingly **self-generate new persistent memory nodes** at runtime (Generative Agents' reflection tree is a well-known precedent), and these nodes get retrieved and influence subsequent behavior. Yet this dimension has no measurement language — no metric, no protocol, no cross-system comparison.

MBM-0 v0 fills this gap: **6 metrics + 1 anti-gaming protocol**.

## 2. Core Concepts (Behavioral Definitions, Black-Box Observable)

The following definitions depend only on externally observable behavior (logs, timestamps, retrieval records), and involve no internal mechanisms.

### 2.1 Memory Node
A minimal persisted, retrievable memory unit carrying a creation timestamp.

### 2.2 Manual Injection
A creation operation whose source is marked as external direct write (human or external system via a write API).

### 2.3 Birth Event ★

A memory node's creation counts as a **Birth Event** if and only if all four conditions hold:

1. **Internal origin**: creation source = system-internal (not manual injection);
2. **Content novelty**: no pre-existing node with equivalent content (not a copy/split);
3. **Independent verification**: ≥1 independent retrieval within the observation window T_obs (retrieval ≠ creation request);
4. **Complete log chain**: trigger context → creation → retrieval → behavior change, all four links auditable.

⚠️ We do not ask *why* a node was born (that is mechanism). We only verify *whether it was born and whether it gets used* (that is behavior).

## 3. Metrics

| # | Metric | Symbol | Definition | Formula | Window |
|---|--------|--------|-----------|---------|--------|
| A | Birth Rate | MBR | Birth events per unit time | Birth Events / window | weekly |
| B | Survival Rate | MSUR | New nodes still active after T days / total new nodes | — | T=30d (other T allowed, must be labeled) |
| C | Activation Rate | ACT | Nodes retrieved ≥1 time / total nodes | — | rolling |
| D | Behavior Impact Rate | BIR | Retrievals that changed agent behavior / total retrievals | — | rolling |
| E | Cross-scenario Reuse Rate | XRE | Nodes retrieved in ≥2 distinct task scenarios / retrieved nodes | — | rolling |
| F | Node Contribution Score | NCS | Successful tasks the node participated in (attribution protocol; advisory in v0) | — | not required in v0 |

**Minimal reportable set (v0): A + B + D.** Others optional, subject to log completeness.

## 4. Measurement Protocol (Anti-Gaming)

1. **Log-chain audit**: all four Birth Event links required; missing any link → not counted.
2. **Timestamps**: monotonic clock + wall-clock time, recorded together; no backfilling.
3. **Independence**: verification retrieval must occur in a different request than creation (prevents "self-production, self-consumption" gaming).
4. **Behavior-change determination**: based on comparable decision/output differences (behavioral diff with vs. without the node).
5. **Anti-stuffing**: a node counts toward MBR only if it survives T_obs and is independently retrieved ≥1 time — MBR counts births that *pass initial screening*, not those dead on arrival.
6. **Observation windows**: T_obs ≥ 7 days; MSUR uses T = 30 days (or self-reported, labeled). Zero-window scoring is prohibited.
7. **BIR attribution caveat**: proving a behavior change is caused by the node (rather than random variance) is **unsolved in v0**. An attribution protocol (e.g., counterfactual re-run controls) is deferred to v1. In this version, BIR is accepted only as self-reported with log corroboration.
8. **Third-party recomputation**: testers need only **log access** to recompute all metrics — no source code required. This is the enabling condition of the black-box protocol.

## 5. v0 Reference Data Point (First System Under Test, Anonymized)

System under test codename: **DSH** (single-host agent memory system, in operation).

### Case #001: First candidate Birth Event — UNDER OBSERVATION

| Field | Value |
|---|---|
| Birth time | 2026-09-08 (exact timestamp recomputable from logs) |
| Trigger type | Runtime task-derived scenario (mechanism undisclosed) |
| Observation window T_obs | In progress (T_obs = 7 days, until 2026-09-15) |
| Independent retrievals | 3 (cross-request, log-recomputable) |
| Behavior-change instance | **Pending link 4**: expected form — agent proactively executes a confirmation action in a similar later scenario, auditable diff |
| Status | **Under observation** (links 1–3 passed: trigger → creation → retrieval; link 4 not yet occurred; not counted as a full Birth Event; retained as a protocol-executability demonstration) |

### Metric snapshot (v0 baseline)

| Metric | Value |
|---|---|
| MBR | 1 candidate event / week 1 (pending promotion after link-4 confirmation) |
| MSUR(30d) | sample window not yet elapsed — not reported |
| ACT / BIR / XRE | pending data after observation window |

The role of the v0 data point: not to prove performance, but to verify **protocol executability** — every field is independently recomputable from logs.

## 6. Relation to Existing Benchmarks

| Dimension | LoCoMo / LongMemEval | MBM-0 |
|---|---|---|
| Measures | retrieval quality of stock memory | **growth dynamics** of the memory system |
| Analogy | exam: how much was memorized | how much new knowledge was self-generated |
| Relation | **orthogonal & complementary**: complete evaluation = retrieval metrics + MBM | |

## 7. Version Governance

- **v0 (this version)**: metric draft + single-system reference point. Anyone may apply §2/§3/§4 to any memory system.
- **v1 trigger**: ≥3 distinct systems providing measured data points; thresholds and metric weights revised accordingly.
- **Revision rules**: metric definition changes must record a version diff; published data points are labeled with their measurement version.
- **Attribution**: data points are attributed to the system-under-test codename; protocol revisions are attributed to contributors.

---

*v0 · Rev.A · 2026-09-08 · Protocol and metric definitions freely quotable with attribution to MBM-0.*

**FeiYing Dual-Agent System** · 46894742@qq.com
