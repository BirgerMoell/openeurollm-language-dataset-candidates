# OpenEuroLLM Context

This document records the working assumptions behind the candidate list.

## What OpenEuroLLM Already Appears To Cover

OpenEuroLLM already appears strong on pre-training data and related mixture
experiments.

Covered or active data families include:

| Dataset or Family | Notes |
| --- | --- |
| DCLM | Present in the training-data catalogue and active English mix discussions. |
| FineWeb / FineWeb-Edu | Present or discussed for preprocessing and future runs. |
| FinePDFs | Present in the catalogue and active English mix discussions. |
| HPLT 3 / HPLT 4 | Present and actively evaluated for multilingual and English data. |
| Nemotron-CC | Present and actively used. |
| Nemotron-CC-Math / ClimbMix / FinePhrase | Discussed or tested on the board. |
| Common Pile | Present in the catalogue and discussed as a future run candidate. |
| The Stack / StarCoder / The Stack 2 | Present or discussed for code mixture work. |
| MegaMath / FineMath | Used in math/code mixture ablations. |
| Dolci-Instruct-SFT / Dolci-Think-SFT / Dolci-DPO | Active translation, replay, and annotation work. |
| Tulu-3 SFT/DPO | Active post-training and filtering work. |
| WildChat / LMSYS / OASST2 | Used in multilingual post-training ablations. |
| OpenThoughts | Already listed in the catalogue and related post-training work. |
| MaLA translation corpus | Active parallel-data experiment; quality issues noted. |

## Important Board Signals

The candidate list is shaped by a few findings from the board:

- OpenEuroLLM is revisiting flagship data mixtures, especially English,
  multilingual, code, math, and parallel data.
- English data-mix experiments suggest that higher math/code proportions can
  improve reasoning-oriented evaluations, with some tradeoff against
  commonsense tasks.
- Dolci replay appears important for preserving English during multilingual
  post-training.
- More generic chat diversity, such as WildChat/LMSYS/OASST2-style mixtures,
  did not clearly solve English forgetting in the referenced OLMo-3 experiment.
- HPLT4 and quality signals such as WDS, Propella, BSC-Edu, and JQL are active
  areas for pre-training selection.
- Tool calling is present as an open task, but the board does not appear to
  have a mature Swedish/EU tool-calling data recipe yet.

## What This Repo Adds

This repo intentionally focuses on candidates that are not just more
pre-training text.

The main gaps are:

- multilingual human-authored instruction data;
- preference, feedback, and edit data;
- reward-model and judge-alignment data;
- instruction-following and constraint-following data;
- function-calling and structured-output data;
- Swedish-first adaptation recipes.

The most immediately useful candidates are Aya Dataset and HelpSteer3 because
they are relevant, reasonably clear to use, and have comparatively friendly
licenses.

