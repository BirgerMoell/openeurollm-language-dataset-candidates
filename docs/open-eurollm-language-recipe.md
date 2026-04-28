# OpenEuroLLM Target-Language Post-Training Recipe

This is a concrete experiment plan for improving instruction behavior in
OpenEuroLLM target languages while preserving English and broader
European-language ability.

## Goal

Build small but high-quality target-language post-training mixtures that answer:

- Can target-language instruction ability improve without English collapse?
- Does human-authored multilingual data help beyond translated Dolci?
- Does preference/edit data improve helpfulness and judge scores?
- Can we add localized constraint-following and tool-calling behavior cheaply?

## Core Principle

Do not train one target language in isolation. Use English replay and retention
evaluation from the start, and compare across several OpenEuroLLM languages
where possible.

OpenEuroLLM dataset results suggest that the source and style of English replay
data can matter more than the exact English/EU ratio. Each target-language
experiment should therefore preserve English with source-style replay data
while adding language-specific and multilingual signal.

## Data Components

| Component | Candidate Sources | Purpose |
| --- | --- | --- |
| English replay | Dolci-Instruct-SFT or base-model source-style English | Preserve English ability during target-language adaptation. |
| Target-language SFT | Aya target-language slice, translated Dolci-Instruct-SFT | Teach assistant behavior in the target language. |
| Target-language reasoning | Small translated MathX/Numina/Nemotron Science slice | Measure and improve STEM reasoning in the target language. |
| Target-language constraints | Translated/adapted IFEval-like data | Improve exact instruction following in the target language. |
| Preference/edit | HelpSteer3 multilingual or translated subset | Improve helpfulness and reward/judge behavior. |
| Tool calling | xLAM/Hermes/ToolACE schema adapted to local tools | Teach structured output and local tool use. |
| Evaluation | Native target-language eval, m-Arena-Hard, English Arena-Hard | Measure gains and regressions. |

## Suggested Ablations

| Run | Data |
| --- | --- |
| A | English replay only |
| B | English replay + translated Dolci in one target language |
| C | English replay + Aya examples in the same target language |
| D | English replay + translated Dolci + Aya in the same target language |
| E | Run D + target-language constraint-following data |
| F | Run E + HelpSteer3-style preference tuning |
| G | Run F + localized tool-calling data |

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
| Native target-language instruction following | Public-sector prompts, summaries, format constraints, register control. |
| Target-language source-grounded tasks | Summarize or answer from target-language source text without adding facts. |
| Multilingual comparison | m-Arena-Hard or comparable EU-language evals. |
| English retention | Arena-Hard, IFEval, or comparable English instruction tasks. |

Do not accept a target-language gain if English retention collapses.

## Native Prompt Ideas

Good native eval prompts should include things that translated English
benchmarks often miss:

- formal and informal address;
- plain language suitable for public authorities;
- local date, address, and identifier-like formatting constraints;
- source-grounded summaries of documents in the target language;
- legal/health/public-service tone without giving unsafe advice;
- exact-output constraints in the target language;
- "answer only in the target language" tasks;
- localized tool-use tasks with field names in the target language.

## First Milestone

Create a 5k-20k sample pilot dataset:

| Slice | Size |
| --- | ---: |
| English replay | 5k |
| Aya target-language or EU slice | 2k-5k |
| Translated Dolci target-language slice | 2k-5k |
| Target-language constraint-following | 1k-2k |
| HelpSteer3 preference/edit sample | 1k-2k |
| Localized tool-calling sample | 500-1k |

Then run one small SFT pass and one small DPO/reward-model pass, with careful
evaluation before scaling.
