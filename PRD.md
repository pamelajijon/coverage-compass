# PRD: Coverage Compass

**Owner:** Pamela Jijon · **Status:** Prototype · **Last updated:** October 2026

## 1. Problem

Patients who receive a prescription denial, partial approval, or formulary notice often don't understand what was decided, why, or what to do next. The letter is technically complete but written in plan and regulatory language. The appeal deadline is easy to miss.

Menopause treatment is a sharp version of this problem. Hormone therapy and newer non-hormonal options are frequently subject to step therapy, formulary restrictions, and quantity limits. When a patient can't decode the letter, the default outcome is abandoning treatment.

## 2. Users

**Primary:** A woman in perimenopause or menopause who just received a coverage letter and wants to know what to do.

**Secondary:** Pharmacy patient-support and care-navigation teams who answer "what does this letter mean?" calls today.

## 3. Assumptions to validate

I'm treating these as hypotheses, not facts, until tested:

- Patients misread or ignore coverage letters often enough to cause missed appeals and dropped therapy.
- A plain-language explanation with a clear next step increases contact with the prescriber within 7 days.
- Patients will trust an AI explanation more when it quotes the letter directly.
- Pharmacy support teams spend meaningful call time explaining letters that a tool could pre-explain.

## 4. Goals

1. A patient understands what was decided and why within 60 seconds.
2. A patient knows the deadline and their next step.
3. Zero false reassurance: the tool never implies there's time when there isn't.

**Non-goals:** clinical recommendations, filing appeals on the patient's behalf, insurance navigation beyond the letter in hand, storing patient data.

## 5. Requirements

| Priority | Requirement |
|---|---|
| P0 | Explain the decision in plain language (6th–8th grade reading level) |
| P0 | Extract the deadline and calculate the date from the notice date |
| P0 | Back every claim about the decision and deadline with an exact quote from the letter |
| P0 | Say "your letter doesn't say" instead of guessing |
| P0 | Never recommend starting, stopping, or changing medication |
| P0 | Escalate ("call today") for deadlines within 14 days, expedited situations, possible gaps in supply, or unclear letters |
| P1 | Highlight and define insurance jargon without needing the model |
| P1 | Suggest 2–3 questions for the prescriber or pharmacist |
| P2 | Draft an appeal request for prescriber review |
| P2 | Spanish-language support |

## 6. Risks and mitigations

| Risk | Mitigation |
|---|---|
| Wrong deadline date | Quote the source sentence; deterministic date check in evals; escalate when uncertain |
| False reassurance | Conservative escalation threshold; measured as the primary safety metric |
| Hallucinated reasons or options | Grounding requirement; grounding rate measured in evals |
| Drifting into clinical advice | Hard rule in the system prompt; red-team cases in the test set |
| Patient privacy | No backend or storage; prompt users to remove identifiers before pasting |

## 7. Success metrics

- **Safety:** false reassurance rate = 0 on the test set; deadline accuracy ≥ 98%; grounding rate ≥ 95%.
- **Comprehension (usability testing):** ≥ 80% of participants correctly state the decision, deadline, and next step after using the tool.
- **Outcome (pilot):** increase in patients contacting their prescriber within 7 days of the letter; reduction in therapy abandonment after denial.

## 8. Rollout path

1. **Prototype** (this repo): fictional letters, offline eval set.
2. **Usability study:** 5–8 participants with real experience of a denial.
3. **Pharmacy pilot:** used by patient-support staff first, with a human reviewing outputs before they reach patients.
4. **Patient-facing beta:** only after the pilot meets safety targets.
