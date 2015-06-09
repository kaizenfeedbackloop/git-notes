# Notes for Pro Git, 2nd Ed.

## Chaper 1 - Getting Started

### About Version Control
* SVN is a CVCS (Centralized Version Control System).  Used to be used for a lot of open source projects (i.e. sourceforge).  Pretty sure TFS falls under this category.
* Git is a DVCS (Distributed Version Control System).  These were very trendy like 5 years back.  hg, bzr, git, and others.  However, I think git has won at this point. 

### A Short History of Git
* After using primarily tarball source control, the Linux Kernel switched to using BitKeeper (commercial software).  BitKeeper's free licenses to open source developers were revoked.  The Linux development community decided to create git.

### Git Basics
* Git "thinks about its data" as a snapshot of a filesystem, rather than a list of file based changes.  What exactly does this mean?
* Local operations make things merging a specific commit *very* fast as opposed to TFS.
* Everything is a hash.  Allows for repository integrity.  Usually, only need to specify a small subset of characters, not entire hash.
* Generally only adds data.  Most operations are reversible (which is good, because I regularly screw stuff up).
* Three "states" for data: in git directory (i.e. in .git), in working directory (i.e. the actual files you modify), and the staging area (temperary state to prepare actual git modification).
* Basic workflow:  edit files locally, *add* files to staging area, *commit* snapshot

### The Command Line
* How the kool kids use git.
* `git <cmd> <options> <args>`

### First-Time Git Setup
* Setting `git config --global user.name "John Doe"` and `git config --global user.email johndoe@example.com` allows other users to see who made a given commit.
* Display HTML help with `git help <cmd>`.
* IRC channels #git #github are available on irc.freenode.net


## Chaper 2 - Git Basics

### Getting a Git Repository
* Create a new repository with `git init`.  This won't be shared in any fashion, but you could start versioning some files.  You could use this for versioning evals!
* Add files to the staging area with `git add *` and then create the new snapshot with `git commit -m '2015 was a great year'`.

### Cloning an Existing Repository
* If you want to be a social coder (or lurking coder), this is more likely how you'll get a new repository, `git clone https://gitdak.visualstudio.com/defaultcollection/_git/Tutorial`.  Creates a new folder in the working directory called 'Tutorial'

### Recording Changes to the Repository
* Use `git status` to show the states of tracked and untracked files.
* Use `git add <file>` to start tracking an untracked file (stages it).
* When a file is staged, a snapshot at that point in time is saved.  Any further changes, will not be staged.
* '.gitignore' files can be used to specify files that will never be commited (i.e. build output).
* Use `git diff` to view all changes (line-by-line).
* `git rm` or `git mv` can be used to manipulate entire files.  git is pretty smart about noticing a move even if you perform a `mv` followed by an `git add`.

### Viewing the Commit History
* Use `git log` to view repository history.
* Alternatively, `git log -- <file>' will show only commits related the specified file(s).
* Options like `--stat` and `-p` provide additional information about the commits.
* Options like `--author="Myself"` and `--grep`. `-S` shows changes with lines containing a specific string.  `git log --committer="Scheier" -S Welcome` shows all commits made by Yours Truly that contained a line added/modified/removed containing 'Welcome'.

### Undoing Things
* Use `git commit --amend` if you forgot to comment a file or part of a file.
* `git reset` demotes a file from the staging area.  This does not modify the local workspace.
* `git reset --soft HEAD~1` will undo your last commit with the previous commit update in the staging area.
* `git checkout -- file` will "undo" the local you have made.

### Working with Remotes
* `git remote -v` lists information about the configured remote repositories.  These can be a central server or other users.
* `git pull origin` and `git push origin master` will pull/push changes from/to a server.

### Tagging
* `git tag` marks a commit as important.
* Tags can be pushed/pulled to remotes like branches.

### Git Aliases
* For the hunt-n-peckers, when 8 characters is just too many, use an alias...
* However, can be useful to simplify commands (i.e. `git config --global alias.rollback 'reset --soft HEAD~1'`).