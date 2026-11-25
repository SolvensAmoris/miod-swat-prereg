# MIOD-SWAT Preregistration

Preregistration for the MIOD-SwaT experiment: does SwaT transfer factual
information via KV-cache mapping between models (Qwen3-4B → Llama3-8B)?

Repository: https://github.com/SolvensAmoris/miod-swat-prereg

## Versions

- **v2.1** (current) — `MIOD-SWAT-PREREG-V2.1.md`. Points 1, 3, 4, 6, 7 closed;
  points 2, 5, 8, 9 pending before the P pilot.
- **v2** — `MIOD-SWAT-PREREG-V2.md`. Complete protocol; superseded by v2.1.
- **v1** — referenced as immutable history, but its text is not in this
  repository yet. [VERIFICAR — add v1 text when available.]

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
