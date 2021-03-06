## GIT中的一些常识命令操作

* 删除untracked files => `git clean -f`
* 连untracked 目录也删除 => `git clean -fd`
* `git clean -n`显示将要删除的文件和目录
* 从当前分支新建一个分支，并切换到新建的分支=>`git checkout -b fixed-bug-234`
* 删除本地分支 => `git branch -d fixed-bug-234`
* 删除远程分支fixed-bug-234 => `git push origin :fixed-bug-234`
* 删除文件同步，`git rm` + `git commit -m '删除'`或者`rm ` + `git commit -am '删除'`
* 回退到某个版本，保留源码，回退index、commit信息 => `git reset HEAD^` 或者 `git reset --mixed HEAD^`，`--mixed`是默认参数

#### git fetch && git rebase
要声称一次新的提交
1. git fetch
2. git rebase origin/{branch}
3. 解决冲突
4. git add {冲突文件}
5. git rebase --continue

#### git stash
将本地的变更暂存起来，不影响本地其他操作
* `git stash [save {message}] [-u]`，将本地的修改放入暂存区中，`save {message}`可选，对应的暂存提示信息，`-u`可选，表示将 git 未追踪的文件(untracked files)也放入暂存区。
* `git stash list`，列出暂存区中所有的暂存信息。
* `git stash show [stash@{0}] [-p]`，`-p`是显示某次提交的暂存区中的详细区别，`stash@{0}`是指定查看哪次暂存区，默认显示第一个暂存区。
* `git stash apply [stash@{0}]`，将某个暂存区中的内容恢复，`stash@{0}`是可以指定哪次暂存区，默认第一个暂存区。
* `git stash pop [stash@{0}]`，将某个暂存区的内容恢复，并将暂存区记录删除，`stash@{0}`同上。
* `git stash drop [stash@{0}]`，删除某个暂存区记录，`stash@{0}`同上。
* `git stash clear`，清空暂存区。


#### git diff
* git中的状态分为3个状态：工作区、暂存区、提交版本
* `git diff`是工作区和暂存区的比较
* `git diff --staged`,`git diff --cached`是暂存区和提交版本(HEAD)的比较
* `git diff HEAD`是工作区和提交版本的比较
* `git diff HEAD^^ --stat`查看变动的文件列表
* `git diff {fileName}`工作区和暂存区单个文件的比较，其中fileName也可以用模糊匹配，比如`git diff *.yml`
* `git diff {commitId} {fileName}`工作区和{commitId}版本比较
* `git diff {commitId} {commidIdOther} {fileName}`比较两个提交的变动，从commitId到commitIdOther有哪些变动
* `git diff --word-diff`，按照单词进行比对

#### git revert
`git revert {commitId}`只会revert`commitId`对应的单次提交。

* `git revert HEAD`，重新建立一个提交，内容是取消上一次提交
* `git revert 3e2a6a09..HEAD`，取消从`3e2a6a09`到HEAD的每一次提交，有几次提交新建几次回滚提交记录，相当于上个文件的集合
* `git revert` 的时候提示`error: commit is a merge but no -m option was given`，此时需要制定`-m 1`参数，因为这个提交是一个合并的版本，git没法知道回退合并分支的还是被合并分支的，因此要加上`-m`参数，1表示合并分支的，2表示被合并分支的。

#### git cherry-pick
* `git cherry-pick {commitId}` 合并某个分支的commitId这次提交到当前分支，并生成一次提交

#### git rm
* `git rm /path/to/file --cahed`，从版本库中删除一个文件，本地并不删除
* `git rm -r /path/to/dir/ --cached`，从版本库中删除一个文件夹，本地不删除

#### 同步其他分支的文件到当前分支
像将其他分支的文件同步到当前分支，可以使用页面的命令，参考[https://stackoverflow.com/questions/3334475/git-how-to-update-checkout-a-single-file-from-remote-origin-master](https://stackoverflow.com/questions/3334475/git-how-to-update-checkout-a-single-file-from-remote-origin-master)

`git checkout origin/which-branch -- path/to/file`

其中的双下划线`--`表示后面的内容是一个文件，而不是一个目录

#### 合并多次提交为一个
`git rebase -i master[|commitId]`，参数`-i`意思是启动交互式模式，你可以干预整个`rebase`过程

例如，当前`git log --pretty=oneline`，结果如下所示
```
00a7aafe0062adb852392895b936cfe6099f5992 (HEAD -> master) t3
d2183db7861bfa63fbdd775ea0fca1cdca985736 t2
f0b84f8b2fcf70056e47bd51b6c91f0e2fec0b12 t1
b25e2d6f02cf96c1da37350bbce9ea4a9ce9d9cc 合并多个提交为一个
e6dadacdca9884d165c89d7205651450ab77c430 test
0fd77443f2c05c5b2a68a8a4d44082343dbe4fe9 添加source
```

运行，`git rebase -i b25e2d6f02cf96c1da37350bbce9ea4a9ce9d9cc`，会打开vim，内容修修改为如下，保存退出之后会打开合并之后的提交的日志
```
pick f0b84f8 t1
s d2183db t2
s 00a7aaf t3
```

编辑之后退出，会发现选中的三次提交合并为一个了

#### `git bisect`二分法查找问题
用来查找那次提交出现了错误

`git bisect start [commitIdA] [commitIdB]`开始在`commitIdA`和`commitIdB`之间查找错误
`git bisect good`二分之后前半段没问题，继续在后半段中查找
`git bisect good`二分之后前半段有问题，在前半段的基础上继续用二分法查找
`git bisect reset`恢复版本库至原始状态
