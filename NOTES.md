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
* Alternatively, `git log -- <file>` will show only commits related the specified file(s).
* Options like `--stat` and `-p` provide additional information about the commits.
* Options like `--author="Myself"` and `--grep`. `-S` shows changes with lines containing a specific string.  `git log --committer="Scheier" -S Welcome` shows all commits made by Yours Truly that contained a line added/modified/removed containing 'Welcome'.

### Undoing Things
* Use `git commit --amend` if you forgot to comment a file or part of a file.
* `git reset` demotes a file from the staging area.  This does not modify the local workspace.
* `git reset --soft HEAD~1` will undo your last commit with the previous commit update in the staging area.
* `git checkout -- <file>` will "undo" the local change you have made.

### Working with Remotes
* `git remote -v` lists information about the configured remote repositories.  These can be a central server or other users.
* `git pull origin` and `git push origin master` will pull/push changes from/to a server.

### Tagging
* `git tag` marks a commit as important.
* Tags can be pushed/pulled to remotes like branches.

### Git Aliases
* For the hunt-n-peckers, when 8 characters is just too many, use an alias...
* However, can be useful to simplify commands (i.e. `git config --global alias.rollback 'reset --soft HEAD~1'`).

## Chapter 3 - Git Branching

### Branches in a Nutshell
* Staging a file results in the creation of a blob in git (a version of a file in the git repo).  A commit (snapshot) consists of a hash of the root directory's blobs and recursive hashes from sub-directories and their blobs.
* Each commit points to one or more parent commits.  This creates a DAG.  A branch is simply a pointer to a commit (node) on the DAG.
* Switching branches involves traversing the dag and applying updates to the blobs specified by each commit.
* This switch will leave local workspace modifications alone.  If a blob change collides with local modifications, git will will not switch.

### Basic Branching and Merging
* Use `git checkout -b <branch>` to create and checkout a new branch from current branch/position (fast as it doesn't modify local workspace).
* Use `git merge <topic>` to update the current branch with commits from another branch.
* *fast-forward* indicates no merge-commits were made.  Essentially, your branch now just points to the same commit/snapshot as the topic branch.
* *merge-commits* bring two branches together.  It creates a new commit/snapshot with two parents (one being the previous commit of the active branch and the other being the commit currently pointed to by the topic branch). 
* `git branch -d <topic>` will delete the topic branch.  However, if you haven't merged that branch back into the master or another branch, this call may fail.
* `git branch -D <topic>` forces the delete and should always succeed (as long as the topic branch exists).
* A *merge-conflict* occurs when two branches modified the same file (and specifically the same lines in the file).
* To help resolve a *merge-conflict*, git will update the file to contain the changes from both branches.  The user is then expected to manually merge the changes.
* Use `git branch` to list the local branches in the repository. `*` will indicate the active branch.
* `git branch -v` shows some info of the current commit/snapshot for each branch (i.e. hash and comment).

### Branching Worflows
* Long-Running vs. Experimental Branches...

### Remote Branches
* `git fetch <remote>` updates the local repository's *remote-tracking branches*.  These *remote-tracking branches* are pointers to commit/snapshots (just like ordinary branches).  Unlike, ordinary branches, you cannot update what they point to directly.
* In order to 'play' with changes made in these remote branches, you need to merge them into a local branch (ala `git merge <remote>/<topic>`).
* `git checkout -b <topic> <remote>/<topic>` will create a local *tracking-branch* that will allow you to automatically update it with `git pull <remote> <branch>` .
* *master* is usually created as a tracking branch by default when cloning a remote repository.
* `git push <remote> <branch>` creates or updates the commit/snapshot pointed to by *branch* on the *remote* server.
* *@{u}* (relative to a tracking branch) is shorthand for *<remote>/<branch>*.
* `git config --global credential.helper cache` will keep credentials in memory for up to 15 minutes by default.
* `git pull` is basically equivalent to `git fetch <remote> <branch>; git merge <remote>/<branch>`.
* Book recommends using *fetch* and *merge* rather than *pull*.

### Rebasing
* All your base belong to us.
* 'git rebase master' will go to the first common ancestor of both *master* and the active branch.  The rebase will rewrite all new/added commits on top of the latest master.  This avoids *merge-commits*.
* Allows for cleaner merging later down the line.  Topic deviation to be grouped on 'top' of master commits.  These can be *squashed* to a single commit for even simpler merging/history.
* `--onto` allows rebasing from a descendant to some base branch (excluding changes from child/parent in between.
* **Golden Rule of Rebasing** Do not rebase "public" commits that exist in other repository.  (Only use on private commits).  Rebases re-write the commits.  This will ruin compatibility/integration with others.
* `git pull --rebase` performs a *fetch* and *rebase* rather a *fetch* and *merge*.

## Chapter 5 - Distributed Git

## Chapter 7 - Git Tools

### Revision Selection
* Refer to a given revision in history by it's SHA-1 hash.  This doesn't have to be the entire hash, just enough to be unambiguious.  The initial checkin has a hash of '1b4142e75518a819d1c668436a2558c7194229e5'.  However, at the time of this writing, simply using '1b41' is enough to unabiguously reference this commit.
* A branch is really just pointer to some commit.  You can checkout a specific commit by `git checkout <hash>`.  This puts your workspace into a *detached HEAD* state (i.e. it won't be advancing some branch reference).
* `git rev-parse <topic>` will print the SHA-1 hash for the commited pointed to by *topic*.
* Git maintains a *reflog*, a log of what your HEAD and branch references pointed two over the last couple months.  You can view this with `git reflog`.  The *reflog* is invaluable when repairing damage done from a poorly performed rebase...
* Reflog is purely a local history.  `git reflog HEAD@{3.weeks.ago}` shows the changes to HEAD 3 weeks ago... Kind of cool.
* Commits in the *ancestry* can be referenced relatively using *^*.  `git show HEAD^` will show the previous commit where `HEAD^^` will show the "uber" previous commit (i.e. grandparent). `HEAD~2` is equivalent to `HEAD^^`.  This comes in handy when comparing the current state to the previous commit (i.e. `git diff HEAD^ -- NOTES.md`).
* *double dot* syntax allows you to specify a difference of commits.  `git log master..topic` will show all commits that are in topic, but not master.  Whereas `git log topic..master` will show all commits that are in master, but not topic.
* Git provides *not* syntax as well.  Either with the attribute *--not* or with a *^* before the reference.  `git log master ^review` and `git log master --not review` are equivalent to `git log review..master`.
* *triple dot* syntax is an **xor** operator between two branches.

### Interactive Staging
* Somtimes it is nice to clean as you go.  Let's say you are in the middle of enhancing a function.  While do this, you notice that there are a few inconsistent curly brackets.  Do you modify them right away, or wait until your function enhancements are done?  With interactive staging, you can separate changes made to the same file.
* `git add -i` will enter the interactive staging mode (note that interactive mode can be used in other places like rebase/merge).
* Initially, you will be presented with the `What now>` prompt.  Where there are several options.
* `u` (*update*) - This prompt allows you to select what files you'd like to modify (in this case add).  Afterwards, the selected files will be indicated by an asterisk.  Hitting *<enter>* with no additional selections, will confim/exit the update.
* `2` or `u` (*update*) - This prompt allows you to select what files you'd like to modify (in this case add).  Afterwards, the selected files will be indicated by an asterisk.  Hitting *<enter>* with no additional selections, will confim/exit the update.
* `3` or `r` (*revert*) - This will all you to unselect staging selections you've made with update.
* `5` or `p` (*patch*) - Allows you select *hunks* to stage rather than the whole file.  Again, select files via their number.  However, once you hit *<enter>* with no additional selections, it does not take you back to the main menu right away.  Rather, you will be taken through hunk-by-hung of the selected files to determine whether or not to stage it.  In addition to simple staging options, you can further breakdown hunks into smaller hunks.
<pre><code>Stage this hunk [y,n,a,d,/,j,J,g,e,?]? ?
y - stage this hunk
n - do not stage this hunk
a - stage this and all the remaining hunks in the file
d - do not stage this hunk nor any of the remaining hunks in the file
g - select a hunk to go to
/ - search for a hunk matching the given regex
j - leave this hunk undecided, see next undecided hunk
J - leave this hunk undecided, see next hunk
k - leave this hunk undecided, see previous undecided hunk
K - leave this hunk undecided, see previous hunk
s - split the current hunk into smaller hunks
e - manually edit the current hunk
? - print help</code></pre>
* `add`, `stash save`, `checkout`, and `reset` all have `--patch` options.

### Stashing and Cleaning
* The stash is useful for temporarily removing (or stashing) workspace modifications (whether a bunch of debugging statements or incomplete work).  This is a useful alternative to an actual commit (i.e. doesn't compile or for debugging).
* `git stash` or `git stash save` push all workspace modifications onto the stash.  This includes any staged files.  Untracked files will not be stashed by default.
* `--include-untracked` or `-u` will tell git to stash untracked files.
* `--keep-index` will only stash unstaged changes.
* `--index` will tell git to try and re-stage appropriately when applying a stash.
* `git stash list` shows the current stash stack.
* `git stash apply <stash@{n}>` re-applies the specified stash to your workspace.  If no stash is explicitly specified, git will apply the top.
* `git stash drop <stash@{n}>` removes the specified stash from the stack.  If no stash is explicitly specified, git will apply the top.
* `git stash pop <stash@{n}>` re-applies the specified stash and then drops it.  If no stash is specified, it will apply and drop the top.
* `--patch` will allow you to interactively choose what to stash.
* `git stash branch <topic> <stash@{n}>` allows you to create a branch from the specified stash.
* `git clean -f` will forcibly remove all untracked files.
* `git clean -n` performs a dry run and lists files that would be removed.
* `git clean -f -d` will remove all untracked files and directories.
* `git clean -f -x` will look for ignored files too.
* `git clean -f -i` will perform an interactive clean.

### Searching
* `git grep <keyword>` searches files for a keyword.  `-n` specifies that the line number should be included in the result.
* `git grep -e <regex> <commit>` searches the specified commit (i.e. label, branch, commit id) for occurences of the provided expression.
* `--and` provides a convenient way to combine multiple expressions.
* `--break` and `--heading` provides some organization to the results.
* `git log -S<string>` displays all commits that modified a line containing *string*.
* `git log -G<regex>` displays all commits that modified a line matching the given expression.
* `git log -L<regex_begin>,<regex_end>,<file>` allows you to log the history for a range of lines in a file.  For example, `git log -L '/Two roads/','/all the difference/':decisions.txt` will show all the changes in *decisions.txt* between the first line containing *Two roads* and the first following line containing *all the difference*.

### Rewriting History
* `git commit --amend` allows you to modify your staging area to add/remove from your previous commit.  Like a rebase, do not do this operation after making changes public.
* `git merge --squash` will merge in a topic branch as a single commit.
* `git rebase -i` lets you do all sorts of crazy stuff.
