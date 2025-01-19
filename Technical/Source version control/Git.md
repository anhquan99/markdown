# Git
## Definition
- [[git]] is a version control system for tracking changes in computer files and used to help coordinate work among several people on a project.
## Version control system (VCS)
- Records all the changes made to a file or set of data, so a specific version may be called later if needed.
- This helps ensure that all team member are working on the latest version of the file.![[Pasted image 20220315082757.png]]
## Git repository
- A place where all the Git files are stored. These files can either be stored on the local repo or on the remote repo
## Advantages 
- Faster release cycles
- Easy team collaboration
- Widespread acceptance
- Maintains the integrity of source code
- Pull request
## Pull request
- Are a mechanism for a developer to notify team members that they have completed a feature. From there, the source code is sent to a host where everyone has the access to review code and leave a comment on this code.
## Commit command
- git commit -m "message"
## Git push
- Push the content in a local repo to a remote repo.
- After a local repo has been modified, a push is executed to share the modifications with remote team members.
## Fetch vs pull
Fetch | Pull
------------ | ------------
Downloads new data from a remote repo | Updates the current HEAD branch with the latest changes from the remote server
Does not integrate any of new data into working files | Downloads new data and integrate it with current working file
## Git merge conflict
- Is an event occurs when Git is unable to resolve the differences in code between the two commits automatically
- **Resolve:**
	- Identify the files responsible for the conflicts
	- Implement the desired changes to the files
	- Add the files using the git add command
	- Commit the changes
- **Stage:**
	- Starting the merge process: if there are changes in the working directory, the merge will fail to start.
	- During the merge process: There's a conflict between the local branch and the branch being merged. In this case conflict must be resolved manually.
## Revert commit
- Undo all changes in the commit `git revert <commit id>`
## Git stash
- When you switch branch and don't want to lose the changes in the current branch, Git stash help you to stash the changes in that current branch.
## Git Bisect
- Performs a binary search to detect the commit which introduced a bug or regression in the project's history
- Syntax: `git bisect <subcommand> <options>`
## Find a list of files that has been changed in a particular commit
- `git diff-tree -r {commit hash}`
## Git config command
- The git config command is used to set git configuration values on a global or local level. It alters the configuration options in your git installation. It is generally used to set your Git email, editor, and any aliases you want to use with the git command.
## Git clean command
- It removes the untracked files from the working directory.
## Git squash
- Combining two or more commits into one.
## Fork vs branch vs clone
- **Fork:** is the process when a copy of the repo is made. Experimentation in the project without affecting the original.
- **Branch:** Refer to individual projects within a git repo.
- **Clone:** Create a copy existing git repo in a new directory. It create a connection that point back to the original repo. 
	- When we run **git clone** locally, git creates a copy of the **.git** file in a new subdirectory, but to save space it uses hard links where possible.
## Git merge vs rebase
- #### Merge: 
	- is used to incorporate new commits into you feature branch.
	- Creates an extra merge commit every time you need to incorporate changes
	- It pollutes your feature branch history
	- ![[Pasted image 20220315105058.png]]
- #### Rebase:
	- Incorporates all the new commits in the master branch
	- Rewrites the project history by creating new commits for each commit in the  original branch
	- ![[Pasted image 20220315105231.png]]
## Command used to fix a broken commit
- You may use the `git commit --amend` command, which helps you combine the staged changes with the previous commits instead of creating an entirely new commit.
## Recover a deleted branch that was not merged
- To recover a deleted branch, first, you can use the git reflog command. It will list the local recorded logs for all the references. Then, you can identify the history stamp and recover it using the git checkout command.
## Git stash drop
- Used to remove a particular stash. 
- `git stash drop "item name"`
## Reverting vs resetting
- **Reverting:** used to create a new commit that undoes the changes made in the previous commit. When you use this command, a new history is added to the project, the existing history is not modified.
- **Resetting:** used to undo the local changes that have been made to a Git repository. Git reset operates on the following: commit history, the staging index, and the working directory.
## Discover a branch has already been merged
- `git branch --merged` return the list of branches that have been merged into the current branch.
- `git branch --no-merged` return the list of branched that have not been merged.
## Cherry pick
- `git cherry-pick` enables you to pick up commits from a branch within the repo and apply it to another brand. This command is useful to undo changes when any commit is accidentally made to the wrong branch. Then, you can switch to the correct branch and use this command to cherry-pick the commit.
## Sparse checkout
- `sparse-checkout` initialize and modify the sparse-checkout.
## View log pretty
```bash
git log --pretty=oneline -n 20 --graph --abbrev-commit
```
## Note 
``````bash
- commits ở master luôn luôn theo sau hoặc update to date với dev

- feature branch nên được tạo từ latest dev (lâu lâu cũng có thể tạo từ master), mỗi lần merge xong zô dev thì nên delete, khi nào có nhu cầu thì tạo lại từ dev tránh trường hợp để đó bị out of date rồi bữa sau lại vô đó code tiếp thì ko ổn.

- hotfix branch tạo từ master, fix xong thì merge vô dev và merge vô master.

- nếu muốn merge code từ feature branch về dev thì nên làm như sau:

 + chú ý nếu có người khác làm chung feature_branch thì trước khi merge thì nên báo tất cả mọi người commit và push latest code lên và dừng code lại để tránh issue do đoạn rebase ở bên dưới, còn nếu feature_branch này có 1 mình mình làm thì ko sao.

 + git checkout feature_branch

 + git pull --rebase

 + git rebase dev

 + resolve conflict nếu có, build và test lạ i.

 + commit

 + git push --force-with-lease

 + báo các bạn khác làm chung feature_branch:

 + git checkout feature_branch

 + git reset --hard origin/feature_branch

 + các bạn tự test lại feature dưới local để make sure ko có vấn đề gì sau khi rebase, nếu có issue thì tiếp tục code fix issue rồi commit push code lên sau đó quay lại từ đầu.

 + nếu mọi thứ đều đã ok để merge zô dev thì đi tiếp các bước bên dưới

 + báo TL/TA review code (optional)

 + nếu có review feedback(s) thì tiếp tục code fix issue rồi commit push code lên sau đó quay lại bước TL/TA review code.

 + nếu mọi thứ đều đã ok để merge zô dev thì quay lại từ đầu

 + git checkout dev

 + git merge feature_branch

 + git branch -d feature_branch

- từ dev về master thì submit pull request (lẽ ra từ feature_branch về dev cũng cần phải submit pull request nhưng sợ a ko đủ thời gian review nên mấy đứa sẽ bị stuck)

[https://megakemp.com/2019/03/20/the-case-for-pull-rebase/](https://megakemp.com/2019/03/20/the-case-for-pull-rebase/)

[https://stackoverflow.com/questions/21364636/git-pull-rebase-preserve-merges](https://stackoverflow.com/questions/21364636/git-pull-rebase-preserve-merges)`
``````