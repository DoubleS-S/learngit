### 一、创建本地库

初始化一个Git仓库，使用`git init`命令。

添加文件到Git仓库，分两步：

1. 使用命令`git add <file>`，注意，可反复多次使用，添加多个文件；
2. 使用命令`git commit -m <message>`，完成。

### 二、状态管理

- 要随时掌握工作区的状态，使用`git status`命令。
- 如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容。
- `git diff`：若stage(暂存区)不为空，是对比working tree与index的共同文件；若stage为空，对比的是工作区与最后一次commit提交··的仓库的共同文件
- `git diff --cached`：是查看index与repository的差别的。
- `git diff HEAD`：是查看working tree和repository的差别的。其中：HEAD代表的是最近的一次commit的信息。

### 三、穿梭版本

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

### 四、远程库

- 要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；

  关联一个远程库时必须给远程库指定一个名字，`origin`是默认习惯命名；

  关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；（-u，关联）

  此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；

  分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！

- 要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

  Git支持多种协议，包括`https`，但`ssh`协议速度最快。

### 五、分支

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

- 开发一个新feature，最好新建一个分支；

  如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

- 哪些分支需要推送，哪些不需要呢？

  - `master`分支是主分支，因此要时刻与远程同步；
  - `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
  - bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
  - feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
  
- 多人协作的工作模式通常是这样：

  1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
  2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
  3. 如果合并有冲突，则解决冲突，并在本地提交；
  4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

  如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

- - 查看远程库信息，使用`git remote -v`；
  - 本地新建的分支如果不推送到远程，对其他人就是不可见的；
  - 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
  - **在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；**
  - 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
  - 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

- rebase操作可以把本地未push的分叉提交历史整理成直线； rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

### 六、标签

- - 命令`git tag <tagname>`用于新建一个标签，默认为`HEAD`，也可以指定一个commit id；
  - 命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息；
  - 命令`git tag`可以查看所有标签。
  - **注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。**
- - 创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。
  - 命令`git push origin <tagname>`可以推送一个本地标签；
  - 命令`git push origin --tags`可以推送全部未推送过的本地标签；
  - 命令`git tag -d <tagname>`可以删除一个本地标签；
  - 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

### 七、Github

```ascii
┌─ GitHub ────────────────────────────────────┐
│                                             │
│ ┌─────────────────┐     ┌─────────────────┐ │
│ │ twbs/bootstrap  │────>│  my/bootstrap   │ │
│ └─────────────────┘     └─────────────────┘ │
│                                  ▲          │
└──────────────────────────────────┼──────────┘
                                   ▼
                          ┌─────────────────┐
                          │ local/bootstrap │
                          └─────────────────┘
```

Fork---->Clone from my repository---->Code---->Push to my repository---->Pull request

- 在GitHub上，可以任意Fork开源仓库；
- 自己拥有Fork后的仓库的读写权限；
- 可以推送pull request给官方仓库来贡献代码。
- 有三个仓库，local（本地的），origin（远程你自己的） 和 upstream（远程别人的）。
  1.  local 和 origin 要同步，就是采用之前讲的 git pull 和 git push；
  2.  local 和 upstream 要同步，就要设置上游（git remote add upstream <upstream地址>），之后 git pull upstream master 拉下来同步
  3. origin 和 upstream 要同步，可以选择去 github 操作，去 origin 仓库向 upstream 发起 pull request，但是 head 设置为 upstream，base 设置为 origin，这相当于反向 pull request，可以让你 fork 的仓库与原仓库保持同步

