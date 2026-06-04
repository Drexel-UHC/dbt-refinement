# Relevant PRs for Designing the Harness dbt Project

**Source repo:** `SALURBAL-Climate/salurbal-data-infrastructure`
**Purpose of this doc:** Guide a future agent building the *fixture/harness dbt project* (see `src.md`)
that tests the `dbt-col-lineage` package (`ProvenanceTracer`). It records **which PRs to read and
why**, so the agent doesn't waste time on the wrong history.

## TL;DR

The PRs that matter are the **`Lineage`-labeled** ones — they contain the actual
`ProvenanceTracer` / `trace_provenance.py` code and its I/O contract.

The **`Refinement`-labeled** PRs do **NOT** contain the tracer logic. They are skill-workflow/UX
iterations and *closed, never-merged* data runs. Ironically, none of the refinement work was merged
into `main`; the only real end-to-end run was a **staging** test (no production DUA contracts yet),
documented in #234. Skip them for harness development except #234 as a golden-data reference.

Read order: **#198 → #199 → #222 → #227** (code + contract), then **#195 / #196 / #17**
(fixture metadata shape), then lift **#234**'s artifacts as the first golden test case.

---

## 🟢 Tier 1 — Must review (the tracer code + I/O contract)

Define *what the package does* and *what the harness must assert against*.

| PR | State | Why it's essential |
|----|-------|--------------------|
| **#198** [Lineage] POC-1 Phase 3 — SQLGlot provenance query | MERGED | **The original tracer.** Introduces the sqlglot lineage walk for `dthred_annual_cause_zerofilled` — the exact logic the package extracts. Source of the SQL patterns the harness must reproduce. |
| **#199** [Lineage] Hotfix — re-apply Pass 1 code review fixes from #198 | MERGED | Correctness fixes on top of #198. Read with #198 to see which edge cases were already caught → encode them as regression tests. |
| **#222** [Lineage] Trace fn upgrade — fully deterministic (script, not agent) | MERGED | Turns metadata derivation deterministic. This is the **"manifest-only input"** behavior `src.md` calls the stable contract — the harness depends on determinism. |
| **#227** [Lineage] Trace fn upgrade — deterministic inputs + structured `provenance.json` | MERGED | **Defines the output schema** (`provenance.json` shape). Expected-output fixtures must match this exactly. Most important single PR for assertion design. |

## 🟡 Tier 2 — Review for fixture inputs (source/DUA metadata + golden data)

The harness needs fake source freezes with known metadata; these define those fields.

| PR | State | Why |
|----|-------|-----|
| **#196** [Lineage] Phase 2 — programmatic `_sources.yml` from freeze metadata | MERGED | How source nodes carry freeze metadata → drives `source_freeze` / `*_uuid` output fields. Model fixture sources on this. |
| **#195** [Lineage] Phase 1 — mockup DUA + codebook (21 DIN freezes) | MERGED | The DUA/source mockups that become `dua_uuid`, `source_raw_data_uuid`, `dmc_harmonized_data_uuid`. Good template for fixture metadata. |
| **#17** [DUA] Structured JSON schema in manifest | MERGED | Canonical DUA schema the tracer emits — pin fixture metadata to it. |
| **#234** [Refinement] DTHRED v0.1.3 (staging) | CLOSED | Only as a **golden reference** — real `provenance.json` + `manifest.json` + model `.yml` (SOT) artifacts from the one real (staging) run. Copy a slimmed version as a fixture; do **not** review for code. |

## ⚪ Tier 3 — Skim or skip for this task

- **#192** [PRD] DTH metadata propagation planning (POC-1) — skim once for intent/vocabulary; not needed for implementation.
- **#201, #228, #229, #231, #233** (Refinement / Skill) and the closed data runs (**#230, #232, #217, #220, #223**) — skill-workflow/UX iterations, not lineage logic. **Skip** for harness development.

---

## Context notes

- The package extracts the POC at
  `salurbal-data-infrastructure/admin-api/_context/release/v0.2/features/dbt-col-lineage-query/poc-1/trace_provenance.py`
  (see `src.md`). The Tier-1 `Lineage` PRs above are the history of that file.
- Stable public interface the harness validates (from `src.md`):
  ```python
  from dbt_col_lineage import ProvenanceTracer
  tracer = ProvenanceTracer(manifest: dict, dbt_package: str)
  rows = tracer.trace(model_name: str, column: str) -> list[dict]
  # row keys: target, column, stage_model, source_freeze, country,
  #           dua_uuid, source_raw_data_uuid, dmc_harmonized_data_uuid
  ```
- Manifest must come from `dbt compile` (has `compiled_code`), **not** `dbt parse`.
- DuckDB dialect only (`dbt-duckdb`); sqlglot version pin matters (lineage API drifts).

## How this list was derived

Filtered all PRs in `salurbal-data-infrastructure` by the `Lineage`, `Refinement`, and `DUA`
labels plus title keywords (`lineage`, `provenance`, `trace`, `manifest`). The `Refinement` data
PRs (#234, #232, #230, #223, #220, #217) were all CLOSED against feature/POC branches — never
merged to `main`. The tracer logic lives entirely in the `Lineage` PRs.
