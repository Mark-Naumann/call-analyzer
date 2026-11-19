# call-analyzer — AI quality review for every voice-agent call

**After every call, an AI reviewer grades how my voice agent handled it and emails me a
score, the rules it checked, the problems it found and one suggested prompt fix. A person
reviews every fix before it goes live.**

🎬 **Demo video:** coming soon

**Runs on:** Sam v14 ([painting-agent](https://github.com/Mark-Naumann/painting-agent)) and
Jordan v10 ([transit-agent](https://github.com/Mark-Naumann/transit-agent)) · Nov 2026

---

## Why

Scripted evals check the cases I thought of. Free-form test calls find the ones I didn't.
Instead of reading every transcript, each call is reviewed automatically and I read the
reviews, starting with the lowest scores. It has already found a real bug (see
[examples](#real-findings)).

## What the review checks

The review is a Vapi **structured output** named "call quality review" (type `ai`): at the
end of each call, an AI model reads the transcript and fills in these 8 fields.

| Field | What the reviewer answers |
|---|---|
| `overall_score` | 1 to 10 for how well the agent handled the call. 10 = as well as a great human rep would. |
| `caller_goal_met` | yes/no: did the caller get what they called for (an answer, a booking, their info taken, or a correct referral)? |
| `one_question_per_turn` | yes/no: did the agent ask only one question per turn and never answer its own question? |
| `remembered_context` | yes/no: did the agent use what the caller already said instead of asking again? |
| `accuracy` | yes/no: did the agent avoid making up facts, prices, times or policies, and repeat names and phone numbers back with the **correct digits** (10 digits, matching what the caller said)? Numerals or dashes in the transcript are fine; only wrong, missing or extra digits (or made-up info) count as "no". |
| `caller_sentiment` | happy, neutral or frustrated, by the end of the call |
| `issues_found` | Short list of the agent's specific mistakes, each with a short quote from the transcript, or "none" |
| `suggested_prompt_fix` | One specific rule to add or change in the prompt to prevent the biggest issue, ready to paste, or "none" |

The full definition is in [`config/structured-output-call-quality-review.json`](config/structured-output-call-quality-review.json)
(redacted export from Vapi).

## How it works

```
Caller ──► Vapi voice agent (Sam or Jordan)
                │
                │ call ends
                ▼
     Vapi runs the "call quality review" structured output:
     an AI model reads the transcript and fills in the 8 fields
                │
                ▼
     End-of-call report ──► Make webhook ──┬─► email 1: the lead / request (to the business)
                                           └─► email 2: "Call review: Jordan 8/10" (to me)
                                                         │
                                                         ▼
                                    A PERSON reads it. Nothing changes automatically.
```

- The review email (Make module 3) shows the score, every yes/no check, the issues found,
  the suggested prompt fix (highlighted), the note *"Review this before changing the
  prompt. The analyzer only suggests fixes."*, and a link to the call recording. See
  [`config/make-call-review-email-module.json`](config/make-call-review-email-module.json).
- **One review, two agents:** the same structured output is attached to both Sam and Jordan,
  so scores are comparable.

## A person reviews fixes before they go live

The reviewer is good at **spotting** problems and unreliable at **fixing** them, so:

1. Read the review email; open the recording if the score is low or a check says "no".
2. Decide whether the issue is real (the reviewer can be wrong too, see Finding 2).
3. Write or edit the prompt change by hand, using the suggested fix as a draft.
4. Publish the new version, **re-attach the structured outputs** (see Finding 3), and make
   a test call.
5. Export the config to the agent's repo so the change is versioned and reviewable.

## Real findings

| # | Finding | Lesson |
|---|---|---|
| 1 | [Jordan read back an 11-digit phone number](examples/01-eleven-digit-phone-readback.md). The analyzer's own suggested fix had an error too (its example number had 11 digits), so I rewrote it by hand → Jordan v10. | The reviewer is a detector; its fix is a draft. |
| 2 | [Grading the grader](examples/02-grading-the-grader.md): a correct read-back was flagged because the transcript showed the digits with dashes. The accuracy rule now checks digits, not formatting. | Graders have bugs too; grade only what the transcript can show. |
| 3 | [Incident: publishing in Vapi silently detached the review](examples/03-incident-review-silently-detached.md). Caught by a blank review email, confirmed in the call logs, worked around by re-attaching after each publish. | Monitor for *missing* data, not just bad data. |

## Privacy

No recordings, transcripts, caller names or phone numbers are in this repo. Configs are
redacted exports (org ID, webhook, emails replaced with placeholders), and the examples
describe what happened in plain words.

## What I'd do next

- **Fixed issue labels:** have the reviewer choose `issues_found` from a short list
  (`phone_readback`, `asked_two_questions`, `missed_context`, …) instead of free text, so
  issues can be counted reliably and can't contain a caller's name.
- **Every finding becomes an eval:** turn each real bug (like the 11-digit read-back) into
  a Vapi Eval that runs before every publish.
- **Spot-check the grader:** each week, compare a few reviews with the recordings and track
  how often the reviewer is wrong.
- **Attachment check:** an automatic check after every publish that both agents still have
  their structured outputs attached.
- **Score trends:** chart average score per prompt version in
  [call-analytics](https://github.com/Mark-Naumann/call-analytics) to show whether each
  change actually helped.

## Related projects

| Repo | What it is |
|---|---|
| [painting-agent](https://github.com/Mark-Naumann/painting-agent) | Sam, AI receptionist for a painting company (Vapi) |
| [transit-agent](https://github.com/Mark-Naumann/transit-agent) | Jordan, AI customer service for a fictional transit agency (Vapi) |
| [call-analytics](https://github.com/Mark-Naumann/call-analytics) | Python + SQLite monitoring: quality scores, transfers, tool errors, dead air, cost |
| [transit-sim](https://github.com/Mark-Naumann/transit-sim) | The simulated bus system and API Jordan's tools call |
