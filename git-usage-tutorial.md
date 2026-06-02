# Git 使用教程

主人，这份教程基于 `D:\Code\Git-test` 练习仓库整理，命令按 Windows PowerShell 写法准备。每个代码块都可以直接复制执行。

## 1. Git 最核心的概念

Git 是用来保存代码版本的工具。一次 `commit` 就是一个版本。

常见位置：

| 名称 | 含义 |
| --- | --- |
| 工作区 | 你正在编辑的真实文件 |
| 暂存区 | 准备提交的修改清单 |
| 本地仓库 | 你电脑里的版本历史 |
| 远端仓库 | GitHub、Gitee、GitLab 等平台上的仓库 |

最重要的关系：

```powershell
git add .
git commit -m "提交说明"
git push
```

含义：

```text
add     把修改放进暂存区
commit  保存为本地版本
push    推送到远端仓库
```

## 2. 进入练习仓库

```powershell
cd D:\Code\Git-test
```

查看当前状态：

```powershell
git status
```

查看简洁状态：

```powershell
git status --short --branch
```

不知道当前发生了什么时，先运行：

```powershell
git status
```

## 3. 第一次提交

如果一个文件夹还不是 Git 仓库，先初始化：

```powershell
git init
```

添加所有修改：

```powershell
git add .
```

提交一个版本：

```powershell
git commit -m "Initial commit"
```

查看版本历史：

```powershell
git log --oneline
```

## 4. `git add` 的快捷方案

添加当前目录下所有变化：

```powershell
git add .
```

添加整个仓库所有变化：

```powershell
git add -A
```

只添加已经被 Git 跟踪过的文件变化，不添加新文件：

```powershell
git add -u
```

一块一块选择要提交的修改：

```powershell
git add -p
```

实际工程中，建议先看状态再添加：

```powershell
git status
git add .
```

## 5. 查看文件改了什么

查看未暂存的修改：

```powershell
git diff
```

查看已经暂存的修改：

```powershell
git diff --staged
```

只看某个文件：

```powershell
git diff test1.txt
```

不进入翻页器，直接输出：

```powershell
git --no-pager diff
```

`git diff` 翻页时常用按键：

| 按键 | 作用 |
| --- | --- |
| 空格 | 下一页 |
| b | 上一页 |
| Enter | 下一行 |
| `/关键词` | 搜索关键词 |
| n | 下一个搜索结果 |
| q | 退出 |

## 6. 撤销文件修改

撤销还没有暂存的修改：

```powershell
git restore .
```

撤销某一个文件的未暂存修改：

```powershell
git restore test1.txt
```

如果修改已经 `git add` 进暂存区，先取消暂存：

```powershell
git restore --staged .
```

再撤销文件内容：

```powershell
git restore .
```

所以完整撤销所有已暂存和未暂存修改：

```powershell
git restore --staged .
git restore .
```

记忆：

```text
git restore .             撤销工作区修改
git restore --staged .    取消暂存
```

注意：`git restore .` 不会删除 `??` 这种未跟踪的新文件。

## 7. 查看版本历史

查看简洁历史：

```powershell
git log --oneline
```

查看分支图：

```powershell
git log --oneline --graph --all
```

查看最近 10 个版本：

```powershell
git log --oneline --decorate -10
```

查看当前版本号：

```powershell
git rev-parse --short HEAD
```

## 8. 回退版本

先查看历史，找到目标版本号：

```powershell
git log --oneline
```

比如历史是：

```text
24c6ede change the test1.txt
5163ddc add the test2
b741411 add the test1
25827d7 Update hello practice text
3f5b8db Initial practice repo
```

### 8.1 回退到上一个版本，但保留修改

```powershell
git reset --soft HEAD~1
```

效果：

```text
版本退回去了
文件修改还在
修改在暂存区里
```

### 8.2 回退到指定版本，但保留后续修改

```powershell
git reset --soft b741411
```

效果：

```text
HEAD 回到 b741411
后面版本做过的修改还留着
修改在暂存区里
```

如果这时再执行：

```powershell
git restore --staged .
git restore .
```

最终效果基本等于：

```powershell
git reset --hard b741411
```

### 8.3 彻底回到指定版本

```powershell
git reset --hard b741411
```

效果：

```text
HEAD 回到 b741411
暂存区回到 b741411
工作区文件也回到 b741411
```

注意：这会丢掉后续版本里的文件修改。

### 8.4 更安全的撤销方式

如果错误提交已经推送到远端，推荐使用：

```powershell
git revert 24c6ede
```

它不会删除历史，而是新增一个“反向提交”来抵消原来的提交。

## 9. 实际工程推荐的回退方案

简单原则：

```text
没 push：可以 reset
已 push：优先 revert
多人共享分支：不要随便 reset --hard 后强推
```

本地提交错了，还没推送：

```powershell
git reset --soft HEAD~1
```

本地提交和修改都不要了：

```powershell
git reset --hard HEAD~1
```

已经推送到远端，安全撤销：

```powershell
git revert 提交号
```

不推荐在共享分支随便执行：

```powershell
git reset --hard b741411
git push --force
```

如果团队确认必须强推，也优先用：

```powershell
git push --force-with-lease
```

一句话：

```text
个人本地用 reset
团队远端用 revert
```

## 10. Git 分支

分支可以理解成：从当前版本复制出一条独立开发线。

查看分支：

```powershell
git branch
```

创建并切换到新分支：

```powershell
git switch -c feature-practice
```

切回主分支：

```powershell
git switch main
```

把某个分支合并到当前分支：

```powershell
git merge feature-practice
```

常见命名：

```text
main            稳定主线
feature-xxx     开发新功能
bugfix-xxx      修 bug
experiment-xxx  做实验
```

一句话：分支让你在不影响主线的情况下，单独做一组修改。

## 11. 把某个版本的文件复制到另一个文件夹

推荐用 `git archive`，它不会改变当前仓库。

把 `b741411` 版本导出到新文件夹：

```powershell
cd D:\Code\Git-test
New-Item -ItemType Directory -Force D:\Code\Git-test-b741411
git archive b741411 | tar -x -C D:\Code\Git-test-b741411
```

这句的意思：

```text
git archive b741411
把 b741411 版本里的 Git 跟踪文件打包输出

tar -x -C D:\Code\Git-test-b741411
把前面的内容解压到目标文件夹
```

中间的 `|` 是管道：

```text
把左边命令的输出交给右边命令处理
```

也可以先打包成 zip：

```powershell
cd D:\Code\Git-test
git archive --format=zip -o D:\Code\b741411.zip b741411
Expand-Archive -LiteralPath D:\Code\b741411.zip -DestinationPath D:\Code\Git-test-b741411
```

注意：`git archive` 只复制 Git 管理的文件，不复制 `.git` 历史，也不复制未跟踪文件。

## 12. push 到远端仓库

`commit` 是保存到本地仓库，`push` 是上传到远端仓库。

如果远端还没有仓库，步骤如下。

### 12.1 本地先确认有提交

```powershell
cd D:\Code\Git-test
git status
git log --oneline
```

如果没有提交，先提交：

```powershell
git add .
git commit -m "Initial commit"
```

### 12.2 去远端平台创建空仓库

比如 GitHub、Gitee 或 GitLab。

创建时建议：

```text
不要勾选初始化 README
不要添加 .gitignore
不要添加 License
```

因为本地已经有仓库，远端最好保持空的。

### 12.3 绑定远端仓库

HTTPS 示例：

```powershell
git remote add origin https://github.com/用户名/Git-test.git
```

查看远端是否绑定成功：

```powershell
git remote -v
```

### 12.4 第一次推送

```powershell
git push -u origin main
```

其中：

```text
origin   远端仓库默认名字
main     要推送的本地分支
-u       建立关联，以后可以直接 git push
```

以后推送：

```powershell
git push
```

完整流程：

```powershell
cd D:\Code\Git-test

git status
git add .
git commit -m "Initial commit"

git remote add origin https://github.com/用户名/Git-test.git
git remote -v

git push -u origin main
```

## 13. 远端有没有本地 git log 的历史

不一定。

本地 `git log` 看到的是本地仓库里的提交历史。远端有没有，要看这些提交有没有被 `push` 过去。

更新远端信息：

```powershell
git fetch
```

查看远端 `main` 历史：

```powershell
git log --oneline origin/main
```

查看本地比远端多了哪些提交：

```powershell
git log --oneline origin/main..main
```

查看本地和远端的分支关系：

```powershell
git log --oneline --graph --all
```

一句话：

```text
本地 git log 的历史，只有 push 之后远端才有
```

## 14. 本地资料没了，还能从远端拉回上上个版本吗

可以，但前提是：

```text
上上个版本已经 push 到远端
远端历史没有被强制改写删除
```

重新克隆：

```powershell
git clone 远端仓库地址
cd 仓库名
```

查看历史：

```powershell
git log --oneline
```

切到某个旧版本查看：

```powershell
git switch --detach 版本号
```

只导出某个旧版本：

```powershell
git archive 版本号 | tar -x -C D:\Code\old-version
```

如果远端没有那个 commit，本地资料又没了，就拉不回来了。

## 15. 把这份教程提交并推送到远端

现在这份文件就是：

```text
D:\Code\Git-test\git-usage-tutorial.md
```

你可以这样提交并推送：

```powershell
cd D:\Code\Git-test
git status
git add git-usage-tutorial.md
git commit -m "Add Git usage tutorial"
git push
```

如果是第一次推送当前分支：

```powershell
git push -u origin main
```

推送完成后，远端仓库就会有这份 Markdown 教程。

## 16. 远端仓库改名后，本地配置如何改

如果只是远端仓库改名，本地一般只需要修改 `origin` 的 URL。

先查看当前远端地址：

```powershell
cd D:\Code\Git-test
git remote -v
```

假设远端从：

```text
https://github.com/moonlight2587/git-test.git
```

改名成：

```text
https://github.com/moonlight2587/Git-How-Use.git
```

本地执行：

```powershell
git remote set-url origin https://github.com/moonlight2587/Git-How-Use.git
```

再确认：

```powershell
git remote -v
```

测试是否能连接远端：

```powershell
git fetch
```

如果正常，以后仍然直接使用：

```powershell
git push
git pull
```

如果你改的是本地远端别名，比如想把 `origin` 改成 `github`，才使用：

```powershell
git remote rename origin github
```

实际工程里通常不需要改远端别名，只改 URL 就够了。
