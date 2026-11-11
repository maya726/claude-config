---
name: git-workflow
description: Operate git and GitHub on behalf of a designer who does not use git directly — branches, commits, pull requests, CI checks, merging after CI passes, conflicts, and undoing mistakes safely. Use when starting work in a repository, saving or uploading work, or when the user mentions 저장, 커밋, 올려줘, 푸시, PR, 머지, 충돌, 되돌려, 깃, 깃허브.
allowed-tools: Bash
metadata:
  reviewed: 2026-11-10
---

# Git Workflow

The user does not run git themselves. You run it and report the outcome in plain Korean.
A repository's `CLAUDE.md` overrides this file on the base branch, merge method and required
checks. No reviewer is required unless the repository says so.

## Never

Do not run these. If one looks necessary, stop, explain why, and ask.

- Commit directly on the default branch.
- `git push --force` or `--force-with-lease`.
- `git reset --hard`, `git clean -f`, `git checkout .`, `git restore .`.
- `git rebase` on a pushed branch.
- `git stash`. Save unfinished work as a commit (`chore: 작업 중`) on the current branch.
- `git add -A` or `git add .`. Name the files.
- `--no-verify`, or `gh pr merge --admin`.
- `git tag`, `gh release`, or editing a version number, unless the repository allows it and
  the user asks.

## Procedure

### 1. Start

```bash
git status
git switch <base> && git pull --ff-only
git switch -c <type>/<short-english-description>
```

- If `git status` shows uncommitted work, ask whether it belongs to this task before moving.
- Base branch: the repository's `CLAUDE.md`, else
  `gh repo view --json defaultBranchRef -q .defaultBranchRef.name`.
- Branch types are the commit types below. Example: `fix/upload-button-height`.

### 2. Commit

Run `git status` and `git diff`, then stage named files. Do not commit secrets, `.env` files,
certificates, or real personal data; if one appears, stop and tell the user.

Format: `<type>(<scope>): <description>`. The description may be Korean.

| Type | Use |
|---|---|
| `feat` | Something new appears or the look changes on purpose |
| `fix` | Something was wrong and now works or looks right |
| `docs` | Documentation only |
| `chore` | Everything else, including work-in-progress saves |

CSS and markup changes are `feat(ui)` or `fix(ui)`. Do not use `style`, which in Conventional
Commits means whitespace-only formatting.

Example: `fix(ui): 업로드 단추 높이를 맞춘다`

### 3. Push and open a pull request

```bash
git push -u origin <branch>
gh pr create --base <base> --title "<commit format>" --body "<body>"
```

- Body, in Korean: what changed and why; what you checked; before and after screenshots when
  the screen changed.
- Take the screenshots yourself with a headless browser and save them outside the repository,
  in `~/pr-screenshots/<branch>/`.
- `gh` cannot attach images. Give the user the folder and the PR link, and ask them to drag the
  screenshots into a comment on the PR page.
- Before they upload, remind them that screenshots must not show real personal data.

### 4. Wait for checks

```bash
gh pr checks <number> --watch
```

On failure, read `gh run view <run-id> --log-failed`, explain the cause, fix it on the same
branch, and push a new commit. If it fails again and you do not understand why, stop and ask.

### 5. Merge

Merge only when both are true: every check passed, and the user said to merge in this
conversation. Complete the repository's pre-merge steps first.

```bash
gh pr merge <number> --merge --delete-branch
git switch <base> && git pull --ff-only
```

If GitHub refuses the merge, report the reason. Do not work around it.

### 6. Bring in others' changes

```bash
git fetch origin
git merge origin/<base>
```

Use merge, not rebase, so a force push is never needed. If a push is rejected as
non-fast-forward, run `git pull --no-rebase`, then push again.

## Conflicts

Stop editing. Run `git status`, name the conflicted files, and explain in plain words what each
side changed. Propose a result and get the user's choice when the two sides disagree about how
something should look or behave. `git merge --abort` returns to the state before the merge.

## Undoing

| Situation | Action |
|---|---|
| Not committed | Show what will be lost, confirm, then `git restore <file>` per file |
| Committed, not pushed | `git reset --soft HEAD~1` keeps the changes in the working tree |
| Pushed, not merged | Add a new commit, or `git revert <commit>` |
| Merged | New branch `fix/revert-<topic>`, `git revert -m 1 <merge-commit>`, then a normal PR |

## Common errors

- `Permission denied (publickey)`: the SSH key is not set up. Check with `ssh -T git@github.com`.
- `Repository not found`: the account has no access to that repository. Ask the owner.
- `gh: not logged in`: run `gh auth login --hostname github.com --git-protocol https --web`
  yourself, give the user the one-time code it prints, and have them approve in the browser.
- `Your local changes would be overwritten`: commit them first, then repeat the command.

## Checklist

- [ ] On a branch, not the default branch.
- [ ] Only the intended files were committed.
- [ ] The changed screen was opened and looked at.
- [ ] Checks passed before merging, and the user asked for the merge.
- [ ] Told the user the result and the PR link.
