# Auto Commit And Push Prompt

Use this prompt when you want to stage all changes, generate a commit message automatically, commit, and push.

## Prompt

Stage all repository changes with git add -A. If there are no staged changes, stop and report that there is nothing to commit. Otherwise, generate a concise commit message based on the staged diff summary and changed files, commit the changes, then push the current branch to origin.

## Safe Command Template

```bash
set -euo pipefail

git add -A

if git diff --cached --quiet; then
  echo "No staged changes to commit."
  exit 0
fi

branch="$(git rev-parse --abbrev-ref HEAD)"
count="$(git diff --cached --name-only | wc -l | tr -d ' ')"
stats="$(git diff --cached --shortstat | sed 's/^ *//')"
files="$(git diff --cached --name-only | head -5 | paste -sd ', ' -)"

if [[ "$count" -gt 5 ]]; then
  files="${files}, ..."
fi

msg="chore: auto-commit ${count} file(s)"

git commit -m "$msg" -m "Files: ${files}" -m "Stats: ${stats}"
git push origin "$branch"
```
