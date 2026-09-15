# skill-scorecard

A 30-second safety scorecard for AI agent skills — run it on any skill folder or GitHub repo before you install it.

**Part of the Evidence-first Agents suite** — tooling that makes AI agents accountable instead of just capable: [answer-contract](https://github.com/chenhz01/answer-contract) (output discipline) · [skill-spec](https://github.com/chenhz01/skill-spec) (spec discipline) · [memory-wiki](https://github.com/chenhz01/memory-wiki) (memory discipline) · [skill-os](https://github.com/chenhz01/skill-os) (the assembly line). Same author, same zero-dependency philosophy.

## Why

GitHub's own documentation warns that third-party agent skills may contain prompt injections, hidden instructions, or malicious scripts — and research on marketplace skills found roughly 1 in 8 carries a serious defect, with skills bundling a `scripts/` directory at ~2x the odds. Yet the standard install advice is still "stick to trusted sources", with no tool to check anything in under a minute.

skill-scorecard is that check: seven structural probes, one score, zero dependencies.

## Quick start

```bash
# a local skill folder
python tools/skill_scorecard.py path/to/skill/

# or a GitHub repo (downloaded to a temp dir, nothing installed)
python tools/skill_scorecard.py https://github.com/owner/some-skill
```

Output: a grade (A–D), a score out of 100, and the findings behind it. Exit code 1 on any FAIL — wire it into a pre-install hook.

## The seven checks

| check | what it asks |
|---|---|
| SKILL.md present | is this a skill folder at all? |
| YAML frontmatter | does it declare name + description? |
| SPEC contract | is behavior pinned to a falsifiable spec? |
| license | are usage terms stated? |
| scripts/ directory | does it ship code the agent may run? (~2x defect odds — read it first) |
| hidden content | opaque blobs that could be encoded payloads? |
| injection scan | known prompt-injection / exfiltration phrasing? |

## Honest boundaries

- It reads **structure**, not intent. Grade A means nothing suspicious was found — not that the skill is safe. A malicious skill with clean structure passes; that's what human review is for.
- The injection scan is a deniability-raising net, not a sandbox. Use it as a first filter, then read SKILL.md yourself (it's short on purpose).
- Repos that are skill *collections* (no root SKILL.md) are flagged with a pointer to their subfolders.

## License

MIT — use it, fork it, wire it into your installer. Attribution appreciated, not required.

<a href="https://github.com/chenhz01/skill-scorecard">canary: CANARY-ZS-SC-20260916-5A1E93D4</a>
