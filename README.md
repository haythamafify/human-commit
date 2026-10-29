# human-commit

An [OpenCode](https://opencode.ai) skill that keeps every git commit
human-authored: the human identity from git config as author **and** committer,
and a message with zero AI attribution — no `Co-Authored-By` trailers, no
`Generated with …` signatures, no bot emoji.

Works for normal commits, `--amend`, rebase rewords, merges, cherry-picks, and
squashes. The bundled checker doubles as a standalone tool or a `commit-msg`
hook, so the rules hold even when the commit is made outside the agent.

## Install

Global (all projects):

```sh
mkdir -p ~/.config/opencode/skills
git clone https://github.com/haythamafify/human-commit.git \
  ~/.config/opencode/skills/human-commit
```

Project-only:

```sh
mkdir -p .opencode/skills
git clone https://github.com/haythamafify/human-commit.git \
  .opencode/skills/human-commit
```

OpenCode picks it up automatically (V2 discovers `~/.config/opencode/skills`
and `.opencode/skills`); no `opencode.json` edit is needed. Verify by running
`git commit` with the agent — it will keep identity clean and run the checker
before pushing.

## Contents

```text
human-commit/
├── SKILL.md                       # the agent-facing rules and workflow
├── references/
│   └── commit-style.md            # human commit-message style guide
├── scripts/
│   ├── check-human-commit.sh      # read-only compliance checker
│   └── install-hook.sh            # optional per-repo commit-msg enforcement
├── README.md
└── LICENSE
```

## The checker

```sh
scripts/check-human-commit.sh [options] [<rev-or-range>]
```

| Option | Effect |
| ------ | ------ |
| *(none)* | checks `HEAD` |
| `<rev>` | checks one commit |
| `origin/main..HEAD` | checks every commit in the range |
| `--msg-file <file>` | checks a raw message file (hook mode) |
| `--quiet` | exit code only, no output |
| `--version`, `-h` | version / help |

Exit codes: `0` clean, `1` violation, `2` usage error. The checker is
read-only — it never rewrites anything.

Catches, in the author, committer, and message:

- `Co-Authored-By: Claude/ChatGPT/Copilot/Gemini/OpenCode/Cursor/Aider/Codex/…`
- Bot addresses: `@anthropic.com`, `@openai.com`, `<bot@…>`, `[bot]`
- `Generated with …` / `Made with …` / `Assisted by …` + a tool name
- Tool links: `opencode.ai`, `claude.ai`, `chatgpt.com`, `cursor.sh`, `aider.chat`
- Robot emoji and "AI-generated" wording

A human using `users.noreply.github.com` is not flagged.

## Optional hard enforcement

Behavioural skills guide the agent; a hook stops the commit itself:

```sh
scripts/install-hook.sh --dry-run   # preview first
scripts/install-hook.sh             # install into this repo's hooks
scripts/install-hook.sh --remove    # uninstall (restores any saved hook)
```

The hook calls the checker in `--msg-file` mode, so every commit in that
repository is validated at commit time, regardless of who commits.

## Rules the skill enforces

1. Identity comes from `git config` only — the agent never overrides it.
2. No attribution trailers, signatures, or tool links in messages.
3. Messages describe the change, in imperative style — never mention AI.
4. Git config is never modified; a missing identity stops and asks.
5. Push only when asked; no history rewrites without explicit consent.

## Compatibility

Written for OpenCode V2 (`~/.config/opencode/skills/<id>/SKILL.md`). The
layout also matches the shared `.claude/skills` / `.agents/skills` conventions,
so the same directory can be symlinked for other agents.

## License

[MIT](LICENSE)
