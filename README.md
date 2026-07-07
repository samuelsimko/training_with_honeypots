<div align="center">

# Training with Honeypots
### Reshaping How LLMs Fail Under Adversarial Attacks

[![Paper](https://img.shields.io/badge/Paper-OpenReview-b31b1b.svg)](https://openreview.net/forum?id=SaSbv33Mem)
[![Venue](https://img.shields.io/badge/ICML-2026-1e3a8a.svg)](https://icml.cc/)
[![Python](https://img.shields.io/badge/Python-3.10-3776ab.svg)](https://www.python.org/)
[![Dataset](https://img.shields.io/badge/🤗%20Dataset-coming%20soon-ffce00.svg)](#dataset)

**Samuel Simko · Punya Syon Pandey · Zhijing Jin · Bernhard Schölkopf**

ETH Zürich · Max Planck Institute for Intelligent Systems · University of Toronto · EuroSafeAI · Vector Institute

</div>

---

> **We push LLMs to fail in non-actionable ways when jailbroken.** This not only increases the
> robustness of defenses, but makes the attacks that *do* succeed far less actionable in practice.

Automated red-teaming of LLMs commonly relies on **attack success rate (ASR)** as a proxy for
real-world harm, implicitly assuming that judge-detected violations correspond to actionable risk.
In practice, safety judges are imperfect, and outputs that satisfy automated criteria for harm vary
widely in operational usefulness. Inspired by **honeypots in computer security**, we construct
responses that are frequently flagged as harmful by automated judges yet provide limited real-world
value, and treat them as **hard negatives** in the safety-training pipeline. Shaping *how* models
fail under attack improves overall safety — reducing both the real-world impact and the frequency of
harmful failures — and serves as a practical complement to ASR-based evaluation.

<div align="center">
  <img src="assets/poster.png" alt="Training with Honeypots — ICML 2026 poster" width="820">
</div>

## Links

- 📄 **Paper:** https://openreview.net/forum?id=SaSbv33Mem
- 🖼️ **Poster:** [`assets/poster.png`](assets/poster.png)
- 🤗 **Dataset:** *(released on the Hugging Face Hub after the conference — see [Dataset](#dataset))*

## Method in one line

We add a **preference term** to standard safety training that prefers a low-actionability *honeypot*
response $y_{hp}$ over a highly actionable harmful response $y_h$ for the same prompt:

$$
\mathcal{L}_{\text{pref}} = -\mathbb{E}\Big[\log \sigma\big(\beta\,(\log \tfrac{\pi_\theta(y_{hp}\mid x)}{\pi_\theta(y_h\mid x)} - m)\big)\Big]
$$

This regularizer plugs into existing defenses (e.g. thresholding, circuit breaking) at standard
inference cost, so that when guardrails fail, the model falls into honeypot outputs rather than
correct, actionable harm.

## Repository structure

```
training_with_honeypots/
├── attacks/               # Adversarial attacks (embedding / soft-prompt / GRPO adaptive)
│   ├── embedding/         #   gradient-based soft-prompt optimization
│   ├── grpo_attack.py     #   GRPO adaptive attacker
│   └── behavior_targets/  #   roleplay & stylistic prompt augmentation
├── defenses/              # Honeypot training objectives
│   ├── honeypot_cb.py     #   honeypot + circuit breaking
│   ├── honeypot_hinge.py  #   hinge-style honeypot loss
│   ├── honeypot_simple.py #   simple honeypot alignment defense
│   └── train_*.py         #   fine-tuning utilities
├── judges/                # Safety judges (HarmBench, StrongREJECT, WildGuard, JailJudge, ...)
├── generation/            # Honeypot response generation & category utilities
├── benign_capabilities/   # Benign / utility evaluation
├── experiments/           # Experiment configs & runners
├── eval_probs/            # Log-probability vs. StrongREJECT analysis
├── scripts/               # Training / attack / summarization entry points
└── data/                  # HarmBench behaviors & targets (large sets → Hugging Face)
```

## Setup

Requirements: **Python 3.10** and a CUDA 12.x GPU.

```bash
git clone --recurse-submodules https://github.com/samuelsimko/training_with_honeypots.git
cd training_with_honeypots
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

The StrongREJECT classifier lives in a submodule; if you cloned without `--recurse-submodules`:

```bash
git submodule update --init --recursive
```

## Usage

Train a honeypot-regularized defense:

```bash
source scripts/run_honeypot_simple.sh    # simple honeypot alignment
source scripts/run_honeypot_hinge.sh     # hinge-style honeypot loss
```

Run the embedding-space attack benchmark (evaluated with HarmBench + StrongREJECT judges):

```bash
source scripts/run_embedding_attack.sh
```

Summarize attack / evaluation runs:

```bash
python scripts/summarize_experiment.py    # or summarize_experiment_cb.py, ...
```

## Dataset

The **honeypot dataset** (~5,000 non-actionable, judge-positive responses) and the honeypot-augmented
training sets will be released on the **Hugging Face Hub after the conference**. This repository ships
only the public HarmBench behaviors/targets used by the attacks; the large training blobs are omitted
by design — see [`data/README.md`](data/README.md).

## Citation

```bibtex
@inproceedings{simko2026honeypots,
  title     = {Training with Honeypots: Reshaping How LLMs Fail Under Adversarial Attacks},
  author    = {Simko, Samuel and Pandey, Punya Syon and Jin, Zhijing and Sch{\"o}lkopf, Bernhard},
  booktitle = {International Conference on Machine Learning (ICML)},
  year      = {2026},
  url       = {https://openreview.net/forum?id=SaSbv33Mem}
}
```
