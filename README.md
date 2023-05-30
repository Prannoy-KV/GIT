### Install Git lens for VS code

Search for 'eamodio.gitlens' and install

<br/>

### Download git

https://git-scm.com/downloads
<br/>

### To check version

```
git version
```

---

# Initial Settings

### Global Settings

|                                                                     Command | Description                                                                                                            |
| --------------------------------------------------------------------------: | ---------------------------------------------------------------------------------------------------------------------- |
|                                  git config --global user.name "Prannoy KV" | To configure user name                                                                                                 |
|                    git config --global user.email "k.vprannoydev@gmail.com" | To configure user email                                                                                                |
|                               git config --global core.editor "code --wait" | Configure VS code as default editor and wait till the current opened files are closed                                  |
|                                      git config --global core.autocrlf true | Setting line ending for windows (Mac and Windows follows different format so it's required)                            |
|                                                      git config --global -e | To edit all global settings in a default editor                                                                        |
|                                        git config --global diff.tool vscode | Just give a name to default diff tool                                                                                  |
| git config --global difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE" | Inform git how VS code should open as a diff tool <br/> $LOCAL and $REMOTE are placeholders for old and new file names |

**Note :- Sometimes $LOCAL and $REMOTE might not be set. To add manually open config file and edit using 'git config --global -e'**
It should look something like :-

```
[difftool "vscode"]
	cmd = code --wait --diff $LOCAL $REMOTE
```

<br/>

- If there is space in username or email we need to use quotes around the values.
- In autocrlf, CRLF stands for 'Carriage Return Line Feed'.
- All the above settings will be stored in .gitconfig file
- '--global' is used to set global settings
- If we need a colorful terminal we can install 'posh-git' for windows, 'zsh with git plugin' for Mac

<br/>

---

# Creating Snapshots

### Initializing a repository

```
git init
```

### Staging files

```
git add file1.js                # Stages a single file
git add file1.js file2.js       # Stages multiple files
git add *.js                    # Stages with a pattern
git add .                       # Stages the current directory and all its content
```

### Skipping the staging area

```
git commit -am "Message"
```

### Committing the staged files

```
git commit -m "Message"     # Commits with a one-line message
git commit                  # Opens the default editor to type a long message
```

### To view files in the staging area

```
git ls-files
```

### Viewing the status

```
git status          # Full status
git status -s       # Short status
```

### To ignore files

Create a file called '.gitignore' at the root of the project and add files or directories or patterns.<br/>
To ignore a file which is already committed to the repo, we need to remove the file from staging area and commit it

```
git rm --cached file1.js    # Removes from staging area only
git rm --cached -r bin/     # Removes entire folder from staging area only
```

Templates of .gitignore for various languages :- https://github.com/github/gitignore

### Viewing the history

```
git log                 			# Full history
git log --oneline       			# Summary
git log --reverse       			# Lists the commits from the oldest to the newest
git log --oneline --all				# To view all changes across all branches
git log --oneline --all --graph		# To view branching and merging in a better way
```

### Unstaging files (undoing git add)

```
git restore --staged file.js
```

### Discarding local changes

```
git restore file.js                 # Copies file.js from index to working directory
git restore file1.js file2.js       # Restores multiple files in working directory
git restore .                       # Discards all local changes (except untracked files)
git clean -fd                       # Removes all untracked files
```

### Restoring an earlier version of a file

```
git restore --source=HEAD~2 file.js
```

---

# Browsing History

### Viewing the history

```
git log --stat              # Shows the log along with list of modified files
git log --oneline --stat    # Shows the log in oneline with list of modified files
git log --patch             # Shows the actual changes made (patches)
```

### Filtering the history

```
git log -3                          # Shows the last 3 entries
git log --author="Mosh"             # Shows all the commits made by an author
git log --before="2020-08-17"       # Shows all the commits before a particular date
git log --after="one week ago"      # We can even use relative date
git log --grep="GUI"                # Commits with "GUI" in their message. It is case-sensitive
git log -S"GUI"                     # Commits with "GUI" in their changes (patches)
git log hash1..hash2                # Range of commits, to get all the commits made between any two commits
git log file.txt                    # Commits that touched file.txt
```

### Viewing a commit

```
git show 921a2ff        # Shows the given commit
git show HEAD           # Shows the last commit
git show HEAD~2         # Two steps before the last commit
git show HEAD:file.js   # Shows the version of file.js stored in the last commit
```

### Comparing commits

```
git diff HEAD~2 HEAD 				# Shows all the changes made between any two commits
git diff HEAD~2 HEAD file.txt 		# Changes to file.txt only made between any two commits
```

### Checking out a commit

```
git checkout dad47ed 	# Checks out the given commit
git checkout master 	# Puts the HEAD pointer back to the original state
```

After checking out a commit, we will see log from only that commit, to see all the commits, use flag '--all'

```
git log --oneline --all
```

### Finding a bad commit

```
git bisect start			# Starts the bisect session
git bisect bad 				# Marks the current commit as a bad commit
git bisect good ca49180 	# Marks the given commit as a good commit
git bisect reset 			# Terminates the bisect session
```

To bisect we need to know in which version application was working fine, in this case ca49180.
This means issue was introduced somewhere between current commit and commit with hash ca49180.

After 'git bisect good ca49180', HEAD pointer will be moves to some commit between current and ca49180 (use `git log --oneline --all` to verify). Test if bug exists on this commit.
Current commit will be marked as 'refs/bisect/bad' and ca49180 will be marked as 'refs/bisect/good'

If it is a good commit, mark it as good commit using

```
git bisect good
```

If it is a bad commit, mark it as bad commit using

```
git bisect bad
```

By marking commits as good and bad we move pointers 'refs/bisect/good' and 'refs/bisect/bad' as well, to narrow the search

Repeat the steps till git provides us the information about a particular commit in detail, which will be the root cause of the issue

Once we are done, use `git bisect reset`

### Finding contributors

```
git shortlog
```

### Viewing the history of a file

```
git log file.txt 				# Shows the commits that touched file.txt
git log --stat file.txt 		# Shows statistics (the number of changes) for file.txt
git log --patch file.txt 		# Shows the patches (changes) applied to file.txt
```

If we get an error about ambiguous argument while passing file name, use '--' before file name `git log -- file.txt`

### Restoring a deleted file

```
git checkout ca49180 toc.txt	# Restore it from some previous version and commit
```

### Finding the author of lines

```
git blame file.txt 					# Shows the author of each line in file.txt
git blame -e file.txt 				# Shows the author's email instead of name for each line in file.txt
git blame -e -L 1,3 file.txt 		# To see details of first 3 lines in file.txt
```

### Tagging

```
git tag v1.0 							# Tags the last commit as v1.0
git tag v1.0 5e7a828 					# Tags an earlier commit
git tag 								# Lists all the tags
git tag -a v1.1 -m "My version 1.1" 	# To annotate a tag with a message
git tag -n								# To see messages along with tags (Uses message from annotated tag, if provided or a commit msg)
git tag -d v1.0 						# Deletes the given tag
git checkout v1.0						# To checkout a commit using it's tag
```

# Branching & Merging

### Managing branches

```
git branch bugfix 						# Creates a new branch called bugfix
git checkout bugfix 					# Switches to the bugfix branch
git switch bugfix 						# Same as the above
git switch -C bugfix 					# Creates and switches
git switch -C bugFix origin/bugFix		# Create a local branch based on some origin branch
git branch -m bugFix signupBugFix		# Renaming a branch
git branch -d bugfix 					# Deletes the bugfix branch
git branch -D bugfix 					# Force Deletes the bugfix branch
```

### Comparing branches

```
git log master..bugfix 			# Lists the commits in the bugfix branch not in master
git diff master..bugfix 		# Shows the summary of changes
```

### Stashing

```
git stash push -m "New tax rules" 			# Creates a new stash
git stash push -all							# To push even untracked files to stash,  is shortcut
git stash push -a							# Shortcut for git stash push -all
git stash push -am "New tax rules"			# Push even untracked files with a message
git stash list 								# Lists all the stashes
git stash show stash@{1} 					# Shows the given stash
git stash show 1 							# shortcut for stash@{1}
git stash apply 1 							# Applies the given stash to the working dir
git stash drop 1 							# Deletes the given stash
git stash clear 							# Deletes all the stashes
```

### Merging

```
git merge bugfix 				# Merges the bugfix branch into the current branch
git merge --no-ff bugfix 		# Creates a merge commit even if FF is possible
git merge --squash bugfix 		# Performs a squash merge
								  (Doesn't include all the commit history from bugfix,
								  but rather combines all the commits in bugfix to a single commit and moves to staging area,
								  we need to give a message and then commit these changes)
git merge --abort 				# Aborts the merge
```

### Viewing the merged branches

```
git branch --merged 		# Shows the merged branches (Note:- Doesn't show branches on which we have done squash merge)
git branch --no-merged 		# Shows the unmerged branches (Note:- Shows branches on which we have done squash merges as well)
```

**Note:- Try not to add any new code, while trying to resolve conflicts and merging, as much as possible.**

### Merge Tool

Install p4merge from perforce site :- https://www.perforce.com/downloads/visual-merge-tool

## Configuring P4merge as the default merge tool:-

```
git config --global merge.tool p4merge													# Give a name to merge tool
git config --global mergetool.p4merge.path "C:/Program Files/Perforce/p4merge.exe"		# Specify where to find this p4merge
git config --global mergetool.keepBackup false											# To disable mergetool creating backup files
```

## To merge changes using default mergetool which we configured above

```
git mergetool
```

### To abort a merge conflict

```
git merge --abort			# Takes code to the original state. (Before starting to merge)
```

### Reverting a commit

## Undoing commits (Use only when code is not yet pushed to remote repo)

```
git reset --hard HEAD~1		# Undoing a last commit, To reset HEAD to last commit
git reset --hard 5e7a828	# To reset HEAD to some commit
```

## Reverting commits

```
git revert -m 1 HEAD	# Revert current commit(HEAD) to the last commit on the master branch(1), provide a commit msg after this command
```

### Rebasing

When we create a branch from master or any other branch, it will use the current commit as a base and creates a branch.
If we make some additional commits on this new and base branch our branches would have deviated.
To make it linear, we need to changes this base.

**NOTE:- Do not use this if the code is commited to a central repo**

```
git rebase master 		# Changes the base of the current branch (We should be on the new branch we are working to rebase the tip of master)
git rebase --continue	# If there are conflicts resolve it and then continue rebasing
git rebase --abort		# To abort rebasing
```

### Cherry picking

```
git cherry-pick dad47ed 							# Applies the given commit on the current branch
git restore --source=bugFixBranch toc.txt			# To cherry pick changes from a different branch
git restore --source=bugFixBranch -- toc.txt		# If git is unable to interpret file name correctly.
```

---

## Collaboration

### Cloning a repository

```
git clone url
git clone url aliasName			# To give a different name to a project
```

### Managing remotes

```
git remote 						# Shows remote repos
git remote -v 					# Shows more details about remote repos
git remote add upstream url 	# Adds a new remote called upstream
git remote rm upstream 			# Remotes upstream
```

Note:- when we do 'git log --oneline' we get output like '7a61fde (HEAD -> master, origin/master, origin/HEAD) commitMsg<br/>.
origin/master is the pointer to remote branch
origin/HEAD is the HEAD pointer on remote branch

### Syncing with remotes

To store credentials to push our code, instead of giving username and password everytime we can go to link:-<br>
https://github.com/microsoft/Git-Credential-Manager-for-Windows and download the installer and follow instructions on this page

```
git fetch origin master 		# Fetches master from origin
git fetch origin 				# Fetches all objects from origin
git fetch 						# Shortcut for "git fetch origin"
git merge origin/master			# To merge changes from remote master into local master
git pull 						# Fetch + merge
git pull --rebase				# To rebase our changes on top of origin master
git push origin master 			# Pushes master to origin
git push 						# Shortcut for "git push origin master"
```

**Note:- We should never use force push `git push -f`, unless we have screwed up something.<br/>**
**Force push will cause others changes to be dropped. Instead we need to do a `git pull` and then merge or rebase the changes, solve conflicts and then push**

### Sharing branches

```
git branch -r 					# Shows remote tracking branches
git branch -vv 					# Shows local & remote tracking branches and how both are diverged
git push -u origin bugfix 		# Pushes bugfix to origin
git push -d origin bugfix 		# Removes bugfix from origin
git remote prune origin			# To remove remote tracking branches that are deleted on remote
```

### Sharing tags

By default push command will not transfer our tags to remote repository. We have to explicitly push them

```
git push origin v1.0 				# Pushes tag v1.0 to origin
git push origin —delete v1.0		# Deleted tag v1.0 on origin
```

---

## Rewriting History ( Whenever we re-write history, commit hash changes )

### Undoing commits ( Do not use this when code is published to others)

```
git reset --soft HEAD^ 			# Removes the last commit, keeps staging area and working directory unchanged (^ is the same as ~1)
git reset --mixed HEAD^ 		# Unstages the changes as well ( This is the default option )
git reset --hard HEAD^ 			# Discards local changes
```

### Reverting commits

```
git revert 72856ea 						# Reverts the given commit
git revert HEAD							# Revert commit with HEAD pointer
git revert HEAD~3.. 					# Reverts the last three commits
git revert --no-commit HEAD~3..HEAD		# Reverts the last three commits, but instead of committing this immediately, it will put all changes into staging area
git revert --no-commit HEAD~3..			# Short form for above command
git revert --continue					# After using --no-commit option, if everything goes good, to commit all the changes at once
git revert --abort						# After using --no-commit option, if we need to abort in between
```

### Recovering lost commits

```
git reflog 						# Shows the history of HEAD
git reflog show bugfix 			# Shows the history of bugfix pointer
git reset --hard HEAD@{1}		# To go back to a commit, which was lost and then found using reflog, we can even use a commit-id, present before HEAD@{1}
```

### Amending the last commit

```
git commit --amend
```

### Amending an Earlier commit

Go to the parent of a commit which we want to amend -> Change 'pick' to 'edit' ( We can edit multiple commits as well ) -> close the editor -> Make necessary changes
to code -> Then amend changes

```
git rebase -i parentCommitHash			# Rebase to the parent of the commit we want to edit
git commit --amend						# Amend changes to commit
git rebase --continue					# Finish rebasing
git rebase --abort						# Abort rebasing
```

Note:- Changes made to a particular commit will be carried on to the rest of the history

### Interactive rebasing

Running below command will give us a list of our commits, we can change 'pick' in front of commit-id to 'drop', or we can remove complete line to remove a particular commit.->
close the editor

```
git rebase -i HEAD~5		# Lists last 5 commits ( Latest commit will be at the bottom )
```

#### If there are conflicts

If a file is added in the commit which we are trying to remove we will get conflicts, start mergetool using `git mergetool` and then it might ask us to enter 'm' or 'd' or 'a'
to modify, delete or abort. Choose 'm' to keep the file with modified changes from next commit. Then `git rebase --continue`

### Rewording commit messages

```
git rebase -i commitHash^		# Rebase to the parent of the commit we want to reword
```

Change 'pick' to 'reword' for the commit we want to reword

### Reordering commits

```
git rebase -i commitHash^		# Rebase to the parent of the commit we want to re-order
```

Editor opens up with the list of commits, just move the lines however we want and history will be re-created as per the order of lines in this editor

### Squashing commits

```
git rebase -i commitHash^		# Rebase to the parent of the commit we want to squash
```

Start changing 'pick' to 'squash' in the editor. Git will start squashing changes with the parent commits.
If the commits we need to squash is not in the same order, we can re-order first by moving lines as required and then we can change to 'squash' and close editor ->
Opens commit message editor -> Change commit message as required

Note:- Instead of 'squash' we can use 'fixup' but 'fixup' doesn't allow us to change commit message, it uses squashed commit's parent's message by default.
(With which we have squashed our changes)

### Splitting a commit

```
git rebase -i commitHash^			# Rebase to the parent of the commit we want to split
git reset --mixed HEAD^				# Move all the changes of the commit we want to split to working directory
git rebase --continue				# After making necessary commits to split, finish rebasing
```

Make necessary commits then `git rebase --continue`

## Change 'pick' to 'edit' for the commit we want to split

### Creating releases on github ( This is not a git feature )

Go to the repo on Github -> Click on Tags -> Switch to 'Releases' button -> Click on 'Create a new release' -> Either choose a tag or create a new one
-> Choose Target (Leave default for latest code or choose any commit) -> Provide 'Release title' ->
Provide Release description (We can use '# ' to provide title and '- ' for bullet points) -> Click on 'Publish release'

### Creating Pull Requests

Once the code is pushed, go to Github then Click on either 'Compare & pull request' or go to 'Pull requests' tab -> Click on 'New pull request' ->
If we wish to merge this changes into master choose base as master -> For compare branch, choose the branch that we need to push code from ->
Click on 'Create pull request' -> Change title and description if required -> Click on 'Create pull request'
<br/>
<br/>
Once we add reviewers and make some modifications to address their comments, there will be a 'refresh' kind of icon next to the person who has requested changes,
we call click on it to Re-request review.

<br/>
Reviewer can select the checkbox 'Viewed' to indicate that he has reviewed that file.

<br/>
Once review is completed we can choose one of the merge options available in 'Merge pull request' dropdown and then click on 'Merge pull request' to merge our changes
<br/>
If there are conflicts, we can either use 'command line' or 'web editor'.
If we use command line, we have to pull the latest code, where we get merge conflicts which we can resolve and add a new commit and push to Github.
To use web editor, click on 'Resolve conflicts' -> Make necessary changes to solve conflicts -> Click on 'Mark as resolved' -> Click on 'Commit merge'

### Creating issues in Github

Click on 'Issues' tab in github to create an issue.<br/>
We can assign this issue to someone using Assignees.<br/>
We can add labels as required.(We can create new Labels under 'Issues' tab and then clicking on 'Labels' button) <br/>
We can link this issue to a pull request, when we merge this pull request, issue will be closed.<br/>

### Creating milestones in Github

Click on 'Issues' tab in github. -> Click on 'Milestones' button -> 'New Milestone' -> Give a title and Due date -> Go to issues or while cerating issues
assign issues to the milestone.

---

### Forking a repo

Go to a repo and click on 'Fork' this will create a copy of the repo in our Github account -> We can clone this repo and work on it as if we own this repo by
creating branches -> Difference will pushing the code from our forked repo branch is that, while creating a pull request, it will 'compare' our branch from
forked repo ('head repository') with base of original repo ('base repository'). <br/>
Maintainers of the original repo will be notified about the changes and only they can merge this new PR.

### Keeping a forked repo upto date

When we fork a repo and clone to our machine, we have a reference to this forked repo in our account called 'origin'.
To get latest code from original/base repo, we should add another reference to pull latest code from this.

```
git remote add base baseRepoURL 	# To add a reference to base repo. (We can name it anything, here we are calling it base, most commonly people call it upstream)
git remote -v						# To see details of this remote references
git remote rename base upstream		# To rename a reference
git remote rm base					# To remove a reference
git fetch base						# To fetch latest code from base repo (if we omit base, origin will be taken from default)
git switch master					# Switch to local master branch, if we are on someother branch
git merge base/master				# Merge latest code from base master into local master
git push							# To push merged latest code into forked repo
git switch bugFix
git merge master					# IF we are working on a branch in forked repo, we should always merge latest code into this branch as well
```
