# Aegis agent family (local tooling)

Lives only on branch `tooling/agent-family` of the fork `BigPaperBear/Aegis_SBR`. Never merged
into Torchlite-bit. Not part of the addon.

## Layout

| Path | What |
|---|---|
| `..\Aegis_SBR-agents\` | git worktree on `tooling/agent-family` — the tracked copy |
| `..\Aegis_SBR\.claude` | directory junction to the folder above, excluded via `.git/info/exclude`, so the setup is active on every branch of the dev folder |

## Use

- Turn the orchestrator on: `/output-style aegis-orchestrator`, or `/config` → **Output style**.
  Takes effect from the next message and combines with any permission mode. `default` turns it
  off. The choice is saved in `.claude/settings.local.json` (git-ignored).
- New or edited style/agent files are read at start-up: restart Claude Code after changing them.
- `/agents` lists the workers; `/adhd <problem>` runs the ADHD skill.

| Agent | Model / effort | Loads CLAUDE.md | Job |
|---|---|---|---|
| `aegis-scout` | Haiku 4.5 | no | find code / docs / changelog facts |
| `aegis-implementer` | Fable 5.1 / xhigh | yes | Lua changes from an exact spec |
| `aegis-reviewer` | Fable 5.1 / xhigh | yes | review a diff before commit / PR |
| `aegis-rotation-auditor` | Opus 5.5 / high | yes | Rule #1 report vs `docs/rotations.md` |
| `aegis-log-analyst` | Sonnet 5 / medium | no | `/sbr log`, probe, SavedVariables → tables |
| `aegis-release-clerk` | Haiku 4.5 | no | version ×3 + CHANGELOG entry |

No agent has the `Agent` tool, so none can spawn further agents.

## Committing changes to this setup

`/.claude` is in `.git/info/exclude`, which both worktrees share, so new files need `-f` and
explicit paths. Never `git add -f .claude` — that would pick up `settings.local.json`.

```bash
git -C ../Aegis_SBR-agents add -f .claude/agents .claude/output-styles .claude/skills .claude/README.md
git -C ../Aegis_SBR-agents commit -m "tooling: ..."
git -C ../Aegis_SBR-agents push
```

The branch tracks `fork/tooling/agent-family`, so a bare `git push` goes to the fork, never to
Torchlite-bit.

## ADHD skill

`skills/adhd/` is an unmodified copy of `skills/adhd/SKILL.md` and `LICENSE` (MIT) from
https://github.com/UditAkhourii/adhd at commit `dd08acc38693127cd0ca2325fe6bf9579131ede1`.
To update: download both files at a newer commit, read the diff, replace, update the SHA here.

## Removal

From the dev folder (`Aegis_SBR`):

```powershell
cmd /c rmdir .claude                    # removes only the junction
git worktree remove ..\Aegis_SBR-agents
```

Do not use `rm -rf` or `Remove-Item -Recurse` on the junction: they delete the target's files.
