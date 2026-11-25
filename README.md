# MIOD-SWAT Preregistration

Preregistration for the MIOD-SwaT experiment: does SwaT transfer factual
information via KV-cache mapping between models (Qwen3-4B → Llama3-8B)?

Repository: https://github.com/SolvensAmoris/miod-swat-prereg

## Versions

| Version | File | Status |
|---|---|---|
| V1-DRAFT | `prereg/V1.md` | Superseded. Never formally frozen. Known error preserved, not corrected in situ: cites 57.7/34.2 for "Lowest" (correct: 53.1/34.2). |
| V2 | `prereg/V2.md` | Current. Corrects the V1 error in §3; adds three levels, reformulated step 0, F, H0–H4, 16-contrast family. |
| V2.1 | `prereg/V2.1.md` | Current. Technical precision corrections on V2. |

See `MANIFEST.md` for versions, relations, and the no-edit convention.

## Status

The definitive evaluation is **not executed**. Precondition: the level 1 and
level 2 pilots pass (§5, §6 of v2).

**Deviation rule:** any change vs the current version is recorded with date,
reason, and moment (before/after observing data). Changes made after observing
data are reported as exploratory and cannot falsify hypotheses.

## Key design

- Three levels: technical validity / instrumental validity / scientific evidence.
- Unit of analysis and independence: document d. Splits by document, never
  by question.
- δ = 5 absolute accuracy points. Positive control ≥ 70 %, negative control
  ≤ 10 %. Calibration statistic G = Acc(C_d^R) − Acc(Z_d).
- 16 primary contrasts (4 hypotheses × 4 types), Holm-Bonferroni over the
  evaluable ones.
- Primary probe: V-mean per layer.

## Environment

Python 3.10, PyTorch 2.6.0, transformers 5.2.0, datasets 4.5.0, 2 GPUs.
GPU model is declared in the execution record before the pilot and not changed
afterwards.
