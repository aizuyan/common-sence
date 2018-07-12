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
* `git diff HEAD^^ --stat`查看变动的文件列表

#### git revert
`git revert {commitId}`只会revert`commitId`对应的单次提交。

* `git revert HEAD`，重新建立一个提交，内容是取消上一次提交
* `git revert 3e2a6a09..HEAD`，取消从`3e2a6a09`到HEAD的每一次提交，有几次提交新建几次回滚提交记录，相当于上个文件的集合
* `git revert` 的时候提示`error: commit is a merge but no -m option was given`，此时需要制定`-m 1`参数 TODO 搞明白

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
