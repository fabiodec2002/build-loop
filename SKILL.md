---
name: build-loop
description: Use when starting or carrying out any code change in any repo (feature, bugfix, refactor, prompt or model tuning), when a check is still red after a fix attempt, or when a slow or paid eval, or a full test suite, is about to run more than once.
---

# Build loop

**Speed = tries per hour × share of tries kept.** A try is cheap when the check is fast, the judge stays fixed, and a failed try costs a few commands to undo. Adapted from [Karpathy's autoresearch](https://github.com/karpathy/autoresearch) loop. If you also use the superpowers skills, this one wins where they differ.

**Not for taste work** with no machine check (copy quality, UI feel). There the loop is: draft, a person reacts, rewrite. Keep that cycle short.

## The cycle

1. **Agree the done check.** Test names and expected behavior in chat; the user's yes approves. Commit the failing tests alone and note the SHA. Money, auth, migrations, webhooks: use your fuller review process instead.
2. **Inner loop, per try:** one small change, then the fast check: tests affected by the change plus typecheck. Use the repo's `check:fast` script; if it has none, run `tsc --noEmit && vitest run --changed` (or the runner's equivalent) and offer to add one. Target under 60 s.
   - Green: commit.
   - Red: one fix on the same approach.
   - Red after 2 fixes, or 20 min on one approach: shelve it and start a different approach, without asking. Shelve: `git switch -c shelved/<approach>`, `git add -- <files you touched>`, `git commit -m "shelved: <approach>"`, `git switch -`. Never `git stash` (the stack is shared across sessions) and never `git reset --hard`.
   - Third approach shelved: stop. Report the goal, each approach, why it failed, and the `shelved/` branches.
3. **Slow or paid evals have two tiers.** Compare tries on a reduced eval: the fewest cases that show the effect (a subset filter; add one if the eval has none). Run the full eval once, on the winner, at the promote gate. Read earlier runs first for a baseline.
4. **Promote gate, once per PR:** full test suite, lint, full eval if a prompt or model changed, and `git diff <test SHA> HEAD -- <approved test files>` is empty.
5. **PR** with the run evidence in its description. Merge per the project's deploy rules.
6. **Close the thread:** delete its handoff file, remove its worktree, delete its merged and `shelved/` branches.

**Open threads:** starting new work with 3 or more `HANDOFF-*.md` in the repo root? Name them to the user in one line and ask which can close.

**Unattended runs** only when the done check is fully machine-scored, with step 2's stop rule. Run them somewhere that outlives the session, such as a scheduled cloud agent; a loop inside one local session dies with it.

## Red flags

| Thought | Reality |
|---|---|
| "Attempt 3 will work" | A hunch. Shelve, change approach. |
| "Reverting throws away work the user hasn't seen" | The `shelved/` branch keeps it. Shelve without asking; report after approach 3. |
| "systematic-debugging says stop and question the approach" | Here, questioning it means changing it. Shelve, try another; stop after approach 3. |
| "Full paid eval per tweak, so each effect is attributable" | The reduced eval attributes each tweak. Full eval once, on the winner. |
