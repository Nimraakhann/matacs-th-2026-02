# AI-assisted Proposal Triage Design

**Candidate:** Nimra Khan  
**Assessment reference:** MATACS-TH-2026-02  
**Time actually spent:** **2 hours 30 minutes total** (1 hour reading and understanding the assessment brief; 1 hour 30 minutes preparing the design and deliverables)

This repository presents a **design exercise, not a working app**. The process combines AI-assisted evidence extraction, deterministic eligibility checks and scoring, and a human-controlled award decision. Contractor documents are treated as untrusted data.

## Deliverables

- [Process-flow diagram](diagram/flow.png) — upload-to-output workflow, with AI, rules, and human checkpoints.
- [WRITEUP.md](WRITEUP.md) — answers to all five questions, assumptions, scoring method, and the example ranking (under 1,000 words).
- [PROMPTS.md](PROMPTS.md) — the system/user prompts for structured extraction.
- [ranking.json](ranking.json) — proposed results for all five bids, in the requested format.

## Important assumptions

The assessment supplies weights, but not exact scoring curves. The example scoring rubric in [WRITEUP.md](WRITEUP.md) is a **proposed, admin-approved configuration**, not an official Matacs rule. The five short summaries are not independent verification of licences, insurance, or previous installations. In particular, counts stated as "since 2020" or "since 2021" do not prove exactly how many installations fall inside a rolling five-year window. For this **illustrative ranking only**, the stated counts are provisionally used; a real reviewer must verify dates and evidence before award. Descriptions of a "named lead" receive provisional checklist credit even though a person's name is not reproduced in the summaries.

## AI-use disclosure

I used ChatGPT to help structure the workflow, draft extraction prompts, and check the example calculations. An earlier AI-generated answer listed numeric rankings without showing how the scores were obtained; that output was corrected by adding the explicit, repeatable rubric because unexplained numbers cannot be audited. Unverified experience-date claims are also labelled provisional rather than presented as established facts.

## Submission notes

The original assessment brief and contractor proposal text are **not** reproduced in this repository. No API keys, production client data, or deployment are included. The reviewer remains responsible for final approval and for correcting anything incomplete before submitting.
