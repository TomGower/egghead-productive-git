# Productive Git for Developers

## 1: Only commit some of my currently modified files into the repository

`git status` shows files that have been changed.
`git add file1 file` will let you add multiple files to the commit. Can also use `git add src/app/app*` to add e.g. `app.js` and `app.ts` without typing out name of each file.
Now can just commit those files with `commit -m 'change App files'`.
Or just use `git add .` for all of them.
`git log` shows the commit history.

## 2: Make my git log look pretty and readable

`git log` gives us the overview of what is happening on the branch we are looking at. It shows us the hash of each particular commit, who committed it, when it was committed, and the commit message. But this is a lot of text, especially if there's been a lot of work in the particular repo.

What we can do instead is create an alias to present a nicer version of this. To create, a git alias, create a gitconfig file in the root alias of your user. `ls ~/user/.gitconfig`. Your `.gitconfig` might exist and have stuff in it already (mine has my name and email). Now create a new 'category' `[alias]` and a new alias `lg = log --color --graph --pretty=format:REALLY_LONG_CONFIG_STRING --abbrev-commit --branches`.
Color makes it easier to read.
Graph is useful if there are multiple branches, and it shows us when they were created and how they merge.
The pretty is to make it more readable.
Abbreviated commit is *something*.
Branches is *something*.

This gives us a nice-looking report of
`hash shorthand - (branch change, if any) commit message (time) <commit author>`

## 3: Move some commits to a separate branch that I have accidentally committed to master

There are a couple ways to do this.
Setup: commits have been made locally but have not yet been synced to the remote repository. If we have, this won't work.

Step one: create a new branch with `git branch -b app-refactoring`.
Step two: you're now still in the old branch and the new branch, so switch to just the old branch with `git checkout main`.
Step three: remote the commits from the old branch. Check `git lg` and find the hash of the last 'good' commit. Use `git reset --hard HASH` to reset those commits.
Step four: we're now done with our work in `main`, so switch to the new branch with `git checkout app-refactoring`.

Now `git lg` should show us the commits are still there and just on the new branch. We can now do what we need with the new branch (in his example, push them up for review).

## 4: Update my feature branch with the latest changes from master

a

## 5: Push a rebased local branch by using `--force-with-lease`

a

## 6: Polish my git feature branch before merging or submitting for review

a

## 7: Automate the cleanup of my feature branch with Git Autosquash

a

## 8: Squash all of my commits into a single one and merge into master

a

## 9: Change the commit message of my last commit

a

## 10: Add a file I've forgotten to add to my last commit

a

## 11: Undo my last commit and split it into two separate ones

a

## 12: Wipe a commit from my local branch

a

## 13: Undo a commit that has already been pushed to the remote repository

a

## 14: Temporarily store some work in progress because I have to jump to another branch

a
