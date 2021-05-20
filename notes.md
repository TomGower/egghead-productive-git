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

General best practice: commit as often as possible in case something bad happens.
Effect of using this best practice: if working on a separate feature branch, your colleagues would have to review multiple interstitial commits instead of just the ultimate changes they should be judging.

So... it's a good idea to clean up your feature branch before merging with `main`. To do this, copy the hash of the last 'good' commit and use `git rebase -i HASH`. `-i` is for interactive.

Now we get a list of the commits that have happened from that hash onward. From here you can do several things. Use `pick` to keep a commit. Use `r` to reword a commit. Use `f` to keep a commit's code changes but discard the log message.

Once we close that file, we go into interactive rebasing and can change the wording of the commit we chose `r` to reword. Once we change that and exit, the rebase will be done and executed.

## 7: Automate the cleanup of my feature branch with Git Autosquash

If we don't want to individually polish the frequent commits in our feature branch, as in #6, then we can automate the steps so that all subsequent commits "smush" (my word, not the technical term) into the first commit. To make subsequent commits "smush" into that commit, get the hash of that commit, then after `git add .` on a subsequent commit, use `git commit --fixup HASH`.

In `git lg`, this will be shown as `fixup! top_commit_message`. For a subsequent commit to be smushed, you can use `git commit --fixup :/update` to search for language in the commit message ('update README' is Juri's example commit message). In `git lg`, this will be shown as `fixup! fixup! top_commit_message`.

You can then make a separate change to a separate file, use `git add` and `git commit -m 'Some other change'`, and go back to working on previous commit. You can then use `git commit --fixedup :/update` to add it to the same history to be "smushed".

When you're done, you can use `git rebase -i --autosquash HASH`, where HASH is the commit before you started making changes (not the commit whose message everything should be squashed into, but a commit before that one). In the `git rebase` message screen, the commits to be "smushed" (fixup) are displayed after the commit they will be "smushed" into, even if a purely temporal list of commits would be different.

If we run `git lg` after doing this, we see our history has been cleaned up, with just the two (in our example) commits, and the "smushed" commits no longer visible.

## 8: Squash all of my commits into a single one and merge into master

If we finish working on feature branch and want to merge it all into main in one commit, then use `git checkout main` to switch to main and run `git merge --squash app-refactoring`.

After we run this, `git lg` doesn't show anything. If we run `git commit`, we see "Squashed commit of the following: [details]". We can then edit that message on the top line to read what we want it to say (e.g., "add app-refactoring"). Once we save and exit, if we run `git lg` again, we see that is the latest commit. We can see from the log that commit was not actually in the feature branch but instead was a commit to `main`.

## 9: Change the commit message of my last commit

Imagine a commit `git commit -am 'add credit section to readme'`, but we want to change the commit message to fix a typo or better describe what we did or to match some sort of naming convention. Use `git commit --amend`. This opens up editing mode, and we can change the commit message to whatever we want. Running `git lg` shows the commit has been renamed.

N.B. we can do this when we have not already synced the changes with the remote repository. This changes the id of the commit, and so would break the merges with other people.

## 10: Add a file I've forgotten to add to my last commit

If a file has been modified and you want to add it to the last commit, use `git add fileName`. If you then use `git status`, you should see that the file is to be committed. You can then use `git commit --amend`. This opens up the editor, and the file that you added will be listed among the "Changes to be committed:" You can use `git lg` to show that there is no new commit, and `git status` to confirm that the modified file is not longer listed among the files to be committed.

## 11: Undo my last commit and split it into two separate ones

Scenario: `git add file1 file2`, `git commit`, commit has not yet been pushed to master. To just undo the last commit, use `git reset HEAD~`. `git status` will show the files as not yet committed. The last commit is also removed from `git lg`. You can now `git add file1`, `git commit -m 'first commit'`, and commit the first file, then `git add file2` and `git commit -m 'second commit'` to commit the second file, like you meant to do. Using `git lg` will now show these as separate commits.

## 12: Wipe a commit from my local branch

Key point: you can return up to the point of the last sync with the remote repository, but cannot sync with a state before that.

Use `git reset --hard HASH` to return to the commit at HASH. Any commits after that point have been discarded, as you can see in `git lg`. Using `--hard` reverts the same folder status as that commit, so any changes you have made in your local workspace will have been discarded.

## 13: Undo a commit that has already been pushed to the remote repository

a

## 14: Temporarily store some work in progress because I have to jump to another branch

a
