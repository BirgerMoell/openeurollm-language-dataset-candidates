# Swedish-First Post-Training Recipe

This is a concrete experiment plan for improving Swedish instruction behavior
while preserving English and broader European-language ability.

## Goal

Build a small but high-quality Swedish/EU post-training mixture that answers:

- Can Swedish instruction ability improve without English collapse?
- Does human-authored multilingual data help beyond translated Dolci?
- Does preference/edit data improve helpfulness and judge scores?
- Can we add Swedish constraint-following and tool-calling behavior cheaply?

## Core Principle

Do not train "Swedish only" in isolation. Use English replay and retention
evaluation from the start.

OpenEuroLLM board results suggest that the source and style of English replay
data can matter more than the exact English/EU ratio. The Swedish experiment
should therefore preserve English with source-style replay data while adding
Swedish and EU-language signal.

## Data Components

| Component | Candidate Sources | Purpose |
| --- | --- | --- |
| English replay | Dolci-Instruct-SFT or base-model source-style English | Preserve English ability during Swedish adaptation. |
| Swedish SFT | Aya Swedish slice, translated Dolci-Instruct-SFT | Teach Swedish assistant behavior. |
| Swedish reasoning | Small translated MathX/Numina/Nemotron Science slice | Measure and improve Swedish STEM reasoning. |
| Swedish constraints | Translated/adapted IFEval-like data | Improve exact instruction following in Swedish. |
| Preference/edit | HelpSteer3 multilingual or translated subset | Improve helpfulness and reward/judge behavior. |
| Tool calling | xLAM/Hermes/ToolACE schema adapted to Swedish tools | Teach structured output and local tool use. |
| Evaluation | Swedish native eval, m-Arena-Hard, English Arena-Hard | Measure gains and regressions. |

## Suggested Ablations

| Run | Data |
| --- | --- |
| A | English replay only |
| B | English replay + translated Dolci Swedish |
| C | English replay + Aya Swedish |
| D | English replay + translated Dolci Swedish + Aya Swedish |
| E | Run D + Swedish constraint-following data |
| F | Run E + HelpSteer3-style preference tuning |
| G | Run F + Swedish tool-calling data |

Keep each run small before scaling. The goal is to learn the direction of
effect, not to produce the final model on the first pass.

## Minimum Metadata

Every sample should keep:

- `source_dataset`
- `source_url`
- `source_license`
- `data_type`: `sft`, `preference`, `edit`, `tool_call`, `eval_seed`
- `language`
- `translated`: `true` or `false`
- `translation_model`, if translated
- `native_reviewed`: `true` or `false`
- `domain`
- `split`

## Evaluation

Use at least four buckets:

| Bucket | Examples |
| --- | --- |
| Swedish native instruction following | Swedish public-sector prompts, summaries, format constraints, register control. |
| Swedish source-grounded tasks | Summarize or answer from Swedish source text without adding facts. |
| Multilingual comparison | m-Arena-Hard or comparable EU-language evals. |
| English retention | Arena-Hard, IFEval, or comparable English instruction tasks. |

Do not accept a Swedish gain if English retention collapses.

## Swedish Native Prompt Ideas

Good Swedish eval prompts should include things that translated English
benchmarks often miss:

- formal and informal address;
- plain-language Swedish suitable for public authorities;
- Swedish date, address, and personal-number-like formatting constraints;
- source-grounded summaries of Swedish documents;
- legal/health/public-service tone without giving unsafe advice;
- exact-output constraints in Swedish;
- "answer only in Swedish" tasks;
- Swedish tool-use tasks with Swedish field names.

## First Milestone

Create a 5k-20k sample pilot dataset:

| Slice | Size |
| --- | ---: |
| English replay | 5k |
| Aya Swedish or EU slice | 2k-5k |
| Translated Dolci Swedish | 2k-5k |
| Swedish constraint-following | 1k-2k |
| HelpSteer3 preference/edit sample | 1k-2k |
| Swedish tool-calling sample | 500-1k |

Then run one small SFT pass and one small DPO/reward-model pass, with careful
evaluation before scaling.

