---
name: single-file-guard
description: lock coding tasks to a single user-selected file. use when the user explicitly says to only view one file, only modify one file, or not look at other files. enforce single-file reading and editing, forbid project-wide browsing by default, allow requesting at most one additional file only when the current file is insufficient, and stop modification when key information is still missing. ideal for vibe coding, bounded code edits, local patches, and cases where the user values strict scope control over repository-wide optimization.
---

# Single File Guard

## Overview

Lock the task to the user-selected file.

Prefer strict scope control over repository-wide understanding. Treat the selected file as the only authorized source and the only authorized edit target unless the user explicitly approves one additional file after a focused request.

## Required behavior

Follow these rules in order:

1. Identify the selected file from the user's instruction.
2. Read only that file.
3. Analyze and edit only that file.
4. Refuse to browse the project, inspect unrelated files, search for references, or read configs, tests, imports, or neighboring modules unless the exception path below is required.
5. If the current file is sufficient, produce the modified full file content and a short modification note.
6. If the current file is insufficient, request exactly one additional file using the required template below.
7. If the user does not provide that file, stop modifying. Give only a conservative single-file option or explain why no safe modification can be completed.

## Scope rules

Interpret the user's boundary literally.

- Treat “only look at this file”, “only modify this file”, and “do not look at other files” as hard constraints.
- Treat the selected file as both the default read boundary and the default write boundary.
- Do not upgrade the task into repository exploration.
- Do not trade boundary compliance for engineering completeness.
- Prefer a local, conservative patch over a broader or cleaner multi-file solution.

## Forbidden actions

Do not do any of the following unless the user explicitly approves the single requested file:

- list or scan the project tree
- search across the repository
- inspect call sites, imports, configs, schemas, tests, or adjacent modules
- perform cross-file edits
- restructure the project or suggest a repository-wide refactor as the main answer
- guess hidden interfaces or behavior and continue editing as if they were confirmed

## Exception path: request one additional file

Request one additional file only when both conditions are true:

1. The current file does not contain enough information for a safe edit.
2. One specific file would materially reduce the uncertainty more than any other single file.

When requesting that file, output exactly this structure:

```text
Need one more file before I can safely continue.
Requested file: <path>
Why this file: <why this is the single most helpful file>
What remains uncertain without it: <specific interface, data shape, behavior, or contract>
```

Additional rules for this exception path:

- Request only one file.
- Do not request a directory, a glob, or a list.
- Do not continue browsing while waiting.
- Do not request a second file until the user responds to the first request.

## Decision rule after the request

If the user provides the requested file, continue with the same discipline and keep edits confined to the originally selected file unless the user explicitly expands the write scope.

If the user does not provide the requested file, stop modifying.

In that case, do one of the following:

- provide a conservative single-file revision that clearly avoids unverified assumptions, or
- explain that no safe change can be made from the current file alone

Never present an uncertain change as a confident fix.

## Output format

### When the selected file is sufficient

Respond in this order:

1. One sentence stating the edit goal.
2. The full modified file content.
3. A short modification note with no more than 3 bullets.

### When one more file is required

Use the exact request template in the exception path.

### When information remains insufficient

Respond in this order:

1. A direct statement that safe modification cannot continue.
2. A conservative single-file option if one exists.
3. A brief reason why the stronger change would require the requested file.

## Style rules

- Keep explanations brief and operational.
- Do not add repository-level advice unless the user later asks for it.
- Do not pad the answer with background theory.
- Stop as soon as the selected-file task is completed or safely blocked.

## Priority order

When tradeoffs appear, optimize in this order:

1. obey the user's file boundary
2. avoid unverified assumptions
3. complete a conservative local edit
4. improve elegance or architectural quality

## Example triggers

- “Only look at `src/foo.ts` and fix this bug.”
- “Modify this file only. Do not inspect the rest of the repo.”
- “Just patch `app/models/user.rb`. Do not browse other files.”
- “Only view this file first. Ask me for one more file if you truly need it.”
