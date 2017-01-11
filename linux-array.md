## Linux shell中的数组

#### 数组定义
放在一行的时候，之间用空格分开，元素不连续的时候，用双引号包裹

```shell
names=(
    yanruitao
    yanruixin
    燕睿涛
    燕睿馨
    "Bruce Lee"
)
```

获取数组中元素个数：`${#array_name}`、`${#array_name[@]}`或者`${#array_name[*]}`

获取某个元素长度`${#array_name[n]}`

索引数组定义：

```shell
names=(
    [yrt]=24
    [yanruitao]=25
    [yrx]=22
    [yanruitao]=22
    [Bruce Lee]=46
)
```
