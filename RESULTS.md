# MHPP-10 Ablation — Results

## Headline (pass rate, as scored)

| Condition | Pass rate |
|---|---|
| B (raw baseline, no harness) | 9/10 (90%) |
| D (dynamic code harness, `mode=code`, top-1 retrieval) | 9/10 (90%) |
| A (adaptive code harness, `mode=adaptive-code`, top-5 + adapter) | 9/10 (90%) |

Quarantined entries: none.

The single shared failure, `mhpp_130`, was later traced to a test-authoring error, not a solver error: all three conditions independently computed the correct answer, and the AI-authored hidden test asserted the wrong value. After correcting that test the pass rate is 10/10 in every condition. See `REPORT.md` Section 3.2 for the correction. Either way, the conditions are tied.

## Pre-registration: prediction vs actual

Predicted pass rates (out of 10), committed in `PRE_REGISTRATION.md` at commit `851f37e5` before any solver ran:

| Condition | Predicted | Actual (as scored) |
|---|---|---|
| A (adaptive code, top-5 + adapter) | 6-8 | 9 |
| D (dynamic code, top-1, content matched, no adapter) | 4-6 | 9 |
| B (baseline, Opus 4.8 native) | 2-4 | 9 |

Pre-registered hypotheses: **H1** A > D > B (clean step-ladder); **H2** A roughly equal to D, both > B; **H3** null result, all roughly equal.

Outcome on pass rate: **H3.** Claude Opus 4.8 saturates this 10-task slice in every condition, so the directional ladder (H1) was not observed at the pass-rate level. The pre-registered ordering was not confirmed on pass rate, and we publish that unedited.

## Per-task P/F matrix (10 x 3)

| Task ID | B | D | A |
|---|---|---|---|
| mhpp_121 | P | P | P |
| mhpp_123 | P | P | P |
| mhpp_124 | P | P | P |
| mhpp_126 | P | P | P |
| mhpp_128 | P | P | P |
| mhpp_130 | F | F | F |
| mhpp_132 | P | P | P |
| mhpp_134 | P | P | P |
| mhpp_136 | P | P | P |
| mhpp_140 | P | P | P |

The single failure (`mhpp_130`) failed identically across all three conditions with the same `AssertionError`, the test-authoring bug noted above. No condition rescued a task the others failed; no condition broke a task the others passed.

## What this slice does and does not show

Pass rate is the wrong instrument for a saturated solver. n=10 against a ~90% Opus 4.8 ceiling leaves almost no room to resolve a lift, so a flat pass rate here is the expected outcome of a slice that is easy for the baseline, not evidence that the harness is inert.

Where the conditions do differ is in the character of the code they produced. The dynamic (D) and adaptive (A) outputs state invariants earlier, add defensive guards, and choose cleaner algorithmic classes than the baseline, monotonically B < D < A on every structural axis measured. A pre-registered blind review of the same outputs, by three independent senior-SWE expert agents scoring anonymized submissions, ranked them A > D > B on 8 of 9 ballots (points: B 9/27, D 19/27, A 26/27), including one reviewer measuring a ~21,000x speedup on adversarial input (21.4s versus 0.001s on a 5000-character worst case). Those findings, the structural metrics, and the line-by-line case studies are in `REPORT.md`, `BLIND_EVAL.md`, and `case_studies.md`.

The honest summary: a null on pass rate for this slice, and a measurable effect on code character that pass rate cannot see when the solver is already saturated. A larger n, a harder slice (a difficulty 6-7 mix to push the baseline toward 60-70%), or a weaker solver would be needed before any claim of pass-rate lift on MHPP.

## Files in this repo

- `PRE_REGISTRATION.md` — predictions committed before scoring (commit 851f37e5).
- `RESULTS.md` — this document (pass-rate scoreboard).
- `REPORT.md` — full methods, structural metrics, and discussion.
- `BLIND_EVAL.md` — independent blind review of the outputs.
- `case_studies.md` — line-by-line of the three case-study tasks.
- `raw_scores.json` — full per-task per-condition scoring array.
- `chart.svg` — bar chart of the three pass rates.
