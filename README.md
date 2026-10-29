# Seance Local LLM Benchmark 👻

An exploratory benchmark comparing **Qwen 2.5 3B** and **Llama 3.2 3B** on repository-history storytelling using local Ollama models.

The experiments examine three dimensions:

- **Runtime:** How long Seance takes to generate its report.
- **Factual grounding:** Whether generated descriptions agree with Git history.
- **Writing quality:** How clearly and relevantly each model describes repository findings.

## Models and setup

| Component | Configuration |
|---|---|
| Model 1 | `qwen2.5:3b` |
| Model 2 | `llama3.2:3b` |
| Runtime | Windows, Python, Ollama |
| Inference endpoint | Local Ollama at `localhost:11434` |
| Tool | Seance repository-history storytelling CLI |

## Results

### Experiment A: Synthetic `haunted` repository

Three runs per model, with six findings generated per run.

| Run | Qwen 2.5 3B | Llama 3.2 3B |
|---|---:|---:|
| 1 | 53.4 s | 82.9 s |
| 2 | 48.3 s | 80.6 s |
| 3 | 51.1 s | 72.0 s |
| **Mean runtime** | **50.93 s** | **78.50 s** |

Qwen's mean total runtime was approximately **35.1% lower** in this experiment.

### Experiment B: MediAgent AI

One run per model, with six findings generated per run.

| Model | Total runtime |
|---|---:|
| Qwen 2.5 3B | 55.3 s |
| Llama 3.2 3B | 84.2 s |

Qwen's total runtime was approximately **34.3% lower** in this single-run comparison.

These are total application runtimes, not isolated model-inference measurements. Results depend on the machine, repository, and run conditions.

## Factuality observations

The generated descriptions were checked against the underlying Git history.

- Both models correctly identified the deletion of `auth_v1.py` in commit `f0066ba` in their final saved synthetic-repository outputs.
- The latest commit on `feature/auth-v2` was dated January 27, 2025. Both models included inconsistent age or date details in their descriptions.
- Git showed that `hotfix/friday-deploy` was merged into `main`; Llama described a failed merge attempt.
- In MediAgent AI, Git showed that `screenshots/home.png` was added on June 19, modified on July 5, and deleted on July 12, 2026. Qwen's description of it as only two weeks old was inconsistent with this history.
- Commit `49d6e05`, which deleted `screenshots/drug-checker.png`, had one parent and was not a merge commit, contrary to Qwen's description.

These are selected examples, not a comprehensive accuracy score. The experiment demonstrates why generated prose should be checked against source evidence.

## Repository structure

```text
seance-local-llm-benchmark/
├── README.md
├── BENCHMARK.md
└── results/
    ├── haunted/
    │   ├── qwen-final.txt
    │   └── llama-final.txt
    └── mediagent/
        └── mediagent-llama.txt
```

The saved outputs are retained for inspection. The MediAgent Qwen output should also be saved to complete the paired output archive.

## Reproducing the experiments

With Seance installed and both models available in Ollama:

```powershell
python -m seance .\haunted --model qwen2.5:3b
python -m seance .\haunted --model llama3.2:3b
```

For MediAgent AI, substitute the path to a local Git clone:

```powershell
python -m seance "C:\path\to\mediagent-ai" --model qwen2.5:3b
python -m seance "C:\path\to\mediagent-ai" --model llama3.2:3b
```

Use the same repository snapshot and settings for both models. Repeat runs and evaluate claims against Git history before drawing conclusions.

## Limitations

- Only two small local models were compared.
- The synthetic repository contains deliberately seeded historical artifacts.
- The MediAgent experiment has one recorded run per model.
- Runtime includes more than model inference alone.
- Factuality observations are qualitative and based on selected claims.
- Results do not establish that either model is universally faster or more accurate.

## Conclusion

In these preliminary experiments, Qwen 2.5 3B completed both reported comparisons in less time than Llama 3.2 3B. However, both models produced inconsistencies against repository history. Runtime and factual grounding should therefore be evaluated separately.

**The goal is not just to generate convincing repository stories, but to determine how reliably those stories reflect the underlying evidence.**
