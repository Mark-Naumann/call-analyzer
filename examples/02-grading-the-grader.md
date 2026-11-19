# Finding 2: Grading the grader (a correct read-back marked wrong)

**Agent:** both · **Found by:** a person checking the review against the call ·
**Fixed in:** the "call quality review" accuracy rule · **Status:** fixed

## What happened

The reviewer **flagged** a call's phone-number read-back as wrong, but when I checked the
call, the read-back was **correct**.

## Why the reviewer got it wrong

The reviewer doesn't hear the call; it reads the **transcript**, and the transcript showed
the number as digits with dashes. The reviewer judged the *formatting* in the transcript
instead of whether the *digits* were right, and flagged a read-back that was correct.

## The fix

The accuracy rule was changed to judge **the digits, not the formatting**: a read-back is
correct if the digits match what the caller said, however the transcript happens to
write them.

## What it taught me

- An automated grader has its own bugs. Its scores need spot-checking against real
  calls, just like the agent's answers do.
- Grade only what the grader can actually see: the transcript can show *which digits*
  were said, but not reliably *how* they were said.
- A false alarm costs trust: if the review email cries wolf, people stop reading it.
