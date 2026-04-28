# OpenEuroLLM Language Post-Training Dataset Candidates

A practical catalogue of dataset candidates for OpenEuroLLM target-language
post-training. The focus is on datasets that can help with supervised
fine-tuning, preference tuning, reward modeling, tool use, instruction
following, multilingual reasoning, and adaptation across European languages.

This repo was created from a comparison between:

- OpenEuroLLM datasets;
- [mlabonne/llm-datasets](https://github.com/mlabonne/llm-datasets).

The main conclusion is simple: OpenEuroLLM is already strong on pre-training
corpora. The most interesting gaps are in post-training data.

## What This Repo Is For

Use this repo to answer:

- Which datasets should we test for OpenEuroLLM target-language instruction tuning?
- Which datasets are useful for SFT, DPO, reward models, or tool calling?
- Which candidates are production-friendly and which are research-only?
- Which datasets should be translated or adapted into OpenEuroLLM languages?
- Which datasets are already covered by OpenEuroLLM work?

## Files

| Path | Purpose |
| --- | --- |
| [data/candidates.csv](data/candidates.csv) | Structured candidate table with category, size, license, OpenEuroLLM-language fit, recommendation, use cases, and cautions. |
| [docs/dataset-guide.md](docs/dataset-guide.md) | Detailed explanation of every major candidate and how to use it. |
| [docs/open-eurollm-context.md](docs/open-eurollm-context.md) | Notes on what OpenEuroLLM already appears to cover and where these candidates fit. |
| [docs/open-eurollm-language-recipe.md](docs/open-eurollm-language-recipe.md) | A concrete target-language post-training recipe using the best candidates. |

## Best Candidates

| Priority | Dataset | Why It Matters |
| --- | --- | --- |
| Very high | [Aya Dataset](https://huggingface.co/datasets/CohereForAI/aya_dataset) | Human-annotated multilingual SFT data with permissive licensing. Best first candidate for target-language instruction seeds. |
| Very high | [HelpSteer3](https://huggingface.co/datasets/nvidia/HelpSteer3) | Preference, feedback, and edit data for DPO, reward models, and judge calibration. |
| High | [M2Lingual](https://huggingface.co/datasets/ServiceNow-AI/M2Lingual) | Multilingual multi-turn SFT data. Useful for research and benchmark design, but license likely limits production use. |
| High | [AutoIF-instruct](https://huggingface.co/datasets/Post-training-Data-Flywheel/AutoIF-instruct-61k-with-funcs) and [ifeval-like-data](https://huggingface.co/datasets/argilla/ifeval-like-data) | Good candidates for target-language instruction-following and constraint-following data. |
| High | [xLAM function calling](https://huggingface.co/datasets/Salesforce/xlam-function-calling-60k), [ToolACE](https://huggingface.co/datasets/Team-ACE/ToolACE), [Hermes function calling](https://huggingface.co/datasets/NousResearch/hermes-function-calling-v1) | Useful for tool calling, structured output, and localized tool tasks. |
| Medium-high | [Nemotron-SFT-Multilingual-v1](https://huggingface.co/datasets/nvidia/Nemotron-SFT-Multilingual-v1) | Useful multilingual reasoning baseline for supported European languages; coverage should be checked against OpenEuroLLM targets. |
| Medium-high | [MathX-5M](https://huggingface.co/datasets/Modotte/MathX-5M), [NuminaMath-CoT](https://huggingface.co/datasets/AI-MO/NuminaMath-CoT), [Nemotron-Math-Proofs-v1](https://huggingface.co/datasets/nvidia/Nemotron-Math-Proofs-v1) | Reasoning SFT candidates. Best tested through small STEM translation/adaptation slices per target language. |

## Recommended First Project

The strongest first experiment is:

**Target-language instruction and preference mixtures for multilingual post-training.**

Start with:

- English replay data from Dolci-style source data, to avoid English collapse;
- target-language SFT data from Aya plus translated Dolci;
- target-language constraint-following data from IFEval-like sources;
- HelpSteer3 preference/edit data, either directly in the target language or
  carefully translated/adapted;
- small localized tool-calling sets using xLAM/Hermes-style schemas;
- native evaluation prompts for each language plus English retention checks.

See [docs/open-eurollm-language-recipe.md](docs/open-eurollm-language-recipe.md)
for a concrete plan.

## Licensing Notes

This catalogue is not legal advice. Each dataset should be checked against its
current dataset card before ingestion.

Use the `license` and `recommendation` fields in
[data/candidates.csv](data/candidates.csv) as triage signals:

- `very_high` and permissive license: good first candidates;
- `research_only`: useful for ablations, but not production training;
- `check_dataset_card`: requires license/provenance review before use;
- non-commercial or share-alike licenses: keep out of flagship training unless
  explicitly approved.

## How To Use The CSV

Example with Python:

```python
import csv

with open("data/candidates.csv", newline="") as f:
    rows = list(csv.DictReader(f))

top = [
    row for row in rows
    if row["recommendation"] in {"very_high", "high"}
]

for row in top:
    print(row["dataset"], row["category"], row["license"])
```

## Status

Draft, April 2026. The candidates should be treated as a research and planning
aid until each dataset has been sampled, license-checked, decontaminated, and
tested in small ablations.
