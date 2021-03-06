# Definition
- [[Git]] is a version control system for tracking changes in computer files and used to help coordinate work among several people on a project.
# Version control system (VCS)
- Records all the changes made to a file or set of data, so a specific version may be called later if needed.
- This helps ensure that all team member are working on the latest version of the file.![[Pasted image 20220315082757.png]]
# Git repository
- A place where all the Git files are stored. These files can either be stored on the local repo or on the remote repo
# Advantages 
- Faster release cycles
- Easy team collaboration
- Widespread acceptance
- Maintains the integrity of source code
- Pull request
# Pull request
- Are a mechanism for a developer to notify team members that they have completed a feature. From there, the source code is sent to a host where everyone has the access to review code and leave a comment on this code.
# Commit command
- git commit -m "message"
# Git push
- Push the content in a local repo to a remote repo.
- After a local repo has been modified, a push is executed to share the modifications with remote team members.
# Fetch vs pull
Fetch | Pull
------------ | ------------
Downloads new data from a remote repo | Updates the current HEAD branch with the latest changes from the remote server
Does not integrate any of new data into working files | Downloads new data and integrate it with current working file
# Git merge conflict
- Is an event occurs when Git is unable to resolve the differences in code between the two commits automatically
- **Resolve:**
	- Identify the files responsible for the conflicts
	- Implement the desired changes to the files
	- Add the files using the git add command
	- Commit the changes
- **Stage:**
	- Starting the merge process: if there are changes in the working directory, the merge will fail to start.
	- During the merge process: There's a conflict between the local branch and the branch being merged. In this case conflict must be resolved manually.
# Revert commit
- Undo all changes in the commit `git revert <commit id>`
# Git stash
- When you switch branch and don't want to lose the changes in the current branch, Git stash help you to stash the changes in that current branch.
# Git Bisect
- Performs a binary search to detect the commit which introduced a bug or regression in the project's history
- Syntax: `git bisect <subcommand> <options>`
# Find a list of files that has been changed in a particular commit
- `git diff-tree -r {commit hash}`
# Git config command
- The git config command is used to set git configuration values on a global or local level. It alters the configuration options in your git installation. It is generally used to set your Git email, editor, and any aliases you want to use with the git command.
# Git clean command
- It removes the untracked files from the working directory.
# Git squash
- Combining two or more commits into one.
# Fork vs branch vs clone
- **Fork:** is the process when a copy of the repo is made. Experimentation in the project without affecting the original.
- **Branch:** Refer to individual projects within a git repo.
- **Clone:** Create a copy existing git repo in a new directory. It create a connection that point back to the original repo. 
# Git merge vs rebase
- ## Merge: 
	- is used to incorporate new commits into you feature branch.
	- Creates an extra merge commit every time you need to incorporate changes
	- It pollutes your feature branch history
	- ![[Pasted image 20220315105058.png]]
- ## Rebase:
	- Incorporates all the new commits in the master branch
	- Rewrites the project history by creating new commits for each commit in the  original branch
	- ![[Pasted image 20220315105231.png]]
# Command used to fix a broken commit
- You may use the `git commit --amend` command, which helps you combine the staged changes with the previous commits instead of creating an entirely new commit.
# Recover a deleted branch that was not merged
- To recover a deleted branch, first, you can use the git reflog command. It will list the local recorded logs for all the references. Then, you can identify the history stamp and recover it using the git checkout command.
# Git stash drop
- Used to remove a particular stash. 
- `git stash drop "item name"`
# Reverting vs resetting
- **Reverting:** used to create a new commit that undoes the changes made in the previous commit. When you use this command, a new history is added to the project, the existing history is not modified.
- **Resetting:** used to undo the local changes that have been made to a Git repository. Git reset operates on the following: commit history, the staging index, and the working directory.
# Discover a branch has already been merged
- `git branch --merged` return the list of branches that have been merged into the current branch.
- `git branch --no-merged` return the list of branched that have not been merged.
# Cherry pick
- `git cherry-pick` enables you to pick up commits from a branch within the repo and apply it to another brand. This command is useful to undo changes when any commit is accidentally made to the wrong branch. Then, you can switch to the correct branch and use this command to cherry-pick the commit.
# Note 
``````
- commits ??? master lu??n lu??n theo sau ho???c update to date v???i dev

- feature branch n??n ???????c t???o t??? latest dev (l??u l??u c??ng c?? th??? t???o t??? master), m???i l???n merge xong z?? dev th?? n??n delete, khi n??o c?? nhu c???u th?? t???o l???i t??? dev tr??nh tr?????ng h???p ????? ???? b??? out of date r???i b???a sau l???i v?? ???? code ti???p th?? ko ???n.

- hotfix branch t???o t??? master, fix xong th?? merge v?? dev v?? merge v?? master.

- n???u mu???n merge code t??? feature branch v??? dev th?? n??n l??m nh?? sau:

 + ch?? ?? n???u c?? ng?????i kh??c l??m chung feature_branch th?? tr?????c khi merge th?? n??n b??o t???t c??? m???i ng?????i commit v?? push latest code l??n v?? d???ng code l???i ????? tr??nh issue do ??o???n rebase ??? b??n d?????i, c??n n???u feature_branch n??y c?? 1 m??nh m??nh l??m th?? ko sao.

 + git checkout feature_branch

 + git pull --rebase

 + git rebase dev

 + resolve conflict n???u c??, build v?? test l??? i.

 + commit

 + git push --force-with-lease

 + b??o c??c b???n kh??c l??m chung feature_branch:

 + git checkout feature_branch

 + git reset --hard origin/feature_branch

 + c??c b???n t??? test l???i feature d?????i local ????? make sure ko c?? v???n ????? g?? sau khi rebase, n???u c?? issue th?? ti???p t???c code fix issue r???i commit push code l??n sau ???? quay l???i t??? ?????u.

 + n???u m???i th??? ?????u ???? ok ????? merge z?? dev th?? ??i ti???p c??c b?????c b??n d?????i

 + b??o TL/TA review code (optional)

 + n???u c?? review feedback(s) th?? ti???p t???c code fix issue r???i commit push code l??n sau ???? quay l???i b?????c TL/TA review code.

 + n???u m???i th??? ?????u ???? ok ????? merge z?? dev th?? quay l???i t??? ?????u

 + git checkout dev

 + git merge feature_branch

 + git branch -d feature_branch

- t??? dev v??? master th?? submit pull request (l??? ra t??? feature_branch v??? dev c??ng c???n ph???i submit pull request nh??ng s??? a ko ????? th???i gian review n??n m???y ?????a s??? b??? stuck)

[https://megakemp.com/2019/03/20/the-case-for-pull-rebase/](https://megakemp.com/2019/03/20/the-case-for-pull-rebase/)

[https://stackoverflow.com/questions/21364636/git-pull-rebase-preserve-merges](https://stackoverflow.com/questions/21364636/git-pull-rebase-preserve-merges)`
``````