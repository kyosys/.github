# KyoSys Git Workflow

Use this process for all KyoSys repository changes.

## 1. Start with an Issue

Create or identify the GitHub Issue for the work before making changes.

Examples:

- Feature
- Bug
- Architecture Change
- Security Task
- Documentation Change

Record the Issue number, for example `#12`.

---

## 2. Sync `main`

Always start from the latest version of `main`.

```powershell
git switch main
git pull origin main
git status
```

Expected:

```text
On branch main
Your branch is up to date with 'origin/main'.
nothing to commit, working tree clean
```

---

## 3. Create a Branch

Never develop directly on `main`.

Use:

```text
feature/<issue>-<description>
fix/<issue>-<description>
security/<issue>-<description>
docs/<issue>-<description>
test/<issue>-<description>
```

Example:

```powershell
git switch -c docs/12-deployment-architecture
```

Verify:

```powershell
git branch --show-current
```

---

## 4. Make and Review Changes

Make the required changes only within the scope of the Issue.

Check your work:

```powershell
git status
git diff
git diff --check
```

No output from `git diff --check` means the check passed.

---

## 5. Stage and Commit

Stage the files related to the Issue:

```powershell
git add <files>
```

Or a directory:

```powershell
git add adr/
```

Review the staged changes:

```powershell
git diff --cached
git diff --cached --check
```

Use clear Conventional Commit-style messages:

```text
feat(registry): add snapshot retrieval
fix(holder): handle registration failure
security(verifier): reject consumed challenge
docs(architecture): add deployment architecture
test(crypto): add invalid proof tests
```

Commit:

```powershell
git commit -m "docs(architecture): add deployment architecture"
```

---

## 6. Push the Branch

For the first push:

```powershell
git push -u origin <branch-name>
```

Example:

```powershell
git push -u origin docs/12-deployment-architecture
```

For later updates:

```powershell
git push
```

---

## 7. Open a Pull Request

Create a Pull Request using:

```text
base: main
compare: your branch
```

Use a clear PR title, for example:

```text
docs(architecture): add deployment architecture
```

Link the controlling Issue in the PR description:

```text
Closes #12
```

Complete the KyoSys Pull Request template and resolve all review comments before merge.

---

## 8. Merge

KyoSys uses:

**Squash and merge**

Do not use:

- Merge commit
- Rebase and merge

This keeps `main` linear and gives each completed Pull Request one clean commit.

---

## 9. Update and Clean Up

After the Pull Request is merged:

```powershell
git switch main
git pull origin main
git branch -d <branch-name>
git fetch --prune
```

If the remote branch still exists:

```powershell
git push origin --delete <branch-name>
```

Final check:

```powershell
git status
git branch -a
```

The repository should normally return to:

```text
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
```

> **Note:** Because KyoSys uses squash merging, Git may warn that the old branch has not been directly merged into `HEAD`. This is expected because GitHub creates a new squash commit on `main`. Confirm the changes are present on `main` before deleting the old branch.

---

# Repository Controls

| Control | KyoSys Policy |
|---|---|
| Direct development on `main` | Blocked |
| Pull Requests | Required |
| Squash merge | Required |
| Merge commits | Disabled |
| Rebase merging | Disabled |
| Linear history | Required |
| Force pushes | Blocked |
| Branch deletion | Restricted |
| Review conversations | Must be resolved |
| Code Owner reviews | Enabled when sufficient reviewers exist |
| Required approvals | Enabled when sufficient reviewers exist |
| CI status checks | Required once CI is implemented |

---

# Standard Workflow

```text
Issue
  ↓
Sync main
  ↓
Create branch
  ↓
Make changes
  ↓
Review / Test
  ↓
Stage
  ↓
Commit
  ↓
Push
  ↓
Pull Request
  ↓
Review
  ↓
Squash and Merge
  ↓
main
  ↓
Issue Closed
  ↓
Branch Cleanup
```

## Rules to Remember

1. Never develop directly on `main`.
2. Start work from a GitHub Issue.
3. Create a dedicated branch for the Issue.
4. Keep the change within the Issue scope.
5. Review and test changes before committing.
6. Never commit secrets, credentials, private keys, or real identity data.
7. All changes go through a Pull Request.
8. Resolve review findings before merge.
9. Use **Squash and merge only**.
10. Pull the updated `main` and clean up completed branches after merge.