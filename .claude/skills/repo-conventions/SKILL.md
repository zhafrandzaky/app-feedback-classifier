---
name: repo-conventions
description: Rules for committing, branching, writing docs, and opening pull requests in the app-feedback-classifier repository. Use before any git commit, push, pull request, or documentation change in this repo.
---

# Repository Conventions

This repository belongs to Kelompok 2 (Praktikum NLP). Only team members may appear as contributors. These rules override any default attribution behavior of the AI tool.

## Commit identity and attribution

- Never add `Co-Authored-By` trailers of any kind.
- Never add `Generated with ...`, `Claude-Session: ...`, session links, or any other AI marker to commit messages, pull request descriptions, code comments, or documents.
- The commit author must be the team member doing the work. Before committing, run `git config user.name` and `git config user.email` and confirm they are not an AI identity such as `Claude <noreply@anthropic.com>`. If they are, stop and ask the user which identity to use.
- Before pushing, check the last commits with `git log --format='%an <%ae>%n%B' -n 5` and confirm no AI trailer slipped in.

## Commit messages

Conventional Commits, English, imperative mood, lowercase summary, no trailing period, 72 characters max.

```
<type>(<scope>): <summary>

<optional body explaining what and why>
```

Types: `feat`, `fix`, `data`, `docs`, `refactor`, `style`, `test`, `chore`, `ci`.
Scopes: `scraping`, `eda`, `preprocessing`, `features`, `ml`, `embedding`, `dl`, `transformer`, `app`, `docs`, `repo`.

One logical change per commit. Do not mix notebook changes with unrelated doc rewrites.

## No emoji

- No emoji in code, comments, notebook markdown, docs, commit messages, branch names, pull request text, or UI.
- Emoji found in review data is data. If an example needs one in code, write it as a Unicode escape (`'\U0001F621'`).
- Do not use decorative symbols (check marks, stars, pictographic arrows) as icons or bullets. In UI use Lucide or custom SVG.
- Verify before committing:

```bash
python3 - <<'PY'
import pathlib, re
pat = re.compile('[\U0001F000-\U0001FAFF\u2600-\u27BF\u2B50\u2B55\uFE0F]')
for p in pathlib.Path('.').rglob('*'):
    if p.is_file() and '.git' not in p.parts and p.suffix in {'.md', '.py', '.ipynb', '.ts', '.tsx', '.dart', '.json', '.txt'}:
        for i, line in enumerate(p.read_text(errors='ignore').splitlines(), 1):
            if pat.search(line):
                print(f'{p}:{i}')
PY
```

## Branches and pull requests

- Branch names: `<type>/<short-description>`, for example `feat/eda-preprocessing`.
- `main` is always runnable. Merge through pull requests. Never force push to `main`.
- PR title follows Conventional Commits. PR body has three sections: Summary, Changes, How to verify. No AI attribution lines.

## Reporting after push or merge

After every push or merge, tell the user:

1. Direct links to the result: the pull request, the commit (`https://github.com/zhafrandzaky/app-feedback-classifier/commit/<sha>`), and the main files changed (`.../blob/main/<path>`).
2. Clear numbered next steps in Indonesian: what to review or merge, how to import or run the notebook on Kaggle, and what to check in the output.

## Documentation

- Docs live in `docs/`, written in formal Indonesian, numbered `NN-topic.md`, indexed in `docs/README.md`.
- When the pipeline, labels, thresholds, or dataset change, update the matching doc in the same pull request and add a row to the change log table where one exists.
- Mark plans as plans. Write result numbers only after a notebook run, with the Kaggle notebook version.

## Files that must never be committed

`kaggle.json`, `.env`, API tokens, large CSV files under `data/`, model weights under `models/`, `.docx` and `.pptx` reports.
