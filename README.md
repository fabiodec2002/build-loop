# build-loop

A Claude Code skill that stops the agent retrying the same broken fix.

1. Agree the test first.
2. Make one small change.
3. Check it in under a minute.
4. If a fix fails twice, park that approach on a `shelved/` branch and try a different one.
5. After three shelved approaches, stop and report why each one failed.

The full rules are in [`SKILL.md`](SKILL.md).

## Install

```bash
mkdir -p ~/.claude/skills/build-loop
curl -o ~/.claude/skills/build-loop/SKILL.md https://raw.githubusercontent.com/fabiodec2002/build-loop/main/SKILL.md
```

Claude Code picks up the skill in your next session. To have it run on every code change, add this line to `~/.claude/CLAUDE.md`:

> As soon as a task involves changing code, invoke the `build-loop` skill first.

## Credit

Adapted from Andrej Karpathy's [autoresearch](https://github.com/karpathy/autoresearch).

## License

MIT
