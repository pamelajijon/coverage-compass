# Evaluation plan

## The framing: borrowed from biometrics

In fingerprint and facial recognition, every system makes two kinds of mistakes:

- **False accept (FAR):** letting the wrong person in
- **False reject (FRR):** locking the right person out

You can't drive both to zero. You pick a threshold based on which error costs more. A bank vault and a phone unlock sit at very different points on that curve.

Coverage Compass has the same structure around one decision: **does this patient need to act now?**

| Biometrics | Coverage Compass | Cost |
|---|---|---|
| False accept | **False reassurance:** the tool says "you have time" when the deadline is close, the situation is urgent, or the letter is unclear | High. A missed appeal or a gap in medication |
| False reject | **False escalation:** the tool says "call today" when there's plenty of time | Low to medium. An unneeded phone call; alert fatigue if it happens often |

Because false reassurance is the expensive error, the escalation triggers are deliberately conservative. I accept some false escalation to keep false reassurance at zero.

## Metrics

| Metric | Definition | Target |
|---|---|---|
| False reassurance rate | Escalation-required cases where the model didn't escalate | **0%** (release gate) |
| False escalation rate | Routine cases where the model escalated | ≤ 20% |
| Deadline accuracy | Extracted date matches the label (including "no deadline") | ≥ 98% |
| Grounding rate | Quotes that actually appear in the letter | ≥ 95% |
| Clinical-advice flags | Lines that look like medication instructions | 0 after manual review |

## Test set

`test-cases.json` holds 12 fictional, labeled letters. The eval date is fixed so deadline-based labels stay valid over time. Cases cover:

- **Routine:** step therapy denial, non-formulary notice, partial approval, approval, non-standard 65-day window
- **Must escalate:** deadline in 11 days, deadline already passed, expedited review, supply running out in 4 days
- **Edge cases:** a pharmacy newsletter (not a coverage letter), a letter cut off before the decision
- **Red team:** a patient note asking whether to stop their estrogen

Each case tests one thing on purpose. When something fails, I want to know why.

## How it runs

`run_evals.py` reads the system prompt and model straight from `index.html`, so the eval always tests what ships. It prints each case, the summary metrics, and any deadline misses, then saves `results.json`. It exits with an error if false reassurance is above zero, so it can run as a CI gate before any prompt change merges.

## What this set doesn't cover yet

- Real letters. The next version needs de-identified letters from several plans, since real formatting is messier.
- Reading level. I'd add an automated readability score and a small human rating panel.
- Comprehension. Whether patients actually understand the output can only be measured with people, in usability testing.
- Scale. Twelve cases shows the method, not statistical confidence. A production set needs a few hundred, weighted toward the cases that must escalate.
