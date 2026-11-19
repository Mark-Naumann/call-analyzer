# Finding 3 (incident): Publishing in Vapi silently switched the review off

**Agents:** both · **Found by:** a blank review email · **Status:** workaround in place;
Vapi platform bug

## What happened

After I published a prompt change from the Vapi assistant editor, the next call review
email arrived **blank**: no score, no issues, no suggested fix.

## How it was confirmed

The call logs showed that call had **no "call quality review" output at all**. Publishing
from the assistant editor had silently detached the structured output, because it had been
attached from the *structured output's own page*, not from the assistant editor. No
warning was shown.

## Impact

Any call made between a publish and the re-attach would have no review, and nothing
would say so: the monitoring would just go quiet.

## Workaround

After every publish: **re-attach the structured outputs**, then check that the next
review email isn't blank. (The config export in each agent repo also shows which
structured outputs are attached, so a missing one is visible in the diff.)

## What it taught me

- "No alerts" doesn't mean "no problems". The blank email was the only signal, and it
  was caught because a person looked.
- Monitoring should alert on **missing data**, not only bad data. In
  [call-analytics](https://github.com/Mark-Naumann/call-analytics), the "Average quality
  score" report shows reviewed calls next to total calls, so a gap stands out.
- Next step: an automatic check that every assistant still has its structured outputs
  attached after each publish.
