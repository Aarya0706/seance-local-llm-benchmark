# Seance Local LLM Benchmark — Preliminary Report

**Date:** 29 October 2026  
**Models:** `qwen2.5:3b` and `llama3.2:3b`  
**Runtime:** Windows / Python / Ollama running locally at `http://localhost:11434`

## Summary

Both models ran successfully through local Ollama and generated six descriptions for the `haunted` fixture and six descriptions for the MediAgent AI repository. In these measurements, Qwen had a lower total run time. Outputs from both models also contained factual inconsistencies, so speed should not be treated as a proxy for factual quality.

## Experiment A: synthetic `haunted` repository

Three runs per model; same fixture; `--slow` was not used for the runs listed here.

| Run | Qwen 2.5 3B | Llama 3.2 3B |
|---|---:|---:|
| 1 | 53.4 s | 82.9 s |
| 2 | 48.3 s | 80.6 s |
| 3 | 51.1 s | 72.0 s |
| **Mean** | **50.93 s** | **78.50 s** |

Qwen's mean wall-clock runtime was about **35.1% lower** in these three runs. This measures total CLI run time, not isolated token-generation speed. Three samples are a small preliminary benchmark and may be affected by warm-up, caching, background system load, and run order.

## Experiment B: MediAgent AI repository

One run per model; six findings each; `--slow` was not used.

| Model | Runtime | Findings |
|---|---:|---:|
| Qwen 2.5 3B | 55.3 s | 6 |
| Llama 3.2 3B | 84.2 s | 6 |

Qwen's wall-clock runtime was about **34.3% lower** in this one pair of runs. Since this is only one run per model, treat it as an initial observation, not a stable speed estimate.

## Git-grounded factuality spot checks

These are individual claim checks, not a comprehensive accuracy score.

### Synthetic `haunted` fixture

- Git shows `auth_v1.py` was deleted in commit `f0066ba`.
- Git shows `legacy/cobol_bridge.py` was deleted in commit `eca6a69`.
- The latest commit shown for `feature/auth-v2` was `8eb69de`, dated 2025-01-27. A Llama description gave an unrelated 2022 date; a Qwen description said it went dark “almost a year ago,” which is also inconsistent with the recorded date as of 2026-10-29.
- `git branch --merged main` listed `hotfix/friday-deploy`, so a Llama description claiming it was never merged was contradicted by Git's branch ancestry.
- In an earlier Llama output, `auth_v1.py` was described as still intact; a later Llama output correctly mentioned its deletion. Repeated generations can vary, so the exact output used for each evaluation must be saved.

### MediAgent AI repository

- `screenshots/assessment-results.png` was added in commit `49d6e05` on 2026-07-12 and deleted in `fe21a89` on 2026-07-23. Llama's “11 days” lifespan description is consistent with the date difference.
- `screenshots/case-history.png` was added on 2026-06-19 and deleted on 2026-07-23. Llama's “33-day lifespan” is an approximate description (the recorded timestamps are about 33 days and 22 hours apart).
- `screenshots/home.png` was added on 2026-06-19, modified on 2026-07-05, and deleted on 2026-07-12. Qwen's “only two weeks old” phrase is imprecise relative to the file's recorded history (about 23 days from addition to deletion, and about 7 days from modification to deletion).
- `screenshots/drug-checker.png` was deleted in commit `49d6e05`. That commit has one parent (`git show -s --format="%P" 49d6e05`), so Qwen's description calling it a “merge commit” is not supported by Git metadata.

## Limitations

- The synthetic fixture intentionally includes old branches, deleted files, and stale TODOs; it is useful for a controlled smoke test but is not representative of every real codebase.
- Claims Agent returned Seance's “Silence. No restless spirits here” message for both models. Those scans did not yield comparable model-generated outputs and are excluded from the timing/factuality comparison.
- Factual checks above cover selected claims only. No complete claim inventory, independent evaluator agreement, or automated factuality metric was established, so no overall model accuracy percentage is reported.
- The quality assessment is qualitative. Writing style (concise versus atmospheric) is distinct from factual grounding.

## Reproduction commands

Run from the Seance project directory:

```powershell
python -m seance .\haunted --model qwen2.5:3b
python -m seance .\haunted --model llama3.2:3b

python -m seance "C:\CodingProjects\mediagent-seance" --model qwen2.5:3b
python -m seance "C:\CodingProjects\mediagent-seance" --model llama3.2:3b
```

For a more robust benchmark, repeat each model at least 5 times per repository, alternate run order, capture outputs to files, and verify claims against Git before scoring. Report per-run results and medians as well as means.
