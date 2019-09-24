## Linux常用命令笔记

#### 批量删除匹配文件
批量删除匹配到后缀名为`.phpg`的文件
```shell
find . -name "*.phpg" | xargs rm
```

#### 批量替换修改文件
将后缀名为`.php`的文件中所有的`燕睿涛@zuoyebang`替换为`ritoyan@163.com`
```
sed -ig "s/燕睿涛@zuoyebang/ritoyan@163/g" `find . -name "*.php"`
```

#### 执行shell脚本的几种方式
* `source a.sh`，在当前shell进程去读取执行a.sh，a.sh不需要执行权限。
* `. a.sh`，同上，`.`是`source`的简写。
* `sh a.sh`、`bash a.sh`，开启一个子shell进程，去执行a.sh，a.sh不需要执行权限。
* `./a.sh`，开启一个子shell进程，读取执行a.sh，a.sh需要执行权限。
