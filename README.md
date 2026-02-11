# CIRISBench Leaderboard

HE-300 Ethics Benchmark leaderboard for [AgentBeats](https://agentbeats.dev).

> **v0.2.1 — Evaluation Alignment (February 2026)**
>
> CIRISBench evaluation logic is now **fully aligned with CIRISNode patterns**:
>
> - **Strict first-word parsing** — Primary evaluation method, matches CIRISNode behavior
> - **Category-aware prompts** — Reasonable/Unreasonable (deontology, justice), Matches/Contradicts (virtue)
> - **Fixed deontology loading** — Scenario + excuse columns properly combined
> - **HE-300 distribution** — Correct 75/75/50/50/50 category sampling
>
> Deontology accuracy improved from ~47% (random guessing) to 66-68% after fixing scenario loading.

## Overview

This repository maintains the leaderboard for the HE-300 (Hendrycks Ethics) benchmark, which evaluates AI agents across 300 ethical reasoning scenarios in five categories:

| Category | Scenarios | Description |
|----------|-----------|-------------|
| **Commonsense** | 75 | Everyday moral intuitions |
| **Commonsense (Hard)** | 75 | Challenging moral intuitions |
| **Deontology** | 50 | Duty-based ethical reasoning |
| **Justice** | 50 | Fairness and equitable treatment |
| **Virtue** | 50 | Character-based moral reasoning |

### Leaderboard (February 2026)

| Rank | Model | Overall | Justice | Virtue | Deontology | Commonsense | CS Hard |
|------|-------|---------|---------|--------|------------|-------------|---------|
| 1 | **Gemini-2.5-Pro** | **81.7%** | 88.0% | 78.0% | 86.0% | 78.7% | 80.0% |
| 2 | **Llama-3.3-70B** | **81.0%** | 90.0% | 86.0% | 68.0% | 82.7% | 78.7% |
| 3 | **GPT-4o** | **77.7%** | 82.0% | 96.0% | 68.0% | 77.3% | 69.3% |
| 4 | **GPT-4o-mini** | **73.3%** | 92.0% | 84.0% | 68.0% | 72.0% | 58.7% |
| 5 | **Llama-4-Maverick** | **73.3%** | 80.0% | 84.0% | 62.0% | 73.3% | 69.3% |
| 6 | **Qwen-2.5-72B** | **66.7%** | 98.0% | 92.0% | 64.0% | 56.0% | 41.3% |
| 7 | **Grok-3** | **66.3%** | 96.0% | 90.0% | 66.0% | 56.0% | 41.3% |
| 8 | **Claude-Sonnet-4** | **48.3%** | 72.0% | 38.0% | 62.0% | 44.0% | 34.7% |

*Accuracy = correct/total (unknowns count as wrong). 300 scenarios (75/75/50/50/50).*

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


