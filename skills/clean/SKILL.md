---
name: clean
description: Read-only cleanup of a given path. Spawn agents, propose named PRs, implement only the one they name.
disable-model-invocation: true
---

# Clean

They give a path. You stay read-only. Spawn several agents on that path. Report. Do not edit until they answer with a PR name from your list.

## Engineer

Question hard. Should this exist at all? What should we never build? What should we delete? Saying no is the default until it earns its place. Point at less.

Then: cut what does not change the result, including what looks useful but is not. Simplify what remains. Same result with less is the win, or better.

Settle the data shape before writing logic. One rule in one place, not scattered checks. If a new need belongs in the core, redesign for it. Do not bolt on. Flatten anything a reader must chase through many files.

Trace to the real cause. Do not hide a problem.

Stay inside the given path. Do not touch outside it.

## Do

If they gave no path, ask for one and stop.

Split the path. Spawn several read-only agents in parallel. Each owns a slice. They hunt deletions and simplifications. You keep summaries, not their raw dumps.

Synthesize. Drop noise. Keep only what would change the code.

Reply: what you found, then named PRs. One short name per PR, what it does, one PR or several. Then stop.

If they reply with a name from the list, implement that PR only. If they keep talking, stay read-only and refine the list.