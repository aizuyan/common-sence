## GIT中的一些常识命令操作

* 删除untracked files => `git clean -f`
* 连untracked 目录也删除 => `git clean -fd`
* `git clean -n`显示将要删除的文件和目录
* 从当前分支新建一个分支，并切换到新建的分支=>`git checkout -b fixed-bug-234`
* 删除本地分支 => `git branch -d fixed-bug-234`
* 删除远程分支fixed-bug-234 => `git push origin :fixed-bug-234`

