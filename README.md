# MHPP-10 Ablation

A pre-registered, three-condition ablation of the Ejentum reasoning harness on the 10 hardest tasks of the MHPP coding benchmark, solved by Claude Opus 4.8.

## Conditions

- **B (baseline)**: the solver receives the task, no harness call.
- **D (dynamic)**: the solver calls `api.ejentum.com/harness/` with `mode=code` (top-1 retrieval) the moment it sees the task, places the returned scaffold in a `[REASONING CONTEXT]` block, then solves.
- **A (adaptive)**: same as D but `mode=adaptive-code` (top-5 retrieval plus an adapter-LLM rewrite tailored to the specific task).

The harness is called by the solve agent as an agentic tool at task time, not pre-generated, matching the production pattern.

## Result

Pass rate was identical across all three conditions: **9/10 each**. The single failure (`mhpp_130`) failed the same assertion in every condition and was confirmed on review to be a test-authoring error, so the corrected pass rate is 10/10/10. On pass rate, this is a clean null.

The code itself was not identical. Adaptive-mode output carried **240% more comments**, **100% more defensive guards**, **+26% lines**, and consistent shifts in algorithmic class:

- sliding window (O(26n)) instead of brute force (O(n^2))
- backward dynamic programming instead of forward
- closed-form derivation instead of iteration

The reading: pass-rate saturation on a top-tier solver masks a real, measurable scaffold effect that lives in code character. The harness moved the model from ad-hoc-correct to engineered-correct (state the invariant first, derive the code from it).

## Blind review

Three SWE-expert agents (a systems engineer, a competitive programmer, and a staff engineer on code review) ranked the three submissions per task, blinded as X/Y/Z with labels rotated per task to block positional bias. Scoring was 3/2/1 per ballot, max 27 across the three case-study tasks.

| Condition | Score (max 27) |
|---|---|
| A (adaptive) | 26/27 |
| D (dynamic) | 19/27 |
| B (baseline) | 9/27 |

A placed first on 8 of 9 ballots; the single deviation still ranked B last. B was last on all 9. The reviewers, not knowing which submission was which, converged on the same **A > D > B** ordering the pre-registration predicted, on independent criteria (algorithmic class, invariant clarity, reviewability). On the substring task one reviewer ran the adaptive output against 5000 characters with `count=5000`: the brute-force submission took 21.4s, the sliding-window adaptive output finished in 0.001s, a measured ~21,000x speedup. Full per-task reasoning is in `BLIND_EVAL.md`.

## Pre-registration

The hypothesis ladder **A > D > B** was committed to this repo at `851f37e5` (2026-05-31T15:01:20Z) before any solve agent ran. Predicted pass rates were A 6-8, D 4-6, B 2-4. The baseline saturated far above prediction (9/10), which is itself the finding: this slice was too easy to resolve a pass-rate effect, so the effect surfaced in code character instead.

## Honest limits

- n=10 with a 90% baseline ceiling leaves almost no room for a measurable pass-rate lift on this difficulty bucket.
- Tests were AI-authored because MHPP withholds canonicals; one error was caught, others may remain.
- Structural metrics (comment and guard counts) are proxies. The case studies are the actual evidence; the metrics are the screening pass.

A round-2 ablation against a weaker solver, where the engineered-versus-ad-hoc distinction should convert into pass-rate lift, is pre-registered.

## Files

- `PRE_REGISTRATION.md` — predictions committed before scoring (`851f37e5`).
- `RESULTS.md` — pass-rate scoreboard and per-task matrix.
- `REPORT.md` — full methods, structural metrics, case studies, threats to validity.
- `BLIND_EVAL.md` — three-expert blind ranking, per-task ballots and reviewer reasoning.
- `case_studies.md` — line-by-line of the three algorithmic-class shifts.
- `raw_scores.json` — per-task per-condition execution results.
- `chart.svg` — pass-rate bar chart.

## The harness

[ejentum.com](https://ejentum.com)
