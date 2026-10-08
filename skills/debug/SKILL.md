---
name: debug
description: Something is broken and the cause is unknown.
---

# Debug

You own the hunt. Do not guess from source. Do not ask the user to repro. A bug you cannot reproduce, you cannot prove fixed.

## Engineer

Question only this failure, not the product. Fight to add nothing. The smallest change the evidence justifies ships, nothing more. A "might help" guard is not a fix; revert it. Same result with less is the win, or better.

Trace to the real cause. Do not hide a problem. A nil-check that silences a crash is a symptom fix. If a workaround needs a paragraph to justify it, the code is wrong.

## Do

To reach the app, read the local skill. Reproduce it yourself on the real surface. Won't fire? Force it: tighter trigger, more logs, synthetic input. Ask the user only after you drove as far as the surface allows, with a stated reason.

Form hypotheses. Each pass, cut the most remaining space. Read the program as it runs. When state is unclear, instrument. Don't guess. Confirm the surviving mechanism with runtime evidence before you design a fix. A plausible cause can be wrong while the real one sits one layer over.

Restart bugs: suspect stale state first (config, cache, locks), then code. Check the pattern, not just the instance.

Fix only what the evidence names. Cheap local test path: failing check first, then the fix. Expensive or unclear: skip the new test, use the original repro, say why.

Verify on the same surface. The original repro now fails to happen. Inconclusive is not a pass. Unit tests show a branch, not bug absence. If the check looks green too easily, suspect the observation method before the system. Clean up instrumentation after.

Cause not obvious: use the debug subagent. You stay lead. You do not ship a fix from its summary; you inspect the artifact.
