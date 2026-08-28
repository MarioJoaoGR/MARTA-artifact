# Results

Two configurations, one directory each:

- `results/deepseek-coder-v2_16b/` — the main evaluation, 27 projects / 486 modules
- `results/qwen2.5-coder_32b/` — the model-capacity subset, 10 projects / 31 modules

Inside each, one directory per system. Each holds, per project, the generated
test suites as delivered and `run_results/<project>.json` with that project's
token counts, phase timings and validation counters.

| directory | what it is |
|---|---|
| `Results_MARTA` | MARTA, three generation rounds. Also contains the quarantined candidates it discarded |
| `Results_Test4PyBaseline` | the single-prompt LLM baseline, same context, model and repair budget |
| `Results_Pynguin` | the search-based generator, 300 s per module (16B directory only; it uses no model) |
| `Results_CoverUp` | the test suites released by CoverUp, staged here and measured under our pipeline (16B directory only) |
| `Results_MARTA_phase1` | MARTA's Phase 1 run in isolation against an empty cache, used for the cost comparison (9 projects) |

## Which file backs which table

| paper | file |
|---|---|
| Oracle strength | `deepseek-coder-v2_16b/test_quality.csv` |
| Structural coverage | `deepseek-coder-v2_16b/coverage_measured_PRECOPY.csv` |
| Mutation score | `deepseek-coder-v2_16b/mutmut_permodule.csv` |
| Generation yield, salvage | `deepseek-coder-v2_16b/gen_efficiency.csv`, plus the quarantine directories under `Results_MARTA` |
| Cost | `run_results/*.json` under `Results_MARTA`, `Results_MARTA_phase1` and `Results_Test4PyBaseline` |
| Model capacity | the two CSVs in `qwen2.5-coder_32b/` |

**Note on the coverage files.** The figures in the paper come from
`coverage_measured_PRECOPY.csv`, not from `coverage_measured.csv`. The latter is
a later re-measurement in which each project is copied to a fresh directory
before its suites are executed, so that a destructive test cannot influence a
subsequent measurement. Per-project means agree within about one percentage
point and the ordering of the three systems is identical, but the two disagree
substantially on `ansible`, the benchmark's largest subject, for reasons we
could not establish. We therefore report the original measurement and record
both here.

## Other files

Superseded or exploratory, kept for completeness:

| file | what it was |
|---|---|
| `coverage_measured.csv` | the isolated re-measurement described above |
| `coverage_measured_g0.csv`, `_g1.csv` | coverage restricted to the first and first-two generations per function, for an ablation of the outer loop that we did not report |
| `mutmut.csv` | mutation under full-suite attribution, superseded by per-module |
| `mutmut_baseline_v1.csv`, `_v2.csv` | earlier baseline mutation runs |
| `consolidated_16b.csv`, `pynguin_coverage.csv` | earlier consolidation passes |
