# Single File Guard

A Codex skill that locks coding tasks to **one user-selected file**.

Use it when you want strict scope control: “only read this file”, “only modify this file”, “don’t browse the repo”, etc.

---

## What this skill enforces

- Identify the *selected file* from the user’s instruction.
- Read **only that file**.
- Edit **only that file**.
- Refuse project-wide browsing (tree listing, repo search, inspecting imports/configs/tests/adjacent modules) by default.
- If the current file is insufficient, request **exactly one** additional file (and stop if the user doesn’t provide it).

This is intentionally conservative: it prioritizes boundary compliance over “perfect” engineering completeness.

## Install

Put this folder under your Codex skills directory (commonly `~/.codex/skills/`), for example:

```bash
mkdir -p ~/.codex/skills
git clone git@github.com:WhitEiller/single-file-guard.git ~/.codex/skills/single-file-guard
```

Or using HTTPS:

```bash
git clone https://github.com/WhitEiller/single-file-guard.git ~/.codex/skills/single-file-guard
```

## Usage

In your Codex chat, explicitly invoke the skill (either works):

- `$single-file-guard`
- `single-file-guard`

Then give a strict single-file instruction, e.g.:

```text
只看 src/app.ts，只修改这个文件。不要浏览其它文件。
```

```text
Only look at src/app.ts and fix the bug. Only modify this file.
```

If the assistant truly cannot proceed safely, it will request one additional file using a fixed template. If you don’t provide that file, it must stop and avoid making unverified assumptions.

## Repo contents

- `SKILL.md`: the skill definition and enforcement rules
- `agents/openai.yaml`: UI metadata (display name / short description)

## License

No license file is included yet. Add one if you plan to share or reuse this broadly.
