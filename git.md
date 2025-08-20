[TOC]

### Git 核心概念理解（先理解再操作）

在开始敲命令之前，先理解三个核心区域，这对掌握 Git 至关重要：

1. **工作区 (Working Directory)**：就是你电脑里能看到的项目目录，你在这里直接编辑文件。
2. **暂存区 (Staging Area / Index)**：像一个缓存区域，临时存放你的改动。`git add` 就是把工作区的改动放到这里。
3. **本地仓库 (Local Repository)**：最终安全存放项目历史的地方。`git commit` 就是把暂存区的所有内容正式提交到这里，生成一个快照。

**一次标准的提交流程**：`工作区` --`git add`--> `暂存区` --`git commit`--> `本地仓库`

------

### 第一部分：从头开始 - 初始化与基础操作

#### 1. 初始化一个新仓库：`git init`

如果你有一个本地项目文件夹，想用它开始一个 Git 仓库。

```bash
# 进入你的项目目录
cd my-project
# 初始化 Git 仓库
git init
```

这个命令会在 `my-project` 目录下创建一个隐藏的 `.git` 文件夹，这是 Git 用来跟踪管理版本库的所有数据。

#### 2. 克隆一个现有仓库：`git clone`

如果你想获取一个远程仓库（如 GitHub, GitLab, Gitee 上的项目）的完整副本。

bash

```bash
# 通过 SSH 方式克隆 (需要配置 SSH key)
git clone git@github.com:username/repository-name.git

# 通过 HTTPS 方式克隆
git clone https://github.com/username/repository-name.git

# 克隆到指定目录
git clone https://github.com/username/repository-name.git my-local-folder
```

#### 3. 检查当前状态：`git status`

这是你最常用的命令之一。它显示哪些文件已被修改、哪些已暂存、哪些未被跟踪。当你不知道下一步该做什么时，就先 `git status`。



```bash
git status
```

#### 4. 将文件添加到暂存区：`git add`

让 Git 开始跟踪一个新文件，或者将已修改的文件放入暂存区。

```bash
# 添加单个文件
git add filename.txt

# 添加所有当前更改（新建、修改）和已删除的文件，但不包括未跟踪的新文件
git add -u
# 或者
git add --update

# 添加所有更改，包括未跟踪的新文件（最常用）
git add .
# 或者更精确的方式（Git 2.x）
git add -A
# 或者
git add --all

# 交互式添加，可以选择文件中的部分更改（高级用法）
git add -p
```

#### 5. 提交更改到本地仓库：`git commit`

将暂存区的内容创建一个永久快照，保存到本地仓库的历史记录中。

```bash
# 最简单的提交，会启动默认编辑器（如 Vim）来让你输入提交信息
git commit

# 更常用的方式：直接在命令行中填写提交信息
git commit -m "这里填写有意义的提交说明，例如：修复了登录页面的样式问题"

# 一个更便捷的组合：自动把所有已跟踪文件的修改添加到暂存区并提交
# (注意：不会包含未跟踪的新文件，即从未 git add 过的文件)
git commit -am "提交信息"

# 修改上一次的提交（例如修改提交信息或漏掉了文件）
# 1. 先把漏掉的文件 add 进来
# 2. 然后执行
git commit --amend
```

**重要**：提交信息应清晰、简洁，说明本次提交**为什么**要做这些更改，而不是简单地写了“改了啥”。

#### 6. 查看提交历史：`git log`

查看项目的提交历史记录。

```bash
# 基本查看
git log

# 单行简洁显示，更易读
git log --oneline

# 图形化显示分支和合并历史
git log --graph --oneline --decorate --all

# 显示最近2次提交
git log -2

# 显示某个文件的修改历史
git log -p filename.txt
```

------

### 第二部分：分支管理 - 并行开发的利器

分支是 Git 的杀手级功能，让你可以在不同的线上同时开发功能，互不干扰。

#### 1. 查看、创建和切换分支

```bash
# 查看所有本地分支（当前分支前会标有 *）
git branch

# 查看所有分支（包括远程分支）
git branch -a

# 创建一个新分支
git branch new-feature

# 切换到另一个分支
git checkout new-feature

# 创建并立即切换到新分支（最常用）
git checkout -b new-feature

# (Git 较新版本) 切换分支的另一种方式
git switch other-branch

# (Git 较新版本) 创建并切换分支
git switch -c another-feature
```

#### 2. 合并分支：`git merge`

将一个分支的修改整合到当前分支。

```bash
# 1. 首先，切换到你想合并到的目标分支（通常是 main 或 master）
git checkout main

# 2. 将 new-feature 分支的更改合并到当前分支 (main)
git merge new-feature
```

合并通常会产生两种结果：

- **Fast-forward (快进合并)**：如果目标分支自创建新分支后没有新的提交，Git 只是简单地将指针向前移动。这是最理想的情况。
- **三方合并**：如果两个分支都有新的提交，Git 会创建一个新的“合并提交”，将两个分支的历史连接起来。可能会遇到**冲突**。

#### 3. 删除分支

```bash
# 删除一个已合并的分支（安全）
git branch -d new-feature

# 强制删除一个分支，即使它还没有被合并（谨慎使用！）
git branch -D some-experimental-branch
```

#### 4. 解决合并冲突

当两个分支对同一文件的同一部分进行了不同的修改，Git 无法自动合并时，就会发生冲突。文件内容会变成这样：

```text
<<<<<<< HEAD
这是当前分支的代码
=======
这是要合并过来的分支的代码
>>>>>>> new-feature
```

**解决步骤**：

1. 用编辑器打开冲突文件。
2. 手动选择要保留的代码，或者编写一段新的代码来融合两者。**删除所有 `<<<<<<<`, `=======`, `>>>>>>>` 标记**。
3. 保存文件。
4. 使用 `git add filename.txt` 将解决后的文件标记为已解决。
5. 完成合并提交：`git commit`。

------

### 第三部分：与远程仓库协作 - 团队开发的核心

远程仓库是托管在互联网或其他网络上的项目仓库，用于团队协作和数据备份。

#### 1. 查看远程仓库：`git remote`

bash

```bash
# 查看已配置的远程仓库，通常命名为 origin
git remote -v

# 添加一个新的远程仓库
git remote add upstream https://github.com/original-author/repo.git
```

#### 2. 获取与拉取更新：`git fetch` & `git pull`

```bash
# 从远程仓库下载所有数据（更新所有分支信息），但不会自动合并到你的工作区
git fetch origin

# 更常用的命令：下载数据并立即尝试合并到当前分支
# git pull = git fetch + git merge
git pull origin main

# 推荐使用 rebase 方式拉取，可以使历史线形更整洁
git pull --rebase origin main
```

#### 3. 推送更新到远程仓库：`git push`

将你的本地提交上传到远程仓库。

bash

```
# 将当前分支推送到远程仓库的对应分支（如果远程没有，则会创建）
git push origin main
git push origin new-feature

# 设置上游关联后，可以简化推送（首次推送时使用 -u）
git push -u origin new-feature
# 之后再次推送，只需要
git push
```

------

### 第四部分：高级与实用操作

#### 1. 撤销操作

bash

```
# 1. 撤销工作区的修改（危险！会丢失所有未暂存的更改，无法恢复）
git checkout -- filename.txt

# 2. 撤销暂存区的修改（将文件从暂存区放回工作区）
git reset HEAD filename.txt

# 3. 撤销提交（软重置）：保留工作区和暂存区的更改，只撤销 commit
git reset --soft HEAD~1

# 4. 撤销提交（混合重置，默认）：保留工作区更改，但撤销 commit 和暂存
git reset HEAD~1
# 或者
git reset --mixed HEAD~1

# 5. 撤销提交（硬重置，非常危险！）：彻底丢弃这次提交以及所有工作区更改
git reset --hard HEAD~1
# (谨慎使用！除非你确定要丢弃所有代码)

# 6. 创建一个新的提交来“反做”之前的提交（安全，适用于已推送的提交）
git revert <commit-hash>
```

#### 2. 储藏更改：`git stash`

当你的工作做到一半，需要临时切换到另一个分支处理紧急任务时，可以用它来保存现场。

bash

```
# 储藏当前工作区和暂存区的所有修改
git stash

# 查看所有储藏列表
git stash list

# 恢复最近一次的储藏，并从储藏列表中删除它
git stash pop

# 恢复储藏，但不从列表中删除
git stash apply

# 恢复指定的储藏，stash@{0} 是编号
git stash apply stash@{0}

# 删除一个储藏
git stash drop stash@{0}
```

#### 3. 查看差异：`git diff`

bash

```
# 查看工作区和暂存区的差异
git diff

# 查看暂存区和最后一次提交的差异
git diff --staged
# 或者
git diff --cached

# 比较两个分支的差异
git diff main..new-feature

# 比较两次提交的差异
git diff commitA commitB
```

------

### 总结：日常工作流示例

一个典型的 Feature Branch 工作流：

1. **保持同步**：`git pull --rebase origin main` (从主分支拉取最新代码)
2. **创建分支**：`git checkout -b feat/new-button` (为新功能创建一个分支)
3. **开发功能**：编写代码...
4. **暂存更改**：`git add .` (或指定文件)
5. **提交更改**：`git commit -m "feat: add a new styled button component"`
6. **重复 3-5** 直到功能完成。
7. **再次同步**：`git checkout main` -> `git pull --rebase` -> `git checkout feat/new-button` -> `git rebase main` (确保你的功能分支是基于最新的主分支代码，减少冲突)
8. **解决冲突**（如果有的话）。
9. **推送分支**：`git push -u origin feat/new-button`
10. **发起 Pull Request (PR) / Merge Request (MR)**：在 GitHub/GitLab 等平台上申请将你的分支合并到 `main`。
11. **代码审查**后合并，然后删除远程和本地的功能分支。

### 配置相关

bash

```
# 设置全局用户名和邮箱（重要！提交者身份）
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 设置默认编辑器为 VSCode
git config --global core.editor "code --wait"

# 让命令行显示当前所在分支（非常有用）
git config --global prompt.integration true
```