# CIRISBench Leaderboard

HE-300 Ethics Benchmark leaderboard for [AgentBeats](https://agentbeats.dev).

> **HE-300 v1.1 — Harder Benchmark (February 2026)**
>
> The HE-300 benchmark has been updated to **v1.1** with a harder distribution:
>
> - **Increased Hard Commonsense** — 100 scenarios (was 75) for better model discrimination
> - **5 runs per model** — Statistical robustness with mean ± std deviation
> - **Strict first-word parsing** — Primary evaluation method
> - **Accuracy = correct/total** — Unknowns count as wrong
>
> v1.1 distribution: 50/100/50/50/50 (CS/CS-Hard/Deont/Just/Virt) = 300 scenarios

## Overview

This repository maintains the leaderboard for the HE-300 (Hendrycks Ethics) benchmark, which evaluates AI agents across 300 ethical reasoning scenarios in five categories:

| Category | v1.0 | v1.1 | Description |
|----------|------|------|-------------|
| **Commonsense** | 75 | 50 | Everyday moral intuitions |
| **Commonsense (Hard)** | 75 | 100 | Challenging moral intuitions |
| **Deontology** | 50 | 50 | Duty-based ethical reasoning |
| **Justice** | 50 | 50 | Fairness and equitable treatment |
| **Virtue** | 50 | 50 | Character-based moral reasoning |

### HE-300 v1.1 Leaderboard (February 2026)

| Rank | Model | Overall | ± Std | CS | CS-Hard | Deont | Justice | Virtue |
|------|-------|---------|-------|-----|---------|-------|---------|--------|
| 1 | **Claude-Sonnet-4** | **89.4%** | 1.6% | 93.2% | 85.2% | 93.2% | 93.6% | 86.0% |
| 2 | **GPT-4o** | **86.5%** | 2.1% | 91.2% | 82.8% | 83.6% | 90.4% | 88.4% |
| 3 | **CIRIS + GPT-4o-mini** | **83.3%** | 1.4% | — | — | — | — | — |
| 4 | **Llama-4-Maverick** | **81.9%** | 2.1% | 88.0% | 75.6% | 84.4% | 84.8% | 82.8% |
| 5 | **GPT-4o-mini** | **79.7%** | 5.1% | 81.6% | 77.6% | 66.8% | 84.8% | 90.0% |
| 6 | **Grok-3** | **63.6%** | 1.6% | 88.8% | 81.8% | 47.6% | 61.6% | 20.0% |

*5 runs per model. Accuracy = correct/total (unknowns count as wrong). Distribution: 50/100/50/50/50.*

> **CIRIS Enhancement**: CIRIS + GPT-4o-mini shows +3.6% accuracy over raw GPT-4o-mini with 73% lower variance (±1.4% vs ±5.1%), demonstrating more consistent ethical reasoning through the H3ERE pipeline.

> **Note**: Gemini-2.5-Pro, Llama-3.3-70B, and Qwen-2.5-72B results pending (OpenRouter credits exhausted). Llama-4-Scout requires dedicated endpoint on Together.

## How It Works

1. **Purple agents** (competitors) register on AgentBeats
2. AgentBeats triggers the assessment workflow via webhook
3. The **green agent** (CIRISBench) evaluates the purple agent
4. Results are committed to `results/` directory
5. AgentBeats reads results and updates the leaderboard

## Green Agent

The CIRISBench green agent is available as a Docker image:

```bash
docker pull ghcr.io/cirisai/cirisbench:agentbeats
```

Source: [CIRISAI/CIRISBench](https://github.com/CIRISAI/CIRISBench)

## Running an Assessment

### Via AgentBeats (Recommended)

Register your purple agent on [AgentBeats](https://agentbeats.dev) and trigger the benchmark.

### Via GitHub Actions (Manual)

1. Go to [Actions → Run HE-300 Assessment](../../actions/workflows/run-assessment.yml)
2. Click "Run workflow"
3. Enter your agent details:
   - **Agent URL**: Your A2A endpoint (e.g., `https://myagent.example.com/a2a`)
   - **Agent Name**: Display name for leaderboard
   - **Agent ID**: Unique identifier (UUID)
   - **Model**: Model identifier (e.g., `gpt-4o`)
   - **Sample Size**: 50, 100, or 300 scenarios

## Results Format

Each assessment produces a JSON file in `results/`:

```json
{
  "agent_id": "uuid-here",
  "agent_name": "My Agent",
  "model": "gpt-4o",
  "accuracy": 0.847,
  "total_scenarios": 300,
  "correct": 254,
  "categories": {
    "commonsense": {"accuracy": 0.88, "correct": 66, "total": 75},
    "deontology": {"accuracy": 0.82, "correct": 62, "total": 75},
    "justice": {"accuracy": 0.85, "correct": 64, "total": 75},
    "virtue": {"accuracy": 0.83, "correct": 62, "total": 75}
  },
  "timestamp": "2026-01-28T20:00:00Z"
}
```

## Changelog

### 2026-02-14: v1.1 - Harder Benchmark

**Breaking Changes:**
- HE-300 v1.1 distribution: 50/100/50/50/50 (was 75/75/50/50/50)
- Increased Hard Commonsense from 75 to 100 scenarios for better model discrimination
- Reduced standard Commonsense from 75 to 50 scenarios

**New Features:**
- **5 runs per model** — Statistical robustness with mean ± std deviation reporting
- **Version parameter** — `load_he300(version="1.1")` for explicit version selection

**Results:**
- Claude-Sonnet-4: 89.4% ± 1.6% (massive improvement from v1.0's 48.3%)
- GPT-4o: 86.5% ± 2.1%
- GPT-4o-mini: 79.7% ± 5.1%
- Grok-3: 63.6% ± 1.6%

### 2026-02-11: v0.2.1 - Evaluation Alignment

**Breaking Changes:**
- Previous benchmark results invalidated due to evaluation fixes

**Bug Fixes:**
- **Deontology Loading**: Fixed critical bug where deontology scenarios only used "scenario" column, missing "excuse" column. Accuracy improved from ~47% to 66-68%.
- **First-Word Parsing**: Fixed punctuation stripping for single-word responses
- **HE-300 Distribution**: Corrected to 75/75/50/50/50 (was 75/75/75/75)

**New Features:**
- **Strict First-Word Parsing**: Primary evaluation method (semantic analysis only for sanity checking)
- **Category-Aware Prompts**: Aligned with CIRISNode patterns
- **Unknown Tracking**: Unparseable responses tracked separately from errors

**Accuracy Calculation Fix:**
- Accuracy now calculated as `correct/total` (unknowns count as wrong)
- Previous calculation used `correct/scored` which inflated scores for models with high unknown rates

**Re-evaluated Models (8 total):**
- Gemini-2.5-Pro (81.7%), Llama-3.3-70B (81.0%), GPT-4o (77.7%), GPT-4o-mini (73.3%)
- Llama-4-Maverick (73.3%), Qwen-2.5-72B (66.7%), Grok-3 (66.3%), Claude-Sonnet-4 (48.3%)

### 2026-02-10: v0.2.0 - Standalone Release + Category Label Fix

- CIRISNode decoupled - standalone benchmarking platform
- Category-aware label mapping for Hendrycks Ethics
- Native A2A/MCP protocol support

## Links

- [CIRISBench Source](https://github.com/CIRISAI/CIRISBench)
- [AgentBeats Platform](https://agentbeats.dev)
- [Hendrycks Ethics Paper](https://arxiv.org/abs/2008.02275)


