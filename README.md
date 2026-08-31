# MARTA — artifact

Artifact for the paper **MARTA: A Decoupled Multi-Agent Architecture for Python
Test Generation** (ICTSS 2026).

MARTA is a Python regression test generation pipeline that separates abstract
test planning from concrete test implementation. A Planner Agent designs logical
test scenarios from a statically derived semantic context; an Assertion Agent
translates each plan into a complete Pytest file. An execution-driven repair loop
and a coverage-guided planning loop refine the result.

## What is here

| | |
|---|---|
| `marta/` | the MARTA implementation |
| `scripts/` | the measurement pipeline: coverage, mutation analysis, oracle quality, generation cost |
| `deucalion/` | the SLURM job scripts used to run the evaluation |
| `baselines/` | how the baselines were obtained and patched |
| `artifact/` | the complete results: every generated test suite, the per-project run records, the measurement CSVs |
| `projects.json` | the 486 target modules, by project |

## The evaluation

27 open-source Python projects (486 modules) from the benchmark introduced by
Pynguin and subsequently used by CodaMosa and CoverUp, with
DeepSeek-Coder-V2-Lite (16B) served locally through Ollama, plus a ten-project
subset with Qwen2.5-Coder-32B.

`artifact/results/` holds, per model and per system:

- the generated test suites, as delivered
- `run_results/<project>.json` — per-project token counts, phase timings and
  validation counters
- `coverage_measured.csv`, `mutmut_permodule.csv`, `test_quality.csv` — the
  measurements behind every table in the paper

Four systems are covered at 16B: MARTA, a single-prompt LLM baseline, the
search-based generator Pynguin, and the test suites released by CoverUp,
re-measured here under the same pipeline over the same target modules.

For MARTA at 16B the archive holds the 4,774 retained test files and the 8,815
discarded candidates, which together support the discard rate reported in the
paper.

## Baselines

The baselines are not vendored here. The single-prompt baseline is the
generation module of TEST4PY and the search-based baseline is Pynguin, both
obtained from their own repositories. The benchmark subjects come from the
CodaMosa replication package.

`baselines/patches/` carries the twelve changes we applied to the single-prompt
baseline, as a `git am` series against its upstream. They are corrections
required to run it at all in our environment, not changes to how it generates:
an import-root fix for projects whose source directory is a container rather
than a package, a `PYTHONPATH` fix without which its syntax check reported every
generated test as invalid, tolerance for non-UTF8 subprocess output and for
deleted working directories, a device setting for the embedder, caching of its
own Phase 1 analysis so that restarts do not repeat it, and a round-aware resume
so that later rounds are not skipped. Applying them to a clean checkout of
TEST4PY reproduces the baseline as we ran it.

## Configuration

The pipeline reads its model and embedder settings from a `.env` file in the
repository root. Copy `.env.example` to `.env` and adjust the endpoint if you
serve the model elsewhere; the values shipped are the ones used in the
evaluation. Without it, importing the package fails, since the embedder path is
read at import time.

## Reproducing a measurement

Copy `.env.example` to `.env` first. These four recompute reported figures from
the included results alone, with no further setup:

    python3 scripts/analyze_test_quality.py --results artifact/results/deepseek-coder-v2_16b --projects projects.json
    python3 scripts/generation_efficiency.py --results artifact/results/deepseek-coder-v2_16b
    python3 scripts/per_module_coverage.py  --results artifact/results/deepseek-coder-v2_16b --projects projects.json
    python3 scripts/cost_breakdown.py       --results artifact/results/deepseek-coder-v2_16b

`measure_coverage.py` and `run_mutmut_permodule.py` execute the delivered suites
against the code under test, so they additionally need the benchmark subjects
from the CodaMosa replication package, which are not vendored here.

Regenerating the suites themselves requires a GPU and a locally served model;
`deucalion/` contains the job scripts as they were run.

## License

MIT. See `LICENSE`.
