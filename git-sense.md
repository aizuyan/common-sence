## GIT中的一些常识命令操作

* 删除untracked files => `git clean -f`
* 连untracked 目录也删除 => `git clean -fd`
* `git clean -n`显示将要删除的文件和目录
* 从当前分支新建一个分支，并切换到新建的分支=>`git checkout -b fixed-bug-234`
* 删除本地分支 => `git branch -d fixed-bug-234`
* 删除远程分支fixed-bug-234 => `git push origin :fixed-bug-234`
* 删除文件同步，`git rm` + `git commit -m '删除'`或者`rm ` + `git commit -am '删除'`
* 回退到某个版本，保留源码，回退index、commit信息 => `git reset HEAD^` 或者 `git reset --mixed HEAD^`，`--mixed`是默认参数

#### git diff
* git中的状态分为3个状态：工作区、暂存区、提交版本
* `git diff`是工作区和暂存区的比较
* `git diff --staged`,`git diff --cached`是暂存区和提交版本(HEAD)的比较
* `git diff HEAD`是工作区和提交版本的比较
