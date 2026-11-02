---
name: requirement-git-mr-workflow
description: Prepare a normal requirement branch, commit, push, and MR across one or more independent repositories using the user's opt-<需求编号> convention.
argument-hint: "<需求编号> <需求标题> [iteration-branch]"
disable-model-invocation: true
allowed-tools: Read, Grep, Bash
---

# Requirement Git/MR workflow

## When to use

Use after the user explicitly authorizes implementation, commit/push, or MR creation for an ordinary requirement. Use `$0` as the requirement ID, `$1` as its concise title, and `$2` as the iteration branch when supplied.

Do not use for read-only analysis, emergency release repair, or when the repository/user specifies a different branch policy.

## Inputs and context

1. Identify every independent repository in scope and the requirement's target iteration branch for each.
2. Read the worktree status, current branch, configured remotes, and any repository-local rules.
3. Confirm the requested change and acceptance boundary. For large, multi-module, or unclear work, state impact and approach and obtain confirmation before editing.

## Procedure

1. Fetch the relevant remote and fast-forward the local iteration branch to its current remote state. Do not discard unrelated user changes.
2. Create `opt-$0` from the confirmed iteration branch. Immediately inspect `git branch -vv`; it must not track the iteration/release branch.
3. Implement only the approved scope. Keep non-deliverable analysis, planning, and personal notes outside the repository unless the user explicitly requests a versioned document.
4. Before committing, inspect the diff and run only user-authorized checks. Use commit message `opt-$0 $1`.
5. Push the requirement branch explicitly with `git push -u origin opt-$0`; recheck its upstream.
6. For each independent repository, create a separate MR from `opt-$0` to the confirmed iteration branch. Use the same title as the commit. The description format is `需求 URL 《需求完整标题》`, with only the URL linked. Set assignee to 聂显达, reviewer to 李立, and enable delete-source-branch plus squash-commits unless the user says otherwise.

## Efficiency plan

- Inventory all repositories and branch bases before changing any worktree; cache this mapping for the run.
- Use `git status --short`, `git branch -vv`, and `git log -1 --oneline` as fast stop checks.
- If a branch already exists, stop and compare its base/upstream rather than recreating it.

## Pitfalls and fixes

- Symptom: a normal push targets `release-*`. Cause: the new branch inherited the base branch's upstream. Fix: unset the upstream, then push with `-u origin opt-$0`; recheck `git branch -vv`.
- Symptom: same-named repositories or paths tempt bulk copying. Fix: treat each checkout as an independent release unit and verify its branch, diff, and MR separately.
- Symptom: unclear requirement has several plausible implementations. Fix: state the consequential choices and get a user decision before editing.

## Verification checklist

- Every repository has the confirmed iteration base and `opt-$0` tracking `origin/opt-$0`.
- `git status --short` contains only intended changes before commit.
- Commit/MR title and MR target follow the convention.
- MR description, assignee, reviewer, delete-source-branch, and squash settings match the procedure.
- Report unrun validation and any unresolved integration work honestly.
