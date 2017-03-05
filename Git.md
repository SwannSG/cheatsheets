## GIT and Github

another change

### Data Structure

As a programmer we have a *Working directory*, which contains our files and sub-directories that contain programs, libraries and other assets. That is where we work.

Behind the scenes when we create a git *repository*, two git specific areas are created. The *repo* itself, and a *staging* or *index* area.

The *repo* is where a snapshot of the files in the *staging* area are permanently stored.

In the filesystem the git "stuff" is located in a hidden file called *.git*. (*workingDirectory/.git*).

A *snapshot* in the *Repository* contains a complete picture of the Working Directory at the time of the commit.

### Surveilance of the working directory

Once git is activated, it keeps a very close eye on the working directory.

git observes any:
- files or directories that are added to the working directory
- files that are changed in the working directory
- files that are deleted in the working directory
- files that are moved in the working directory  

We can also explicitly tell git to ignore certain files and sub-directories. These files or sub-directories are then never *tracked*.

### git Flow

Only files in the *staging* area are *commited to the repo*.

*Commited to the repo* means files in the staging area are stored as a permanent snapshot inside the .git folder. This happens each time the staging area is commited. Each commit is recorded as a separate snapshot.

 If a change is not in the *staging* area it is not commited to the *repo*. This even if there are files changed in the Working Directory, but not staged.

Hence, when a change is detected in the *working directory*, it first needs to be *staged* for it ever to be commited to the *repo*.

### Categorisation of differences between working directory and last repository snapshot

git determines there is a "difference" by comparing *Working Directory* files to *Repository* last snapshot files.

Any new file, that has never been commited by git before, is considered to be *untracked*.

Any *tracked* file that is changed, is considered to be *modified*.

When a file is staged, it is indicated by *changes to be commited*.

Conversely when a file is not yet staged but tracked, it is indicated by *changes not staged for commit*.

### git Configuration

git configuration is held at both a global and project level.

Global configuration is held in the file HOME/.gitconfig .

Globally it is essential to configure:
- user.email
- user.name

To make configuration changes, either use the command line or directly edit the config file.

```javascript
// file HOME/.gitconfig
[user]
	email = joe@gmail.com
	name = Joe Bloggs
// end of file
```

```javascript
// configuration from the command line
git config --global user.email "joe@gmail.com"
git config --global user.name "Joe Bloggs"	// omit --global flag for project level configuration
```
### Telling git to never track certain files

There are certain files and sub-directories we never want git to *track*. We want git to completely ignore these files and sub-directories.

We can configure this at both a global (*HOME/.gitignore_global* file) and local level (*WorkingDirectory/.gitignore* file).

*.gitignore_global* and *.gitignore* local work in tandem.

It is a very good idea to setup the "gitignore stuff" at the start of a project, to prevent uneccessary garbage going into the repo.  

```javascript
//  file WorkingDirectory/.gitignore
*.wp					// ignore all files ending in ".wp"
newDir			// ignore sub-directory newDir
// end file
```
There are many configuration patterns to identify exactly what needs to be ignored. Look them up when required.

#### Create a git environment

```javascript
git init																				// will tack on a sub-directory .git
git clone [remoteRepo] [[localRepo]]			// will clone a remote repository and (optionally) rename working directory = localRepo
```

#### Move files from Working Directory into Staging area

```javascript
git add --all				// add all files
git add .					// add all files, identical to git add --all
git add [file]			// add a specific file
git add [file1] [file2] [file3]		// add a number of specified files`
git add [pattern]			// add files according to a pattern
```

#### Move files from Staging Area to Repository

```javascript
git commit -m "Some commit message"

git commit
// opens file in editor
commit title of one line
// must have an empty line
some additional text to describe the commit more fully ...
// then uncomment the files (delete the "#") to commit to the snapshot

// the -am flag stages and commits in one step
git commit -am 'Adds to staging area and makes the commit in one step'
```

#### View status of Working Directory

This shows files untracked, modified, staged, and not staged.

```javascript
git status
```

#### View commit snapshots

Each commit snapshot has some meta information:
	- commit id SHA1
	- author
	- date
	- commit message

We can search the snapshots like a database, and their are a range of search parameters we can use.

```javascript
git log 							// show all snapshots in time order (most recent to least recent)
git log -p 1				// show last snapshot details
git log -n 2				// show last two snapshots
git log --since-2017-01-01	// show snapshots since this date
git log --until-2017-01-01	// show snapshots up to this date
git log --author="Joe Bloggs"
git log --grep="part of commit message"
git log --name-only -- test5.txt
git log --name-only SHA1 -n 1 // get commit details for just one specific SHA1
git log --oneline    // very usefull summarised information
git log --stat --summary
git log --format=oneline | short | raw
git log --graph
git log --oneline --graph --all --decorate
git log [branch] --oneline
```

#### View difference between Working Directory and snapshot

```javascript
git diff
git diff [file]
```

Actually, the title is a little misleading. If a file is added to the Staging Area, *diff* assumes it will be commited in some future snapshot. So, where the file is staged, *diff* computes the difference between the Working Directory file and the Staged file.   

#### View difference between Working Directory and Staging area

```javascript
git diff --staged [file]
```

#### Delete a file or directory in the Working Directory but already in the Repository

It is not possible to alter the contents of a snapshot in the repository. A snapshot is immutable.

The only exception is the snapshot for the last commit. It is not actually altered, but simply replaced.

There are two ways to delete a file in the Working Directory
	- through the operating system gui
	- using a special git command

```javascript
// delete the file though the operating system ui
git status			// will show file is deleted but not yet staged
git add [file]	 // add the deleted file (sounds weird) to staged area
git commint -m 'file deleted'
```

```javascript
// delete the file using a special git command
git rm [file]		// removes file from Working Directory and adds to deletion to Staged Area
git commint -m 'file deleted'
```

If the file or directory to be removed in the Working Directory, is not tracked by git because it is specifically excluded in .gitignore, the *git rm file* command will fail.

#### Move a file or directory in the Working Directory and already in the Repository

There are two ways to move a file or directory in the Working Directory
	- through the operating system gui
	- using a special git command

	```javascript
	// move the directory though the operating system ui
	git add .	 // stage changes
	git commint -m 'moved dir'	// commit changes
	```

	```javascript
	// delete the file using a special git command
	git mv [src] [dst]		// updates Working Directory and Staging Area
	git commit -m 'file deleted' // commit the changes
	```
### More on the data structure of the Repository

Each commit is a "snapshot write" to the git database which we call the Respository.

Every write or commit is immutable.

snapshot(last) builds on snapshot(2) which builds on snapshot(1).

```javascript
snapshot(last)	<------HEAD
v
snapshot(2)
v
snapshot(1)
```
Each snapshot actually represents the changes since the previous snapshot. But taken together any snapshot can be used to represent the Working Directory (excluding the gitignore files) at the point the snapshot is taken.

HEAD is a pointer to snapshot(last). It is possible to move HEAD to another snapshot as show below. But then snapshot(last) is an orphan and will be "cleaned up" by git.

```javascript
snapshot(last)
v
snapshot(2)	<------HEAD
v
snapshot(1)
```

#### Restoring a deleted file from the repo

Suppose we wish to recover a deleted file. The file is no longer in the Working Directory.

We need to find the snapshot, just before the commit where the file is deleted.

```javascript
git log --name-only -- file     // find the commit before the file delete commmit, get SHA1
git checkout SHA1 -- file     // checkout just that file at that specific SHA1 commit id
git commit -m 'restored file'
```

#### Restoring any file from the repo

Find the commit snapshot where the file version is correct.

Checkout the file for that specific commit. If the desired file is in the most recent snapshot, there is no need to specify the SHA1.

```javascript
// place this version of file in working directory
// correct file version is in most recent commit
git checkout -- file
git commit -m 'restored file'
```

```javascript
// place this version of file in working directory
// correct file version is in most commit SHA1
git checkout SHA1 -- file
git commit -m 'restored file'
```

#### Removing file from the Staging area

```javascript
git reset HEAD -- file  // removes file from the Staging Area
```
This is often used to package up a set of changes that can be commited under a sensible commit message.

#### Replacing the last commit

We can replace the most recent commit with a new revised commit. It is only possible to replace the most recent commit.

Setup the Staging Area as desired for the commit.

```javascript
git commit --amend -m 'replace the last commit'
```

We can also use this to simply just change the commit message.

```javascript
git commit --amend -m 'my new commit message'
```

When the last commit is replaced the commit id (SHA1) will change. It overwrites the previous last commit.

### Moving the HEAD pointer

The HEAD pointer always points to the most recent commit.

However, we can manualy move HEAD pointer using a git command.

These are dangerous commands and need to be used with extreme caution.

Always make a separate log file of all commits before using the *git reset* commmand, outside of the Working Directory. It allows, the floating snapshots, to be referenced.

```javascript
// HEAD pointer is moved`
snapshot(last)    // this is now a floating snapshot
v
snapshot(2)	<------HEAD
v
snapshot(1)

git log --name-only > ../log.txt    // copy git log to parent of Working Directory
```

The *--soft* option does not change the contents of the Working Directory and Staging Area. But we now point at the snapshot(SHA).
```javascript
// the safest option
git reset --soft SHA
```

The *--mixed* option, which is the default, does not change the contents of the Working Directory. The Staging Area and Repo point at the snapshot(SHA).
```javascript
// the default option
git reset --mixed SHA
git reset SHA
```

The *--hard* option writes over the Working Directory, Staging Area and the Repo points to snapshot(SHA). This is a very dangerous option.
```javascript
// most dangerous option
git reset --hard SHA
```

#### Viewing the orphaned snapshots after a reset

To view all the snapshots, including the orphaned snapshots, use the *git reflog* command.

#### Deleting untracked files from the Working Directory

Untracked files, that is files that have never been added to the Staging Area, can be deleted from the Working Directory.

First check what files will be permanently deleted.
```javascript
git clean -n   // trial run, nothing is deleted, but shows what will be deleted
```
Then delete the files.
```javascript
git clean -f   // force deletion of untracked files
```

#### Tree-ish Object

Tree-ish is a git concept. It represents a sequence of commits. We can select a specific snapshot by:
- SHA, short SHA
- HEAD
- branch reference
- tag reference
- ancestry

The *git ls-tree* shows the exact makeup of a snapshot. It list blobs and trees.

```javascript
git ls-tree HEAD
git ls-tree master
git ls-tree HEAD newDir/
git ls-tree <tree ref>
```

#### Ancestry

Allows the selection of a specific snapshot.

```javascript
HEAD^
HEAD^1
HEAD~
HEAD~1   // back 1 snapshot
HEAD~2   // back 2 snapshots
```
### How to look at a single snapshot in details

The *git show* command allows us to view a commit in detail.

```javascript
git show SHA
git show <options> SHA
```

#### Compare WorkingDirectory(snapshot(1)) with WorkingDirectory(snapshot(2))

*git diff* can be used for this.

```javascript
git diff SHA                  // show differences between snapshot(last) and snapshot(SHA)
git diff SHA..HEAD   // equivalent to git diff SHA
git diff SHA1..SHA2    // show differences between snapshot(SHA1) and snapshot(SHA2)
git diff SHA1..SHA2 file // show differences between snapshot(SHA1) and snapshot(SHA2) for "file"
git diff --stat --summary SHA..HEAD
git diff --ignore-space-change SHA..HEAD
git diff --ignore-all-space SHA..HEAD  
```
### Branching

When we move to a newly created branch, HEAD points to master.snapshot(latest).

```javascript
master----------------new
branch----------------branch
snap(latest=1) <----HEAD
snap(2)
snap(3)
```

On the first commit in the new branch HEAD now points to newBranch.snapshot(latest).

```javascript
master----------------new
branch----------------branch
snap(latest=1)<-----snap(latest=1)<----HEAD
snap(2)
snap(3)
```

On a new commit in the *master* branch, HEAD now points to master.snapshot(latest).

```javascript
master----------------new
branch----------------branch
snap(latest=1) <--HEAD
snap(2) <-------------snap(latest=1)
snap(3)
snap(4)
```

#### Create a new branch

When we create a new branch it is created off the current branch. We do not automatically move to the newly created branch.
```javascript
// current branch = bugfix
git branch newBranch       // "newBranch" branch is created off "bugfix" branch
```

#### List all branches

To list all branches associated with a respository.
```javascript
git branch           //list branches, branch marked with "*" is live
```

#### Move to a branch

When we move to a different branch, the Working Directory is completely refreshed. It now reflects the state of the Working Directory when the last commit was made in the new branch. The files for the branch we moved from is not lost. We can simply move back to it.
```javascript
git checkout branch           // move to a branch

git checkout -b branch     // move to branch if exists, else create branch and move to it
```

If we try to move to a branch and we have unsaved changes, git will prevent the move. To get the current branch into a state where a move is allowed, we can:
- scrap the changes
- commit the changes
- *stash* the changes

#### Comparing branches

```javascript
// not the dot dot (..) is called the range operator
git diff master..new_branch   // compare "master" and "new_branch"
```

To show all branches that are completely included in current branch. If the branch is included, then there is no need to merge.

```javascript
git branch --merged     // shows branches that are completely included in current branch
```

#### Changing a branch name

```javascript
// change the name of a branch
// from "branch_name" to "branch_new_name"
git branch --move branch_name branch_new_name
```

#### Deleting a branch

We can delete an existing branch as follows.

```javascript
git branch --delete branch_name
```

### Merging

Merging allows one branch to be mixed into another branch.

We need to be on the receiving branch.

```javascript
// make sure we are on the receiving branch
git merge other_branch
```

### Fast forward merge

master----------------new
branch----------------branch
snap(latest=1)<-----snap(latest=1)<----HEAD
snap(2)
snap(3)
```
When we merge the above, branch.snapshot(latest=1) has had no new snapshots, we can simply connect newBranch.snap(latest=1) directly to branch.snapshot(latest=1)

```javascript
master--------------------------------------------------new
branch--------------------------------------------------branch
snap(new_branch, latest=1)<----HEAD
snap(2, previously branch.snapshot(latest=1))<--snap(latest=1)
snap(3)
snap(4)
```

A simple connect to the receiving branch is called a fast-foward, and is the easiest and least complicated merge.

To disallow a fast-foward merge (when one could have been performed) and force a new commit on the receiving branch, we use the *git merge --no-ff* command.

If we want to only allow a merge to take place, if it is a simple merge which can be fast-forwarded, we use the *git merge --ff-only* command.

#### True Merge

A fast-foward merge is not considered to be a true merge. We simply connect the source branch commit onto the head of the receiving branch.

A true merge occurs when the newly created branch, and the original branch, both have new commits on their respective branches.

First we move to the receiving branch
```javascript
git merge branch_from
```
The merge may proceed smoothly or we may get a conflict.

#### Merge conflict

A merge conflict occurs when the same file has been edited on the same line(s) in more than one branch.

To  resolve we have three options
-- *git merge --abort*
-- resolve manually
-- resolve with a merge tool

To resolve manually we need to:
- open the conflicted file
- manually edited
- save the file
- add and commit to the repository

#### Viewing merges in branches

[Ways to display branches and merges](https://gist.github.com/datagrok/4221767)

```javascript
git log --graph --topo-order --decorate --oneline --all
git log --graph --topo-order --decorate --oneline --boundary master..dev
git log --graph --topo-order --decorate --oneline --boundary master..dev
git log --graph --all --topo-order --decorate --oneline --boundary --force-branch-columns=master,dev
git log --graph --all --topo-order --decorate --oneline --boundary
git log --graph --all --topo-order --decorate --oneline --boundary --force-branch-columns=master,dev
```

#### Check if a branch is fully merged into current branch
git branch --mergegit branch --merge
*git branch --merge* shows which branches are fully merged onto current branch. The branch we are on is displayed by an asterick. Any other branches that are listed, without and asterisk, are fully merged into current branch.

```javascript
git branch --merge
// result
branchX        // branchX is fully merged into master
*master
```
### Stashing changes

The *stash* is a fourth, global area in git.

We can temporarily save any changes in the Working Directory, when we don't yet want to do a full commit.

The stash is **globally available to all branches**.

```javascript
git stash save "some message"
```

Once we have stashed the changes we can move to another branch.

To list the stashes, we use *git stash list* command.

```javascript
git stash list
// stash@{0}: On [branch]: some message
```

To show more details about a stash we use the command *git stash show*.

*stash@{n}* is a reference to any individual stash.


```javascript
git stash show stash@{0}
git stash show -p stash@{0}      // show stash as a patch
```

To retrieve the stash we use the command *git stash pop|apply*

Pop removes the item from the stash.

Apply keeps the item in the stash.

```javascript
git stash pop stash@{0}
// or
git stash apply stash@{3}
```

To delete a stash item we used the *git stash drop* command.

```javascript
git stash drop stash@{0}
// or
git stash clear   // clears all items in the stash
```

### Remotes

We have a local machine with local git.

We now introduce a Remote machine with remote git.

In local git we define a name (alias) and resource location (url) for a remote repository. We use *git remote add [alias] [url]* command.

By convention alias is often set as "origin".

We can set the default branch for "origin" on the Remote.

```javascript
// local machine #1
git branch
*master
// end local machine

// introduce a remote
git remote add origin <url>
// fetch from remote where alias ="origin", remote branch = "master"
git fetch origin master    
// end introduce a remote

// after remote branch fetched
git branch
*master
origin/master     // notice new local repo "origin/master", snapshot taken on "git fetch"
// end after remote branch fetched
```

#### Movement of data

A *git fetch* downloads data from the specified remote, and updates [alias]/[branch] repo on the local machine. In the example above the repo is *origin/master*.


#### Typical Workflow

In practice the workflow is typically as follows:
- we make changes on *local.master*. *local.master.HEAD* has moved on from *local.origin/master*.
- we fetch most recent *remote.master* branch, which also may have moved on with additional commits.
- we merge *remote.master* into *local.master*
- push *local.master* to *remote.master*.

#### What Remotes does local git know about ?

```javascript
// list all remotes local git is aware of
git remote -v
// alias url (push)
// alias url (fetch)
```

#### Inform local git of a Remote

We inform local git of a Remote using the *git remote* command.

*alias* is a local short name for the Remote, and is an easy reference to the *resource location*

.config file is updated.

```javascript
git remote add <alias> <url>
// <url> describes where the resource is located
```
To remove a a remote that has been added for local.git, use *git remote rm*.

```javascript
gir remote rm <alias>
```

#### Push local branch to remote branch

To update the remote.branch we use the *git push -u* command.

```javascript
git push -u <alias for remote> <local branch>
// -u flag makes the branch tracking
```

The advantages of making a branch tracking are:
- can just use *git fetch* and *git push* with no additional parameters
- git informs you of unpushed and unpulled commits

#### Initialing a git environment and downloading an entire repository

We can use the *git clone* command.

Downloads the default branch for the remote repository.

This is configurable in Github.

```javascript
git clone <url> <local folder>
```

#### Downloading another remote branch (not default)

Use the *git fetch* command.

```javascript
git fetch <remote alias> <branch on remote>

git fetch      // <remote alias>, <branch on remote> are implied
```

**A *fetch* does not *merge* into the local branch.**

**After a *fetch* it is necessary to *merge*.**

#### Making a non-tracking branch a tracking branch

```javascript
git branch --set-upstream <local branch>  <remote alias>/<remote branch>
```

A tracking branch allows just *git push* to be used.

Otherwise we need *git push <remote alias> <local branch>*.

#### Comparing local and remote branches

```javascript
git diff <remote alias>/<remote branch>..<local branch>
```

#### Reference to a remote branch

We can reference a remote branch and use it as if it were a local branch.

The reference is <remote alias>/<remote branch/>

```javascript
git log --oneline server/master
```

#### Using fetch often

*git fetch* is never destructive to the local Working Directory. With that in mind here are some basic "rules":
- fetch before you start any work
- fetch before you push
- fetch often

After a *git fetch* do a *git merge*.

#### Remote has had multiple commits since your last fetch

The remote cannot accept a push if it has new commits.

These new commits may have been added by other developers.

We first need to fetch the changes on the remote. Then merge these changes, and try to push to the remote again.

#### Deleting a Remote

This a destructive delete !

The entire branch on the Remote is deleted.

```javascript
// git push <alias> --delete <branch>
git push server --delete branchC
```

#### Configuring a Remote with SSH

Using SSH for push and fetch from the Remote is very helpful, because we don't need to enter a password each time.

Steps to achieve this are:
- configure SSH keys on local machine (if you don't have them already)
- grab the public key using *cat ~/.ssh/id_rsa.pub*
- paster the public key into the Remote service (github, bitbucket, gitlab)
- test with a *fetch* or *push*


#### Adding a new feature to master branch workflow

We are going to add a new feature. The end product will be merged into the *master* branch. But we will make the actual changes in a separate branch, and then bring them back into the master.

The steps Joe take are:
- *git checkout master* (move to the local master branch)
- *git fetch origin/master* (get the latest version from the Remote)
- *git merge origin/master* (merge these changes into master branch)
-*git checkout -b new_feature* (create and move to branch "new_feature")
- make all the necessary changes in the Working Directory
- *git commit -am 'new_feature added'*
- *git fetch origin/master* (check for any changes on the Remote) . Assume there were no changes.
- *git push -u origin new_feature* (push the new_feature branch onto the Remote. This will create the "new_feature" branch on the Remote, for James to review)
- send email to James asking him to review

The steps James takes:
- *git checkout master* (move to her local master branch)
- *git fetch origin/master*   
- *git merge origin/master*
- *git checkout -b new_feature origin/new_feature (create "new_feature" branch from "origin/new_feature" and move to it )
- *git log* (to see the commits)
- *git show [commit]* (have a look at the commit in detail)
- *git commit -am 'James wants to make some changes'
- *git fetch*
- *git push*
- James sends an email to say he has made some additions

Back to Joe:
- *git fetch*
- *git log -p new_feature ..origin/new_feature* (-p is the patch option, and will show the diff between "new_feature" and "origin/new_feature")
- *git merge origin/new_feature* (merge changes into "new_feature" branch)
- *git checkout master* (switch back to "master" branch to receive the changes from "new_feature")
- *git fetch* (get latest remote version)
- *git merge origin/merge* (merge changes in from "origin/merge")
- *git merge new_feature* (merge changes in from "new_feature")
- *git push*

### Keyboard shortcuts using aliases

Set these up in the global configuration file.

```javascript
git config --global alias.st "status"  // st will execute 'git status'
