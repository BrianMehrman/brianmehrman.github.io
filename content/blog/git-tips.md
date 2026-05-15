---
title: "Git Tips"
date: 2018-02-06T00:00:00-05:00
tags: []
aliases:
  - /blog/2018/02/06/git-tips/
---

# Git

Git is a free version control system designed for both small and large projects.

# Tips

## Rebase

Whenever I am working on a project with other developers I find that I need to rebase
my feature branch with the common branch the rest of the team is using. This branch
is usually called `dev` or `ci`.

```
$ git fetch
```

To make sure I have the latest branches I run a `fetch` to update all my local branches.

```
$ git rebase -i origin/dev my_branch
```

Rebase can work on `local` branches as well as `remote` branches. In the above example
I rebased my branch on the `remote` version of `dev`.
The `-i` flag stands for interactive. This allows me to check the commits that I
am rebasing onto the dev branch.

## Reset

### HEAD

```
$ git reset HEAD
```

This command will reset my current branch to whatever the latest commit is. This
is a great way to clean up a branch and start from where you last committed.

```
$ git reset HEAD~
```

The `~` (tilde) character next to `HEAD` tells git to go back 1 commit. I use this
command to remove bad commits. Resetting the commit will unstage the files from
the previous commit.

## Delete Branches

```
git branch -d my_branch
```

This will delete the branch named `my_branch`, only if the branch has been merged.

```
git branch -D my_branch
```

This will delete the branch even if the branch has not been merged.

```
git branch --list
```

Lists out all the branches that are local to your machine.

```
git branch -D $(git branch --list hot*)
```

This deletes all branches prefixed with hot