# Dataset Guide

This guide explains the main dataset candidates in practical terms: what they
are, when to use them, what to avoid, and how they fit a Swedish or European
LLM post-training plan.

## Quick Decision Table

| Goal | Best First Candidates |
| --- | --- |
| Swedish instruction tuning | Aya Dataset, translated Dolci, IFEval-like translated data |
| Preference tuning / DPO | HelpSteer3, Dolci-DPO, UltraFeedback cleaned |
| Reward model or judge model | HelpSteer3, Skywork Reward Preference, Arena preference data |
| Tool calling | xLAM function calling, Hermes function calling, ToolACE |
| Swedish constraint following | IFEval-like data, AutoIF-instruct, native Swedish prompts |
| Multilingual multi-turn chat | Aya Dataset, M2Lingual |
| Reasoning SFT | MathX, NuminaMath-CoT, Nemotron Math, OpenThoughts |
| Code SFT | CodeX, rStar-Coder, Nemotron code datasets |
| Safety and over-refusal | FalseReject, manually adapted Swedish safety prompts |

## Aya Dataset

URL: <https://huggingface.co/datasets/CohereForAI/aya_dataset>

Aya is one of the most attractive candidates because it is human-annotated,
multilingual, and Apache-2.0. It contains about 204k prompt-completion pairs
across many languages.

Use it for:

- Swedish and EU-language SFT seed data;
- human-authored multilingual examples;
- evaluation prompt seeds;
- comparison against translated synthetic instruction data.

Why it matters:

OpenEuroLLM is already translating Dolci into several European languages,
including Swedish. Aya gives a different signal: native or community-authored
multilingual instructions rather than only translated English-centric data.

First experiment:

Train a small SFT ablation:

| Run | Data |
| --- | --- |
| A | English replay + translated Dolci Swedish |
| B | English replay + Aya Swedish |
| C | English replay + translated Dolci Swedish + Aya Swedish |

Evaluate Swedish capability, English retention, and general EU-language
instruction behavior.

Cautions:

- Check actual Swedish coverage and quality.
- Keep source metadata.
- Deduplicate against any Swedish eval set.

## HelpSteer3

URL: <https://huggingface.co/datasets/nvidia/HelpSteer3>

HelpSteer3 contains preference, feedback, edit, edit-quality, and principle
subsets. It is especially useful for alignment work rather than plain SFT.

Use it for:

- DPO, IPO, ORPO, or similar preference tuning;
- reward-model training;
- judge calibration;
- feedback/edit behavior;
- multilingual helpfulness evaluation.

Why it matters:

OpenEuroLLM already has judge and evaluation work through tools like
JudgeArena. HelpSteer3 is directly relevant if the project wants better reward
models, judge models, or preference-tuned assistants.

First experiment:

Compare:

| Run | Data |
| --- | --- |
| A | Dolci-DPO baseline |
| B | HelpSteer3 preference subset |
| C | Dolci-DPO + HelpSteer3 |

Evaluate on Arena-Hard, m-Arena-Hard, Swedish native prompts, over-refusal, and
English retention.

Cautions:

- Confirm whether Swedish is present in the multilingual subset.
- Do not mix preference/edit data into SFT without conversion.
- Keep preference, feedback, and edit tasks as separate data types.

## M2Lingual

URL: <https://huggingface.co/datasets/ServiceNow-AI/M2Lingual>

M2Lingual is a multilingual multi-turn instruction dataset. It is useful for
research on multilingual conversation, but its CC-BY-NC-SA-4.0 license likely
makes it unsuitable for production model training.

Use it for:

- research-only multilingual multi-turn ablations;
- Swedish benchmark design;
- comparing task-evolved data against human-authored data;
- studying transfer across languages.

Cautions:

- Treat as research-only unless legal review says otherwise.
- Manually inspect samples for synthetic style.
- Do not mix into a production flagship recipe by accident.

## Instruction-Following Data

Useful datasets:

- AutoIF-instruct-61k-with-funcs:
  <https://huggingface.co/datasets/Post-training-Data-Flywheel/AutoIF-instruct-61k-with-funcs>
- ifeval-like-data:
  <https://huggingface.co/datasets/argilla/ifeval-like-data>

These datasets are small but high-leverage. They train models to follow explicit
constraints: answer length, formatting, output language, required phrases,
ordering, and other prompt rules.

Use them for:

- Swedish instruction-following SFT;
- IFEval-style training;
- native Swedish formatting and register control;
- public-sector writing constraints.

Suggested Swedish adaptations:

- "Answer only in Swedish."
- "Use plain Swedish suitable for a public authority website."
- "Use Swedish date format."
- "Use formal tone, but avoid bureaucratic wording."
- "Return exactly three bullet points."
- "Summarize the source text without adding facts."

Cautions:

- Check contamination against IFEval.
- Keep translated and native Swedish prompts separate.
- Constraint-following gains can be brittle; evaluate directly.

## Tool Calling and Structured Output

Useful datasets:

- xLAM function calling:
  <https://huggingface.co/datasets/Salesforce/xlam-function-calling-60k>
- ToolACE:
  <https://huggingface.co/datasets/Team-ACE/ToolACE>
- Hermes function calling:
  <https://huggingface.co/datasets/NousResearch/hermes-function-calling-v1>
- ToolMind:
  <https://huggingface.co/datasets/Nanbeige/ToolMind>

Use them for:

- JSON/function-call formatting;
- argument extraction;
- tool selection;
- multi-turn tool trajectories;
- structured output reliability.

Swedish-specific angle:

Create Swedish tool tasks around local needs:

- finding a care provider;
- summarizing a 1177-style page;
- filling a municipality form;
- searching a Swedish document collection;
- booking or rescheduling an appointment;
- retrieving public-sector contact information.

Cautions:

- Normalize all tool schemas before mixing datasets.
- Evaluate exact JSON validity and argument accuracy.
- Do not assume English function-calling data teaches Swedish tool use.

## Multilingual Reasoning

Useful datasets:

- Nemotron-SFT-Multilingual-v1:
  <https://huggingface.co/datasets/nvidia/Nemotron-SFT-Multilingual-v1>
- MathX-5M:
  <https://huggingface.co/datasets/Modotte/MathX-5M>
- NuminaMath-CoT:
  <https://huggingface.co/datasets/AI-MO/NuminaMath-CoT>
- Nemotron-Math-Proofs-v1:
  <https://huggingface.co/datasets/nvidia/Nemotron-Math-Proofs-v1>
- OpenThoughts3-1.2M:
  <https://huggingface.co/datasets/open-thoughts/OpenThoughts3-1.2M>
- Nemotron-Science-v1:
  <https://huggingface.co/datasets/nvidia/Nemotron-Science-v1>

Use them for:

- reasoning SFT;
- math/science instruction tuning;
- small Swedish STEM translation experiments;
- measuring whether reasoning traces transfer across languages.

Cautions:

- Chain-of-thought style data affects model behavior and should be handled
  deliberately.
- Translated math prompts need native review.
- Swedish STEM evaluation should include direct-answer and explanation modes.

## Code and Software Engineering

Useful datasets:

- CodeX-7M-Non-Thinking:
  <https://huggingface.co/datasets/Modotte/CodeX-7M-Non-Thinking>
- rStar-Coder:
  <https://huggingface.co/datasets/microsoft/rStar-Coder>
- Nemotron-SFT-Competitive-Programming-v2:
  <https://huggingface.co/datasets/nvidia/Nemotron-SFT-Competitive-Programming-v2>
- Nemotron-SFT-OpenCode-v1:
  <https://huggingface.co/datasets/nvidia/Nemotron-SFT-OpenCode-v1>
- Nemotron-SFT-SWE-v2:
  <https://huggingface.co/datasets/nvidia/Nemotron-SFT-SWE-v2>
- Code-Preference-Pairs:
  <https://huggingface.co/datasets/Vezora/Code-Preference-Pairs>

Use them for:

- code instruction tuning;
- software engineering tasks;
- competitive programming reasoning;
- code preference tuning;
- practical patch-generation evaluation.

Cautions:

- OpenEuroLLM already has active code-source work.
- These are more useful as post-training candidates than as replacements for
  code pretraining corpora.
- Evaluate with HumanEval, MBPP, LiveCodeBench-style tasks, and real patch
  tasks.

## Safety and Over-Refusal

Useful dataset:

- FalseReject:
  <https://huggingface.co/datasets/AmazonScience/FalseReject>

FalseReject targets benign prompts that look safety-sensitive. It is useful for
studying over-refusal, especially in sensitive domains like public services,
health information, or legal information.

Use it for:

- over-refusal evaluation;
- safety DPO research;
- Swedish public-sector and health-information adaptations.

Cautions:

- License is CC-BY-NC-4.0.
- Needs safety review.
- Swedish adaptations should be manually reviewed.

