## What is git cherry-pick?
git cherry-pick is a precision command used to *apply a specific commit from one branch onto another branch*. Unlike a merge or rebase, it allows you to selectively copy individual commits rather than the entire history.

## Common Use Cases
You should use cherry-pick when you need high precision and control:
* *Multi-branch Bug Fixes:* Apply the same fix to main and develop.
* *Feature Isolation:* Move a single feature commit without merging the full branch.
* *Work Recovery:* Rescue accidentally committed work.
* *Hotfixes:* Port critical fixes directly from develop to main.

## How It Works
* *New Identity:* Git copies the changes and replays them on the current branch, creating a *new commit with a unique hash*.
* *Authorship:* The original author's name is preserved, but you become the *committer*.
* *Prerequisite:* You must have the unique commit hash (SHA) of the work you want to copy.

## Basic Commands & Workflow

### 1. The Standard Process
| Step | Action | Command |
| :--- | :--- | :--- |
| *1* | Find the Commit Hash | git log |
| *2* | Switch to Target Branch | git checkout <target-branch> |
| *3* | Apply the Commit | git cherry-pick <commit-id> |

### 2. Picking Multiple Commits
* *Specific List:* git cherry-pick <commit1> <commit2> <commit3>
* *Range of Commits:* git cherry-pick <start-commit>^..<end-commit>

## Handling Conflicts
If Git cannot automatically apply the changes, it will trigger a merge conflict.

1. *Resolve:* Manually fix the conflicts in your editor.
2. *Stage:* Run git add ..
3. *Finish:* Run git cherry-pick --continue.
   * Note: Use git cherry-pick --abort to cancel the operation entirely.

## Best Practices
* *Keep it Small:* Cherry-pick small, isolated commits to reduce conflict risk.
* *Avoid Merges:* Try not to cherry-pick merge commits.
* *Author Changes:* Only change the author using git commit --amend --author="..." if specifically justified.

## Summary Table
| Feature | Behavior |
| :--- | :--- |
| *Content* | Same as original |
| *Commit ID* | *Different* (New hash created) |
| *Author* | Original author preserved |
| *Committer* | Person performing the cherry-pick |
