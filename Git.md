## GIT and Github

git show ?????


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
.newDir			// ignore sub-directory newDir
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
git reset HEAD file  // removes file from the Staging Area
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
