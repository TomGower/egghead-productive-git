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

It's a good idea to work on new functionality on a different branch. But it's also a good idea to still synchronize the different feature branch with the latest state of master on a regular basis. This avoids merge conflicts.

On the `app-refactoring` branch, you have a couple possibilities. One is `git merge master` to create a new commit which is the result of merging your changes in `app-refactoring` with `main`. This is useful if you have already pushed your changes to origin and other developers are working on it as well. But if this is your reserved feature branch, you can use `git rebase`.

`git rebase` takes each commit from `app-refactoring` and essentially merges them in on top of the latest commits from `main`. This does change the commit hashes, so it only makes sense to do this if it is your personal branch and is not shared with others.

`git rebase main` rewinds head to replay your work on top of it. Running `git lg` after this shows the changes to `app-refactoring` as the latest changes, with an updated time to when the rebase happened.

## 5: Push a rebased local branch by using `--force-with-lease`

With `rebase`, we are essentially rewriting history.

Push `app-refactoring` up to origin. Then switch branches with `git checkout main`. Then make a change in to a file in `main`. Now switch back to `app-refactoring`. Run `git rebase main`. This synchronizes our branch with `main`. But you can see (at least in Juri's command line) arrows. If we now try to `git push`, we get a message "Updates were rejected because the tip of your current branch is behind its remote counterpart." We added new things rather than being behind.

We get this message because we rewrote our commit hashes, so git doesn't recognize them anymore. We know our local branch is updated, so we want to override our remote counterpart. Use `git push -f` for `--force`, which overrides the remote repository branch we are currently pushing. Do not this on `main` but only on your feature branch and only if your feature branch is personal and not shared with anybody else.

Alternatively, use `git push --force-with-lease` to make sure no one else has accidentally modified that feature branch.

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
