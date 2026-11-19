# Finding 1: Jordan read back an 11-digit phone number

**Agent:** Jordan (transit customer service) · **Found by:** the call quality review ·
**Fixed in:** Jordan v10 · **Status:** fixed

## What happened

On a real test call, a caller gave their 10-digit callback number. When Jordan read it
back to confirm, the number Jordan said had **11 digits**. If the caller had said "yes"
without listening closely, staff would have been given a number that can't be dialed,
and the caller would never get their callback.

## How it was caught

The call quality review graded the call, and its review email flagged the wrong read-back
and suggested a prompt fix. No one had to read the transcript to find it.

## The fix, and why a person wrote it

The analyzer's own suggested fix **had an error too**, so it couldn't be pasted into the
prompt as it was. This is exactly why the review only *suggests* fixes and a person
decides what goes live.

I rewrote the rule by hand. Jordan v10's prompt now says to read the number back once,
with **every digit as a word**, in groups of 3, 3 and 4 ("five oh five, …"), never as
numerals or dashes, and to **count: exactly 10 digits, no more, no less** before reading
it back.

## What it taught me

- Phone numbers are where voice agents fail quietly: a single extra digit looks fine in a
  transcript and still loses the customer.
- An AI reviewer is a great *detector*, but its suggested fix is a draft. Review it like
  any other code change.
- Next step: turn this call into a Vapi Eval ("caller gives a 10-digit number; the
  read-back must have exactly 10 digits") so the bug can't come back unnoticed.
