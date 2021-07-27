### linux下面的一些字符串的使用

##### 大小写转换
```
${var^} 首字符大写
${var^^} 所以字符大写
${var,} 首字符小写
${var,,} 所有字符小写
```

##### 搜索截取
```
${parameter%word} 最小限度从后面截取word
${parameter%%word} 最大限度从后面截取word
${parameter#word} 最小限度从前面截取word
${parameter##word} 最大限度从前面截取word
```

1. `#`号操作符，作用是从左边删除第一次出现子字符串及其左边的字符，保留右的字符，
用法为`#*substr`，例如：

```shell
str="https://github.com/aizuyan/common-sence"
echo ${str#*//}
```

上面的一小段脚本会输出截取的字符串`github.com/aizuyan/common-sence`

2. `##`号操作符，从左边开始删除最后一次出现子串及其左边字符，保留右边字符，
用法为`##*substr`，例如：

```shell
str="https://github.com/aizuyan/common-sence"
echo ${str##*/}
```

上面的一小段脚本会输出截取的字符串`common-sence`

3. `%`号，从右边开始删除第一次出现子字符串及其右边的字符，保留左边的字符，
用法为`%substr*`，例如：

```shell
str="https://github.com/aizuyan/common-sence"
echo ${str%/*}
```

上面的脚本输出`https://github.com/aizuyan`

4. `%%`号，从右边开始删除最后一次出现的子串及其右边的字符，保留左边的字符，
用法为`%%substr*`，例如：

```shell
str="https://github.com/aizuyan/common-sence"
echo ${str%%/*}
```

上面的脚本输出为`https:`


##### 普通截取

1. 从左边第M个字符开始截取N个字符

```shell
str="https://github.com/aizuyan/common-sence"
echo ${str:0:5}
```

上面的脚本输出`https`，其中0表示从第一个字符开始，5表示截取5个字符

2. 从左边第M个字符开始知道结束

```shell
str="https://github.com/aizuyan/common-sence"
echo ${str:8}
```

上面的脚本输出为`github.com/aizuyan/common-sence`，8表示从第9个字符开始，知道结束

3. 从后面第M个字符开始截取N个字符

```shell
str="https://github.com/aizuyan/common-sence"
echo ${str:0-20:7}
```

上面的脚本输出为`aizuyan`，0-20表示从右边起往左边数20个字符，7表示截取字符的个数


4. 从右边第M个字符开始到结束

```shell
str="https://github.com/aizuyan/common-sence"
echo ${str:0-12}
```

上面的脚本输出为`common-sence`，0-12表示最后12个字符

























