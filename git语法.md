### 一

初始化一个Git仓库，使用`git init`命令。

添加文件到Git仓库，分两步：

1. 使用命令`git add <file>`，注意，可反复多次使用，添加多个文件；
2. 使用命令`git commit -m <message>`，完成。

### 二

- 要随时掌握工作区的状态，使用`git status`命令。
- 如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容。
- `git diff`：若stage(暂存区)不为空，是对比working tree与index的共同文件；若stage为空，对比的是工作区与最后一次commit提交··的仓库的共同文件
- `git diff --cached`：是查看index与repository的差别的。
- `git diff HEAD`：是查看working tree和repository的差别的。其中：HEAD代表的是最近的一次commit的信息。

### 三

- `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
  - 若未关闭命令行，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。
  - **注意：从来没有被添加到版本库就被删除的文件，是无法恢复的！**

- 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
- 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

- git 是 管理修改，而不是管理文件，每次修改，如果不用`git add`到暂存区，那就不会加入到`commit`中。
- - 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`/ `git restore file`
  - 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
  - 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，使用命令`git reset --hard commit_id`，不过前提是没有推送到远程库。
- 文件管理器删除 rm file，仅删除工作区文件，可`git checkout -- file`恢复至最新版本库。(新版：use "git restore <file>..." to discard changes in working directory)
- 命令`git rm`用于删除版本库一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失**最近一次提交后你修改的内容**。

### 四

- 要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；

  关联一个远程库时必须给远程库指定一个名字，`origin`是默认习惯命名；

  关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；（-u，关联）

  此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；

  分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！

- 要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

  Git支持多种协议，包括`https`，但`ssh`协议速度最快。

### 五

- Git鼓励大量使用分支：

  查看分支：`git branch`

  创建分支：`git branch <name>`

  切换分支：`git checkout <name>`或者`git switch <name>`

  创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`

  合并某分支到当前分支：`git merge <name>`

  删除分支：`git branch -d <name>`

- 当Git无法自动合并分支时，就必须首先解决冲突（手动）。解决冲突后，再提交，合并完成。

  解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

  用`git log --graph`命令可以看到分支合并图。常用`git log --graph --pretty=oneline --abbrev-commit`

  `git status`也可以告诉我们冲突的文件，也可直接查看文件。

- Git分支十分强大，在团队开发中应该充分应用。

  合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

  使用`Fast forward`模式，merge后就像这样：

  ![git-br-ff-merge](https://www.liaoxuefeng.com/files/attachments/919022412005504/0)

  不使用`Fast forward`模式，merge后就像这样：（新commit）

  ![git-no-ff-mode](https://www.liaoxuefeng.com/files/attachments/919023225142304/0)

  合理的分治策略：

  ![git-br-policy](https://www.liaoxuefeng.com/files/attachments/919023260793600/0)

- 修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

  当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场；还可用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；

  你可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

  ```
  $ git stash apply stash@{0}
  ```

  在master分支上修复的bug，想要合并到当前dev分支，可以用`git cherry-pick <commit>`命令，把bug提交的修改“复制”到当前分支，避免重复劳动。