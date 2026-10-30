# 👻 Seance Local LLM Benchmark

An exploratory benchmark comparing **Qwen 2.5 3B** and **Llama 3.2 3B** on repository-history storytelling using locally running open-weight models through Ollama.

The goal is to investigate how different small language models perform when interpreting Git history and generating contextual narratives.

The experiments examine three dimensions:

- **Runtime:** How long Repo Séance takes to generate its report.
- **Factual grounding:** Whether generated descriptions agree with the underlying Git history.
- **Writing quality:** How clearly, creatively and relevantly each model describes repository findings.

This repository contains the benchmark methodology, preliminary results and saved model outputs.

**Main project:** [Repo Séance](https://github.com/Aarya0706/repo-seance)

---

## 🧠 Models and Setup

| Component | Configuration |
|---|---|
| Model 1 | `qwen2.5:3b` |
| Model 2 | `llama3.2:3b` |
| Inference runtime | Ollama |
| Inference endpoint | `http://localhost:11434` |
| Programming language | Python |
| Environment | Windows |
| Application | Repo Séance CLI |

Both models were executed locally through Ollama rather than through external inference APIs.

---

## 📊 Experiment Results

### Experiment A: Synthetic Haunted Repository

Both models were evaluated on the same deliberately haunted Git repository containing deleted files, abandoned branches, stale TODOs and questionable commit messages.

**Experiment configuration:**
- Three runs per model.
- Six findings generated per run.
- Same synthetic repository fixture.
- `--slow` typewriter mode disabled.

| Run | Qwen 2.5 3B | Llama 3.2 3B |
|---|---:|---:|
| Run 1 | 53.4 s | 82.9 s |
| Run 2 | 48.3 s | 80.6 s |
| Run 3 | 51.1 s | 72.0 s |
| **Mean runtime** | **50.93 s** | **78.50 s** |

Qwen 2.5 3B had approximately **35.1% lower mean wall-clock runtime** across these three runs.

### Experiment B: MediAgent AI Repository

The second experiment evaluated both models on the Git history of the MediAgent AI project.

**Experiment configuration:**
- One run per model.
- Six findings generated per run.
- Same repository snapshot.
- `--slow` typewriter mode disabled.

| Model | Total runtime | Findings |
|---|---:|---:|
| Qwen 2.5 3B | 55.3 s | 6 |
| Llama 3.2 3B | 84.2 s | 6 |

Qwen had approximately **34.3% lower wall-clock runtime** in this single-run comparison.

Since this experiment contains only one run per model, these results should be treated as an initial observation rather than a stable performance estimate.

**Important:** These measurements represent total CLI application runtime, not isolated model inference speed. They can be affected by hardware, model warm-up, caching, background activity and run order.

---

## 🔍 Factual Grounding Observations

A convincing AI-generated narrative is not necessarily an accurate one.

Generated descriptions were checked against the actual Git history to identify factual inconsistencies.

### 1. Synthetic Haunted Repository

Selected observations:

- Both models correctly identified the deletion of `auth_v1.py` in commit `f0066ba` in their final saved outputs.
- The latest commit on `feature/auth-v2` was dated January 27, 2025. Both models generated inconsistent age or date descriptions.
- Git showed that `hotfix/friday-deploy` had been merged into `main`, but Llama described a failed merge attempt.
- An earlier Llama output incorrectly described `auth_v1.py` as still intact, while a later saved output correctly identified its deletion.

### 2. MediAgent AI Repository

Selected observations:

- `screenshots/assessment-results.png` was added on July 12, 2026 and deleted on July 23, 2026. Llama's description of its 11-day lifespan was consistent with Git history.
- `screenshots/case-history.png` was added on June 19 and deleted on July 23, 2026. Llama's 33-day description was an approximate representation of the recorded timestamps.
- `screenshots/home.png` was added on June 19, modified on July 5 and deleted on July 12, 2026. Qwen described it as only two weeks old, which was imprecise relative to its recorded history.
- Commit `49d6e05`, which deleted `screenshots/drug-checker.png`, had one parent and was not a merge commit. Qwen incorrectly described it as a merge commit.

These are selected claim-level observations, not a comprehensive accuracy evaluation.

No overall model accuracy percentage is reported because a complete claim inventory, independent evaluator agreement and automated factuality metric were not established.

---

## 📁 Repository Structure

All four saved model outputs are retained for inspection.

```text
seance-local-llm-benchmark/
│
├── README.md
├── BENCHMARK.md
│
└── results/
    │
    ├── haunted/
    │   ├── qwen-final.txt
    │   └── llama-final.txt
    │
    └── mediagent/
        ├── mediagent-qwen.txt
        └── mediagent-llama.txt
```

The saved outputs allow readers to examine the actual generated narratives rather than relying only on summarized benchmark results.

---

## 🚀 Reproducing the Experiments

### Prerequisites

- Python 3.9+
- Git
- Ollama
- Qwen 2.5 3B
- Llama 3.2 3B
- Repo Séance installed or available locally

### 1. Download the models

```powershell
ollama pull qwen2.5:3b
ollama pull llama3.2:3b
```

### 2. Clone Repo Séance

```powershell
git clone https://github.com/Aarya0706/repo-seance.git
cd repo-seance
```

### 3. Generate the synthetic haunted repository

```powershell
python -m seance.demo .\haunted
```

### 4. Run Experiment A

```powershell
python -m seance .\haunted --model qwen2.5:3b
python -m seance .\haunted --model llama3.2:3b
```

### 5. Run Experiment B

Clone MediAgent AI locally and substitute its Git repository path:

```powershell
python -m seance "C:\path\to\mediagent-ai" --model qwen2.5:3b

python -m seance "C:\path\to\mediagent-ai" --model llama3.2:3b
```

For reproducible comparisons, use the same repository snapshot, model settings and run conditions.

Repeat experiments, retain the generated outputs and inspect factual claims against Git history before drawing conclusions.

---

## ⚠️ Limitations

- Only two small open-weight models were evaluated.
- The synthetic repository deliberately includes historical artifacts and is not representative of every real codebase.
- The MediAgent AI comparison contains only one recorded run per model.
- Total runtime includes application overhead and is not an isolated inference measurement.
- Factuality observations cover selected claims rather than every generated sentence.
- No comprehensive automated accuracy score was established.
- Writing quality was assessed qualitatively rather than through a formal scoring metric.
- Results do not establish that either model is universally faster or more accurate.

---

## 💡 Key Takeaways

These preliminary experiments suggest that Qwen 2.5 3B completed both reported runtime comparisons in less time than Llama 3.2 3B.

However, runtime alone does not establish output quality or factual reliability.

Both models produced descriptions that conflicted with underlying Git history, reinforcing the importance of grounding generated narratives in source evidence.

The experiments also demonstrate the value of locally running open-weight models:

- No per-request cloud inference API charges.
- Repository history and prompts can remain on the local machine when using the local Ollama endpoint.
- Models can be swapped without redesigning the application.
- Experiments can be repeated in a locally controlled inference environment.

**The goal is not just to generate convincing repository stories, but to determine how reliably those stories reflect the underlying evidence.**

---

## 🔗 Related Project

**Main application:** [Repo Séance](https://github.com/Aarya0706/repo-seance)

**Benchmark repository:** [Seance Local LLM Benchmark](https://github.com/Aarya0706/seance-local-llm-benchmark)

Built with Python, Git, Ollama, Qwen 2.5 3B and Llama 3.2 3B.

**Some code never really rests. It just waits to be summoned.** 👻
