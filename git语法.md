### 一

初始化一个Git仓库，使用`git init`命令。

添加文件到Git仓库，分两步：

1. 使用命令`git add <file>`，注意，可反复多次使用，添加多个文件；
2. 使用命令`git commit -m <message>`，完成。

### 二

- 要随时掌握工作区的状态，使用`git status`命令。
- 如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容。
- git diff：若stage(暂存区)不为空，是对比working tree与index的共同文件；若stage为空，对比的是工作区与最后一次commit提交的仓库的共同文件
- git diff --cached：是查看index与repository的差别的。
- git diff HEAD：是查看working tree和repository的差别的。其中：HEAD代表的是最近的一次commit的信息。

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
- 文件管理器删除 rm file，仅删除工作区文件，可`git checkout -- file`恢复至最新版本库。
- 命令`git rm`用于删除版本库一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失**最近一次提交后你修改的内容**。

