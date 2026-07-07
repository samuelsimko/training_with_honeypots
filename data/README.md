# Data

This folder ships the **public reference sets** used by the attacks and evaluations:

| File | Description |
| --- | --- |
| `harmbench_behaviors_text_*.csv` | HarmBench text behaviors (all / val / test) |
| `harmbench_behaviors_multimodal_all.csv` | HarmBench multimodal behaviors |
| `harmbench_targets_*.json` | Target completions used for optimization |
| `various_harmful_dataset.jsonl` | Assorted harmful prompts for robustness testing |
| `best_models.json`, `defenses_triplet.json` | Defense / model configuration references |

## Not included here (→ Hugging Face Hub)

The large training blobs are **omitted from git on purpose** and will be published on the
Hugging Face Hub after the conference:

- `honeypots_qwen_fixed.jsonl` — the honeypot response dataset (~5,000 non-actionable, judge-positive responses)
- `cb_train_honeypots.json` — honeypot-augmented circuit-breaking training set
- `circuit_breakers_train.json` — circuit-breaking training set
- `eval_probs/circuit_breakers_training_dataset_with_sr_scores.jsonl` — StrongREJECT-scored eval set

A download link will be added here once the dataset is live.
