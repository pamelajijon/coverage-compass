# AI behavior spec

How the model in Coverage Compass should behave, what it must never do, and how failures are handled.

## What the model does

Given the text of a coverage letter, the model returns structured JSON: a plain summary, the reason with a supporting quote, the deadline with a supporting quote, next steps, questions for the prescriber or pharmacist, and an escalation decision. The UI renders this. The model never writes free text straight to the screen.

## Hard rules

1. **Only the letter.** Every claim about the decision, reason, and deadline comes from the letter and is backed by an exact quote.
2. **No gap-filling.** If the letter doesn't say it, the output says so.
3. **No clinical advice.** Never suggest starting, stopping, or changing a medication or dose.
4. **Plain language.** 6th–8th grade reading level. Jargon gets explained.
5. **Escalate when in doubt.** If the model can't tell what was decided, it escalates.

## Escalation triggers

The output sets `escalate.needed = true` when any of these are true:

- The deadline is within 14 days, or has passed
- The letter mentions an urgent or expedited situation
- The patient may run out of medication soon
- The text isn't a coverage letter
- The decision can't be determined

## Known failure modes

| Failure | Severity | Detection | Handling |
|---|---|---|---|
| Wrong deadline date | High | Eval: date match against labels | Quote shown so the user can check; escalate on uncertainty |
| No escalation on a time-sensitive letter (false reassurance) | High | Eval: false reassurance rate | Conservative triggers; release blocked if > 0 on test set |
| Escalation on a routine letter (false escalation) | Low–medium | Eval: false escalation rate | Tolerated within target; reviewed for copy fatigue |
| Quote not found in letter | Medium | Eval: string match | Treated as ungrounded; counts against grounding rate |
| Clinical advice slips in | High | Red-team cases | Prompt rule; future: output filter |
| Malformed JSON | Low | Parse error | User sees a clear retry message |

## Human in the loop

In a pharmacy pilot, patient-support staff see the output first and send it to patients only after review. The tool moves to direct patient use only after safety targets hold across the pilot.

## Privacy

No backend, logging, or storage. The API key stays in the browser tab. Users are asked to remove names, member IDs, and addresses before pasting.
