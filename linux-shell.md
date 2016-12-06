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
