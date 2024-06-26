# How to Undo or Revert a Local Commit in Git?

I recently made a commit with git and realized I committed the wrong files, or the commit was a mistake. I haven't pushed the commit to the server yet. How can I undo or revert this local commit in Git? What are the commands needed to reset it, and can I keep the changes I made to the files?

---

# Undoing Commits in Git

Undoing a commit in Git can be approached differently depending on whether the commit is local or has been made public. Here are four ways to undo a local commit:

### Option 1: `git reset --hard`
If you want to completely discard the last commit and all changes in your working directory, you can use:

```shell
git reset --hard HEAD~1
```

This command resets your HEAD to the previous commit and changes your files to their state at that commit.

### Option 2: `git reset`
If the last commit was only slightly off and you wish to keep your changes for further editing, use:

```shell
git reset HEAD~1
```

Your HEAD will move back one commit, but your files will remain as they were. The changes will be staged again for commit.

### Option 3: `git reset --soft`
To undo your commit but leave your files and staging index intact, you can use:

```shell
git reset --soft HEAD~1
```

This command will leave your working directory and index unchanged. You can re-commit immediately with the same changes.

### Option 4: Recovering a commit after `git reset --hard`
Sometimes after performing a `git reset --hard`, you might realize that you need to recover the commit you've discarded. Fortunately, Git provides a safety net for this situation with the reflog:

1. Use `git reflog` to find the commit you've removed. It will show a list of recent changes to the HEAD, branches, and other references in your repository.

```shell
git reflog
```

2. Once you've identified the commit you want to recover, create a new branch starting from that commit:

```shell
git checkout -b someNewBranchName SHA_OF_DELETED_COMMIT
```

Commits in Git do not get destroyed right away. They linger in the reflog for some time (default is 30 days for objects unreferenced by any branch or tag), which allows recovery of commits that were lost due to a hard reset.

**Public Commit Reversion:**

In contrast, if you've already pushed a commit to a remote repository, it's best to revert the changes in a way that preserves the integrity of the project's history—especially in collaborative environments:

### Undoing a Public Commit with `git revert`
1. Use `git revert` to create a new commit that reverses the changes made by the last commit without altering the project history:

```shell
git revert HEAD
```

2. Commit the reverted changes:

```shell
git commit -m 'Restore files removed by accident'
```

`git revert` is safe for public commits because it doesn't rewrite history, and it doesn't affect the work of other collaborators. It essentially "undos" your changes with a new commit.

These methods are crucial for effective version control management, ensuring that you can undo changes as needed without losing work or disrupting the workflow of your collaborators.

So far, we've discussed how to undo local commits and recover after a `git reset --hard`, as well as how to revert a public commit. In this part, we'll explore additional considerations:

## Undoing Multiple Local Commits:
If you need to undo multiple commits, you can:

1. Identify how many commits (n) you want to undo.

2. Use `git reset --hard HEAD~n` to discard all changes, or `git reset --soft HEAD~n` to keep the changes for re-committing.

3. For example, if you want to undo the last 3 commits and keep the changes, use:

```shell
git reset --soft HEAD~3
```

Your HEAD pointer moves back, but your changes will stay staged (index) and in your working directory.

## Editing Previous Commit:
If you only need to correct the very last commit, you may choose to edit it using:

```shell
git commit --amend
```

This command allows you to alter the last commit made either by adjusting the commit message or by adding new changes that were not initially staged.

**Note**: Using `--amend` on publicly shared commits should generally be avoided because it modifies the commit history, which can create issues for other collaborators.

## Understanding the Implications of History Rewriting:
Rewriting history with commands like `git reset` and `git commit --amend` has implications, especially for public commits. Always be cautious as these can cause problems for anyone who has based their work on the commits you're altering. Prefer to use `--force-with-lease` instead of `--force` when pushing rewritten history to a remote repository:

```shell
git push origin main --force-with-lease
```

This command provides an extra level of safety, ensuring you do not overwrite someone else's work on the remote. However, it's still a disruptive operation and should be used sparingly and with the full knowledge and agreement of your team.
