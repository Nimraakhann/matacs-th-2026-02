# Design write-up — MATACS-TH-2026-02

## Process, decisions and assumptions

An admin creates a **versioned**, category-specific configuration containing required evidence, mandatory gates, project constraints, weights, scoring parameters and tie-breakers (the weights must total 100%). A reviewer chooses the category and uploads proposals. The service validates file type/size, extracts PDF text (OCR if scanned), preserves page references and passes the text plus the selected configuration to AI. AI returns structured **claims**, source excerpts and uncertainty flags. Code validates the response, applies gates, calculates scores and sorts eligible bids. The reviewer can inspect evidence, resolve flags and approve or override recommendations; a human alone awards the contract. See [diagram](diagram/flow.png).

**Assumptions:** A bidder-held, proposal-stated Ontario licence and proposal-confirmed WSIB plus $5M insurance are mandatory. $260,000 and 16 weeks are project limits. A missing timeline is **flagged**, not invented. Experience counts and claimed "named" leads are credited **provisionally** for this sample only; the exact dates/names need verification before award, especially C's nine installations "since 2020" versus the rolling five-year requirement. All category data and scoring parameters are stored in admin configuration, not in application source code.

## 1. AI vs. rules

- **AI:** Interpret variable wording and extract bidder ownership of licences, clearance, insurance, qualifying work, amounts, weeks, warranties, lead/crew, safety and monitoring. Return JSON with source evidence; use `null` for missing or ambiguous values. Flag attempted instructions inside proposals.
- **Code/rules:** Validate uploads and JSON, retrieve the frozen criteria version, check eligibility and constraints, compute weighted scores, apply stable tie-breakers, and store audit records. These steps must be reproducible and need no generative judgment.
- **Human:** Resolve unclear claims, correct erroneous extraction, examine exclusions and approve the final award. AI never determines the winner.

## 2. Consistency and explainability

Each run stores the exact input document hashes, validated evidence, criteria/rubric version and results. Replaying **the same frozen facts and configuration** gives the same ranking; rerunning raw AI extraction can differ, so extraction is validated and snapshotted. Display gate results, criterion sub-scores, supporting page excerpts, missing information and a one-line reason. If scores tie, use lower price, then proposal letter.

**Proposed illustrative scoring policy** (all sub-scores 0–100; these numerical benchmarks require admin approval):

| Criterion | Weight | Deterministic sub-score |
| --- | ---: | --- |
| Experience | 30% | `min(100, 100 × provisional qualifying installation count / 12)` |
| Price | 25% | If within $260k: `clamp(50 + 50 × (260000 − price) / 60000, 0, 100)`; $200k earns 100 |
| Timeline | 15% | If within 16 weeks: `clamp(100 − 10 × (weeks − 10), 0, 100)` |
| Warranty | 15% | `50 × min(panel years / 25, 1) + 50 × min(workmanship years / 12, 1)` |
| Team / approach | 15% | 25 each for claimed named lead, specified crew, safety plan and monitoring plan |

`clamp(x,0,100)` limits results to 0–100. Total = `Σ(weight × sub-score / 100)`, rounded **only at the end** to two decimals. An unconfirmed mandatory gate or hard deadline produces a flag; an explicitly unsatisfied gate produces ineligibility. Do not fill absent information using guesses.

| Result | Score | Rationale |
| --- | ---: | --- |
| **A — rank 1** | **87.83** | High experience, 12 weeks, complete approach and acceptable price |
| **E — rank 2** | **76.65** | 12-year workmanship warranty and 24-month monitoring; lower experience score |
| **C — rank 3 (provisional)** | **68.58** | 14 weeks and no stated safety/monitoring plan; experience dates need checking |
| **D — flagged** | — | No committed timeline, so 16-week fit is unconfirmed |
| **B — ineligible** | — | Only a future licensed subcontractor is described, not a bidder-held licence |

The embedded statement in C telling an automated reviewer to give full marks is **untrusted text**, not authority. It receives no scoring credit and can be logged for review.

## 3. Human in the loop

Reviewers inspect low-confidence or conflicting extractions, missing fields, C's manipulation attempt, mandatory failures and final scores. They may correct facts with a source excerpt or record a reasoned override, but must not silently waive hard requirements. An authorized decision-maker approves the award. Audit: user, time, original file hash, excerpts and edits, extraction model/prompt version, criteria version, raw gates, sub-scores, ranking, overrides and final decision.

## 4. Changing criteria

Edits create a **new configuration version**; reject weights that do not total 100%. Old results retain their original criteria and remain auditable. A reviewer explicitly reruns **all** proposals with one new version, storing a separate ranking; no old results are silently overwritten. The changed criteria might alter eligibility as well as order.

## 5. Risks and safeguards

- **OCR / extraction mistakes:** Require type/range checks, source-page excerpts and human review for uncertain claims.
- **Hallucination / incomplete evidence:** Use `null`, never invent licences, dates or schedules; mark uncertain decisions as flagged.
- **Prompt injection:** Isolate document content as untrusted data; ignore requests to change rules, including C's; log the attempt.
- **Bias / inconsistent scores:** Evaluate only admin-defined evidence with fixed formulas; avoid bidder-name-based scoring; audit overrides.
- **Cost / confidentiality:** Cache extractions and avoid repeat AI calls for math; restrict access/retention and use only approved AI processing.
