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
| `Results_MARTA_phase1` | MARTA's Phase 1 run in isolation against an empty cache, used for the cost comparison (9 projects) |

## Which file backs which table

| paper | file |
|---|---|
| Oracle strength | `deepseek-coder-v2_16b/test_quality.csv` |
| Structural coverage | `deepseek-coder-v2_16b/coverage_measured.csv` |
| Mutation score | `deepseek-coder-v2_16b/mutmut_permodule.csv` |
| Generation yield, salvage | `deepseek-coder-v2_16b/gen_efficiency.csv`, plus the quarantine directories under `Results_MARTA` |
| Cost | `run_results/*.json` under `Results_MARTA`, `Results_MARTA_phase1` and `Results_Test4PyBaseline` |
| Model capacity | the two CSVs in `qwen2.5-coder_32b/` |
